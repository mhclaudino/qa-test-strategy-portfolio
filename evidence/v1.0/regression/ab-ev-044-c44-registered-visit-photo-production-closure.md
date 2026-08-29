# AB-EV-044 — C44 One Photo per RegisteredVisit Production Closure

**Evidence ID:** AB-EV-044  
**Increment:** C44 — One Photo per `RegisteredVisit`  
**Status:** CLOSED / PRODUCTION PASS  
**Test Lead decision:** Approved — 29 August 2026  
**Evidence type:** Regression + real-backend integration + Production closure  
**Public-evidence note:** User-identifying screenshots and credentials were reviewed in the private working session and are not published in this repository.

---

## 1. Purpose

This record closes the AtlasBadge V1.0 increment that adds one representative photo to an individual registered visit.

The increment was treated as both a product feature and a QA case study because it introduced new persistence, Firebase Storage, privacy/public-projection, quota, cleanup, server-runtime and release-parity boundaries.

The final result was approved only after the complete deployed path worked in Production:

```text
owner Map
→ photo selection/processing
→ explicit Save
→ bounded Storage upload
→ private RegisteredVisit metadata
→ sanitised public projection
→ owner/public server read
→ Profile rendering
→ quota/replacement/removal lifecycle
```

---

## 2. Approved V1.0 product contract

C44 implements:

- one photo per `RegisteredVisit`;
- photo may exist without memory text;
- selection creates a local preview/draft and becomes official only after explicit Save;
- JPG/JPEG, PNG and WebP input, maximum original size 5 MB;
- client processing to WebP, maximum 1024 px while preserving aspect ratio, quality 0.85;
- EXIF/GPS metadata removed by the processing pipeline;
- circular `object-cover` owner/public card presentation plus full-aspect lightbox;
- photo privacy inherited exactly from the visit memory;
- no independent photo-privacy control;
- no public placeholder when a visit has no photo;
- public Profile photo access only for the current sanitised public-memory projection;
- replace uses the existing quota slot and toggles A/B variants;
- persisted removal is explicit and frees the slot;
- Clear Map, visit deletion and account deletion include photo cleanup;
- no migration requirement for legacy accounts.

The V1.0 free plan has a hard limit of **10 active visit photos per user**.

No Premium/Upgrade CTA, billing or paid entitlement exists in V1.0.

---

## 3. Cost-control architecture

The final design uses bounded Storage slots:

```text
users/{uid}/visit-photo-slots/{slotId}/{variant}.webp
```

where:

- `slotId` is exactly `0` through `9`;
- `variant` is `a` or `b`;
- a visit owns at most one slot;
- replacement temporarily uses the inactive variant and deletes the old variant after logical success.

This provides a physical upper bound of ten steady-state active visit photos per user and at most two objects per occupied slot during replacement.

Storage Rules deny arbitrary legacy/new visit-photo paths and deny direct client read/list/delete for the bounded namespace. Owner/public reads are server-mediated.

The private user document holds server-managed slot reservations. Client profile edits must not replay or mutate that metadata.

---

## 4. Public/privacy contract

Private `RegisteredVisit` photo metadata includes bounded slot/path state plus an opaque `photoRef`.

The public projection may expose only the opaque `photoRef` when the corresponding visit memory is currently public.

It must not expose:

- `photoPath`;
- `photoSlot`;
- `photoVariant`;
- private stable visit ID;
- private memory data.

Owner Map reads use the authenticated owner route.

Public Profile reads use the public visit-photo route after the requested `photoRef` is authorised against the current sanitised `publicMemories` projection.

No visit-photo `getDownloadURL` token URL is used as the public contract.

---

## 5. Defects and release blockers found during C44

### C44-D01 — direct Storage mutation rejected after verification

The first Production upload attempt failed because the browser held an ID token whose `email_verified` claim was stale even though the current user state was verified.

The correction performs a targeted token refresh only when the client reports verified e-mail but the token claim is stale.

The `email_verified` security requirement was not weakened.

### C44-D02 — owner/public read and bounded quota hardening

The initial owner read path depended on browser Storage reads and introduced a CORS/retry boundary. The final design moved reads behind same-origin server APIs and introduced ten bounded quota slots.

Local Firebase Admin configuration was a separate environment prerequisite; its absence was not treated as evidence that Production/Vercel configuration was missing.

### C44-D02C — Node 24 Firebase Admin Storage OAuth transport

Real-backend validation reproduced:

```text
ERR_STREAM_PREMATURE_CLOSE
Premature close
https://www.googleapis.com/oauth2/v4/token
```

under Node 24.17.0 on the Google Cloud Storage Admin path.

A controlled A/B qualification showed the same Storage operation fail under Node 24 and pass under Node 22. AtlasBadge was therefore pinned to Node 22 for this server integration before release.

### Public photo projection defect

