# AB-EV-066 — PA-001 Clear Map server-boundary premium hardening

**Evidence ID:** AB-EV-066  
**Programme:** AtlasBadge V1.0 — Premium Hardening  
**Finding:** PA-001  
**Product commit:** `b0fef1d825649a7c961a3413f11faa4c104b8aca`  
**Parent qualification baseline:** `12a0dd53dfe789610e6393b31db1c94c311757ff`  
**Vercel deployment:** `dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK` — `READY`, `production`, exact Product SHA  
**Test Lead decision:** **PASS / PUBLISHED**  
**Residual qualification item:** AC-017 carried to PA-007 / Final Premium RC

## 1. Context

PA-001 was raised by the Premium Architecture Audit after the V1.0 engineering baseline had already reached a technically green Release Candidate state under AB-EV-065. The finding did not invalidate the earlier historical Clear Map work. Instead, it identified that a later ownership change introduced by C44 had made part of the old Clear Map execution boundary unsafe.

AB-EV-037 records the historical C37 correction in which Clear Map became one logical client Firestore batch and used `placesGeneration` to make stale public-place projections non-current. That was the correct historical decision for the then-current schema and Rules model.

C44/AB-EV-044 later introduced visit-photo lifecycle management and a bounded free quota backed by server-managed `visitPhotoSlots`. Ordinary clients were intentionally prevented from mutating that field directly.

The resulting incompatibility was therefore not that the Rules were too strict. The security boundary was correct. The defect was that the destructive Clear Map flow still attempted to own mutation of a field that had become server-managed.

## 2. Premium Audit finding

PA-001 identified that Clear Map could conflict with the protected photo-slot field and could no longer be treated as a purely client-owned destructive write path.

The required correction was to preserve the established user-visible reset contract while moving protected-state mutation behind a narrow authenticated server boundary. Weakening Firestore Rules was explicitly rejected.

## 3. Quality risk

PA-001 affected the same risk families already represented by the V1.0 control set:

- **QR-01 — persistence / write integrity:** a destructive reset must complete as one consistent logical transition rather than leave protected or partial state behind;
- **QR-04 — concurrency / synchronisation:** a place created or changed between enumeration and commit must not silently survive a successful Clear Map;
- **QR-07 — destructive lifecycle integrity:** reset must preserve account/profile/lifecycle data while removing only the approved travel state;
- **QR-31 / QR-34 — public/private projection and privacy integrity:** stale public travel projections must not become current after reset, and private server-managed quota state must remain protected from direct client mutation.

No new QR identifier was introduced. PA-001 is retained as the authoritative Premium Audit finding ID.

## 4. Historical architecture and ownership incompatibility

Before PA-001, `clearUserPlaces()` executed the Clear Map mutation from the client. The historical implementation deleted private place documents, reset root travel fields, advanced `placesGeneration`, reset public Wishlist state and removed `visitPhotoSlots` in the same logical operation.

After C44, `visitPhotoSlots` became server-managed and direct client mutation was intentionally denied by Firestore Rules. This created an ownership mismatch:

```text
historical client Clear Map
        ↓
tries to reset visitPhotoSlots
        ↓
field is now protected / server-managed
```

The correction therefore had to move the reset operation to the privileged boundary rather than relax the field protection.

## 5. Approved architecture

The approved PA-001 flow is:

```text
clearUserPlaces() client wrapper
        ↓
POST /api/account/clear-map
        ↓
authenticated Next.js server route
        ↓
Firebase Admin
        ↓
Firestore runTransaction()
```

The client wrapper preserves its existing call-site contract while delegating protected mutation to the server.

The route derives the target identity exclusively from the verified Firebase ID token. A caller-supplied UID is not accepted as the authority for the destructive operation.

## 6. Authentication and lifecycle contract

Focused route coverage confirmed the privileged reset boundary rejects unauthorised or unsafe requests and accepts the supported active-owner path.

The final contract includes:

- missing bearer token rejected;
- malformed/invalid token rejected;
- revoked token rejected through `verifyIdToken(token, true)`;
- unverified account rejected;
- inactive/deleting account rejected;
- active verified account accepted;
- legacy/current V1 compatibility preserved where `accountState` is absent according to the existing lifecycle contract;
- UID derived only from the verified token;
- internal failures returned through sanitised route responses.

