# AB-EV-068 — PA-003 photo Admin/server-route lifecycle and input-contract premium hardening

**Evidence ID:** AB-EV-068  
**Programme:** AtlasBadge V1.0 — Premium Hardening  
**Finding:** PA-003  
**Product baseline:** `49abb003a9b495f3be43b4e30ea4b36dcfb8ceca`  
**Product commit:** `7616e8ca455f1d2e72a67e4dd839c9c302f98656`  
**Product commit subject:** `fix(visit-photo): complete PA-003 premium hardening`  
**Vercel deployment:** `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` — `READY`, `production`, exact Product SHA  
**Production alias:** `atlas-badge.vercel.app`  
**Test Lead decision:** **PASS / PUBLISHED / PRODUCTION VERIFIED / CLOSED**  
**Residuals:** authorisation-snapshot semantics retained; physical immutable-object GC deferred; old clients cannot write the frozen mutable slot namespace; rollback must preserve that freeze; malformed private immutable locators remain nominally string-typed but fail closed; no Production race test was executed

## 1. Context

PA-003 was the third mandatory AtlasBadge V1.0 Premium Architecture finding after PA-001 moved Clear Map protected state behind an authenticated Admin boundary and PA-002 made visit-photo quota lifecycle exact, durable and retry-safe.

PA-002 proved that active photo quota is a logical lifecycle contract rather than a count of historical Storage objects. PA-003 then examined whether the surrounding photo Admin/server-route boundary was equally strict about request shape, authentication/account state, current logical identity, failure rollback and the relationship between an authorised photo and the physical bytes later streamed from Storage.

The work was intentionally split into bounded slices. Each implementation slice was independently reviewed before Test Lead approval, and the final cumulative candidate received a separate S6 qualification before publication.

## 2. Initial independent audit

The initial read-only Codex audit identified the following findings:

| Finding | Initial assessment | Final decision |
|---|---|---|
| PA3-F01 | High — wrong-type persisted slot could bypass still-referenced protection and corrupt quota | **PASS** |
| PA3-F02 | High — failed replacement upload could release the allocation backing the existing persisted photo | **PASS** |
| PA3-F03 | Medium — revocation/account lifecycle gaps on reserve/read paths | **PASS** |
| PA3-F04 | Medium — malformed POST body/type handling | **PASS** |
| PA3-F05 | Medium — raw/internal error semantics and quota-message coupling | **PASS** |
| PA3-F06 | Low — non-canonical owner/public slot parsing | **PASS** |
| PA3-F07 | Medium — explicit current reference conflict could fall through to legacy resolution | **PASS** |
| PA3-F08 | High impact / timing-dependent — authorised request could later stream bytes belonging to another photo after mutable slot-path reuse | **PASS** |
| PA3-F09 | High assurance — material Admin-boundary tests missing | **PASS WITH OBSERVATION** |
| PA3-F10 | Low — bounded URL/header canonicalisation issues | **PASS** |
| PA3-F11 | Existing sound lifecycle design that must be preserved | **PASS** |
| PA3-F12 | Speculative generic traversal/UID takeover claim without concrete evidence | **REJECTED AS UNSUPPORTED** |

F08 was the material architecture defect. The pre-fix Storage model reused physical names such as:

```text
users/{uid}/visit-photo-slots/{slotId}/{variant}.webp
```

The independent reproduction placed a deterministic barrier after logical authorisation/metadata resolution but before opening the Storage stream. A later replacement or slot reuse changed the bytes behind the already-selected mutable name. The delayed request then returned bytes belonging to the newer photo.

That established a real authorisation-to-byte identity race rather than a theoretical naming concern.

## 3. Approved PA3 acceptance matrix

The Test Lead approved the following cumulative acceptance contract:

