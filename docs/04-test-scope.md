# AtlasBadge Test Scope

## 1. Document purpose

This document defines the testing scope for AtlasBadge during incremental V1.0 development and release preparation.

It identifies covered product areas, expected depth, current coverage, mandatory remaining work and intentionally deferred items.

> **Document status:** Completed and maintained through AB-EV-057. C39–C44 cover the current privacy/order/public-display, editable-name, visual-identity and visit-photo baseline. C45A establishes public locale routing; C45B completes the localized public Home/Hero/Header/Footer surface and language selector; C45C completes Login/auth entry; C45D Onboarding/profile creation; C45E email verification; C45F the authenticated `/app` shell/dashboard; C45G the deep authenticated country/visit editor. C45H completes authenticated `/badges`, all current achievement title/description presentation, locale-aware earned dates and `BadgeUnlockToast` for the six approved V1.0 locales while preserving achievement-domain and public-Profile contracts. C45I completes Profile Edit plus embedded access-method/account-deletion presentation localization while preserving profile persistence, auth/security behaviour and the separate public-Profile localization boundary. C45J subsequently localizes that canonical public-Profile boundary; two narrowly scoped follow-ups fix authenticated Header labels (AB-DEF-019) and short-loading/long-content Header shift (AB-DEF-020), with Production visual approval on 20 September 2026.

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

Coverage includes registration/login/logout, verification/recovery, persistent sessions, protected routes, auth-method linking, username lifecycle, account ownership and account deletion.

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
- Clear Map atomic logical reset;
- generation-based stale public projection invalidation;
- account deletion lifecycle;
- impact on counters/achievements/Profile.

AB-EV-034 closes the previous residual QR-01 assessment gap through write-path architectural equivalence plus focused `flagSortOrder` failure/recovery coverage. QR-01 remains a High `Regression risk` because persistence failure remains consequential, but there is no known Current gap in the assessed V1.0 write paths.

AB-EV-036 adds a real Firestore Emulator rejected-batch regression for the combined Wishlist settings helper. One denied write causes the full Save to fail with private root, public root and public place projections unchanged. C36 also removes per-place order write amplification, keeping the largest supported transition at no more than 253 document writes.

AB-EV-037 extends atomic persistence coverage to the destructive Clear Map path. The previous split public/private commit model and stale root `wishlistOrder` lifecycle were replaced by one logical reset batch of at most 253 writes. Rejected core writes leave the old confirmed state intact; obsolete public child documents are invalidated through `placesGeneration` and Rules/query generation checks rather than being required inside the atomic batch.

C45G/AB-EV-052 requalifies the deep status/visit/memory/photo editing layer while localizing presentation. Persistence identifiers, explicit-Save boundaries, OCC/status semantics, `RegisteredVisit` identity, memory order, visit names and photo-slot/quota state remain locale-neutral and are protected by directly affected regression.

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
- public read-only behaviour;
- viewer-local sorting without persistence;
- account deletion/public projection cleanup.

AB-EV-033 established the public/private projection baseline. AB-EV-036 extends it with the atomic root-order/privacy model, aligned Firestore Rules and final Production validation. AB-EV-037 adds backward-compatible generation-0 public reads plus strict stale-generation denial after a versioned Clear Map reset.

C39/AB-EV-039 moves per-memory visibility into executed V1.0 scope. C40/AB-EV-040 protects memory presentation order independently from visit-history source order. C41/AB-EV-041 adds public-memory display from earned flags and confirms the public modal uses the sanitised projection for both owner and anonymous viewing. C42/AB-EV-042 adds optional editable visit names while preserving stable visit IDs, explicit Save, manual memory order, legacy `VISITA n` fallback and private/public projection boundaries. C45G/AB-EV-052 localizes those owner editing controls and preserves the same public sanitisation/photoRef boundary. C45H/AB-EV-053 confirms localized achievement rendering does not expand the public achievement metadata whitelist or leak authenticated locale context into the still-Portuguese public Profile.

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

AB-EV-035 protects this contract with focused QR-24 domain tests, a full Vitest checkpoint, one proportional Emulator browser/persistence scenario and Test Lead manual QA.

Wishlist uses the existing Want-to-visit source of truth; Wishlist-only records are non-physical and excluded from Manual Visit Order.

