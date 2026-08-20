# AtlasBadge C33–C34 Production Traceability

**Document status:** Completed change record  
**Target release:** AtlasBadge V1.0  
**Owner:** Test Lead/Product Owner  
**Date:** 20 August 2026

## 1. Purpose

This document records the C33 and C34 AtlasBadge changes from requirement clarification through implementation, defect investigation, automated regression, security-rule validation and Production sign-off.

It preserves traceability between product decisions, quality risks, implementation commits, permanent tests, defects and selected public evidence.

Detailed public evidence:

- [AB-EV-031 — Dashboard selected-place sorting](../evidence/v1.0/regression/ab-ev-031-dashboard-selected-place-sorting.md)
- [AB-EV-032 / AB-DEF-013 — Manual visit order and rapid-visit concurrency closure](../evidence/v1.0/defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)

---

## 2. C33 — Dashboard selected-place sorting

### 2.1 User need

The Map tab already displayed the user's selected places, but the list did not provide an explicit way to alternate between chronological travel order and alphabetical order.

C33 added a local presentation control without changing persisted travel data.

### 2.2 Accepted behaviour

The Map tab exposes:

```text
[ Alfabética | Ordem de Visita ]
```

The accepted rules are:

- default Map order is **Ordem de Visita**;
- the Map choice is local/session presentation state and is not persisted as a user preference;
- physical-presence places use the canonical Profile visit-order comparator;
- ranked physical places precede unranked physical places;
- unranked physical places retain the historical chronological fallback;
- non-physical selected records remain available in the dashboard but sort after physical records in Visit Order;
- alphabetical order uses `pt-BR` name comparison with `countryId` as the deterministic tie-breaker;
- changing the sort must not alter search, filters, selected-country state, statuses, visits, counters or Firestore data.

### 2.3 Layout acceptance

Desktop C33 preserves a two-row Map header:

```text
📍 Atualizar Mapa                [ Alfabética | Ordem de Visita ]
[ Buscar país... ] [ Filtros ] [ Limpar filtros ]
```

Responsive behaviour was validated without horizontal overflow or loss of the existing Map interactions.

### 2.4 Implementation traceability

C33 was delivered through the following product commits:

```text
dcf083084f2480bcedcf8cf72be3f6dd2c26cb3e
feat(profile): sort dashboard places and fix e2e tests for flag navigation

9671434b1ff5ee818abad3678edf45e4f3d304eb
test(dashboard): complete sorting e2e test with data integrity check

5f5c7b86173268a5b9eb3c9cf4a6f4db30b94bc3
test(dashboard): correct selected places filter assertion and locators

0164b8c6f2cb25dc510248b43d39a7d1ca1dc538
chore(e2e): fix typescript any lint errors in dashboard tests
```

Main implementation/test scope:

- `src/app/app/page.tsx`;
- `src/lib/badgeSorting.ts`;
- `e2e/dashboard-selected-places-sorting.spec.ts`;
- supporting flag/profile-navigation regression.

### 2.5 Validation and Production

Final C33 closure recorded:

- TypeScript — PASS;
- Vitest — 302 tests across 39 files PASS;
- ESLint — PASS;
- Next.js production build — PASS;
- `git diff --check` — PASS;
- focused C33/C32/flag-sort E2E — 5 PASS;
- Test Lead manual validation — PASS;
- Production Guard — PASS;
- `validate-production.spec.ts` — PASS;
- `validate-clear-map.spec.ts` — PASS.

Final C33 automatic Vercel deployment:

```text
dpl_4a94LAa8p1D57y3cHpzNHRGATQMq
Production / READY
Product SHA: 0164b8c6f2cb25dc510248b43d39a7d1ca1dc538
```

**C33: FIXED / PRODUCTION PASS.**

---

## 3. C34 — Manual visit-order correction

### 3.1 User need

Automatic chronology cannot always infer the user's true travel order, especially for legacy records, incomplete dates or historical data. C34 allows the account owner to correct the order manually while keeping the public Profile read-only.

### 3.2 Final UX contract

Editing exists only in `/app` → Map → **Atualizar Mapa**.

Normal desktop presentation:

```text
📍 Atualizar Mapa        [ Editar ordem ] [ Alfabética | Ordem de Visita ]
[ Buscar país... ] [ Filtros ] [ Limpar filtros ]
```

The Profile keeps only the presentation sort toggle:

```text
[ Alfabética | Ordem de Visita ]
```

The Profile does not expose Edit, Save, Cancel or drag handles.

### 3.3 Editor business rules

`Corrigir Ordem de Visita` contains only places with qualifying physical presence.

- **Born there** remains fixed at the top and does not require a numeric rank;
- movable physical places can be reordered with mouse/touch/keyboard-capable drag handles;
- Wishlist-only and Nationality-only places do not participate in visit chronology and therefore do not appear in the editor;
- Cancel performs zero persistence;
- Save persists the movable physical sequence;
- no visit, memory, status or chronology timestamp may be fabricated solely to support ordering.

The editor microcopy explicitly states that it orders **visited places**.

### 3.4 Persistence model

C34 introduced the optional field:

```text
visitOrderRank?: number
```

under:

```text
users/{uid}/places/{placeId}
```

The accepted rule is an integer rank `>= 1` for ranked movable physical places. Firestore Rules reject invalid rank types/values while preserving the existing owner, account and e-mail-verification controls.

Manual-order persistence uses focused rank updates rather than replacing status/visit/memory payloads. The historical comparator remains available for unranked physical entries.

### 3.5 Defects discovered during C34 validation

C34 validation exposed material persistence/concurrency issues that blocked release:

