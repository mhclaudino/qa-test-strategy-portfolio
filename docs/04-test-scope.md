# AtlasBadge Test Scope

## 1. Document purpose

This document defines the testing scope for AtlasBadge during incremental V1.0 development and release preparation.

It identifies covered product areas, expected depth, current coverage, mandatory remaining work and intentionally deferred items.

> **Document status:** Completed and maintained through AB-EV-068. C39–C44 establish the current privacy/order/public-display, editable-name, visual-identity and visit-photo baseline. C45A–C45M established the historical six-locale rollout; C45N/AB-EV-059 added German and AB-EV-060 closed the final seven-locale visual/Production acceptance. AB-EV-061–065 then established the release-readiness / Node 24 technical RC anchor. Premium Hardening subsequently closed PA-001/AB-EV-066, PA-002/AB-EV-067 and PA-003/AB-EV-068. PA-003 is published and Production verified; final V1.0 Go Live and final Premium Hardening closure remain separate decisions.

---

## 2. Relationship with other portfolio documents

Read together with:

- [Product Overview](01-product-overview.md)
- [Quality Risk Analysis](02-quality-risk-analysis.md)
- [Test Strategy](03-test-strategy.md)
- [Test Environments](06-test-environments.md)
- [System Test Plan](09-system-test-plan.md)
- [Lessons Learned](10-lessons-learned.md)
- [V1.0 Evidence Register](../evidence/v1.0/evidence-register.md)

The actual execution scope is selected according to affected functionality, dependencies, quality risks and consequences of failure. Not every green checkpoint is automatically rerun after every unrelated change.

---

## 3. Coverage status

| Status | Meaning |
|---|---|
| Executed | Meaningful current functional/regression evidence exists for the implemented baseline. |
| Partially executed | Coverage exists but mandatory/final V1.0 work remains. |
| Planned before V1.0 | Required before release approval but not yet complete. |
| Conditional V1.0 candidate | Enters mandatory scope if implementation is accepted for V1.0. |
| Deferred beyond V1.0 | Explicitly not required for first official release. |
| Assessment gap | Available evidence is insufficient for a broad compatibility/quality claim. |

---

## 4. Test priority and depth

### Critical

Deep/release-blocking coverage including positive/negative, persistence, privacy, destructive, recovery, concurrency and regression scenarios where relevant.

### High

Broad coverage of main flows, state changes, integrations, responsive behaviour and important dependencies.

### Medium

Focused validation sufficient for the defined baseline and residual risk.

An apparently visual change may become High/Critical when it makes essential controls unusable, as demonstrated by AB-DEF-016.

---

## 5. Critical scope

### 5.1 Authentication and account lifecycle — Executed

Coverage includes registration/login/logout, verification/recovery, persistent sessions, auth-method linking, username lifecycle, account ownership and account deletion.

Regression is required whenever authentication, owner identity, Firestore access or public-profile resolution changes.

### 5.2 Data persistence and integrity — Executed / QR-01 Regression risk

Coverage includes:

- status creation/change/removal;
- visits and private memories;
- refresh/session restoration;
- optimistic/confirmed state reconciliation;
- rejected-write rollback/recovery;
- repeated/rapid mutation;
- Manual Visit Order through `visitOrderRank`;
- manual memory presentation order through `memoryOrder`, without changing `registeredVisits` source order;
- Wishlist membership through `statuses.wishlist` and canonical order through root `wishlistOrder`;
- atomic combined Wishlist order/privacy Save;
- private/public place/root projection synchronisation;
- Clear Map atomic logical reset and current server-owned protected-state mutation;
- generation-based stale public projection invalidation;
- visit-photo active-quota release, durable exact retry and concurrent-edit-safe reconciliation;
- visit-photo immutable byte identity, replacement rollback and fail-closed physical-locator persistence;
- account deletion lifecycle;
- impact on counters/achievements/Profile.

AB-EV-034 closes the previous residual QR-01 assessment gap through write-path architectural equivalence plus focused `flagSortOrder` failure/recovery coverage. QR-01 remains a High `Regression risk` because persistence failure remains consequential, but there is no known Current gap in the assessed V1.0 write paths.

AB-EV-036 adds a real Firestore Emulator rejected-batch regression for the combined Wishlist settings helper. One denied write causes the full Save to fail with private root, public root and public place projections unchanged. C36 also removes per-place order write amplification, keeping the largest supported transition at no more than 253 document writes.

