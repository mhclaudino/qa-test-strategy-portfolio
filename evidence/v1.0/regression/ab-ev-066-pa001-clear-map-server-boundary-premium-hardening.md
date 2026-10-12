# AB-EV-066 — PA-001 Clear Map server-boundary premium hardening

**Evidence ID:** AB-EV-066  
**Programme:** AtlasBadge V1.0 — Premium Hardening  
**Finding:** PA-001  
**Implementation commit:** `b0fef1d825649a7c961a3413f11faa4c104b8aca`  
**Parent qualification baseline:** `12a0dd53dfe789610e6393b31db1c94c311757ff`  
**Implementation deployment:** `dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK` — `READY`, `production`, exact implementation SHA  
**Test-oracle hotfix commit:** `d0116967fc389353191c11e8865f20e38b9d5835`  
**Hotfix deployment:** `dpl_5M8VAxJ6VcWdcgSM78rsVDWYiP4J` — `READY`, `production`, exact hotfix SHA  
**Test Lead decision:** **PASS / PUBLISHED**  
**Residual qualification item:** AC-017 carried to PA-007 / Final Premium RC  
**Governance carry-forward:** compensating **post-publication** architecture audit of `12a0dd53... → b0fef1d...` remains open before final Premium Hardening closure

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

Final implementation qualification combined:

- client-wrapper unit tests;
- route authentication/lifecycle tests;
- Firestore Emulator server/concurrency tests;
- full non-emulator Vitest regression;
- TypeScript;
- ESLint baseline comparison;
- production build;
- `git diff --check`;
- exact-SHA GitHub/Vercel publication verification.

A temporary manual test skip introduced during remediation was removed before qualification. The pre-existing emulator test was restored to HEAD and the final gate sequence was executed only after the last implementation/test edit.

## 11. Implementation qualification result

| Gate | Implementation qualification result |
|---|---:|
| Client Clear Map unit | **5 / 5 PASS** |
| Route security | **8 / 8 PASS** |
| PA-001 Firestore Emulator server/concurrency | **11 / 11 PASS** |
| Normal full Vitest | **889 PASS / 48 SKIPPED / 0 FAIL** after the later test-oracle repair |
| TypeScript | **PASS** |
| ESLint | **0 errors / 38 warnings / 0 PA-001-created warnings** |
| Production build | **PASS** |
| `git diff --check` | **PASS** |
| Real Firebase requests during destructive qualification | **0** |

The 38 ESLint warnings were the qualified repository baseline and were not increased by PA-001.

## 12. Acceptance-criteria disposition

PA-001 closed the approved implementation criteria with one explicit infrastructure carry-forward.

- **AC-001–AC-016:** qualified by approved focused Emulator/unit/source evidence;
- **AC-018–AC-022:** **PASS**;
- **AC-017:** **BLOCKED BY EXISTING E2E INFRASTRUCTURE**.

AC-017 is not marked PASS or N/A. The missing proof is a browser-level reload through the complete UI → client wrapper → API route → Admin server path while all Firebase dependencies are Emulator-backed.

The existing Clear Map Playwright harness was production-oriented, while the new Admin server path requires broader server/Emulator integration. Altering that infrastructure inside PA-001 would have expanded the finding into PA-007 territory.

The Test Lead accepted this as a bounded residual qualification item rather than a PA-001 publication blocker.

## 13. Original Product publication

PA-001 implementation was committed and pushed as:

```text
b0fef1d825649a7c961a3413f11faa4c104b8aca
fix(clear-map): move reset to transactional server boundary
```