1. the live Firestore Rules initially did not permit the new `visitOrderRank` field, causing `PERMISSION_DENIED` on Save;
2. rapid repeated visit additions/removals could encounter concurrency conflicts or lose queued local intent;
3. an intermediate blanket concurrency bypass would have skipped the transaction that keeps `statuses.born` and `birthplacePlaceId` consistent;
4. an intermediate integration commit contained unrelated mass-edits to existing E2E files and an accidental empty planning artefact.

The Test Lead did not accept those states as the final baseline.

The rapid-visit product regression is formally recorded as **AB-DEF-013** in AB-EV-032.

### 3.6 Final concurrency and birthplace design

Visit mutations use replayable semantic intents:

- `addVisit` generates a stable visit ID before replay and is idempotent;
- `removeVisit` is replayable/idempotent by visit ID;
- `saveVisit` updates the target visit on the latest available state and preserves other visits;
- `visitsCount` is derived from `registeredVisits.length`.

The mutation orchestrator passes the processed intent set to the persistence layer. Normal rapid visit mutations may use the safe concurrency-bypass path, while a `setStatus` intent for `born` explicitly disables that bypass.

This preserves the transaction that atomically maintains:

```text
users/{uid}.birthplacePlaceId
+
users/{uid}/places/{placeId}.statuses.born
```

Firestore Rules retain the birthplace-consistency invariant instead of weakening security to make the test pass.

### 3.7 Implementation history and clean baseline

The first combined C34/rapid-mutation implementation was committed as:

```text
39b543ea0ad7dada5f993f8f8d8702f964da33d9
fix: resolve concurrent visit mutations and P0 regression
```

QA review found that this commit also contained unrelated E2E troubleshooting replacements and an accidental `implementation_plan.md`. Those artefacts were not accepted as the clean release baseline.

The final corrective/hygiene commit is:

```text
8474a78d1f7e6d046b3e918b3be8ac2af01188fc
fix(data): preserve birthplace integrity and clean C34 artifacts
```

The final commit restores the pre-C34 E2E baseline where unrelated, removes accidental artefacts/duplicate exports, preserves the C34 tests, makes persistence intent-aware and retains the birthplace security invariant.

### 3.8 Final automated validation

Final pre-release gates after repository cleanup:

```text
TypeScript: PASS
Vitest: 308 passed, 7 skipped, 0 failed
ESLint: PASS
Next.js build: PASS
Firestore Rules: 224 passed, 0 failed
Focused Emulator E2E: 9 passed, 0 failed
git diff --check: PASS
```

The nine focused E2E scenarios covered:

- C33 dashboard sort/filter/search integrity;
- responsive dashboard sorting;
- flag visit-order regression;
- full C34 manual reorder flow;
- viewer read-only/mobile behaviour;
- Profile map-to-flag desktop/mobile regression;
- rapid `+++` / `+++++` visit mutation;
- rapid `---` removal.

### 3.9 Production release

Final automatic Vercel deployment:

```text
dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi
Production / READY
Product SHA: 8474a78d1f7e6d046b3e918b3be8ac2af01188fc
```

The changed Firestore Rules were deployed separately with the rules-only target.

Final Production validation:

```text
Production Guard: PASS
validate-production.spec.ts: 1 passed (14.3s)
validate-clear-map.spec.ts: 1 passed (25.6s)
Manual Visit Order smoke: PASS
Rapid Visits smoke: PASS
Birthplace integrity smoke: PASS
```

No remaining `PERMISSION_DENIED`, concurrency error or known lost-update condition was accepted in the Production sign-off.

**AB-DEF-013: Closed.**  
**C34: FIXED / PRODUCTION PASS — CLEAN BASELINE.**

---

## 4. Combined traceability matrix

| Change | Requirement / risk | Implementation | Permanent/automated evidence | Production evidence | Final decision |
|---|---|---|---|---|---|
| C33 | QR-18, QR-19, QR-39, QR-40; deterministic selected-place presentation without data mutation | `dcf0830` → test hardening → `0164b8c` | `dashboard-selected-places-sorting.spec.ts`; flag/profile regression; 302 Vitest final C33 baseline | `dpl_4a94LAa8p1D57y3cHpzNHRGATQMq`; Production Guard; validate-production; validate-clear-map | AB-EV-031; FIXED / PRODUCTION PASS |
| C34 | QR-01, QR-04, QR-18, QR-19, QR-22, QR-23, QR-33, QR-39, QR-40; owner-only manual chronology with read-only Profile | `39b543e` implementation → clean final `8474a78` | 224 Rules; 308 Vitest; 9 focused Emulator E2E; `map-manual-visit-order.spec.ts`; `rapid-visit-mutation.spec.ts` | Rules-only deploy; `dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi` READY; Production Guard; both Production validators | AB-EV-032 / AB-DEF-013 closed; FIXED / PRODUCTION PASS |

---

## 5. Current accepted baseline after C34

```text
Product main: 8474a78d1f7e6d046b3e918b3be8ac2af01188fc
Vercel: dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi — Production / READY
C33: FIXED / PRODUCTION PASS
C34: FIXED / PRODUCTION PASS — CLEAN BASELINE
AB-DEF-013: Closed
Counters: 252 Places / 195 Countries / 57 Territories and Entities
Profile: read-only; no visit-order editing
Map: Visit Order / Alphabetical + owner Manual Visit Order editing
Rapid visit mutations: replayable/idempotent regression coverage
Birthplace: transactional pointer/status integrity retained
Firestore Rules: 224/224 PASS at final C34 gate
Focused Emulator E2E: 9/9 PASS
Production Guard: PASS
validate-production: PASS
validate-clear-map: PASS
```

This record is the accepted C33–C34 baseline for subsequent AtlasBadge V1.0 development and final release assessment.