C36/AB-EV-036 protects the separate Wishlist settings contract:

- root `wishlistOrder` is presentation metadata, not membership;
- legacy `wishlistOrderRank` is fallback only;
- order-only, privacy-only and combined Saves use the confirmed root model;
- owner and public views must render canonical saved order after reload;
- successful owner settings changes refresh the confirmed Profile-root state used by the modal.

C37/AB-EV-037 adds the destructive lifecycle requirement that Clear Map removes root Wishlist order/public visibility inside the same atomic logical reset and that obsolete public Wishlist/place projections cannot reappear as current data after a later place write.

C45G localizes the six status labels and status-related error/ARIA presentation without changing the machine IDs or compatibility matrix. C35 coexistence, birthplace uniqueness, last-intent/OCC and physical-presence transitions remain permanent affected-area regression.

### 5.5 Account and data deletion — Executed / regression risk

Coverage includes identity deletion, private/public Firestore cleanup, username release, repeat/retry behaviour and absence of orphaned public records.

AB-EV-033 extends lifecycle expectations to Wishlist membership/ranks/public preference and public-profile places. C36 changes the canonical order source to the user/public root. C37 extends the destructive reset model so Clear Map removes private place/root state atomically while public child cleanup is represented by secure generation invalidation rather than a correctness-dependent second commit.

---

## 6. High-priority scope

### 6.1 Map and geographic catalogue — Executed

Coverage includes all 251 directly selectable records, status colouring, search/filter, normal/micro-marker interaction, desktop/mobile behaviour and the canonical `252 Places / 195 Countries / 57 Territories and Entities` model.

C31/AB-EV-029 covers the audited catalogue classification, UK-derived Place/Country rule, Antarctica classification, counter invariant and affected achievements. C32/AB-EV-030 covers Profile map click → exact earned-flag scroll/highlight, including no-flag fallback, sorting, micro-markers, mobile and read-only proof. C33/AB-EV-031 covers the Map's local Visit Order/Alphabetical selected-place presentation without data mutation.

The technical UK aggregate remains non-selectable/derived. C45F found the legacy UK-selector modal unreachable through supported clicks; that remains a separate non-defect cleanup candidate and C45G/C45H do not alter it.

Clear Map regression includes the supported maximum of 251 private place deletes plus two root writes, for a maximum atomic logical boundary of 253 writes.

### 6.2 Visits, memories and ordering — Executed for current V1.0 behaviour

Coverage includes repeated/detailed visits, explicit-Save memories, visit-count derivation, rapid add/remove/save, Manual Visit Order, Born there integrity and independent Wishlist ordering.

C34/AB-EV-032 covers owner-only Manual Visit Order editing, `visitOrderRank` validation, Born there fixed-first behaviour, exclusion of Wishlist-only/Nationality-only records, Cancel/no-write, Save/rank persistence, rapid replayable visit intents and transactional birthplace integrity. The final C34 gate recorded 308 Vitest PASS, 224 Rules PASS, 9 focused Emulator E2E PASS and Production Manual Visit Order/Rapid Visits/Birthplace smokes PASS.

C35 adds explicit coverage that adding another compatible place status does not manufacture a travel occurrence and does not duplicate existing memories.

C36 separates ordering domains explicitly: Manual Visit Order remains per-place `visitOrderRank`; Wishlist presentation order is root `wishlistOrder` with legacy rank fallback only.

C37 ensures the destructive Clear Map path removes ordering/lifecycle state without depending on a second public cleanup batch.

C44/AB-EV-044 is executed. Coverage includes one processed WebP photo per `RegisteredVisit`, draft preview until explicit Save, replace/remove, privacy inheritance, bounded slots `0..9` with A/B replacement variants, a hard free quota of 10 active visit photos per user, owner/public server-mediated reads, opaque public `photoRef`, Clear Map/remove-visit/account-deletion cleanup and Production validation. Direct client read/list/delete of bounded visit-photo objects remains denied by Storage Rules.

C45G/AB-EV-052 is executed. It localizes CountryActionCard, general-memory editing, VisitEditor, MemoryOrderEditor and visit-photo presentation for all six locales. Release regression re-proved status/OCC, C35 coexistence, C39 privacy, C40 memory order, C42 visit names and the directly affected C44 photo/quota/cleanup boundary with Auth/Firestore/Storage Emulators and `realFirebaseRequests=0`.