AB-EV-037 established atomic logical Clear Map and generation invalidation. PA-001/AB-EV-066 later hardens the same user-visible reset contract after C44 made `visitPhotoSlots` server-managed: protected reset mutation now executes through an authenticated server/Admin transaction rather than by weakening client Rules. AC-017 full browser UI → API → Admin all-Emulator reload evidence remains a PA-007 / Final Premium RC carry-forward.

C45G/AB-EV-052 requalified the deep status/visit/memory/photo editing layer while localizing presentation. PA-002/AB-EV-067 later strengthens ordinary photo/visit removal: logical removal plus exact retry identity persists before release, stale release cannot remove a newer allocation, successful cleanup clears durably against current orchestrated state and refresh is awaited before completion.

PA-003/AB-EV-068 closes the surrounding photo Admin/server-route hardening. New uploads bind each logical photo version to a fresh immutable Storage object keyed by `photoRef`, failed replacement preserves the existing allocation/photo, malformed-present immutable locator state remains fail-closed through save/reload, and the former mutable slot namespace is frozen for client writes.

### 5.3 Privacy and user isolation — Executed for current model

Coverage includes:

- private user/place owner-only access;
- public Profile access by owner/non-owner/anonymous sessions;
- sanitised `publicProfiles/{uid}` and public place projections;
- absence of private memory/visit fields in public responses;
- per-memory public/private controls and legacy-private default;
- sanitised `publicMemories` only for memories explicitly eligible for publication;
- owner and anonymous public-memory UI consuming the same public projection;
- earned country/territory flags opening the read-only public-memory modal;
- public/private whole-profile transitions;
- Wishlist private/public/public→private transitions;
- public root `wishlistOrder` only while Wishlist visibility is public;
- versioned public place projection through `placesGeneration`;
- denial of stale-generation public place reads after Clear Map;
- removal/invalidation of Wishlist-only public projections when privacy/reset logic requires it;
- preservation/sanitisation of mixed public place documents;
- current owner/public visit-photo identity validation after logical removal;
- immutable logical-photo → physical-object binding for new visit-photo versions;
- fail-closed handling of present-invalid immutable photo locators with no weaker legacy fallback;
- public read-only behaviour;
- viewer-local sorting without persistence;
- account deletion/public projection cleanup.

AB-EV-033 established the public/private projection baseline. AB-EV-036 extends it with the atomic root-order/privacy model. AB-EV-037 added generation invalidation; AB-EV-066 preserves that model while moving protected Clear Map ownership to the server/Admin boundary. AB-EV-067 adds immediate logical photo inaccessibility: owner/public requests depend on the current private/public reference, not retained Storage bytes. AB-EV-068 then makes the physical byte identity immutable for new photo versions and proves delayed authorised requests cannot receive bytes from a later replacement or slot reuse.

C39/AB-EV-039 moves per-memory visibility into executed V1.0 scope. C40/AB-EV-040 protects memory presentation order independently from visit-history source order. C41/AB-EV-041 adds public-memory display from earned flags. C42/AB-EV-042 adds optional editable visit names while preserving stable visit IDs and private/public projection boundaries. C45G/AB-EV-052 localizes those owner editing controls. C45H/AB-EV-053 confirms localized achievement rendering does not expand the public achievement metadata whitelist.

### 5.4 Travel status and Wishlist rules — Executed

All six statuses are covered:

- Visited
- Lived / Live there
- Born there
- Nationality
- Passed through
- Want to visit / Wishlist

Coverage includes valid/incompatible combinations, automatic dependent transitions, rapid changes, reload persistence, impact on visits/memories/counters and public projection.

C35 corrects the previous mutual-exclusion interpretation: **Visited and Passed through may coexist**. Passed through may also coexist with Lived/Born because those statuses imply Visited. The second compatible status does not itself create a new `RegisteredVisit` or increment `visitsCount`.

Wishlist uses the existing Want-to-visit source of truth; Wishlist-only records are non-physical and excluded from Manual Visit Order. C36/AB-EV-036 protects the separate Wishlist settings contract; C37/AB-EV-037 and PA-001/AB-EV-066 protect its destructive Clear Map lifecycle.

### 5.5 Account and data deletion — Executed / regression risk

Coverage includes identity deletion, private/public Firestore cleanup, username release, repeat/retry behaviour and absence of orphaned public records.

AB-EV-033 extends lifecycle expectations to Wishlist membership/ranks/public preference and public-profile places. C36 changes the canonical order source to the user/public root. C37/PA-001 extend the destructive reset model so Clear Map removes private place/root state through the approved atomic/server-owned boundary while public child cleanup is represented by secure generation invalidation rather than a correctness-dependent physical delete. PA-002 applies the same logical-versus-physical principle to visit photos: logical access/quota completion is immediate while physical Storage cleanup may be deferred. PA-003 confirms the existing account-deletion prefix cleanup also removes the new immutable `visit-photo-objects/**` namespace while preserving neighbouring users' objects.

