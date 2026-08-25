# AB-EV-036 — Wishlist Atomic Settings Save and Order Integrity

## Evidence summary

**Evidence ID:** AB-EV-036  
**Defect:** AB-DEF-017 — Combined Wishlist Save could partially persist independent changes  
**Change:** C36 — atomic Wishlist settings persistence and root order model  
**Primary risks:** QR-01 persistence integrity; QR-04 concurrency/confirmed-state integrity; QR-17 Wishlist behaviour; QR-31 public/private Profile boundary; QR-34 public→private cleanup  
**AtlasBadge commit:** `5d660b016528e75a2a70b49010a84065d884f883` — `fix(wishlist): make settings save atomic and preserve order`  
**Vercel Production deployment:** `dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9` — `READY`, target `production`  
**Firestore Rules Production project:** `atlas-badge`  
**Rules deployment:** `firestore:rules` only — SUCCESS  
**Test Lead decision:** **AB-DEF-017 CLOSED / PRODUCTION PASS**

---

## 1. Defect discovery

The Wishlist modal exposed one user-visible **Save Changes** action that could change both:

- Wishlist order; and
- Wishlist privacy.

The original implementation executed those operations through two independent helpers and awaited them with `Promise.all()`.

Each helper created and committed its own Firestore `WriteBatch`. `Promise.all()` coordinates JavaScript promises but does not provide Firestore atomicity across independent commits.

A focused Emulator fault-injection investigation proved the defect: the order commit could succeed while the privacy commit was denied, leaving a persisted state that represented only part of the user's single Save intent.

The behaviour was therefore classified as a **Product data-consistency defect**, not merely an architectural observation.

---

## 2. Secondary architectural finding — write amplification

The investigation also identified a scalability problem in the legacy order model.

Wishlist order was persisted as `wishlistOrderRank` on individual place documents. For a public Wishlist covering all 251 directly selectable AtlasBadge records, an order update could require:

```text
251 private place writes
+
251 public place writes
=
502 document writes
```

That exceeds the Firestore 500-write batch limit before combining privacy-transition work.

A naive change that simply moved the existing writes into one larger batch would therefore have preserved a structural failure mode.

---

## 3. Corrected persistence model

C36 introduced root-level order metadata:

```text
users/{uid}.wishlistOrder
publicProfiles/{uid}.wishlistOrder   // only while Wishlist is public
```

`wishlistOrder` is an ordered array of canonical selectable place IDs.

The model deliberately keeps responsibilities separate:

- `statuses.wishlist === true` remains the Wishlist membership source of truth;
- `wishlistOrder` stores presentation order only;
- `visitOrderRank` / Manual Visit Order remains independent;
- legacy `wishlistOrderRank` is retained only as a read fallback for pre-C36 data;
- no bulk migration is required.

The combined Wishlist settings helper now persists order/privacy through **one Firestore batch commit boundary**.

Maximum supported write counts were reduced to:

| Scenario | Maximum writes |
|---|---:|
| Private Wishlist — order only | 2 |
| Public Wishlist — order only | 2 |
| Private → Public with 251 Wishlist items | 253 |
| Public → Private with 251 Wishlist items | 253 |

All supported states remain below the Firestore 500-write limit.

---

## 4. Permanent atomicity proof

A permanent Firestore Emulator regression executes the real combined helper against the Emulator rather than only mocking `batch.commit()`.

The test seeds known private/public state, forces one write inside the combined batch to fail Firestore Rules validation and verifies the backend afterwards.

The rejected Save proves:

- private `isWishlistPublic` unchanged;
- private `wishlistOrder` unchanged;
- public-root visibility/order unchanged;
- no public Wishlist place projection created;
- no other write in the logical Save partially persisted.

Result:

```text
travelMap.wishlistAtomicity.emulator.test.ts
1 test / 1 PASS
```

This closes the original AB-DEF-017 atomicity acceptance criterion.

---

## 5. Manual QA finding during the correction

The first Test Lead manual retest found an additional implementation gap:

```text
Reorder Wishlist
+
Private → Public
+
Save
```

Privacy persisted, but the owner/public presentation lost the intended order.

Backend inspection showed that the new `wishlistOrder` had been persisted correctly. The defect was in the **frontend read path**: the owner Wishlist modal continued to reconstruct order through legacy rank/alphabetical behaviour instead of treating the new root `profile.wishlistOrder` as the primary order source.

A second stale-state gap was then identified: `AuthContext.profile` is not a real-time Profile-root subscription. An order-only Save could persist successfully while the in-memory Profile remained stale until a later reload.