| ID | Acceptance criterion | Final status |
|---|---|---|
| PA3-01 | Mutation slot is numeric integer `0..9`; invalid forms are rejected before mutation; current persisted references cannot be released incorrectly | **PROVEN** |
| PA3-02 | POST ingress requires the approved JSON/object/typed identifier contract; malformed/ambiguous input fails before mutation | **PROVEN** |
| PA3-03 | Failed replacement preserves the current photo/allocation; failed initial upload releases the unpersisted allocation; later lifecycle recovery remains valid | **PROVEN** |
| PA3-04 | Required auth is revocation-aware; optional invalid public auth confers no owner privilege; anonymous public access remains supported where authorised | **PROVEN** |
| PA3-05 | Missing/inactive/deleting account state cannot mutate allocation or serve owner/private photo data; approved legacy absent-state semantics remain preserved where applicable | **PROVEN** |
| PA3-06 | Reserve requires verified email; verified active accounts may reserve; auth/account denials remain distinguishable from quota denials | **PROVEN** |
| PA3-07 | Exact identities protect current allocations; stale R1 cannot release/delete R2; repeated exact cleanup is idempotent | **PROVEN** |
| PA3-08 | Correct bytes only; mismatched refs denied; another authenticated user cannot retrieve owner/private bytes; physical retention alone never grants access | **PROVEN** |
| PA3-09 | Explicit current-reference conflict blocks weaker legacy fallback; genuine legacy-only compatibility remains available where intentionally supported | **PROVEN** |
| PA3-10 | Owner slot representation is canonical decimal `0..9`; malformed/fractional/out-of-range forms fail closed | **PROVEN** |
| PA3-11 | HTTP/query/method duplicate-policy and related bounded request contracts remain deterministic | **PROVEN** |
| PA3-12 | Internal failures are sanitised and domain/auth/quota distinctions remain stable | **PROVEN** |
| PA3-13 | URL construction preserves opaque identifiers through proper encoding/round trip | **PROVEN** |
| PA3-14 | PA2, S1–S5, Rules, account deletion, Clear Map affected regression and C44/local-real-Firebase safety remain preserved | **PROVEN WITH OBSERVATION** |
| PA3-15 | A delayed request authorised for X can return X or fail safely, but can never return Y after replacement or slot reuse; logical photo identity is immutably bound to physical bytes | **PROVEN** |

## 4. S1 — mutation input and error boundaries

S1 closed F01/F04/F05 and the corresponding PA3-01/02/07/12 contracts.

The final mutation boundary rejects malformed request bodies, wrong-type identifiers and invalid persisted slot values before they can mutate allocation state. Firestore document-segment validation also preserves the well-formed Unicode and UTF-8 1,500-byte constraints rather than relying on JavaScript string length alone.

Unexpected internal failures are sanitised at the HTTP boundary. Quota remains a typed domain outcome instead of being inferred from arbitrary error-message substrings.

The final S6 mutation-route group recorded **51 passing tests** across reserve, rollback release and persisted release routes, with additional Emulator/helper boundary evidence.

## 5. S2 — replacement rollback and allocation lifetime

S2 closed F02.

The critical rule is that `reservationId` identifies an **allocation lifetime**, not a byte version. A persisted same-visit replacement may therefore retain the reservation identity while the photo itself changes.

The corrected upload lifecycle distinguishes:

- **failed initial upload:** release the unpersisted allocation;
- **failed replacement:** preserve the existing logical photo and its backing allocation;
- **successful replacement:** retain one logical allocation while changing the photo version;
- **stale cleanup:** exact old identity cannot release the current replacement allocation.

Final evidence included injected Storage failure, actual Storage Rules size denial, quota-lifecycle Emulator coverage and slot-server Emulator coverage.

## 6. S3 — authentication and account lifecycle

S3 closed F03.

The shared photo-token validation uses revocation-aware verification for protected routes. Reserve requires a verified email, and server-side allocation transactions enforce active account state.

Read semantics deliberately differ from mutation semantics:

- owner missing/deleting/deleted root → **404**;
- public inactive root → **404**;
- inactive mutation/reserve boundary → **403 `VISIT_PHOTO_ACCOUNT_INACTIVE`**.

An early independent review found that a valid authenticated user with no root document could reach retained orphan private bytes through the owner route. The bounded S3 correction rejected missing roots before private place/Storage access while preserving the approved legacy case of an existing root with absent `accountState`.

## 7. S4 — read resolution and compatibility contracts

S4 closed F06/F07 and the approved bounded F10 work.

The final read resolution distinguishes **absence** from **presence with an invalid value**. This matters because compatibility fallback is weaker than the current explicit identity model.

