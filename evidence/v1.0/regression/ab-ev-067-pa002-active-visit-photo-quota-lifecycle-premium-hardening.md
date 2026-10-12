# AB-EV-067 — PA-002 active visit-photo quota lifecycle premium hardening

**Evidence ID:** AB-EV-067  
**Programme:** AtlasBadge V1.0 — Premium Hardening  
**Finding:** PA-002  
**Product baseline:** `d0116967fc389353191c11e8865f20e38b9d5835`  
**Product commit:** `49abb003a9b495f3be43b4e30ea4b36dcfb8ceca`  
**Vercel deployment:** `dpl_FTp3iCiARWMSejRv12FcHtaMcWeR` — `READY`, `production`, exact Product SHA  
**Test Lead decision:** **PASS / PUBLISHED / PRODUCTION READY**  
**Residuals:** physical Storage deletion intentionally deferred; PA-003 retains broader photo Admin-route hardening; PA-006A retains deterministic Rules-tooling follow-up

## 1. Context

PA-002 was the second mandatory finding from the AtlasBadge V1.0 Premium Architecture Audit. It followed PA-001, which moved Clear Map protected-state mutation to an authenticated server boundary after C44 made `visitPhotoSlots` server-managed.

C44 established a bounded free visit-photo model: at most ten active photo allocations per user, with server-owned quota metadata and opaque photo access. PA-002 identified that the lifecycle contract for normal photo and visit removal was weaker than the quota contract: a photo could be logically removed while its persisted slot still consumed active quota, and an owner-photo read path could not rely on physical Storage existence alone because old bytes may remain after logical removal.

The finding therefore concerned lifecycle correctness, not a new feature. The approved rule is that quota represents **active logical photos**, not historical reservations or physically retained objects.

## 2. Required business invariant

The PA-002 contract is:

- maximum free quota = **10 active persisted photos**;
- removing a photo immediately removes that photo from the logical Product state;
- removing a photo-bearing visit has the same quota effect;
- capacity is released without waiting for the reservation TTL;
- old owner/public photo requests become inaccessible immediately after logical removal even when the physical Storage object still exists;
- physical object deletion is a separate cleanup concern and does not decide whether logical removal succeeded;
- release/retry is bound to the exact allocation identity: `countryId`, `visitId`, `slotId`, `reservationId`;
- stale cleanup must never delete a newer allocation that reused the same slot;
- a failed release after logical persistence must remain recoverable after reload.

This explicitly rejects a model in which the ten-photo limit counts permanent historical reservations.

## 3. Final architecture

The final lifecycle uses a durable private recovery map, `pendingVisitPhotoReleases`, on the user's private place state.

For a photo-bearing removal, the successful Product sequence is:

```text
logical mutation + exact pending-release tombstone persisted
        ↓
exact server release
        ↓
durable tombstone clear persisted
        ↓
profile/quota refresh awaited
        ↓
operation resolves
```

A release failure does not roll back the already valid logical removal. Instead:

```text
logical removal remains persisted
+
exact tombstone remains persisted
+
operation rejects
+
no success refresh occurs
```

A later Product reload hydrates the tombstone and deterministic reconciliation retries the **same old allocation identity**.

The server release outcomes are interpreted as follows:

- `RELEASED` — old allocation removed;
- `ALREADY_ABSENT` — old allocation is already gone and the request is idempotently complete;
- `STALE` — the exact old allocation is no longer current, so the old cleanup request must not affect the newer allocation;
- `STILL_REFERENCED` — non-success; the persisted photo is still referenced and must not be released.

## 4. Durable recovery and empty-map correction

During independent review, the first durable-clear implementation exposed a real persistence defect. When the final tombstone was removed in memory, the serializer omitted an empty `pendingVisitPhotoReleases` map. On an existing Firestore document, omitting the field left the old persisted tombstone intact.

The correction distinguishes an absent property from an explicitly cleared property. An explicitly present empty map is persisted as `{}` through the normal Product update path.

Permanent Emulator coverage now proves:

1. an existing place contains a non-empty tombstone;
2. Product load/hydration reads it;
3. Product persistence clears it through `upsertUserPlace` on the existing document;
4. Firestore readback shows the old entry absent;
5. a second Product hydration does not resurrect it.

This is the durable-recovery boundary on which PA2-14 depends.

## 5. Full-quota recovery

Another review finding showed that recovery could be unreachable at exactly ten allocations: if the Add Photo action was disabled purely because the root quota equalled ten, the user could not enter the path that reconciles a pending release.

The corrected UI contract keeps the recovery path reachable when the current private country contains pending releases. Before any new reservation/upload begins, the Product reconciles the pending release and refreshes authoritative quota state.

Coverage proves:

- ten slots with no pending release → Add Photo remains disabled;
- ten slots with a pending release → the recovery path remains reachable;
- reconciliation failure → no new reservation/upload begins;
- reconciliation success → normal upload flow may continue.

The client never pretends capacity is free before the authoritative release succeeds.

## 6. Current-state-safe concurrency

A later independent review found a separate Product defect in the reconciliation clear step. The first extracted implementation captured the whole country before awaiting the server release. If another edit completed while the release was pending, the subsequent tombstone clear could persist that stale snapshot and overwrite the newer edit.

The final implementation clears the completed tombstone against the **current country state supplied by the mutation orchestrator at apply time**. It removes only the exact completed key and preserves:

- unrelated fields changed while release was pending;
- other pending release entries;
- a newer allocation/retry identity;
- already-absent/idempotent state.

Deterministic Context coverage and real Emulator readback prove that a concurrent note edit survives reconciliation. Additional race coverage proves that stale R1 cleanup cannot delete a newer R2 allocation.

## 7. Product-path unification

Independent review also found that early Emulator tests exercised extracted lifecycle helpers while `TravelMapContext` still contained parallel removal implementations. That would have allowed tests to prove a helper that the Product did not actually call.

The final structure removes that ambiguity. The real Context paths delegate to the same production lifecycle functions used by acceptance tests:

- `removeVisitPhoto` → `executeRemoveVisitPhotoLifecycle`;
- `removeVisit` → `executeRemoveVisitLifecycle`;
- pending-release reconciliation → `executeReconcilePendingReleasesLifecycle`.

The Context supplies the real mutation orchestrator, authenticated release client and `refreshProfile` boundary. The ordering tests use controlled deferred promises to prove that logical persistence, release, durable clear and refresh occur in the intended sequence.

## 8. Owner/public access contract

Physical Storage retention is not treated as proof that a photo is still active.

The owner-photo route now requires the request to match the current private `RegisteredVisit` identity, including the current visit/photo/slot/variant relationship. Public access continues to depend on the current sanitised projection.

The final regression demonstrates both transitions:

```text
current photo → HTTP 200
logical removal/projection removal
physical bytes still retained
old request → HTTP 404
```

This allows physical cleanup to be deferred without weakening privacy or lifecycle correctness.

## 9. Controlled-recovery incident during implementation

A workstation shutdown interrupted one remediation cycle while the PA-002 candidate contained both tracked and untracked changes. The shutdown cause was not attributed to AtlasBadge; no Product root cause was claimed.

The recovery process itself became quality evidence:

- a complete byte-for-byte external snapshot of the candidate was created;
- the previously captured patch was used to reconstruct the one route Emulator test that had been reverted to HEAD during the interrupted remediation;
- expected SHA-256 and all ten lost hunks were independently verified;
- all other tracked/untracked candidate hashes were checked for preservation before implementation resumed.

This event is process/recovery evidence, not a Product Defect.

## 10. Acceptance matrix

The final independent approval used the original PA-002 oracle. No remapped IDs were accepted.

