# AB-EV-032 / AB-DEF-013 — Manual visit order and rapid-visit concurrency closure

**Evidence status:** Production approved  
**Related quality risks:** QR-01, QR-04, QR-18, QR-19, QR-22, QR-23, QR-33, QR-39, QR-40  
**Target release:** AtlasBadge V1.0  
**Defect severity:** High  
**Defect priority:** P0  
**Final status:** AB-DEF-013 closed; C34 FIXED / PRODUCTION PASS — CLEAN BASELINE

## 1. Evidence purpose

This record documents C34 Manual Visit Order and the material persistence/concurrency failures discovered during its validation.

The change itself was an approved product enhancement. During QA, however, repeated visit mutations and the interaction between new ordering persistence, Firestore Rules and birthplace integrity exposed release-blocking behaviour. The rapid-visit regression is formally tracked here as **AB-DEF-013**.

## 2. C34 approved product rule

Manual visit-order editing belongs only to the authenticated Map tab.

The public Profile remains read-only.

The owner sees:

```text
[ Editar ordem ] [ Alfabética | Ordem de Visita ]
```

within **Atualizar Mapa**.

The Profile retains only the display sort toggle and never exposes edit/save/cancel controls or drag handles.

## 3. Manual-order acceptance criteria

The editor contains only places with qualifying physical presence.

- **Born there** is fixed at the top;
- movable physical places are reorderable;
- Wishlist-only and Nationality-only places are intentionally absent because they are not visits;
- Cancel makes zero writes;
- Save persists the new physical visit order;
- no artificial visit, status, memory or physical-presence timestamp is created to support ordering.

C34 introduced:

```text
visitOrderRank?: number
```

for ranked movable places.

Firestore Rules accept the field only when it is an integer `>= 1` and retain the existing owner/account/e-mail-verification protections.

## 4. Initial observed failures

### 4.1 Manual-order Save

The Test Lead initially observed:

```text
Erro ao salvar a nova ordem
FirebaseError: Missing or insufficient permissions
```

The local code and emulator rules understood `visitOrderRank`, but the live Firebase Rules did not yet permit that field. A rules-only Production deployment corrected that parity failure and Manual Visit Order Save then passed.

### 4.2 Rapid visit mutation

After the Rules correction, repeatedly pressing the visit increment control still produced a failure such as:

```text
Erro de conexão. Tente novamente.
```

The failure was independently reproducible and therefore could no longer be attributed to the rules mismatch.

Expected behaviour for rapid input was deterministic accumulation with no lost update, for example:

```text
1 + + + = 4
4 + + + + + = 9
```

and equivalent safe removal where the product rule permits it.

This regression was classified as **AB-DEF-013**, High severity / P0 priority because an ordinary repeated user action could fail to persist travel history correctly.

## 5. Rapid-visit root cause

The previous visit mutation path was not robust to closely queued changes. Sequential mutations could be processed against stale confirmed state or encounter optimistic-concurrency protection before the previous local intent had converged.

The correction moved visit operations to replayable semantic intents and made the intent itself deterministic/idempotent.

### Add visit

`addVisit` now:

- creates the visit ID once before replay;
- reuses that same ID if the intent is replayed;
- refuses to add a duplicate with the same ID;
- derives `visitsCount` from the resulting `registeredVisits.length`.

### Remove visit

`removeVisit`:

- operates by stable visit ID;
- is replayable;
- becomes a no-op if the same visit is already absent rather than corrupting the queue.

### Save visit

`saveVisit`:

- locates the target visit by ID on the latest state;
- replaces that visit while preserving other registered visits;
- derives the visit count from the final array.

Permanent regression was added in:

```text
e2e/rapid-visit-mutation.spec.ts
```

## 6. Birthplace integrity blocker found during hardening

An intermediate solution applied the fast concurrency-bypass persistence path to all orchestrated mutations.

QA review found that this was unsafe for the `born` status because the normal Firestore transaction is responsible for keeping these two records consistent:

```text
users/{uid}.birthplacePlaceId
users/{uid}/places/{placeId}.statuses.born
```

A blanket bypass could update the place document without atomically updating the user pointer.

The correct fix therefore kept the security invariant rather than weakening Firestore Rules.

## 7. Final intent-aware persistence design

The final mutation orchestrator passes the processed intent set to its persistence callback.

The persistence layer evaluates whether the batch contains a `setStatus` intent for `born`.