Key final rules include:

- canonical owner slot parsing;
- deterministic duplicate security-query rejection;
- explicit current `photoRef` precedence;
- present-invalid explicit references fail closed;
- present-invalid/incomplete modern slot/variant locators fail closed;
- genuine legacy-only absence remains compatible;
- opaque identifiers use encoded URL construction;
- cross-account owner access remains denied.

The first S4 implementation was rejected because whitespace/non-string present references and malformed modern locators could still fall through to old paths. The S4-R01 correction made presence itself security-significant and received independent approval.

## 8. S5 — immutable byte identity

S5 closed F08/PA3-15.

The approved architecture replaces mutable byte identity with a fresh immutable object per logical photo version:

```text
users/{uid}/visit-photo-objects/{slotId}/{photoRef}.webp
```

A private `photoObjectKey` records the authoritative physical locator. `photoRef` is generated **before upload**, so a replacement receives a fresh reference/key even when the same slot and allocation reservation are retained.

The slot remains the logical quota/allocation unit. Physical immutable-object count does not define the ten-photo quota.

### 8.1 Write-once Storage contract

Storage Rules enforce the new namespace as create-only for authorised client uploads and deny client update/delete/read/list according to the existing Admin-proxy model.

The old mutable namespace is frozen for client writes:

```text
users/{uid}/visit-photo-slots/**
```

Existing pre-S5 objects remain readable through controlled server compatibility logic, but clients cannot overwrite those names after the cutover.

This freeze is mandatory to make the no-bulk-migration strategy safe.

### 8.2 Authorisation snapshot semantics

The approved read model uses authorisation-snapshot semantics:

- a request that already authorised X may finish the exact immutable X after X is later removed/replaced;
- a fresh request after logical invalidation is denied;
- a delayed X request must never receive Y.

Delivery-time logical revalidation was not required because it would add another read/race boundary without improving byte-identity correctness.

### 8.3 Deterministic race evidence

The dedicated PA3-15 suite passed **7/7** and the final S6 qualification refreshed supplemental Product-flow evidence:

| Scenario | Final result |
|---|---|
| T1 owner replacement | Delayed X returned exact X; fresh X denied; fresh Y returned Y |
| T2 public replacement | Delayed X returned exact X; fresh X denied; fresh Y returned Y |
| T3 slot reuse by another visit | Same slot reused with a distinct immutable key; delayed X remained X; fresh X denied; fresh Y returned Y |
| T4 upload failure | Actual Storage denial preserved the existing photo/key/allocation |
| T4 upload-success/save-failure | `CONCURRENCY_CONFLICT`; orphan immutable object existed physically but remained unauthorised |
| T5 retained object | Logical removal with retained immutable bytes caused fresh owner/public X requests to return 404 |

Exact payload hashes retained by the final qualification include:

- owner X: `7566c73186f770c44c645e4aa7ecccc0d9acb4e76e4cf998419f1825de663db2`;
- public X: `17649ef6152c7860a24059186bb98d544a39549ef4aa446bd9d378635892f342`;
- supplemental lifecycle X: `a6695eb48685c6622916b42e3f6b067da6dec34d3726d994e1ba397c12747bac`.

The barrier was independently inspected and remained after logical/key selection and before the underlying Storage stream opened; the test did not pre-buffer X.

## 9. S5-R01 — immutable-locator fail-closed correction

The first S5 implementation closed the core race but was rejected for two compatibility/canonicalisation defects discovered by independent review.

### F1 — present immutable key could be bypassed in one public legacy branch

A public projection could authorise X while the private record had no explicit `photoRef`, a present malformed `photoObjectKey`, a matching legacy `photoPath` and retained legacy bytes. The route returned `200 + LEGACY-DECOY` because it entered the legacy branch before evaluating the present immutable locator.

The correction evaluates `photoObjectKey` presence before compatibility branching. A present key declares S5 semantics and therefore requires complete trusted private identity. Invalid/incomplete S5 state returns 404 with no Storage selection.

Independent retest covered `"bad-key"`, empty string, `null`, `false`, `0`, object, array and canonical key with missing private `photoRef`; all returned 404 with no legacy Storage selection.