The commit parent is the qualified Node 24 RC baseline:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
```

Vercel created the exact-SHA Production deployment:

```text
dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK
READY
production
b0fef1d825649a7c961a3413f11faa4c104b8aca
```

This deployment evidence proves publication/deployment integrity only. **No Production functional Clear Map PASS is claimed by AB-EV-066.**

## 14. Governance deviation — independent review ordering

The original PA-001 publication did **not** follow the intended independent-review ordering.

The coordinator authorised commit/push before the planned independent Codex architecture review of the implementation had been completed. This was a governance/process-control deviation. The chronology must not be rewritten to imply that the review happened before publication.

The deviation does not by itself establish a Product Defect and no rollback is justified solely to recreate the same valid Product state under a different review order. The corrective controls are:

1. record the deviation transparently;
2. retain a compensating **post-publication** architecture audit of the exact implementation delta `12a0dd53... → b0fef1d...` as an open governance item;
3. require that audit before final Premium Hardening closure;
4. require independent pre-publication review for subsequent Premium findings.

PA-002 followed the corrected ordering and received independent approval before publication.

## 15. Post-publication UTF-8 test-oracle defect and hotfix

After the implementation was already published, the PA-001 route tests exposed a separate test-oracle defect. Three assertions expected mojibake response text while the runtime route returned correct UTF-8 Portuguese text.

The Product runtime behaviour was already correct. The repair therefore changed **only** the test oracle in:

```text
src/app/api/account/clear-map/route.test.ts
```

The isolated branch/hotfix was independently reviewed by Codex and received **APPROVE / NO FINDINGS** before publication.

The qualified one-file candidate hash was:

```text
112138f095f0235d5aca66b10d0df4d2f44cde0b41e5fd48427f5bc6d38c55c3
```

Final repair qualification included:

- corrected three UTF-8 expectations;
- full Vitest **889 PASS / 48 SKIPPED / 0 FAIL**;
- TypeScript PASS;
- ESLint **0 errors / 38 warnings**;
- production build PASS;
- `git diff --check` PASS.

The test-only hotfix was published as:

```text
d0116967fc389353191c11e8865f20e38b9d5835
test(clear-map): fix UTF-8 route expectations
```

Vercel automatically deployed that exact Product SHA:

```text
dpl_5M8VAxJ6VcWdcgSM78rsVDWYiP4J
READY
production
d0116967fc389353191c11e8865f20e38b9d5835
```

The hotfix introduced **no runtime behaviour change**. It restored reproducible qualification of the already-published correct UTF-8 runtime behaviour.

## 16. Test Lead decision

**PA-001 — PASS / PUBLISHED**

The Test Lead decision remains valid for the implemented server-boundary correction. The later test-oracle repair is recorded as a qualification repair, not as a Product functional correction.

The decision does not close the two explicit carry-forwards:

- AC-017 browser-level Emulator UI → API → Admin reload proof → PA-007 / Final Premium RC;
- compensating post-publication architecture audit of `12a0dd53... → b0fef1d...` → required before final Premium Hardening closure.

## 17. Traceability

| Traceability element | PA-001 record |
|---|---|
| Premium finding | `PA-001` |
| Requirement | Clear Map must work for photo-bearing and photo-free owners without weakening server-managed photo-slot protection |
| Primary risks | QR-01, QR-04, QR-07; public/private projection and privacy integrity under QR-31 / QR-34 where applicable |
| Historical Clear Map evidence | AB-EV-037 |
| Photo/quota ownership evidence | AB-EV-044 |
| RC baseline | AB-EV-065 / Product `12a0dd53...` |
| Implementation | Product `b0fef1d825649a7c961a3413f11faa4c104b8aca` |
| Original deployment | `dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK` READY / production / exact implementation SHA |
| Test-oracle repair | Product `d0116967fc389353191c11e8865f20e38b9d5835` |
| Hotfix review | Codex APPROVE / NO FINDINGS |
| Hotfix deployment | `dpl_5M8VAxJ6VcWdcgSM78rsVDWYiP4J` READY / production / exact hotfix SHA |
| Client tests | 5 / 5 PASS |
| Route tests | 8 / 8 PASS after UTF-8 oracle correction |
| Server/concurrency tests | 11 / 11 PASS |
| Full regression | 889 PASS / 48 SKIPPED / 0 FAIL |
| Static/build gates | TypeScript PASS; ESLint 0 errors / 38 warnings; build PASS; diff-check PASS |
| Evidence | AB-EV-066 |
| Test Lead decision | PASS / PUBLISHED |
| Functional residual | AC-017 → PA-007 / Final Premium RC |
| Governance residual | compensating post-publication architecture audit before final Premium Hardening closure |

## 18. Relationship to earlier and later evidence

AB-EV-066 **does not rewrite** AB-EV-037. AB-EV-037 remains the correct historical record of the C37 client-batch architecture and generation invalidation decision.

AB-EV-066 records the later architectural hardening required after C44 changed the ownership model for visit-photo quota metadata. The later record supersedes only the assumption that the client remained the correct owner of every Clear Map field mutation.

AB-EV-044 remains the authoritative visit-photo/quota lifecycle baseline. PA-001 intentionally did not implement normal photo-removal quota recovery; that separate Premium finding was PA-002 and is closed later by AB-EV-067.

AB-EV-065 remains the Node 24 final RC qualification baseline from which PA-001 implementation was created.

## 19. Carry-forward actions

### AC-017 → PA-007 / Final Premium RC

Provide browser-level destructive-flow evidence across the full UI → API → Admin path under a controlled Emulator-backed server environment, without real Firebase traffic.

### PA-001 compensating architecture audit

Perform and record an explicitly **post-publication** independent architecture audit of the exact implementation delta:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
→
b0fef1d825649a7c961a3413f11faa4c104b8aca
```

This audit must not be described retroactively as pre-publication review. It is a governance closure item required before the Premium Hardening programme is finally closed.

PA-001 itself remains functionally closed and published; the test-oracle hotfix is also closed and published.