---

## 6. High-priority scope

### 6.1 Map and geographic catalogue — Executed

Coverage includes all 251 directly selectable records, status colouring, search/filter, normal/micro-marker interaction, desktop/mobile behaviour and the canonical `252 Places / 195 Countries / 57 Territories and Entities` model.

C31/AB-EV-029 covers the audited catalogue classification, UK-derived Place/Country rule, Antarctica classification, counter invariant and affected achievements. C32/AB-EV-030 covers Profile map click → exact earned-flag scroll/highlight. C33/AB-EV-031 covers the Map's local Visit Order/Alphabetical selected-place presentation without data mutation.

The technical UK aggregate remains non-selectable/derived. The legacy UK-selector modal remains a separate non-defect cleanup candidate.

### 6.2 Visits, memories, photos and ordering — Executed for current V1.0 behaviour

Coverage includes repeated/detailed visits, explicit-Save memories, visit-count derivation, rapid add/remove/save, Manual Visit Order, Born there integrity and independent Wishlist ordering.

C34/AB-EV-032 covers owner-only Manual Visit Order editing, `visitOrderRank` validation, Born there fixed-first behaviour, rapid replayable visit intents and transactional birthplace integrity. C35 adds explicit coverage that compatible status changes do not manufacture occurrences. C36 separates ordering domains. C37 protects destructive ordering cleanup.

C44/AB-EV-044 introduced one processed WebP photo per `RegisteredVisit`, draft preview until explicit Save, replace/remove, privacy inheritance, bounded slots `0..9`, a hard free quota of 10 active visit photos per user, owner/public server-mediated reads, opaque public `photoRef`, Clear Map/remove-visit/account-deletion cleanup and Production validation. Its A/B slot-object naming remains only a compatibility concern for pre-PA-003 data; direct client read/list/delete remains denied by Storage Rules.

PA-002/AB-EV-067 is the ordinary removal/quota baseline. The quota counts active persisted logical photos. Coverage proves exact release on photo/visit removal, immediate capacity reuse, same/different-visit stale-release races, durable failure tombstones across reload, full-quota recovery, current owner/public identity, awaited refresh ordering and current-state-safe concurrent reconciliation. Physical Storage deletion remains intentionally deferred because logical access and active quota no longer depend on it.

PA-003/AB-EV-068 is the current photo Admin/read/upload boundary baseline. New photo versions use immutable keys `users/{uid}/visit-photo-objects/{slotId}/{photoRef}.webp`; the slot remains quota/allocation identity rather than byte identity; same-key overwrite/update/delete is denied to the client; the legacy mutable namespace is client-write frozen; and owner/public compatibility readers support genuine pre-S5 records only when the immutable locator is genuinely absent.

### 6.3 Personal Profile — Executed

Coverage includes profile fields, username/display/bio/social data, profile visibility, Wishlist visibility preference, canonical Wishlist root order, `placesGeneration`, flag-sort persistence/recovery, responsive behaviour and relationship with the public projection.

C45I/AB-EV-054 completes ProfileEditModal localization for the current localization architecture without changing profile/auth/security persistence semantics.

### 6.4 Public Profile — Executed for implemented V1.0 baseline

Coverage includes:

- valid/invalid/private profile resolution;
- authenticated and anonymous viewers;
- public-source-only data access;
- read-only map and C32 map-to-flag navigation;
- flag ordering/presentation controls;
- public achievements from sanitised metadata;
- public Wishlist tile/modal only when public and non-empty;
- canonical public Wishlist order from sanitised root `wishlistOrder`;
- legacy/current-generation public place handling;
- stale-generation direct-read denial;
- no owner-only controls for viewers;
- current public photo-reference enforcement after logical removal;
- immutable current-photo byte selection and stale-projection denial;
- desktop/mobile layout and modal scroll lock;
- sanitisation of public root/place fields.

C45J/AB-EV-055 executes public-Profile presentation localization while preserving public projection/read-only viewer state. AB-EV-067 proves that an obsolete public photo reference returns 404 after projection removal even when the physical object remains. AB-EV-068 further proves that an authorised request already resolved to X can finish only X (or fail safely), never bytes from later Y after replacement/slot reuse; fresh stale-X requests remain denied.

### 6.5 Counters and statistics — Executed / regression risk