| ID | Acceptance criterion | Final status |
|---|---|---|
| PA2-01 | Ten active persisted photos reject an eleventh reservation | **PROVEN** |
| PA2-02 | Photo-only removal retains the visit and releases the exact allocation | **PROVEN** |
| PA2-03 | Freed capacity is immediately reusable without waiting for TTL expiry | **PROVEN** |
| PA2-04 | Actual Product photo-bearing `removeVisit` removes the visit and exact authoritative allocation, completes the tombstone lifecycle and awaits refresh | **PROVEN** |
| PA2-05 | Actual Product photo-free `removeVisit` removes only the target visit, leaves unrelated allocation unchanged and creates no target release/tombstone | **PROVEN** |
| PA2-06 | Persisted same-visit replacement retains exactly one active allocation | **PROVEN** |
| PA2-07 | Repeated exact persisted release is idempotent | **PROVEN** |
| PA2-08 | Stale R1 release after different-visit reassignment preserves current R2 | **PROVEN** |
| PA2-09 | Fresh unpersisted reservation inside the TTL retains capacity | **PROVEN** |
| PA2-09B | Same-visit R2 on the same slot survives stale R1 release before R2 private persistence | **PROVEN** |
| PA2-10 | Expired abandoned reservation is safely reclaimed | **PROVEN** |
| PA2-11 | Current exact authenticated owner photo identity returns HTTP 200 | **PROVEN** |
| PA2-12 OWNER | Logical removal makes the old owner request return 404 while physical bytes remain | **PROVEN** |
| PA2-12 PUBLIC | Logical projection removal makes the old public request return 404 while physical bytes remain | **PROVEN** |
| PA2-13 | Successful removal remains pending until logical persistence → release → durable clear → awaited refresh all complete | **PROVEN** |
| PA2-14 | Release failure leaves a durable exact tombstone; reload, exact Product retry, concurrent-edit preservation, durable clear, second reload and awaited refresh all succeed | **PROVEN** |

## 11. Final independent qualification

After the last scope-hygiene correction, Codex independently re-qualified the exact frozen candidate bytes and returned **APPROVE**.

| Gate | Final independent result |
|---|---:|
| Focused lifecycle | **24 / 24 PASS** |
| Requested PA-002 Emulator run | **39 / 39 PASS**, 0 failures / 0 unexpected skips |
| Core quota lifecycle | **13 / 13 PASS** |
| Slot-server Emulator | **10 / 10 PASS** |
| Route Emulator | **10 / 10 PASS** |
| Context lifecycle ordering | **6 / 6 PASS** |
| Firestore Rules | **242 / 242 PASS**, 0 skipped |
| C44 Edge | **1 / 1 PASS**, `realFirebaseRequests=0` |
| Clear Map route regression | **8 / 8 PASS** |
| Full Vitest | **911 PASS / 67 SKIPPED / 0 FAIL** |
| Vitest files | **125 PASS / 10 SKIPPED** |
| TypeScript | **PASS** |
| ESLint | **0 errors / 37 warnings / 0 PA-002-created warnings** |
| Production build | **PASS** |
| `git diff --check` | **PASS** |
| Frozen-byte integrity | **19 / 19 exact hashes preserved** |

The six Context lifecycle tests use mocked persistence boundaries to prove deterministic ordering. They are not misrepresented as six additional real Firestore tests; the three core Emulator suites account for the 33 real Emulator acceptance declarations.

The final lint delta from the qualified pre-PA-002 baseline was 38 → 37 warnings. No new warning was introduced. The only removed warning was an owner-suite `adminFirestore` binding that became genuinely used by the strengthened owner fixture. Historical unrelated warnings were deliberately preserved rather than cleaned merely to improve the count.

## 12. Product publication

PA-002 was published as exactly one Product commit:

```text
49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
fix(visit-photo): enforce active quota lifecycle
```

Its parent is the PA-001 UTF-8 test-oracle hotfix baseline:

```text
d0116967fc389353191c11e8865f20e38b9d5835
```

Before push, the approved working bytes, staged bytes and committed bytes were checked against the final approved manifest. The C44 file remained a checkout/line-ending status artefact with no content change and was not included as an unrelated commit change.

GitHub `main` was independently verified at the PA-002 SHA after publication.