### 6.3 Personal Profile — Executed

Coverage includes profile fields, username/display/bio/social data, profile visibility, Wishlist visibility preference, canonical Wishlist root order, `placesGeneration`, flag-sort persistence/recovery, responsive behaviour and relationship with the public projection.

The C36 owner modal is covered for root-order precedence and confirmed Profile refresh after order-only, privacy-only or combined settings changes.

C37 adds regression that unrelated public-profile updates preserve both `wishlistOrder` and `placesGeneration` rather than dropping root lifecycle metadata.

C45I/AB-EV-054 completes ProfileEditModal localization for all six locales, including username/profile/social/privacy/photo presentation plus embedded `AuthMethods`, `DeleteAccountSection` and `DeleteAccountModal` copy. The editor owns a scoped locale boundary so it can render localized from both authenticated navigation and the still-unlocalized owner public Profile without persisting locale or changing the parent route.

### 6.4 Public Profile — Executed for implemented V1.0 baseline

Coverage includes:

- valid/invalid/private profile resolution;
- authenticated and anonymous viewers;
- public-source-only data access;
- read-only map and C32 map-to-flag navigation with exact-target highlight/no-mutation behaviour;
- flag ordering/presentation controls, including C33 local ordering semantics;
- public achievements from sanitised metadata;
- public Wishlist tile/modal only when public and non-empty;
- canonical public Wishlist order from sanitised root `wishlistOrder`;
- legacy generation-0 public place compatibility;
- current-generation public place queries;
- stale-generation direct-read denial;
- no status/privacy/save/reorder controls in the public Wishlist modal;
- desktop/mobile layout and modal scroll lock;
- sanitisation of public root/place fields.

C45J/AB-EV-055 executes public-Profile presentation localization for all six V1.0 locales while preserving public projection, read-only viewer state, owner Edit Profile, memory/photo/Wishlist visibility, stable achievement IDs/chronology and unprefixed `/@username` routing. Its localized Header/Footer and anonymous selector preserve the contextual authenticated/anonymous distinction. C45J-FIX1/AB-DEF-019 closes Portuguese fallback in the authenticated public-Profile Header; C45J-FIX2/AB-DEF-020 closes the loading-to-content Header displacement by reserving scrollbar gutter at root. C45G/C45H/C45I remain the preceding isolation/projection baselines; geographic country/continent display names, resetPassword and authenticated language selection are separate future work.

### 6.5 Counters and statistics — Executed / regression risk

Coverage includes countries, territories/entities, conceptual Places, continents, Total Visits, status summaries, repeated visits and cross-screen consistency.

Wishlist-only status/order must not change physical-presence counters. Multiple compatible physical statuses still count a place once, and C35 confirms that adding Visited/Passed-through compatibility does not itself increase Total Visits.

Clear Map remains responsible for resetting the established private statistics in the same logical destructive operation.

### 6.6 Badges and achievements — Executed including C45H localization / continued regression

Coverage includes unlock/relock chronology, World Completion, UK semantics, persisted metadata, notification behaviour, public sanitised `achievementMetadata`, authenticated `/badges` localization and localized `BadgeUnlockToast` presentation.

C45H/AB-EV-053 validates all 31 current achievement IDs (`a1`–`a27`, `a29`–`a32`; no `a28`) across all six locales. Localization is keyed by stable achievement ID and passed additively at the presentation boundary; canonical achievement objects/evaluators are not mutated. Earned dates are locale-formatted without changing timestamps or chronological order.

The strict invalid-earned-metadata assertion remains active. Reconciled Emulator state proved valid `unlockedAt`/sequence values and `nextAchievementUnlockSequence`; the notification queue, five-second timeout, click/Enter/Space behaviour, stable `/badges#<achievementId>` navigation and reconquest remain protected.

Public Profile achievement presentation intentionally remains Portuguese in C45H and the public projection remains `{ unlockedAt, sequence }` only.

### 6.7 Localisation — C45A–C45H executed / remaining surfaces ongoing before V1.0

Supported locales, in the official V1.0 order, are:

- `pt-BR`
- `pt-PT`
- `es-419`
- `es-ES`
- `fr`
- `en-GB`

