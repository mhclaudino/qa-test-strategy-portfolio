# AB-EV-013 — QR-04 Real-Time Synchronisation and Concurrency Production Validation

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-013 |
| Related quality risks | QR-04; affected-area coverage for QR-01, QR-18 and QR-19 |
| Product | AtlasBadge V1.0 |
| Evidence type | Runtime correction, permanent automated regression and Production smoke |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-07-31 |
| Final decision | Passed — QR-04 moved to Regression risk |

## 2. Purpose

This evidence records the closure of the AtlasBadge risk in which two tabs or devices could work from stale travel data and silently overwrite a confirmed change.

The implemented control combines:

- Firestore real-time snapshots;
- confirmed-state reconciliation;
- preservation of local optimistic mutations;
- optimistic concurrency control;
- listener cleanup;
- UID isolation;
- permanent automated coverage;
- controlled two-tab Production smoke.

## 3. Technical implementation

| Control | Implementation position |
|---|---|
| Real-time travel-data subscription | `subscribeToUserPlaces` in `src/lib/travelMap.ts` |
| Pending-write and confirmed-snapshot handling | Firestore snapshot metadata processed by the travel-map subscription |
| Confirmed-state reconciliation | `syncConfirmedState` in `src/lib/mutationOrchestrator.ts` |
| Optimistic concurrency control | `updatedAt` comparison during `upsertUserPlace` |
| Conflict signal | Recoverable `CONCURRENCY_CONFLICT` handling |
| Cache integrity | UID-scoped cache receives consolidated confirmed state |
| Authentication lifecycle | Listener cleanup and state isolation in `src/context/TravelMapContext.tsx` |
| Shared types | `src/types/index.ts` |

Commits:

- runtime implementation: `34b88f3` — `feat(map): real-time multi-device synchronization with OCC`;
- permanent automated coverage: `c200132` — `test(sync): cover QR-04 concurrent updates`.

The full SHAs remain available in the private product repository and release history.

## 4. Confirmed behaviour

The approved synchronisation contract is:

1. Firestore remains the authenticated source of truth.
2. Confirmed changes from another tab are applied without a manual refresh.
3. Changes to different place documents are preserved.
4. A snapshot with a pending local write is not treated as independent remote confirmation.
5. A remote snapshot does not silently remove an optimistic local mutation that is still pending.
6. Confirmed state updates the UID-scoped cache.
7. A rejected local mutation rolls back to the last confirmed state.
8. A stale writer is prevented from silently overwriting a newer confirmed version.
9. Conflict recovery retries from the latest confirmed version.
10. Logout or UID change removes the previous listener and prevents cross-user state reuse.

## 5. Permanent automated regression

The repository retains:

```text
src/context/TravelMapContext.qr04.test.tsx
```

| ID | Scenario | Result |
|---|---|---|
| QR04-UT-01 | Listener receives initial confirmed state. | Passed |
| QR04-UT-02 | Snapshot with a pending local write is not treated as remote confirmation. | Passed |
| QR04-UT-03 | Confirmed remote change updates the application without F5. | Passed |
| QR04-UT-04 | Changes to different places are preserved. | Passed |
| QR04-UT-05 | A remote update does not silently erase a pending local mutation. | Passed |
| QR04-UT-06 | Local confirmation updates confirmed state and cache. | Passed |
| QR04-UT-07 | Rejected local write rolls back to the last confirmed state. | Passed |
| QR04-UT-08 | Concurrent visit updates retain the correct final value. | Passed |
| QR04-UT-09 | Logout removes the active listener. | Passed |
| QR04-UT-10 | UID change isolates the previous user's state. | Passed |
| QR04-UT-11 | A late snapshot after cleanup is ignored. | Passed |
| QR04-UT-12 | Listener error preserves confirmed state. | Passed |
| QR04-UT-13 | Confirmed server snapshot prevails over an earlier cache snapshot. | Passed |
| QR04-UT-14 | Retry after conflict converges without duplication. | Passed |

Execution summary:

- focused QR-04 tests: **14/14 Passed**;
- full Vitest suite: **Passed**;
- directed ESLint validation: **Passed**;
- global lint: **Passed**;
- Next.js production build: **Passed**;
- `git diff --check`: **Passed**;
- test file committed permanently;
- HEAD and `origin/main` aligned after push.

## 6. Production deployment

The Vercel Production deployment containing the QR-04 runtime implementation and permanent test commit reached **Ready**.

| Item | Result |
|---|---|
| Environment | Vercel Production |
| Branch | `main` |
| Runtime commit included | `34b88f3` |
| Permanent test commit included | `c200132` |
| Deployment status | Ready |
| Deployment after push | Confirmed |

No empty commit, force push, Firebase Rules modification or Production failure injection was used.

## 7. Controlled two-tab Production smoke

The Test Lead executed the smoke in two authenticated tabs using the same controlled account and restorable travel data.

The exact browser version and raw screenshots are not included in the public portfolio. No user identity, private travel data, token, environment value or session detail is published.

| ID | Scenario | Result | Observation |
|---|---|---|---|
| QR04-PS-01 | Change made in tab A appears in tab B without F5. | Passed | Remote confirmed state propagated automatically. |
| QR04-PS-02 | Different places are changed from separate tabs. | Passed | Both changes were preserved and remained after refresh. |
| QR04-PS-03 | Status is confirmed in one tab and visits are then changed in the other. | Passed | Both tabs converged and displayed consistent visit metrics. |
| QR04-PS-04 | Near-concurrent changes target the same place. | Passed | No silent data loss occurred; conflict handling remained recoverable. |
| QR04-PS-05 | Logout is performed in one tab. | Passed | The authenticated session ended automatically in the other tab as well, preventing further private synchronisation after logout. |
| QR04-PS-06 | Controlled test data is restored. | Passed | Status, visits and totals returned to the initial baseline. |

## 8. Logout interpretation

Automatic logout of both tabs is the expected safe result for a shared Firebase authenticated browser session.

The important QR-04 and privacy controls are satisfied because:

- the listener is no longer allowed to continue applying private travel updates;
- no authenticated state remains active in the second tab;
- no data from a previous UID is reused after the session ends;
- the user is not shown stale private content as though the session were still valid.

This behaviour is not classified as a defect.

## 9. Residual risk

QR-04 is retained as **Regression risk** rather than removed from the Quality Risk Analysis.

Future changes could still regress:

- listener lifecycle;
- pending-write detection;
- confirmed-cache handling;
- concurrency preconditions;
- conflict retry;
- UID isolation;
- multi-tab authentication propagation.

The Production manual execution covered two tabs. The same Firestore listener and concurrency controls are not tab-specific, while the permanent automated suite retains the broader concurrency contract.

## 10. Traceability

```text
QR-04
→ stale multi-tab or multi-device state
→ Firestore onSnapshot subscription
→ pending-write metadata handling
→ confirmed-state reconciliation
→ updatedAt optimistic concurrency control
→ recoverable CONCURRENCY_CONFLICT
→ listener cleanup and UID isolation
→ runtime commit 34b88f3
→ 14 permanent tests
→ test commit c200132
→ Vercel Production Ready
→ two-tab Production smoke
→ automatic cross-tab logout confirmed
→ test data restored
→ AB-EV-013
→ QR-04 Regression risk
```

## 11. Final conclusion

QR-04 is approved for AtlasBadge V1.0.

Real-time synchronisation, concurrency protection, permanent automated regression and controlled Production validation passed. No further technical or release action remains open for this risk.