## 7. Transaction and concurrency contract

The server operation uses a Firestore transaction instead of an Admin `writeBatch()` built after a non-transactional enumeration.

The canonical selectable domain contains **251** place IDs. Before issuing writes, the transaction reads:

- `users/{uid}`;
- `publicProfiles/{uid}` when applicable to the reset decision;
- all **251 canonical `users/{uid}/places/{placeId}` references**, including references that do not currently exist.

This read-set is the key PA-001 concurrency control. The implementation does not claim to be a generic distributed lock. Its guarantee is bounded to the finite canonical place domain and the user/public roots participating in the reset.

Emulator tests exercise contention scenarios so that concurrent creation or mutation cannot silently survive a successful reset merely because it happened after an earlier enumeration.

## 8. Reset-contract preservation

PA-001 is an ownership/security migration, not a user-visible Clear Map redesign. The final implementation deliberately preserves the established reset semantics.

### User root

The transaction resets the approved travel fields:

- `birthplacePlaceId` → `null`;
- `isWishlistPublic` → `false`;
- `wishlistOrder` → deleted;
- `placesGeneration` → incremented;
- `stats.standardCountriesVisited` → `0`;
- `stats.totalWorldPercentage` → `0`;
- `stats.totalConqueredPlaces` → `0`;
- `stats.continentsVisited` → `0`;
- `stats.totalVisits` → `0`;
- `stats.achievements` → `[]`;
- `visitPhotoSlots` → deleted by the privileged server operation;
- `updatedAt` retains the established numeric `Date.now()` representation.

PA-001 did **not** introduce a new top-level `totalConqueredPlaces` field.

### Private places

Existing canonical private place documents under `users/{uid}/places/{placeId}` are deleted.

### Public root

When `publicProfiles/{uid}` exists:

- `isWishlistPublic` → `false`;
- `wishlistOrder` → deleted;
- `placesGeneration` → incremented consistently with the private root.

### Stale public place projections

Historical public child documents do not have to be physically deleted by PA-001. Their previous `placesGeneration` remains stale, while the root generation advances. Current-generation queries therefore do not treat the old projections as current.

Physical stale-projection garbage collection remains outside PA-001 and is not claimed by this evidence.

## 9. Idempotency and rollback behaviour

The final server predicate treats an account as already reset only when the complete resettable state is canonical, including the relevant travel stats, achievement list, Wishlist state, birthplace state, slot metadata and public-root Wishlist state.

A state with no private places but stale root/public resettable data is therefore still corrected rather than skipped.

Transaction failure coverage confirms that a failed reset does not leave an approved partial post-reset state. Duplicate/rapid requests have deterministic behaviour within the tested contract.

## 10. Test approach

PA-001 deliberately used the smallest destructive test layers that could prove the server boundary without sending destructive traffic to a real Firebase project.

Final qualification combined:

- client-wrapper unit tests;
- route authentication/lifecycle tests;
- Firestore Emulator server/concurrency tests;
- full non-emulator Vitest regression;
- TypeScript;
- ESLint baseline comparison;
- production build;
- `git diff --check`;
- exact-SHA GitHub/Vercel publication verification.

A temporary manual test skip introduced during remediation was removed before final qualification. The pre-existing emulator test was restored to HEAD and the final gate sequence was executed only after the last source/test edit.

## 11. Final automated results

| Gate | Final result |
|---|---:|
| Client Clear Map unit | **5 / 5 PASS** |
| Route security | **8 / 8 PASS** |
| PA-001 Firestore Emulator server/concurrency | **11 / 11 PASS** |
| Normal full Vitest | **889 PASS / 48 SKIPPED / 0 FAIL** |
| TypeScript | **PASS** |
| ESLint | **0 errors / 38 warnings / 0 PA-001-created warnings** |
| Production build | **PASS** |
| `git diff --check` | **PASS** |
| Real Firebase requests during final destructive qualification | **0** |

The 38 ESLint warnings are the qualified repository baseline and were not increased by PA-001.

## 12. Acceptance-criteria disposition