### F2 — canonicalisation erased malformed-present state

`photoObjectKey: ""` or `null` initially failed closed, but early canonicalisation/serialization dropped the field. After save/reload the record looked like a genuine pre-S5 record and old slot bytes became eligible.

The correction preserves defined present values through private canonicalisation/serialization so malformed-present state cannot silently become absence. Independent authenticated Product round trips proved empty string, `null`, `false`, `0`, object and array values remained present and owner/public reads stayed 404 before and after reload.

The nominal TypeScript field remains string-typed, but all current runtime consumers guard type and exact canonical equality; no malformed-value crash or authorisation downgrade was observed.

## 10. Storage Rules and Product-environment parity

The final Rules suite recorded:

**258 total / 258 passed / 0 failed / 0 skipped**.

Coverage includes:

- valid immutable CREATE;
- same-key second write denied;
- update/delete/read/list denied;
- old mutable namespace create/overwrite/delete denied;
- cross-user denial;
- missing/inactive account denial;
- unverified-email denial;
- invalid slot/ref/path denial;
- missing active allocation denial;
- size/content-type controls.

A separate focused smoke under the Product Emulator project `demo-atlasbadge-web` proved the Firestore-backed Storage checks behaved consistently there. After a denied attempt to replace `RULE-X` with `RULE-Y`, Admin download still returned the original `RULE-X` bytes.

## 11. Account deletion compatibility

The existing account-deletion server path enumerates the whole `users/{uid}/` Storage prefix. The new immutable namespace therefore remains covered without a Product implementation redesign.

Executed Emulator evidence created target and neighbouring immutable objects. Account deletion removed the target user's objects and preserved the neighbour's objects.

## 12. Full final qualification

S6 independently qualified the cumulative S1–S5 candidate and found no PA3 Product blocker.

| Gate | Final result |
|---|---:|
| S1 mutation route units | **51 PASS** |
| Owner route unit | **23 / 23 PASS** |
| Combined route Emulator | **45 / 45 PASS** |
| Immutable race suite | **7 / 7 PASS** |
| Quota lifecycle Emulator | **19 / 19 PASS** |
| Slot-server Emulator | **18 / 18 PASS** |
| Account-deletion visit-photo Emulator | **1 / 1 PASS** |
| Required five-suite Emulator group | **90 / 90 PASS** |
| Rules | **258 / 258 PASS**, 0 skipped |
| Authoritative Clear Map server regression | **11 / 11 PASS** |
| Clear Map client units | **5 / 5 PASS** |
| C44 Edge | **1 / 1 PASS**, `realFirebaseRequests=0` |
| Full Vitest | **989 PASS / 123 environment-gated SKIPPED / 0 FAIL** |
| Vitest files | **127 PASS / 11 SKIPPED** |
| TypeScript | **PASS** |
| ESLint | **0 errors / 30 warnings** |
| Production build | **PASS**, Next.js `16.3.6`, static generation `21 / 21` |
| `git diff --check` | **PASS** |
| Final S6 source integrity | **444 tracked/nonignored files unchanged during review** |

The skip audit established that all 123 default-run skips were environment-gated. Every required PA3/PA2 acceptance case was separately executed; no mandatory acceptance test remained skipped.

## 13. Clear Map observation

PA3-14 passed with an observation rather than claiming every historical optional suite was clean.

The authoritative Clear Map server suite passed 11/11 and client unit suite passed 5/5. A separate unchanged legacy `travelMap.clearMap.emulator.test.ts` suite produced 1 pass / 6 failures because its old fixtures no longer model the supported authenticated server-route/private-root visibility contract.

The suite, Rules, server implementation and `clearUserPlaces` function were independently confirmed identical to the PA3 baseline. The failures are therefore pre-existing test-maintenance debt, not PA3 Product regressions.

PA-001 AC-017 remains separately carried to PA-007 / Final Premium RC.

## 14. F09 assurance decision

F09 closed as **PASS WITH OBSERVATION**.

Material Admin/server boundaries now have evidence at appropriate levels:

| Boundary | Evidence |
|---|---|
| F01 wrong-type release | Route-unit rejection, helper validation and Emulator no-mutation checks |
| F02 replacement failure | Mocked upload rejection plus actual Storage denial and allocation preservation |
| F03 auth/account | Revocation-aware call assertions, account-gated Admin transactions and Auth-Emulator/C44 coverage |
| F07 fallback | Persisted malformed-state route fixtures and fail-closed R01 regression |
| F08 byte race | Actual route handlers/Storage streams plus supplemental Product release/reuse/failure flows |

Some token/error conditions use controlled mocks and no Production/backend race was executed. No material required local boundary remained without evidence.

## 15. Publication

After S6 approval and explicit Test Lead final approval, the cumulative PA3 delta was staged and published as one Product commit:

```text
7616e8ca455f1d2e72a67e4dd839c9c302f98656
fix(visit-photo): complete PA-003 premium hardening
```

Fresh remote verification immediately before commit established:

- Product baseline HEAD = `49abb003a9b495f3be43b4e30ea4b36dcfb8ceca`;
- freshly fetched `origin/main` = same baseline;
- divergence = `0 / 0`;
- approved inventory = 22 modified + 4 untracked files;
- no staged files;
- `git diff --check` PASS.

The staged checkpoint contained exactly 26 approved files and passed staged diff-check. After commit the working tree was clean and local `main` was exactly one commit ahead. Normal push then produced:

```text
HEAD        7616e8ca455f1d2e72a67e4dd839c9c302f98656
origin/main 7616e8ca455f1d2e72a67e4dd839c9c302f98656
divergence  0 / 0
```

Vercel automatic Git deployment produced:

```text
dpl_B842NnXHecrGNhcQAfpkC8CgyVhj
READY
production
Git ref main
Git SHA 7616e8ca455f1d2e72a67e4dd839c9c302f98656
```

The Production alias `atlas-badge.vercel.app` resolves to that deployment.

## 16. Post-publication Production verification

The final post-publication check was deliberately non-destructive.

Git was freshly fetched and remained clean/synchronised on the exact PA3 SHA. Vercel inspection confirmed the exact deployment/project/target/commit relationship.

Bounded Production HTTP smoke recorded:

| Request | Result | Final path |
|---|---|---|
| `/` | `307 → 200` | `/pt-br` |
| `/pt-br` | `200` | `/pt-br` |

The localized page served `pt-BR` and the expected AtlasBadge title. Three sampled Next static assets returned 200 with expected content types and non-empty content.

Safe rejected API smoke used no real account/photo identity:

- owner endpoint missing parameters → 400;
- owner endpoint validly shaped synthetic request without authentication → 401;
- public photo endpoint missing parameters → 400.

The responses exposed no private Storage/document paths, stack traces, raw Firebase Admin errors, reservation IDs or environment values.

A deployment-scoped Vercel error/fatal log query over the bounded post-publication window returned no error/fatal groups.

No Auth account creation/update, Firestore write/delete, Storage upload/update/delete, allocation/release, profile mutation, Clear Map or account deletion occurred in Production.

Final decision:

**PA-003 POST-PUBLICATION PASS — READY TO CLOSE**

## 17. Test Lead decision

**PA-003 — PASS / PUBLISHED / PRODUCTION VERIFIED / CLOSED**

The Test Lead approved PA-003 after independent S6 qualification established that PA3-01 through PA3-15 were satisfied, no release blocker remained and the exact qualified bytes were preserved through staging, commit, `origin/main`, Vercel Production and bounded post-publication verification.

## 18. Residuals and carry-forwards

### Authorisation snapshot

An already-authorised request may complete with the exact immutable X even if X is logically removed/replaced afterward. Fresh requests after invalidation are denied. This is the approved PA3-15 semantics.

### Physical immutable-object GC

Historical/failed-save immutable objects may accumulate. Physical GC remains deferred because retained objects confer no logical authorisation and do not count toward active photo quota.

### Frozen old clients

Old clients that still target `visit-photo-slots/**` fail safely after the Storage Rules cutover. Rollback must never reopen mutable writes merely to support stale clients.

### Malformed private locator typing

Malformed persisted values remain fail-closed through save/reload but may exist at runtime inside a nominally string-typed private field. Current consumers type-check before use. A future schema-hardening refactor may make this representation more explicit if justified by risk/cost.

