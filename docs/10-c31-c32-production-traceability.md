# AtlasBadge C31–C32 Production Traceability

**Document status:** Completed change record  
**Target release:** AtlasBadge V1.0  
**Owner:** Test Lead/Product Owner  
**Date:** 19 August 2026

## 1. Purpose

This document provides a concise change-level traceability record for the C31 and C32 AtlasBadge Production corrections/refinements.

It connects the approved product rules to implementation commits, tests, quality risks, defects, evidence and Production sign-off without duplicating the complete operational logs.

The detailed public evidence remains in:

- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity](../evidence/v1.0/defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-030 — Profile map to earned-flag navigation](../evidence/v1.0/smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)

---

## 2. Change C31 — Geographic counter integrity

### 2.1 Trigger

A Test Lead Production observation after C26 showed:

```text
9/251 Places
5/195 Countries
5/56 Territories and Entities
```

The two category counters summed to ten while Places remained nine. AB-EV-027 recorded this as a reopened QR-25 gap and explicitly deferred further arithmetic changes until the catalogue classification was audited.

### 2.2 Requirement clarified by the Test Lead

The accepted rule is:

- all 194 directly selectable sovereign countries belong to Countries;
- completing England, Scotland, Wales and Northern Ireland derives one additional United Kingdom Country;
- the same UK completion also derives one additional conceptual Place;
- the four UK constituents remain individual Territory/Entity contributions;
- Antarctica belongs to Places and Territories/Entities, not Countries;
- all other approved territory and limited-recognition classifications remain in Territory/Entity progress;
- the Places counter must remain mathematically consistent with the two displayed progress categories.

### 2.3 Canonical catalogue and progress model

Directly selectable catalogue:

```text
194 sovereign_country
47 territory
5 limited_recognition
4 constituent_country
1 special_region (Antarctica)
--------------------------------
251 directly selectable records
```

Conceptual progress totals:

```text
252 Places
195 Countries
57 Territories and Entities
```

Mandatory invariant:

```text
Places = Countries + Territories and Entities
252 = 195 + 57
```

### 2.4 Acceptance examples

UK constituents only:

```text
0/4 → 0 Places / 0 Countries / 0 Territories-Entities
1/4 → 1 Place / 0 Countries / 1 Territory-Entity
3/4 → 3 Places / 0 Countries / 3 Territories-Entities
4/4 → 5 Places / 1 Country / 4 Territories-Entities
```

Antarctica only:

```text
1 Place / 0 Countries / 1 Territory-Entity
```

Original Test Lead scenario after correction:

```text
10/252 Places
5/195 Countries
5/57 Territories and Entities
```

Full completion:

```text
252/252 Places
195/195 Countries
57/57 Territories and Entities
```

### 2.5 Achievement acceptance

| Achievement | Accepted C31 rule |
|---|---|
| A15 — United Kingdom | Four UK constituent countries, `4/4` |
| A18 — Lenda Atlas | `252/252 Places` |
| A31 — Mundo Completo | `195/195 Countries` |
| A32 — Além das Fronteiras | `57/57 Territories and Entities`, including Antarctica |

### 2.6 Implementation

```text
627f4f948f7b6af0151fec77ffe800380e73e989
fix(stats): align world coverage counters
```

Main implementation/test impact:

- `src/lib/travelCoverage.ts`
- `src/lib/travelCoverage.test.ts`
- `src/lib/stats.ts`
- `src/data/mock.ts`

### 2.7 Validation

Local/change gates:

- TypeScript — PASS
- Vitest — 294 tests / 39 suites PASS at C31 local sign-off
- ESLint — PASS
- Next.js build — PASS
- `git diff --check` — PASS
- Test Lead manual local validation — PASS

Production:

- automatic Vercel deployment — READY
- Production Guard — PASS
- `validate-production.spec.ts` — PASS
- `validate-clear-map.spec.ts` — PASS
- counter/achievement Production verification — PASS

### 2.8 Defect/risk decision

- **AB-DEF-012:** Closed
- **QR-25:** Current gap → Regression risk
- **C31:** FIXED / PRODUCTION PASS

Public evidence: **AB-EV-029**.

---

## 3. Change C32 — Profile Map → Earned Flag Auto-Navigation

### 3.1 User need

The Profile map could show a location without giving the viewer an easy way to identify its corresponding named flag in the earned-flags grid.

The required refinement was:

> Click a conquered place on the read-only Profile map, scroll to the exact matching earned flag, temporarily highlight it, and preserve the read-only data contract.

### 3.2 Acceptance criteria

