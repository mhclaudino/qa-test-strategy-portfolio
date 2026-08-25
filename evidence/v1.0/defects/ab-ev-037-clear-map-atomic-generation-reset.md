# AB-EV-037 — Clear Map Atomic Generation Reset

## Evidence summary

**Evidence ID:** AB-EV-037  
**Defect:** AB-DEF-018 — Clear Map could fail or leave partially cleared state  
**Change:** C37 — logical Clear Map atomic reset with public-projection generation invalidation  
**Primary risks:** QR-01 persistence/data integrity; QR-04 confirmed-state/concurrency; QR-07 destructive lifecycle; QR-31 public/private boundary; QR-34 public cleanup  
**AtlasBadge commit:** `add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c` — `fix(clear-map): make logical reset atomic`  
**Vercel Production deployment:** `dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz` — `READY`, target `production`  
**Firebase Production project:** `atlas-badge`  
**Rules deployment:** `firestore:rules` only — SUCCESS  
**Test Lead decision:** **AB-DEF-018 CLOSED / PRODUCTION PASS**

---

## 1. Trigger and confirmed failure

C37 began as a focused follow-up after C36 changed Wishlist ordering from per-place `wishlistOrderRank` writes to root `wishlistOrder` metadata.

The existing Clear Map implementation had two independent Firestore commit boundaries:

```text
public cleanup batch
then
private cleanup batch
```

A targeted Emulator investigation confirmed two distinct failure modes.

### Failure mode 1 — C36 root metadata blocked Clear Map

The old Clear Map implementation set `isWishlistPublic = false` but did not remove root `wishlistOrder`.

The C36 Rules correctly required `wishlistOrder` to exist only while the Wishlist was public. Therefore the old Clear Map write became invalid for users carrying the new C36 root field and Firestore rejected the operation.

Observed consequence:

- Clear Map did not complete;
- the user's travel data remained unchanged;
- the confirmation UI remained open after the rejected operation, initially appearing frozen because the error state was not surfaced to the modal.

This was reproduced manually against the deployed C36 Production baseline and classified as a Product Defect.

### Failure mode 2 — split commits allowed partial persistence

Fault injection proved that the public cleanup commit could succeed before the later private cleanup commit failed.

The resulting state could contain:

- public place projections already removed;
- private place data still present;
- private statistics still populated;
- private Wishlist/public-state metadata still representing the pre-clear state.

Because one user action represented a single destructive intent, this was classified as a data-consistency defect rather than acceptable eventual cleanup.

---

## 2. Why simple batch chunking was rejected

At the supported maximum AtlasBadge map size, a naive single batch containing every private and public delete would require:

```text
251 private place deletes
+ 251 public place deletes
+ 1 private root update
+ 1 public root update
= 504 writes
```

A standard Firestore `WriteBatch` is limited to 500 writes.

Splitting those writes into two sequential chunks would not restore atomicity: the first chunk could still commit while the second failed.

The remediation therefore separated **logical Clear Map completion** from **physical cleanup of obsolete public projection documents**.

---

## 3. Final generation architecture

C37 introduced `placesGeneration` as public-projection lifecycle metadata.

Root metadata:

```text
users/{uid}.placesGeneration
publicProfiles/{uid}.placesGeneration
```

Public place projection metadata:

```text
publicProfiles/{uid}/places/{placeId}.placesGeneration
```

Backward compatibility is migration-free:

```text
missing generation field = legacy generation 0
```

Normal public place writers stamp the current private-root generation onto public projections.

Public readers use the current public-root generation, and Firestore Rules reject stale-generation public reads once a profile has entered the versioned model.

---

## 4. Atomic logical Clear Map

The logical Clear Map now uses one Firestore `WriteBatch` containing only the state that must change atomically for correctness:

```text
up to 251 private place deletes
+ private root reset
+ public root generation/privacy reset
= maximum 253 writes
```

The private root reset includes the established Clear Map state plus:

- `isWishlistPublic = false`;
- `wishlistOrder` removed;
- `placesGeneration` advanced.

The public root update includes:

- `isWishlistPublic = false`;
- `wishlistOrder` removed;
- `placesGeneration` advanced in the same logical commit.

Once this one batch commits, Clear Map is functionally complete.

Old public child documents may still exist physically, but they belong to an obsolete generation and cannot be returned as current public travel data.

Physical garbage collection was deliberately deferred because it is no longer required for correctness and a blind post-snapshot delete could create a race with a newly rewritten current-generation document.

---

## 5. Public security boundary

C37 does not rely only on client-side filtering.

The Rules and query model distinguish:

### Legacy profile

Root has no `placesGeneration` and legacy public places have no generation.

Result: existing pre-C37 public profiles remain readable without a bulk Production migration.