- rapid visit intents may use the concurrency-bypass path required for safe queued local mutation;
- `born` explicitly disables that bypass and uses the normal transaction;
- the transaction updates/removes `birthplacePlaceId` together with the place's `born` status;
- Firestore Rules retain `isPlaceBirthplaceConsistent` and validate the post-write state.

This separates two different needs instead of trading one data-integrity risk for another.

## 8. Repository-hygiene release blocker

The first combined implementation commit was:

```text
39b543ea0ad7dada5f993f8f8d8702f964da33d9
fix: resolve concurrent visit mutations and P0 regression
```

A post-deployment QA audit found unrelated troubleshooting edits to existing E2E files, duplicate component exports and an accidental empty `implementation_plan.md` in the commit.

Those changes were not accepted as the final release baseline.

All unrelated E2E files were restored to the pre-C34 baseline, temporary artefacts were removed, duplicate exports were cleaned and the birthplace persistence fix was retained in:

```text
8474a78d1f7e6d046b3e918b3be8ac2af01188fc
fix(data): preserve birthplace integrity and clean C34 artifacts
```

## 9. Firestore Rules validation

Final Rules coverage recorded:

```text
Total: 224
Passed: 224
Failed: 0
Skipped: 0
```

The suite covers the new rank field and the existing access/integrity boundaries, including:

- valid positive integer ranks;
- absence of the optional rank;
- rejection of zero, negative, floating, string and boolean values;
- non-owner rejection;
- unverified-owner rejection;
- birthplace pointer/status consistency.

The final release used a **Firestore Rules-only** deployment after approval.

## 10. Final regression results

Final gates after cleanup:

```text
TypeScript: PASS
Vitest: 308 passed / 7 skipped / 0 failed
ESLint: PASS
Next.js production build: PASS
Firestore Rules: 224 passed / 0 failed
git diff --check: PASS
```

Focused Firebase Emulator E2E:

```text
9 passed
0 failed
```

The nine scenarios covered:

- C33 dashboard sorting/filter/search data integrity;
- C33 mobile layout;
- flag visit-order regression;
- C34 full manual reorder flow;
- C34 viewer read-only/mobile behaviour;
- C32 Profile map-to-flag desktop regression;
- C32 Profile map-to-flag mobile regression;
- rapid `+++` / `+++++` visits;
- rapid `---` removal.

## 11. Production release evidence

Final product commit:

```text
8474a78d1f7e6d046b3e918b3be8ac2af01188fc
```

Automatic Vercel deployment:

```text
dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi
Production / READY
```

Final Production validation:

```text
Production Guard: PASS
validate-production.spec.ts: 1 passed (14.3s)
validate-clear-map.spec.ts: 1 passed (25.6s)
Manual Visit Order smoke: PASS
Rapid Visits smoke: PASS
Birthplace integrity smoke: PASS
```

The final smoke found no accepted `PERMISSION_DENIED`, stale concurrency conflict or lost-update condition in the validated paths.

## 12. Test Lead decision

**AB-DEF-013 closed.**

C34 Manual Visit Order is approved in Production with:

- owner-only editing on the Map tab;
- read-only public Profile;
- optional validated `visitOrderRank` persistence;
- permanent rapid-add/remove regression coverage;
- replayable/idempotent visit intents;
- transactional birthplace pointer/status integrity;
- clean repository baseline;
- Production Guard and both dedicated Production validators passing.

**C34 FIXED / PRODUCTION PASS — CLEAN BASELINE.**

QR-04 remains a **Regression risk** because optimistic mutation, listener reconciliation and multi-action concurrency remain intrinsically high-value regression areas even after the defect closure.

## 13. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-032 |
| Defect | AB-DEF-013 |
| Related risks | QR-01, QR-04, QR-18, QR-19, QR-22, QR-23, QR-33, QR-39, QR-40 |
| C34/rapid implementation | `39b543ea0ad7dada5f993f8f8d8702f964da33d9` |
| Final clean product baseline | `8474a78d1f7e6d046b3e918b3be8ac2af01188fc` |
| Final Vercel deployment | `dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi` — READY |
| Firestore Rules | 224/224 PASS; rules-only Production deployment |
| Permanent E2E | `e2e/map-manual-visit-order.spec.ts`; `e2e/rapid-visit-mutation.spec.ts` |
| Focused Emulator regression | 9/9 PASS |
| Production validation | Production Guard; validate-production PASS; validate-clear-map PASS |
| Result | AB-DEF-013 closed; C34 FIXED / PRODUCTION PASS — CLEAN BASELINE |