Vercel automatically created the exact-SHA Production deployment:

```text
dpl_FTp3iCiARWMSejRv12FcHtaMcWeR
READY
production
49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
```

The production alias includes `atlas-badge.vercel.app`.

Publication verification was metadata/SHA/status verification only. No authenticated Production QA, Production Firestore/Storage mutation or real-Firebase test-data creation was required for this publication decision.

## 13. Test Lead decision

**PA-002 — PASS / PUBLISHED / PRODUCTION READY**

The Test Lead approved publication after independent review established that every original PA2 criterion was proven on the exact frozen candidate bytes and the publication chain preserved those bytes from working tree → staging → commit → GitHub `main` → Vercel Production.

## 14. Residuals and next scope

### Physical Storage cleanup

Physical visit-photo object deletion remains intentionally deferred. This is accepted because PA-002 proves both immediate logical inaccessibility and immediate quota correctness independently of physical object deletion.

This residual must not be interpreted as permission to weaken Storage lifecycle controls. Any later cleanup mechanism must preserve exact allocation/generation safety.

### PA-003 — photo Admin-route hardening

Broader photo Admin/server-route lifecycle and input-contract hardening remains a separate Premium finding, PA-003. PA-002 does not claim to close every Admin route contract merely because the owner read and persisted-release paths were strengthened where necessary for quota correctness.

### PA-006A — deterministic tooling

The final independent review noted that the existing Rules alias still relies on a cached, undeclared `tsx` executable. The Rules result itself is valid, but reproducible tooling declaration remains a separate deterministic-tooling concern under PA-006A.

## 15. Traceability

| Traceability element | PA-002 record |
|---|---|
| Premium finding | `PA-002` |
| Requirement | Visit-photo quota counts active logical photos; removal immediately releases exact quota capacity and old photo access without depending on physical cleanup |
| Primary risks | QR-01 persistence/recovery; QR-04 concurrency; QR-07 lifecycle cleanup; QR-31/32/34 privacy/public-projection integrity; C44 bounded quota contract |
| Historical photo baseline | AB-EV-044 |
| Clear Map server-boundary baseline | AB-EV-066 / Product `d0116967...` after test-oracle hotfix |
| Implementation | Product `49abb003a9b495f3be43b4e30ea4b36dcfb8ceca` |
| Acceptance | PA2-01 through PA2-14, including PA2-09B and separate OWNER/PUBLIC PA2-12 transitions — all **PROVEN** |
| Emulator | 39 / 39 requested PA-002 run; 33 core real Emulator declarations + 6 Context ordering tests |
| Rules | 242 / 242 PASS |
| C44 regression | 1 / 1 PASS; `realFirebaseRequests=0` |
| Clear Map regression | 8 / 8 PASS |
| Full regression | 911 PASS / 67 SKIPPED / 0 FAIL |
| Static/build | TypeScript PASS; ESLint 0 errors / 37 warnings / 0 new; build PASS; diff-check PASS |
| Independent approval | Codex **APPROVE** on exact frozen bytes |
| Evidence | AB-EV-067 |
| Deployment | `dpl_FTp3iCiARWMSejRv12FcHtaMcWeR` READY / production / exact Product SHA |
| Test Lead decision | PASS / PUBLISHED / PRODUCTION READY |
| Residuals | physical Storage deletion deferred; PA-003 separate; PA-006A deterministic tooling follow-up |

## 16. Relationship to earlier evidence

AB-EV-044 remains the historical C44 record that introduced one photo per RegisteredVisit, the bounded free quota and the server-managed slot model.

AB-EV-066 remains the PA-001 record that moved Clear Map protected-state mutation behind the authenticated server boundary. PA-002 starts from the post-hotfix `d0116967...` baseline and strengthens the ordinary photo/visit-removal lifecycle without rewriting PA-001.

AB-EV-067 is the second published Premium Hardening closure. It supersedes only the weaker ordinary removal/quota assumptions identified by PA-002; it does not rewrite the historical C44 or PA-001 decisions.