The final owner read contract became:

1. valid `profile.wishlistOrder`;
2. legacy `wishlistOrderRank` fallback for entries not represented by the root array;
3. deterministic alphabetical fallback.

After any successful Save where either order or privacy changed, the Profile is refreshed before the modal is considered complete.

Focused component tests protect root-order precedence plus order-only/privacy/combined refresh behaviour.

---

## 6. Environment and release-parity observations

During manual QA, localhost initially produced `Missing or insufficient permissions` for the new `wishlistOrder` write.

The failure was not a C36 product regression. The local application was using the **real Firebase backend** while Production still had the pre-C36 Firestore Rules whitelist. This recreated the frontend/Rules parity failure pattern previously documented by AB-EV-006.

The investigation reinforced three environment facts:

- `localhost` does not imply Firebase Emulator usage;
- an Emulator service running does not mean a QA identity/session is ready;
- a Playwright-authenticated browser context is not the Test Lead's existing browser session, and `localhost` and `127.0.0.1` are distinct browser origins.

These observations were converted into Lessons Learned and Test Environment controls rather than misclassified as new Product Defects.

---

## 7. Validation record

### Automated / technical

```text
Focused combined Wishlist Vitest:      19 / 19 PASS
Final WishlistModal focused Vitest:     5 / 5 PASS
Full Vitest:                           375 PASS / 16 skipped
Firestore Rules:                       226 / 226 PASS
Focused Wishlist Playwright:             3 / 3 PASS
Unexpected real Firebase E2E traffic: realFirebaseRequests=0
Real Emulator atomic rejection:          1 / 1 PASS
TypeScript:                                    PASS
ESLint affected paths:                         PASS
Production build:                              PASS
```

The full/broad checkpoints were not repeatedly rerun after every later focused edit. Results were carried forward only when the subsequent change did not invalidate what they proved; focused tests re-established the changed contract.

### Test Lead manual QA

The Test Lead approved the corrected behaviour locally and then completed the focused Production smoke after frontend/Rules parity was restored.

Production validation covered the material C36 journeys, including:

- order + privacy change in one Save;
- persistence after reload;
- public Profile order;
- public Wishlist order-only Save;
- Public → Private transition with the Wishlist no longer exposed publicly.

**Result:** PASS.

---

## 8. Controlled release

The approved product/Rules commit was:

```text
5d660b016528e75a2a70b49010a84065d884f883
fix(wishlist): make settings save atomic and preserve order
```

The Git-triggered Vercel deployment was verified:

```text
Deployment: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9
Target:     production
State:      READY
Git SHA:    5d660b016528e75a2a70b49010a84065d884f883
```

Because `firestore.rules` changed, Production validation was intentionally blocked until the separately authorised Rules deployment completed:

```text
npx.cmd firebase deploy --only firestore:rules --project atlas-badge
```

Result:

```text
firestore: released rules firestore.rules to cloud.firestore
Deploy complete
```

No indexes, Hosting, Functions, Storage Rules or database migration were deployed.

Frontend/Rules parity was therefore restored before the final Production smoke.

---

## 9. Final decision and residual risk

**AB-DEF-017 is CLOSED.**

The correction removes the known partial-persistence path, reduces Wishlist-order write amplification, preserves backward-compatible reads, keeps public Wishlist order sanitised and makes the owner read path consume the new root source of truth.

Relevant risks remain in regression because persistence/privacy failures continue to have material impact, but no known C36 release gap remains after the approved Production validation.

---

## 10. Public-evidence boundary

No raw Auth Emulator identity, password, Production account identifier, browser session, private Wishlist payload, unredacted screenshots or diagnostic scripts are published.

The public record intentionally retains verified commands, counts, architectural decisions, deployment identifiers and Test Lead decisions only.

---

## 11. Traceability

```text
AB-DEF-017
→ independent Wishlist commits identified
→ partial persistence proven by Emulator fault injection
→ 502-write amplification risk identified
→ root wishlistOrder model
→ one atomic Wishlist settings batch
→ <=253 supported write ceiling
→ real Firestore Emulator atomic rejection proof
→ Test Lead manual QA found stale frontend read path
→ root-order precedence + Profile refresh correction
→ focused regression + carried-forward checkpoints
→ Test Lead local PASS
→ commit 5d660b016528e75a2a70b49010a84065d884f883
→ Vercel dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9 READY
→ Firestore Rules-only Production deploy PASS
→ Production smoke PASS
→ AB-DEF-017 CLOSED
→ AB-EV-036
```