### Versioned current projection

Public place generation equals public root generation.

Result: current public data is readable.

### Versioned stale projection

Public place generation differs from the public root generation.

Result: anonymous/non-owner direct read is denied by Firestore Rules.

This makes generation invalidation a security/data-lifecycle boundary rather than a presentation-only filter.

---

## 6. Secondary root-preservation defect caught during C37

The C37 audit found that `updateUserProfile()` rebuilt the public root through a controlled whitelist, but the whitelist did not yet preserve the C36 `wishlistOrder` field.

Without correction, an unrelated Profile update could silently remove the canonical public Wishlist order. The new `placesGeneration` field would have been vulnerable to the same replacement path.

Focused pre-fix regression evidence failed as expected: both `wishlistOrder` and `placesGeneration` were absent from the projected write.

The final public-profile whitelist preserves both fields where valid.

No separate Product Defect ID was created; the finding was contained during the active C37 correction before release.

---

## 7. Confirmation-modal behaviour

Manual reproduction also exposed a UI feedback gap.

When the Clear Map Promise rejected, the confirmation modal remained open. The page kept an error value internally but did not pass it to `ConfirmModal`, making the dialog appear stuck.

C37 aligned the modal state so:

- successful Clear Map closes the confirmation path and reports success;
- rejected persistence does not pretend the destructive operation succeeded;
- failure feedback is available to the modal instead of leaving an unexplained frozen state.

Focused UI regression:

```text
src/app/app/page.modal.test.tsx
2 / 2 PASS
```

---

## 8. Automated qualification

Final focused checkpoints recorded for the released candidate:

```text
Clear Map Firestore Emulator              7 / 7 PASS
Firestore Rules                           226 / 226 PASS
Profile public projection                  9 / 9 PASS
Public Profile source                      5 / 5 PASS
Wishlist combined                          6 / 6 PASS
Wishlist atomic real Emulator              1 / 1 PASS
Wishlist/public projection                17 / 17 PASS
Upsert race Emulator                       4 / 4 PASS
Clear Map modal                             2 / 2 PASS
TypeScript                                 PASS
ESLint                                     PASS
Production build                           PASS
git diff --check                           PASS
```

The previous full Vitest/Playwright checkpoints were not rerun merely for completeness because the affected persistence, Rules, public-source and UI boundaries were restored through focused deterministic coverage.

---

## 9. Environment and release-parity observation

During manual preparation, the uncommitted C37 application was briefly executed at `localhost` against the real `atlas-badge` backend while Production still enforced C36 Rules.

That environment rejected the new `placesGeneration` writes, correctly demonstrating an application/Rules parity mismatch.

The result was classified as **environment/release parity blocked**, not as a second C37 Product Defect.

The release sequence therefore remained controlled:

```text
1. commit/push C37
2. verify Vercel Production READY at exact SHA
3. deploy firestore:rules only
4. confirm application/Rules parity
5. Test Lead Production retest
```

---

## 10. Production release and Test Lead retest

Released application:

```text
Commit:
add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c

Commit message:
fix(clear-map): make logical reset atomic

Vercel deployment:
dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz

State:
READY

Target:
production
```

Firestore Rules were then deployed separately to Production with the approved Rules-only scope:

```text
npx.cmd firebase deploy --only firestore:rules --project atlas-badge
```

Result:

```text
SUCCESS
firestore:rules only
```

After frontend/Rules parity was restored, the Test Lead performed the destructive Clear Map retest using the real Production account and confirmed the functionality was working correctly.

Final decision:

```text
AB-DEF-018 CLOSED
C37 PRODUCTION PASS
```

---

## 11. Traceability

```text
C36 root wishlistOrder lifecycle change
→ Clear Map targeted audit
→ stale root-order cleanup failure proven
→ split public/private commit boundaries proven
→ AB-DEF-018
→ 504-write naive design rejected
→ logical-clear / physical-cleanup separation
→ placesGeneration architecture
→ 253-write atomic core
→ stale public generation denied by Rules
→ legacy generation-0 compatibility
→ Profile root preservation regression
→ modal failure-feedback regression
→ focused Emulator/Rules/component regression
→ add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
→ Vercel dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz READY
→ firestore:rules Production deploy
→ Test Lead Production PASS
→ AB-DEF-018 CLOSED
→ AB-EV-037
```

---

## 12. Residual risk

The current residual risk is regression, not an open Clear Map defect.

Physical deletion of obsolete public-generation documents remains optional housekeeping. Correctness does not depend on that cleanup because obsolete documents are excluded from the current query model and denied to public viewers by Rules.

Any future implementation of physical garbage collection must be idempotent and generation-aware so it cannot delete a same-ID document rewritten into the current generation.