### Separate Premium carry-forwards

PA-003 does not absorb:

- PA-006A — deterministic tooling / undeclared cached `tsx`;
- PA-007 — Firebase namespace/safety and PA-001 AC-017;
- PA-008 — account-deletion origin allowlist;
- PA-001 compensating post-publication architecture audit;
- legacy Clear Map fixture/test debt;
- formal physical Storage GC.

## 19. Lessons from PA-003

PA-003 reinforces several reusable QA rules:

1. **Authorisation must bind to immutable resource identity, not a reusable name.** A logically correct access check is insufficient if the underlying physical locator can later resolve to different bytes.
2. **Presence and validity are different security states.** Compatibility fallback is allowed only for genuine absence; present-invalid modern identity must fail closed.
3. **Persistence/canonicalisation is part of the security boundary.** A malformed-present field that is silently dropped can reopen a weaker legacy path after reload.
4. **Race tests need the barrier at the real semantic boundary.** A test that buffers bytes before mutation can be green while proving nothing about the authorisation-to-stream race.
5. **Post-publication verification should be proportional.** Exact commit/deployment traceability, safe read-only HTTP/API smoke and runtime-log inspection can verify publication without manufacturing destructive Production data.

These rules are promoted to the living Lessons Learned control after PA-003 closure.

## 20. Traceability

| Traceability element | PA-003 record |
|---|---|
| Premium finding | `PA-003` |
| Requirement | Photo Admin/server routes must enforce strict input/auth/account/current-reference contracts and bind authorised logical photo identity to immutable physical bytes |
| Primary risks | quota corruption; replacement rollback; account/auth boundary; request/error contract; private/public identity isolation; stale/legacy fallback; mutable-object byte race; assurance gap |
| Prior Product baseline | PA-002 / AB-EV-067 / `49abb003...` |
| Implementation | Product `7616e8ca455f1d2e72a67e4dd839c9c302f98656` |
| Acceptance | PA3-01 through PA3-15 — all PASS; PA3-14 PASS WITH OBSERVATION |
| Findings | F01–F08 PASS; F09 PASS WITH OBSERVATION; F10/F11 PASS; F12 rejected as unsupported |
| Required Emulator | five-suite group 90 / 90 PASS |
| Rules | 258 / 258 PASS, 0 skipped |
| Full Vitest | 989 PASS / 123 environment-gated SKIPPED / 0 FAIL |
| C44 | 1 / 1 PASS; `realFirebaseRequests=0` |
| Clear Map | authoritative server 11 / 11; client unit 5 / 5; unchanged legacy suite debt separately documented |
| Static/build | TypeScript PASS; ESLint 0 errors / 30 warnings; build PASS; diff-check PASS |
| Independent approval | Codex S6 **APPROVE PA-003 — READY FOR TEST LEAD FINAL DECISION** |
| Test Lead | final approval before publication; post-publication closure accepted |
| Evidence | AB-EV-068 |
| Commit | `7616e8ca455f1d2e72a67e4dd839c9c302f98656` |
| Deployment | `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` READY / production / exact SHA |
| Production smoke | localized page/assets operational; bounded rejected API checks; no error/fatal deployment log groups; zero intentional Firebase mutation |
| Final status | **PASS / PUBLISHED / PRODUCTION VERIFIED / CLOSED** |
| Residuals | authorisation snapshot; deferred physical GC; frozen old clients; rollback freeze constraint; nominal malformed-locator typing; no Production race claim |

## 21. Relationship to earlier evidence

AB-EV-044 remains the historical C44 record that introduced registered-visit photos, opaque public references and bounded free quota.

AB-EV-066 remains the PA-001 Clear Map server-boundary record.

AB-EV-067 remains the PA-002 active-quota lifecycle record. PA-003 preserves PA-002's exact reservation/tombstone semantics while strengthening route input/auth/read contracts and replacing mutable byte identity for new uploads.

AB-EV-068 is the third published Premium Hardening closure. It does not rewrite those earlier decisions and does not absorb the separately tracked PA-006A/PA-007/PA-008/physical-GC work.