The bounded Storage path ends in `a.webp`/`b.webp`, but the public projection still attempted to derive the opaque reference from the old UUID filename convention.

The correction makes `visit.photoRef` authoritative and retains legacy path extraction only as backward-compatible fallback.

### Adjacent profile-write regression

C44 introduced server-managed `visitPhotoSlots` on the private user document. The existing full-profile edit helper replayed the client-held document, causing normal avatar removal to submit a protected server field and be rejected by Firestore.

The correction changed profile editing to an explicit editable-field patch and uses field deletion for removed optional avatar metadata.

### Conditional public Wishlist projection defect

A later Production retest exposed a pre-existing mismatch: `wishlistOrder` was globally whitelisted for the public root but Firestore Rules allow it only while the Wishlist is public.

The projection now emits `wishlistOrder` only when `isWishlistPublic === true`.

This was closed before the C44 release evidence was finalised because it blocked the same profile Save path.

---

## 6. Key implementation/release traceability

| Purpose | Commit |
|---|---|
| Initial C44 implementation | `fc2cea28c7697c18a9e7a79d5d1451b895066e20` |
| Bounded quota / owner API / C44 hardening | `93e31634a9cf747976a01ca6d6e5e3b256fc6f56` |
| Node 22 runtime pin for Firebase Admin Storage | `1dc6edc04952e03906241cda81beb6649d06c8fa` |
| Public visit-photo reference projection | `6b8721140a9e46c6c6260b87b3e6e587e7876ec2` |
| Profile photo removal / protected-field patch | `f6349977492deea98452df5d6581f6edc07a3a6d` |
| Private Wishlist order projection correction | `c5c39c1407aa25b604ff82e9c4d96522575c143e` |

Storage Rules for the bounded-slot namespace were deployed separately to Firebase project `atlas-badge` before the corresponding application release path was validated.

Final Vercel Production deployment:

```text
Deployment: dpl_CcUWVh7v97oUTQ3W9WAuqkJMxfQh
Git SHA:    c5c39c1407aa25b604ff82e9c4d96522575c143e
State:      READY
Target:     production
```

---

## 7. Automated and technical evidence

Recorded C44 checkpoints include:

```text
Storage Rules bounded-slot suite:
24 / 24 PASS

Firestore Rules final focused checkpoint:
235 / 235 PASS

C44 focused Emulator browser E2E:
PASS

Account deletion regression with visit-photo namespace:
82 / 82 PASS

Local/emulator full Vitest checkpoint during C44 hardening:
464 passed / 37 skipped / 0 failed

Focused public projection after bounded photoRef correction:
11 / 11 PASS

Focused profile-photo removal regression:
14 / 14 PASS

Final private-Wishlist projection focused suite:
12 / 12 PASS
```

The C44 investigation also recorded pre-existing full-Playwright baseline debt separately rather than misclassifying it as C44 regression.

---

## 8. Production validation

The Test Lead validated the deployed feature against real Firebase/Vercel Production.

Confirmed outcomes included:

- visit photo Add/Save on the owner Map;
- reload persistence;
- owner visit-photo API HTTP 200;
- photo visible in the public Profile;
- public visit-photo API HTTP 200;
- different photos rendered for different public visits;
- hard ten-photo quota enforced;
- replacement remains available when all ten slots are occupied because it reuses the existing slot;
- Storage objects created under bounded slot paths;
- profile-photo removal regression corrected and retested successfully;
- private Wishlist order no longer blocks unrelated profile Save;
- final deployed revision READY at the expected Git SHA.

The Test Lead explicitly reported the visit-photo behaviour as working correctly and later confirmed the profile-photo removal correction.

---

## 9. Residual risk and future backlog

C44 is closed, but permanent regression should retain:

- Storage Rules bounded namespace;
- ten-slot reservation/concurrency/release behaviour;
- A/B replacement cleanup;
- real server runtime compatibility when Node/dependency versions change;
- owner/public API authorisation;
- public/private photo projection;
- Clear Map/remove-visit/account-deletion cleanup;
- profile-edit protection of server-managed fields;
- conditional public Wishlist projection.

`FUTURE-PAID-01` is recorded as a **possible post-V1.0 product direction**, not an implemented feature or committed commercial decision. If infrastructure cost eventually requires monetisation, candidates include a larger visit-photo allowance above the free ten-photo cap and other premium conveniences. Any billing/entitlement design requires separate requirements, risk analysis, acceptance criteria and security testing.

The next planned V1.0 product-quality area after C44 is localisation/final release work.

---

## 10. Final decision

```text
C44 — One Photo per RegisteredVisit
LOCAL/EMULATOR: PASS
REAL FIREBASE INTEGRATION: PASS
PRODUCTION: PASS
FREE PHOTO QUOTA (10): PASS
PUBLIC PROFILE PHOTO: PASS
TEST LEAD: APPROVED
STATUS: CLOSED
```

AB-EV-044 is the public closure record for this increment.