C45A/AB-EV-045 establishes six explicit public Home routes (`/pt-br`, `/pt-pt`, `/es-419`, `/es-es`, `/fr`, `/en-gb`) and a root `/` resolver. Locale precedence is explicit URL → valid saved `atlasbadge_locale` cookie → browser/device language → `pt-BR` fallback. Authenticated routes remain unprefixed, and the existing root `[username]` public-profile routing contract is unchanged.

C45B/AB-EV-046 completes the translated public Home/Hero/Header/Footer layer across all six locales. Desktop exposes the six flags in official order; narrow mobile uses one compact locale trigger with an accessible six-option menu. Explicit language selection persists `atlasbadge_locale=<locale ID>`, localized Home metadata and `<html lang>` match the active route, and the selector remains available on localized Home for authenticated users while staying absent from `/app`, `/badges` and Login. C45J adds the selector for **anonymous** public Profile viewers only; authenticated public Profile keeps it hidden. The Argentina flag remains the deliberate visual representation for `es-419`; locale logic remains keyed by locale ID, not flag/country name.

C45C–C45E extend localization through Login, Onboarding and email verification. C45F/AB-EV-051 localizes the authenticated `/app` shell/dashboard. C45G/AB-EV-052 localizes the deep country/visit editor. C45H/AB-EV-053 localizes the authenticated `/badges` route, metadata/document language, achievement title/description presentation, earned-date formatting and Badge Unlock notification while preserving locale-neutral achievement IDs/rules/metadata.

C45J/AB-EV-055 completes the canonical unprefixed public Profile presentation for six locales (with FIX1/FIX2 closed). C45K/AB-EV-056 completes geographic **display-name** localization for supported consumers without changing canonical IDs, user-authored data or the public projection. C45L/AB-EV-057 closes resetPassword action presentation in six locales. Remaining localization scope includes the future authenticated language selector (C45M). Profile Edit remains complete under C45I/AB-EV-054. User-authored content is not automatically translated. C45B's root `headers()` solution makes the page tree request-time dynamic; the architecture review accepted this as bounded V1.0 technical debt because eliminating it would require a disproportionate multi-root route migration.

### 6.8 Performance and responsiveness — Partially executed / ongoing

Executed coverage includes map interaction, responsive layouts, repeated interaction, desktop/mobile viewports and important modal/control behaviour.

AB-EV-043 adds a cross-product desktop/mobile visual pass over Home/auth, dashboard/map, picker/filter, editors, Wishlist, Profile, badges and shared modals. The pass harmonised brand tokens and surfaces without changing layout or functional behaviour.

C45G adds focused `390×844` deep-editor validation for `fr`, `pt-PT` and `es-ES`. C45H adds focused `390×844` Badges/toast validation for the same representative locales, including long localized copy, earned dates, locks/progress bars and toast viewport/close-control behaviour. C45I adds Profile Edit `390×844` validation for `fr`, `pt-PT`, `es-ES` and `en-GB`, including long labels, feedback, switches and nested dialogs without horizontal overflow. C45J adds public Profile `390×844` browser regression for those four locales and real-browser Test Lead approval; FIX2's native scrollbar-shift acceptance is manual because headless overlay scrollbars did not replicate Windows layout-width behaviour.

Quantitative performance targets and broader device/browser evidence remain incomplete.

### 6.9 Accessibility — Technical baseline executed

AB-EV-017 establishes a scoped WCAG 2.2 AA technical baseline; later features include targeted keyboard/semantic/dialog regression. AB-EV-043 preserves visible keyboard focus while moving generic interaction emphasis to Atlas Gold and retaining semantic feedback/status colours.

C45G adds localized ARIA labels for status/visit/duration/memory/photo editing. C45H adds localized Badges loading/ARIA text, toast close accessible name, Enter/Space activation and stable anchor/focus behaviour. C45I adds localized form labels, disabled-state coverage, focus handling and nested-dialog presentation for Profile Edit/security/account controls. C45J adds localized public-profile/selector navigation and ARIA presentation; FIX1 checks authenticated nav/avatar accessible names without introducing an authenticated selector.

Formal accessibility certification and comprehensive native assistive-technology coverage remain out of claim.

---