PA-001 closed the approved implementation criteria with one explicit infrastructure carry-forward.

- **AC-001–AC-016:** qualified by approved focused emulator/unit/source evidence;
- **AC-018–AC-022:** **PASS**;
- **AC-017:** **BLOCKED BY EXISTING E2E INFRASTRUCTURE**.

AC-017 is not marked PASS or N/A. The missing proof is a browser-level reload through the complete UI → client wrapper → API route → Admin server path while all Firebase dependencies are emulator-backed.

The existing Clear Map Playwright harness is production-oriented, while the new Admin server path requires broader server/emulator integration. Altering that infrastructure inside PA-001 would have expanded the finding into PA-007 territory.

The Test Lead accepted this as a bounded residual qualification item rather than a PA-001 publication blocker.

## 13. Product publication

PA-001 was committed and pushed as:

```text
b0fef1d825649a7c961a3413f11faa4c104b8aca
fix(clear-map): move reset to transactional server boundary
```

The commit parent is the qualified Node 24 RC baseline:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
```

GitHub `main` was independently verified at the PA-001 SHA after publication.

Vercel created the exact-SHA Production deployment:

```text
dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK
READY
production
b0fef1d825649a7c961a3413f11faa4c104b8aca
```

This deployment evidence proves publication/deployment integrity only. **No Production functional Clear Map PASS is claimed by AB-EV-066.**

## 14. Test Lead decision

**PA-001 — PASS / PUBLISHED**

The Test Lead approved the implementation after the final clean gate sequence demonstrated:

- no manual test skip;
- no unrelated file in the Product commit;
- exact reset-contract parity;
- privileged token revocation checking;
- contention coverage against canonical place creation/update and protected slot state;
- zero destructive real-Firebase calls during final qualification;
- clean Product publication from the authorised baseline.

## 15. Traceability

| Traceability element | PA-001 record |
|---|---|
| Premium finding | `PA-001` |
| Requirement | Clear Map must work for photo-bearing and photo-free owners without weakening server-managed photo-slot protection |
| Primary risks | QR-01, QR-04, QR-07; public/private projection and privacy integrity under QR-31 / QR-34 where applicable |
| Historical Clear Map evidence | AB-EV-037 |
| Photo/quota ownership evidence | AB-EV-044 |
| RC baseline | AB-EV-065 / Product `12a0dd53...` |
| Implementation | Product `b0fef1d825649a7c961a3413f11faa4c104b8aca` |
| Client tests | 5 / 5 PASS |
| Route tests | 8 / 8 PASS |
| Server/concurrency tests | 11 / 11 PASS |
| Full regression | 889 PASS / 48 SKIPPED / 0 FAIL |
| Static/build gates | TypeScript PASS; ESLint 0 errors / 38 warnings; build PASS; diff-check PASS |
| Evidence | AB-EV-066 |
| Deployment | `dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK` READY / production / exact SHA |
| Test Lead decision | PASS / PUBLISHED |
| Residual | AC-017 → PA-007 / Final Premium RC |

## 16. Relationship to earlier evidence

AB-EV-066 **does not rewrite** AB-EV-037. AB-EV-037 remains the correct historical record of the C37 client-batch architecture and generation invalidation decision.

AB-EV-066 records the later architectural hardening required after C44 changed the ownership model for visit-photo quota metadata. The later record supersedes only the assumption that the client remained the correct owner of every Clear Map field mutation.

AB-EV-044 remains the authoritative visit-photo/quota lifecycle baseline. PA-001 intentionally does not implement normal photo-removal quota recovery; that separate Premium finding is PA-002.

AB-EV-065 remains the Node 24 final RC qualification baseline from which PA-001 was implemented.

## 17. Carry-forward actions

The following item remains open by explicit Test Lead decision:

### AC-017 → PA-007 / Final Premium RC

Provide browser-level destructive-flow evidence across the full UI → API → Admin path under a controlled emulator-backed server environment, without real Firebase traffic.

This carry-forward must remain visible until either:

1. PA-007 closes the environment/server-boundary gap and the browser proof is executed; or
2. the Final Premium RC records an explicit residual-risk decision.

PA-001 itself is closed and published.