A qualifying map click must:

1. resolve the canonical `countryId`;
2. find the exact matching earned flag;
3. smooth-scroll the card into view;
4. apply a temporary highlight for approximately two seconds;
5. clear the highlight automatically;
6. work independently of flag sort position;
7. support normal geographies and micro-markers;
8. work in a representative mobile viewport;
9. make no persisted travel-data change.

If no earned flag exists:

- no unrelated card may be highlighted;
- no edit UI may open;
- no mutation may occur.

### 3.3 Read-only invariant

C32 must not:

- open CountryActionCard;
- change statuses;
- change visits;
- change counters;
- change achievements;
- write travel data to Firestore;
- create a virtual UK flag;
- modify flag sort order.

### 3.4 Implementation

```text
c75891b68836ca495e319c31878c3cb230f4f92e
feat(profile): navigate map clicks to earned flags
```

Implementation/test impact:

- `src/app/[username]/page.tsx`
- `src/components/CountryBadgeGrid.tsx`
- `src/components/CountryBadgeGrid.test.tsx`
- `e2e/profile-map-flag-navigation.spec.ts`

Flag cards expose deterministic identity through `data-badge-country-id`, while the Profile map uses its existing `onCountryClick` pathway.

### 3.5 Automated and manual validation

Pre-commit QA closure:

- TypeScript — PASS
- Vitest — 304 tests / 41 files PASS
- ESLint — PASS
- Next.js build — PASS
- dedicated C32 Playwright E2E — PASS
- flag-sort E2E regression — PASS
- Test Lead manual local validation — PASS

Permanent C32 coverage includes:

- normal geography;
- second-country retargeting;
- micro-marker;
- no-flag fallback;
- read-only proof;
- alphabetical sort;
- visit-order sort;
- mobile viewport.

Production:

- automatic Vercel deployment — READY
- Production Guard — PASS
- `validate-production.spec.ts` — PASS
- `validate-clear-map.spec.ts` — PASS
- C27 micro-marker regression — PASS
- C31 counter regression — PASS

### 3.6 Risk decision

C32 did not introduce a new defect ID because it was an approved usability refinement rather than correction of an existing product failure.

It strengthens regression evidence for:

- QR-26 — geographic/map interaction;
- QR-33 — public Profile remains read-only;
- QR-39 — responsive/touch/navigation behaviour;
- QR-40 — interaction/accessibility regression scope.

**C32: FIXED / PRODUCTION PASS.**

Public evidence: **AB-EV-030**.

---

## 4. Combined traceability matrix

| Change | Requirement / risk | Implementation | Automated evidence | Production evidence | Final decision |
|---|---|---|---|---|---|
| C31 | QR-18, QR-25, QR-29, QR-30; canonical counter integrity | `627f4f948f7b6af0151fec77ffe800380e73e989` | Travel coverage/achievement regression; 294 local tests at sign-off | Production Guard, validate-production, validate-clear-map | AB-DEF-012 closed; QR-25 Regression risk; FIXED / PRODUCTION PASS |
| C32 | QR-26, QR-33, QR-39, QR-40; read-only map-to-flag navigation | `c75891b68836ca495e319c31878c3cb230f4f92e` | CountryBadgeGrid tests; dedicated Playwright navigation E2E; 304 tests / 41 files | Production Guard, validate-production, validate-clear-map | FIXED / PRODUCTION PASS |

---

## 5. Evidence and documentation control

The following documentation was updated as part of this traceability closure:

- Quality Risk Analysis;
- V1.0 Evidence Register;
- defect evidence index;
- regression evidence index;
- smoke evidence index;
- V1.0 evidence README;
- AB-EV-027 historical follow-up record;
- this C31–C32 change record.

The historical `9/251 · 5/195 · 5/56` observation remains intentionally visible in AB-EV-027 because it explains why C31 was required. It is not the current product rule.

No C31/C32 Vercel deployment ID is invented where the operational sign-off did not preserve a literal identifier. The verified product commit, `READY` deployment state and Production validation results remain the public traceability anchors.

---

## 6. Current accepted baseline after C32

```text
Product main: c75891b68836ca495e319c31878c3cb230f4f92e
C31: FIXED / PRODUCTION PASS
C32: FIXED / PRODUCTION PASS
Counters: 252 Places / 195 Countries / 57 Territories and Entities
Profile map: canonical read-only map with earned-flag auto-navigation
Production Guard: PASS
validate-production: PASS
validate-clear-map: PASS
```

This baseline remains subject to subsequent AtlasBadge V1.0 development and final Test Lead release approval.