## 7. Automation scope

Permanent automated coverage includes:

- Vitest component/domain tests;
- Firestore Rules suite;
- backend-focused Firestore Emulator integration/fault-injection tests;
- Playwright browser E2E against Firebase Emulators;
- controlled Production-only Playwright validation when authorised.

The normal E2E persistence environment is isolated at `127.0.0.1:3100` with Auth/Firestore Emulators and project `demo-atlasbadge-web`; Storage Emulator is added when visit-photo behavior is in scope.

Real Firebase requests are fail-fast blockers in Emulator regression. C45G and C45H release validation recorded `realFirebaseRequests=0`.

C36 permanent coverage includes real Emulator batch rejection rather than relying only on a mocked `commit()` failure. Ordering coverage also includes the owner read-path/root-order precedence that manual QA initially exposed as missing.

C37 permanent coverage includes real Emulator logical-reset atomicity, maximum-size coverage, legacy/current/stale generation read rules, later-new-place protection, Profile-root metadata preservation and focused Clear Map confirmation-modal success/failure behaviour.

C45G permanent coverage connects localized presentation to the existing status/OCC, C35, C39, C40, C42 and C44 stateful boundaries rather than duplicating all domain permutations at browser level.

C45H permanent coverage connects localized achievement presentation to the stable achievement ID/metadata domain, with catalog completeness, date formatting, strict ordering assertions, notification/reconquest behaviour and public Profile isolation covered directly.

Stale test assumptions and invalid prepared fixtures are test-maintenance/test-data defects, not product defects. They are corrected without weakening functional acceptance criteria or Product assertions.

---

## 8. Regression scope selection

### Core checkpoints

High-value checkpoints include static quality gates, unit/domain tests, Rules, persistence/privacy E2E, backend atomicity, geographic/map regression, lifecycle/destructive scenarios and responsive/profile flows.

### Checkpoint rule

A green checkpoint is retained unless a later change directly invalidates it. Before a broad rerun, the invalidated evidence and reason should be identified.

AB-EV-034/035 demonstrate proportional gap/requirement validation. AB-EV-036 extends the principle: the central Wishlist persistence redesign justified one full Vitest/Rules/build baseline, while later read-path corrections were requalified through focused Wishlist tests and Test Lead retest rather than repeatedly rerunning unrelated suites.

AB-EV-037 follows the same model: Rules/build/static checkpoints were rerun because their artefacts changed; focused Clear Map, public-source, Wishlist/upsert and modal tests restored the affected boundaries without an unnecessary full browser campaign.

AB-EV-052 follows the same model for a high-risk localization change: shared deep-editor code justified focused domain/component and Emulator regression across the touched C35/C39/C40/C42/C44 contracts, but unchanged Firebase Rules and unrelated surfaces did not trigger unrelated release campaigns.

AB-EV-053 applies the same model to achievements: focused Vitest/Emulator/Playwright coverage requalified translation mapping, shared cards, chronology/assertions, notification/reconquest and route isolation without reopening unrelated Firestore Rules or travel-data campaigns.

---

## 9. Production scope

Production validation is focused on real integration after local/Emulator gates.

When Firestore Rules change, Production validation begins only after both:

- Vercel is READY at the expected Git SHA;
- the approved Firestore Rules-only deployment succeeds.

C35 changed production application source but not Firestore Rules/schema/configuration. Its Vercel deployment required no Rules deploy.

C36 changed both production application source and Firestore Rules. The controlled release recorded:

```text
Product SHA: 5d660b016528e75a2a70b49010a84065d884f883
Vercel: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9 — READY / production
Rules command: firebase deploy --only firestore:rules --project atlas-badge
Rules result: SUCCESS
Production smoke: PASS / Test Lead approved
```

C37 also changed both application source and Firestore Rules. The controlled release recorded:

```text
Product SHA: add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
Vercel: dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz — READY / production
Rules command: firebase deploy --only firestore:rules --project atlas-badge
Rules result: SUCCESS
Production Clear Map retest: PASS / Test Lead approved
```

C43 was presentation-only and required no Rules/backend change. Product SHA `bcc10d63e0f3363d39f3029c0498deb49c30180f` reached Vercel deployment `dpl_FHmZdquA35oa6FVQjtpHVLZAcfyt` in READY/production state, followed by Test Lead **Production Visual PASS**.