Coverage includes countries, territories/entities, conceptual Places, continents, Total Visits, status summaries, repeated visits and cross-screen consistency.

Wishlist-only status/order must not change physical-presence counters. Multiple compatible physical statuses still count a place once, and C35 confirms that adding Visited/Passed-through compatibility does not itself increase Total Visits.

Clear Map remains responsible for resetting the established private statistics in the same logical destructive operation.

### 6.6 Badges and achievements — Executed / continued regression

Coverage includes unlock/relock chronology, World Completion, UK semantics, persisted metadata, notification behaviour, public sanitised `achievementMetadata`, authenticated `/badges` localization and localized `BadgeUnlockToast` presentation.

C45H/AB-EV-053 validates all 31 current achievement IDs and their presentation while keeping canonical evaluator/metadata objects locale-neutral. Public Profile achievement presentation later became localized under C45J without widening the projection.

### 6.7 Localisation — C45A–C45N executed / seven-locale V1.0 baseline

Supported locales, in official V1.0 order, are:

- `pt-BR`
- `pt-PT`
- `es-419`
- `es-ES`
- `fr`
- `de-DE`
- `en-GB`

C45A–C45M established the architecture and original six-locale rollout. C45N/AB-EV-059 added `de-DE` through the same architecture; AB-EV-060 records final seven-locale Test Lead visual/Production closure and AB-DEF-022 closure. Locale remains presentation-only; business IDs, Firebase schema/Rules, privacy contracts and user-authored content are not translated.

Localization is therefore regression scope, not an open implementation gate at the current Premium Hardening checkpoint.

### 6.8 Performance and responsiveness — Partially executed / ongoing

Executed coverage includes map interaction, responsive layouts, repeated interaction, desktop/mobile viewports and important modal/control behaviour.

AB-EV-043 adds a cross-product visual pass; later localization evidence adds representative mobile/desktop checks through AB-EV-060. Quantitative performance targets and broader device/browser evidence remain incomplete. PA-011 explicitly requires baseline measurement before optimisation work.

### 6.9 Accessibility — Technical baseline executed

AB-EV-017 establishes a scoped WCAG 2.2 AA technical baseline; later features include targeted keyboard/semantic/dialog regression through the localization programme.

Formal accessibility certification and comprehensive native assistive-technology coverage remain out of claim. PA-004 is a Premium accessibility/localization hardening finding and must be scoped from evidence, not assumed closed by historical checks.

---

## 7. Automation scope

Permanent automated coverage includes:

- Vitest component/domain tests;
- Firestore Rules suite;
- backend-focused Firestore Emulator integration/fault-injection tests;
- Playwright browser E2E against Firebase Emulators;
- controlled Production-only Playwright validation when authorised.

The normal E2E persistence environment is isolated at `127.0.0.1:3100` with Auth/Firestore Emulators and project `demo-atlasbadge-web`; Storage Emulator is added when visit-photo behavior is in scope.

Real Firebase requests are fail-fast blockers in Emulator regression. PA-002 final C44 regression recorded `realFirebaseRequests=0`; PA-003 final qualification retained the same C44 safety result while adding the local Auth/Firestore/Storage Emulator race and Rules coverage.

Premium Hardening also treats the **acceptance-oracle inventory** as part of automation scope. Named acceptance cases may not be silently deleted/relabelled and replaced with superficially similar green tests. Final approval operates on an exact frozen candidate byte set and verifies warning identity where lint totals move.

---

## 8. Regression scope selection

### Core checkpoints

High-value checkpoints include static quality gates, unit/domain tests, Rules, persistence/privacy E2E, backend atomicity, geographic/map regression, lifecycle/destructive scenarios and responsive/profile flows.

### Checkpoint rule

A green checkpoint is retained unless a later change directly invalidates it. Before a broad rerun, the invalidated evidence and reason should be identified.

Premium findings use the same principle. PA-001/PA-002/PA-003 did not reopen unrelated localization/geography evidence; they requalified the exact ownership, persistence, concurrency, privacy, Rules, C44/Clear Map and build/static surfaces their changes could invalidate.

---

## 9. Production scope

Production validation is focused on real integration after local/Emulator gates.

When Firestore or Storage Rules change, Production validation begins only after the approved frontend and Rules release state is understood for the intended smoke. A READY Vercel deployment alone is not represented as proof of stateful Firebase lifecycle behaviour.

Production uses controlled QA accounts/data only. Destructive operations require separate explicit authorisation and are not implied by a normal release smoke.