C45G changed Product/test presentation/error mapping but did not change Firebase Rules/configuration. Its stateful deep-editor writes and Storage lifecycle were proven in Emulators; Production release validation was intentionally non-destructive and route/isolation focused rather than manufacturing a new Production travel/photo dataset.

C45H likewise changed Product/test presentation only. Its stateful achievement unlock/reconciliation/toast behaviour was proven against the Emulators, while Production smoke verified `/badges`/`/app` locale handling, public Profile/resetPassword isolation, deployed assets and Vercel runtime logs without creating or mutating Production achievement state.

Production uses controlled QA accounts/data only. Destructive operations require separate explicit authorisation and are not implied by a normal release smoke.

---

## 10. Deferred / assessment-gap scope

Current gaps/deferred areas include:

- broad Firefox/Safari/macOS/iPhone compatibility;
- formal load/stress testing;
- independent penetration testing;
- formal accessibility certification/native AT matrix;
- quantitative performance SLAs;
- future Story/social features beyond current scope;
- authenticated-selector localization (C45M); resetPassword action (C45L) and geographic display-name mapping (C45K) are completed and remain regression scope;
- geographic presentation regression on future consumers (initial mapping completed by C45K/AB-EV-056);
- separate legacy UK-selector unreachable-code cleanup.

Physical garbage collection of stale public-generation place documents is not an open functional defect. It is optional housekeeping because correctness and public-read safety no longer depend on immediate physical deletion.

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
- frontend/Rules deployment mismatch;
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
- `evidence/v1.0/regression/ab-ev-045-c45a-localization-routing-foundation.md`
- `evidence/v1.0/regression/ab-ev-046-c45b-public-home-localization-and-language-selector.md`
- `evidence/v1.0/regression/ab-ev-047-c45c-login-localization-and-locale-continuity.md`
- `evidence/v1.0/regression/ab-ev-048-c45d-onboarding-localization.md`
- `evidence/v1.0/regression/ab-ev-049-public-home-language-selector-centering.md`
- `evidence/v1.0/regression/ab-ev-050-c45e-email-verification-localization.md`
- `evidence/v1.0/regression/ab-ev-051-c45f-authenticated-dashboard-localization.md`
- `evidence/v1.0/regression/ab-ev-052-c45g-deep-country-visit-editor-localization.md`
- `evidence/v1.0/regression/ab-ev-053-c45h-badges-achievements-localization.md`
- `evidence/v1.0/regression/ab-ev-054-c45i-profile-edit-localization.md`
- `evidence/v1.0/regression/ab-ev-055-c45j-public-profile-localization-and-fixes.md`
- `evidence/v1.0/regression/ab-ev-056-c45k-geographic-localization.md`
- `evidence/v1.0/regression/ab-ev-057-c45l-password-reset-action-localization.md`
- `docs/10-lessons-learned.md`

## C45K / AB-EV-056 — geographic localization closure

C45K completes the six-locale geographic presentation across the picker, search, country cards, Wishlist, manual ordering, dashboard/public maps, public flags/memories/continents and the Home decorative map's accessible labels. Acceptance requires stable IDs, 251/252/195/57 counter semantics, eight canonical continents, UK derivation, status/visit/order/public projection and intact user-authored text. The 66-case final C45/title Edge E2E collection is representative, not exhaustive across every locale × UI surface.

[Consolidated evidence](../evidence/v1.0/regression/ab-ev-056-c45k-geographic-localization.md).

## C45L / AB-EV-057 — password-reset action localization

C45L completes localized resetPassword action states, metadata, validation, errors, success and Login return in pt-BR/pt-PT/es-419/es-ES/fr/en-GB. C45E verifyEmail and unsupported/missing-mode fallback remain regression contracts. 29/29 affected Edge/Firebase Emulator E2E were reported; a separate 1 PASS viewport 390×844 covered fr/en-GB/pt-PT states. Test Lead manually approved supported local visuals. Physical handset and live Production password-reset behavior were NOT EXECUTED; Vercel exact-SHA READY does not substitute for a functional Production reset.

[Consolidated evidence](../evidence/v1.0/regression/ab-ev-057-c45l-password-reset-action-localization.md).