For PA-001/PA-002, Production publication verification was primarily exact-SHA/deployment metadata after destructive/stateful behaviour had been qualified under controlled Emulator/local gates. PA-003 retained that model but added a bounded non-destructive post-publication Production smoke: root→`/pt-br` routing, static assets and rejected unauthenticated/malformed photo API requests were checked, with no Production Firebase mutation and no error/fatal deployment logs observed in the inspected window.

Current Premium Product anchor:

```text
7616e8ca455f1d2e72a67e4dd839c9c302f98656
fix(visit-photo): complete PA-003 premium hardening
```

Vercel Production:

```text
dpl_B842NnXHecrGNhcQAfpkC8CgyVhj
READY
production
exact SHA 7616e8ca455f1d2e72a67e4dd839c9c302f98656
alias atlas-badge.vercel.app
```

---

## 10. Deferred / assessment-gap scope

Current gaps/deferred areas include:

- broad Firefox/Safari/macOS/iPhone compatibility;
- formal load/stress testing;
- independent penetration testing;
- formal accessibility certification/native AT matrix;
- quantitative performance SLAs;
- future Story/social features beyond current scope;
- PA-001 AC-017 browser UI → API → Admin Emulator reload proof, carried to PA-007 / Final Premium RC;
- PA-001 compensating **post-publication** architecture audit, required before final Premium Hardening closure;
- physical immutable visit-photo object garbage collection, intentionally deferred because logical access/quota correctness and immutable byte identity are proven independently;
- old-client visit-photo writes intentionally fail after the legacy mutable namespace freeze; any rollback must preserve that freeze;
- PA-006A deterministic-tooling cleanup for the cached undeclared `tsx` dependency;
- PA-007 Firebase namespace/safety follow-up;
- PA-008 account-deletion origin allowlist follow-up;
- separate legacy UK-selector unreachable-code cleanup.

Physical garbage collection of stale public-generation place documents is likewise not an open functional defect; correctness and public-read safety do not depend on immediate physical deletion.

PA-003 photo Admin/server-route lifecycle and input-contract hardening is **closed / published / Production verified** under AB-EV-068.

---

## 11. Release-blocking principles

Release approval is blocked by evidence of:

- material data loss or partial persistence contrary to an atomic user action;
- private-data exposure or unauthorised private reads;
- stale public-generation data remaining readable after a destructive reset;
- broken account/authentication essentials;
- persistence that cannot be trusted;
- broken status/Wishlist compatibility;
- inconsistent core counters;
- broken achievement chronology/metadata when achievements are affected;
- inaccessible/unusable critical controls;
- frontend/Rules deployment mismatch where both are part of the release;
- missing required independent review or final-byte integrity where those are approved publication gates;
- insufficient evidence for a High/Critical change.

---

## 12. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
- `evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md`
- `evidence/v1.0/defects/ab-ev-037-clear-map-atomic-generation-reset.md`
- `evidence/v1.0/regression/ab-ev-042-editable-visit-names.md`
- `evidence/v1.0/regression/ab-ev-043-visual-identity-alignment.md`
- `evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md`
- `evidence/v1.0/regression/ab-ev-052-c45g-deep-country-visit-editor-localization.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-059-c45n-german-localization-extension.md`
- `evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md`
- `evidence/v1.0/regression/ab-ev-061-v1-release-readiness-audit.md`
- `evidence/v1.0/regression/ab-ev-062-final-v1-release-candidate-regression-charter.md`
- `evidence/v1.0/regression/ab-ev-063-rc-lockfile-reproducibility-recovery.md`
- `evidence/v1.0/regression/ab-ev-064-c46-node24-lts-upgrade-charter.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-068-pa003-photo-admin-server-route-hardening.md`
- `docs/10-lessons-learned.md`

## Current Premium Hardening scope checkpoint

- **PA-001 / AB-EV-066:** functionally closed/published; UTF-8 test-oracle hotfix closed; AC-017 and compensating post-publication architecture audit remain explicit carry-forwards.
- **PA-002 / AB-EV-067:** closed/published/Production READY; all original acceptance criteria independently PROVEN; physical Storage deletion intentionally deferred.
- **PA-003 / AB-EV-068:** closed/published/Production verified; F01–F11 accepted, F12 rejected as unsupported, PA3-01 through PA3-15 proven, immutable byte identity and fail-closed compatibility are now the photo-boundary baseline.
- **Later Premium findings:** remain separately sequenced and must not be pulled into a closed finding without evidence of a direct blocker.

This checkpoint does not claim final Premium Hardening closure or final V1.0 Go Live.