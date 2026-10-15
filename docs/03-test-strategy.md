# AtlasBadge Test Strategy

## 1. Document purpose

This document defines the overall testing strategy for AtlasBadge and how quality is directed and governed during V1.0 development.

It covers risk prioritisation, test design, regression, evidence, AI-assisted execution, automation, release approval and residual risk.

> **Document status:** Completed and maintained through AB-EV-068. The current strategy includes versioned Playwright/Firebase Emulator regression, checkpointed incremental validation, controlled real-backend/Production validation, explicit environment/runtime parity checks, real-browser acceptance, fixed living-document governance and Premium Hardening controls for named acceptance-oracle preservation, independent pre-publication review where required, exact candidate-byte freeze/integrity, cross-service Rules qualification and publication-chain verification. PA-001, PA-002 and PA-003 are published Premium closures. Product `7616e8ca455f1d2e72a67e4dd839c9c302f98656` / Vercel `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` is the current published anchor. Final V1.0 Go Live is not claimed.

---

## 2. Strategy objectives

The AtlasBadge test strategy aims to:

- protect user-created travel data from loss, corruption or unintended deletion;
- protect private account/travel data from unauthorised access;
- validate authentication, account lifecycle and identity flows;
- confirm travel-status and Wishlist compatibility rules;
- maintain consistency between map, profiles, visits, memories, statistics, flags and achievements;
- validate private/public Firestore projection boundaries;
- identify regressions as early as possible;
- provide evidence for deployment/release decisions;
- use automation where it is repeatable and valuable;
- avoid unnecessary reruns when a previously green checkpoint has not been invalidated;
- keep product defects distinct from stale or defective test automation;
- communicate residual risk honestly.

The objective is release confidence, not an unrealistic claim that every theoretical combination was tested.

---

## 3. Strategy context

AtlasBadge is a published web product built with Next.js, React, TypeScript, Tailwind CSS, Firebase Authentication, Cloud Firestore and Vercel.

Its main quality challenges include asynchronous persistence, optimistic state, concurrency, interdependent statuses, detailed visits/memories, Wishlist privacy/order, visit-photo active quota/lifecycle and immutable byte identity, geographic catalogue integrity, public/private profile projection, achievements, responsive map/UI behaviour and account lifecycle.

The strategy should be read together with the [Product Overview](01-product-overview.md) and [Quality Risk Analysis](02-quality-risk-analysis.md).

---

## 4. Quality ownership and governance

AI-assisted tools contribute only within the actor boundaries defined by `GOVERNANCE-ERRATA.md` and repository instructions. ChatGPT coordinates requirements/risk/evidence and QA documentation; Antigravity/Gemini is the default Product implementation agent; Codex provides independent review when required; the Test Lead owns final quality/publication decisions.

The Test Lead remains responsible for:

- clarifying expected behaviour;
- identifying quality risks;
- defining required test depth;
- designing scenarios and acceptance criteria;
- reviewing automated/manual evidence;
- assessing defects and residual risk;
- deciding regression breadth;
- approving or blocking commits, pushes, deployments and Production validation.

Use of AI does not transfer quality ownership.

Where an independent review is an approved publication gate, publication is fail-closed until that review completes. PA-001 contains a transparently recorded review-order deviation and requires a compensating **post-publication** architecture audit before final Premium Hardening closure. PA-002 demonstrates the corrected sequence: uncommitted implementation/remediation → exact candidate freeze → independent Codex APPROVE → Test Lead publication authorisation → exact-byte staged/committed/remote/deployment verification. PA-003 follows the corrected sequence through sliced implementation/review, S6 final qualification, Test Lead final approval, exact Git/Vercel publication identity and a bounded non-destructive post-publication qualification before closure.

---

## 5. Risk-based testing approach

Testing depth is based on impact, likelihood, reach, technical dependencies, privacy/data consequences, regression probability, workaround availability and evidence quality.

Highest priority is given to changes affecting:

1. Firestore persistence and data integrity;
2. privacy, authorisation and public/private projection;
3. authentication/account lifecycle;
4. status and Wishlist compatibility;
5. visits, counters and chronology;
6. geographic catalogue/map integrity;
7. achievements and public metadata;
8. responsive behaviour and accessibility;
9. release/deployment parity.

A feature working through its happy path is not sufficient release evidence for a high-risk change.

---

## 6. Incremental validation workflow

AtlasBadge is tested incrementally.

The current workflow is:

```text
Requirement / product rule
        ↓
Implementation
        ↓
Preliminary technical checks
        ↓
Focused functional validation
        ↓
Affected-area regression
        ↓
Defect correction + direct retest
        ↓
Checkpointed regression gate
        ↓
Quality / repository audit
        ↓
Independent review when required by the current publication gate
        ↓
Test Lead commit/push approval
        ↓
Deployment parity / exact-SHA verification
        ↓
Focused Production validation where proportionate
        ↓
Test Lead sign-off + evidence update
```

### 6.1 Checkpoint preservation

A green test checkpoint is preserved unless a later change directly invalidates it.

Examples:

- a pure E2E locator cleanup does not automatically require full Vitest, Rules and build reruns;
- a production-code persistence change requires focused affected-area tests and relevant static/build gates;
- a Rules change requires the Rules suite and release-parity validation;
- a test-only change is validated with the directly affected tests and static quality gates where applicable;
- a broad presentation-only change does not require backend/Rules re-execution when those layers are untouched, but it still requires the relevant static/component/build gates and rendered-browser visual acceptance when perceptibility, focus or cross-surface consistency is the changed contract.

AB-EV-043 demonstrates the latter model: the product-wide visual identity pass used full Vitest/lint/type/build/diff gates plus Test Lead desktop/mobile and Production visual validation, without inventing backend impact.

AB-EV-044 demonstrates a different high-risk path: bounded Storage Rules/quota and Emulator coverage were followed by real-Firebase Admin/runtime validation because Emulator green status could not prove the Google Cloud Storage OAuth/runtime boundary. Once an exact defect was isolated, later corrections used focused tests and small release gates rather than repeatedly rerunning the entire campaign.

AB-EV-045 applies the same checkpoint discipline to localisation infrastructure: routing architecture was isolated from visual translation, root dynamic-route ownership was audited before implementation, local gates were repeated under the official Node 22 runtime, a framework advisory was patched before publication, and Production smoke then proved locale routing without reopening unrelated Firebase regression.

AB-EV-046 extends that baseline with focused translation/selector coverage: message-catalog completeness, locale-variant assertions, desktop/mobile selector behaviour, cookie persistence, server-rendered document language, authenticated-Home visibility and Production mobile visual acceptance were validated without expanding localisation to unrelated application surfaces. The root-layout request API trade-off was explicitly reviewed and accepted as V1.0 technical debt rather than triggering a disproportionate route migration.

AB-EV-047 applies the same bounded-localisation discipline to authentication entry: `/login` remains unprefixed, locale continuity is carried explicitly from localized Home routes, Login resolves locale by query → cookie → browser → fallback, and auth behaviour is regression-tested separately from presentation. A pre-release review caught and corrected an unintended CTA-routing regression, reinforcing that localisation changes must preserve authentication-aware navigation semantics.

AB-EV-048 localizes the guarded Onboarding/profile-creation flow while keeping business validation and persistence locale-neutral. Shared username/social-link helpers retain existing Portuguese messages for legacy callers and gain additive stable codes only where needed; Onboarding maps those codes to locale-specific presentation. Automated profile creation remains Emulator-only, and Production sign-off separates server/routing evidence from local visual acceptance when no safe Production user state exists.

AB-EV-049 adds a geometry-based multilingual visual-regression pattern: when translated side controls have variable width, visually central controls are verified against the actual container center rather than inferred from flex distribution. The patch was scoped only to localized Home and validated both anonymously and authenticated in Emulator, then across all six Production locale routes.

AB-EV-050 extends bounded localisation testing to a stateful authentication journey: `/verify-email` and `mode=verifyEmail` are localized while the shared `resetPassword` branch remains Portuguese. Stable additive AuthContext error codes support localized presentation without replacing legacy Portuguese messages, and Production validation deliberately uses non-mutating invalid action codes while full verification/resend state transitions remain proven against Firebase Emulators.

AB-EV-051 applies the same bounded model to the authenticated dashboard. Locale changes affect only presentation; canonical country names/IDs, filter machine values, statistics, Wishlist settings/order, manual visit ranks, clear-map semantics and deep visit editors remain behaviourally stable. Stateful write/destructive proof stays in Firebase Emulators while Production sign-off uses non-destructive server/document-locale smoke and route-isolation checks.

AB-EV-052 extends localization into the high-risk deep country/visit editor while keeping status IDs, `RegisteredVisit` identity, memory/privacy rules, C42 visit names and C44 photo/quota semantics locale-neutral. The checkpoint combines additive stable error codes, focused component/domain tests, stateful Auth/Firestore/Storage Emulator regression for C35/C39/C40/C42/C44 and Test Lead rendered-browser acceptance, then limits Production validation to non-destructive route/isolation smoke because recreating deep travel/photo state in Production would add risk without increasing confidence proportionally.

AB-EV-053 applies the same bounded model to a shared achievement domain. Stable achievement IDs and canonical evaluator/metadata objects remain locale-neutral; `/badges` and `BadgeUnlockToast` resolve localized presentation by ID while the shared public Profile remains Portuguese. Focused tests prove catalog completeness, date localization, strict achievement-order assertions, reconquest/notification semantics and public-projection isolation. Stateful achievement unlock proof stays in the Firebase Emulators, while Production validation remains non-destructive and verifies route/document/isolation behaviour only.

AB-EV-055 extends the same bounded model to the canonical public Profile: a route-scoped provider/document locale, anonymous same-path language switching, locale-neutral public projection, reuse of C45H stable-ID achievement presentation and explicit owner/viewer isolation. The Test Lead then found two distinct Production Product Defects: the authenticated public Profile Header omitted the translated authenticated labels (AB-DEF-019), and classic-scrollbar width altered centred Header geometry between short loading and long content (AB-DEF-020). Each fix received a minimal commit, focused Emulator regression, exact READY deployment and real-browser Test Lead acceptance. Headless overlay-scrollbar runs are functional regression evidence, not proof of native pixel stability.

AB-EV-058 closes the planned localization sequence with authenticated language selection. The strategy deliberately separates control behavior, unsaved-data protection, final-file automation, responsive visual acceptance, exact-SHA deployment and Production human retest. AB-DEF-021 demonstrates why two controls that represent the same state must be tested bidirectionally: cookie-only reload was insufficient on a route-authoritative public Home. C45M also operationalised LL-66: agent work was bounded, stale checkpoints were invalidated explicitly, and the final hotfix was accepted only after an edit-free final-file Vitest/E2E freeze.

AB-EV-059 applies the same checkpoint-preservation discipline to the additive German extension. The existing six-locale architecture is carried forward; only German catalogue/resolver/oracle coverage, affected browser boundaries, the seven-locale responsive selector and final Node 22 build are requalified. The 768px overlap found after adding the seventh locale demonstrates why additive presentation scope can cross an existing responsive threshold without invalidating unrelated backend/Rules evidence. AB-EV-060 subsequently closes the final deep authenticated German visual acceptance.

AB-EV-065 then establishes the Node 24 / Next.js 16.3.6 technical RC anchor. Premium Hardening starts from that green checkpoint rather than repeating the whole earlier release campaign for every finding.

AB-EV-066 demonstrates bounded ownership hardening: Clear Map's protected-state mutation moves to the authenticated server/Admin transaction boundary while the historical user-visible reset and generation semantics remain stable. It also records two governance/evidence lessons separately from Product behaviour: a missed pre-publication independent-review gate that requires a post-publication compensating audit, and a post-publication UTF-8 test-oracle repair that changed tests only because runtime behaviour was already correct.

AB-EV-067 demonstrates the mature Premium workflow. PA-002 retained an explicit original acceptance matrix across several remediation cycles, froze exact candidate bytes, rejected stale/remapped oracles, compared lint warning identity rather than only the warning total, received independent Codex APPROVE before publication, and then proved approved working bytes → staged bytes → committed bytes → GitHub `main` → Vercel Production exact SHA. The underlying Product hardening separates logical photo removal/access/quota completion from deferred physical Storage cleanup and preserves concurrent edits by applying tombstone completion to current orchestrated state.

AB-EV-068 extends the same discipline to a security/concurrency boundary. PA-003 first audited route/input/auth/legacy-fallback behaviour, then implemented bounded slices with independent Codex review. F08 was reproduced as an authorisation-to-byte ABA race in the mutable slot namespace and closed by immutable per-photo object identity. S5-R01 proved that **presence versus validity** survives canonicalisation/persistence so malformed-present modern locators cannot silently downgrade into legacy compatibility. Final S6 qualification retained named PA2/PA3 oracles, cross-service Rules evidence, exact final-byte integrity and post-publication non-destructive smoke against the exact Vercel deployment.

This reduces wasted execution time while retaining traceable risk-based coverage.

### 6.2 Fail-fast classification

When a test fails, the first step is classification:

- **product defect** — implementation/behaviour violates the accepted rule;
- **test defect/stale expectation** — automation no longer represents the accepted rule or produces false evidence;
- **test-data/fixture defect** — prepared state violates Product invariants even though valid Product state behaves correctly;
- **environment/infrastructure issue** — execution cannot prove product behaviour;
- **release-control block** — tooling/security/process prevents publication before execution but source/product behaviour is not shown to be wrong;
- **governance/process deviation** — the resulting Product may be technically valid but an approved actor/review/publication control was violated; preserve chronology and correct the control rather than inventing a Product Defect;
- **inconclusive** — evidence is insufficient.

Test defects, fixture defects, process deviations and release-control blocks must not be presented as product defects, and product defects must not be hidden by weakening automation or Product assertions.

AB-EV-033 demonstrates this model: three real product defects were assigned AB-DEF IDs, while stale selectors, unauthenticated test reads and obsolete protocol/offline assumptions were treated as test-maintenance debt.

AB-EV-052 adds a fixture-specific example. A direct C45G visual-QA seed created travel state that earned achievements but bypassed the normal achievement-metadata reconciliation lifecycle. The development invariant correctly rejected the incomplete state on Badges/public Profile. Reconciliation of Emulator data alone restored all dependent surfaces with no Product change, confirming a QA test-data defect rather than a Product regression.

AB-EV-053 adds a release-control example. The first C45H push attempt was blocked by an automated security/release review because explicit authorization to publish `main` and trigger Production was not recognised. No push/deployment occurred until the Test Lead authorized it explicitly. The same approved commit was then pushed normally and deployed successfully; no Product defect was created.

AB-EV-066 adds a governance example: PA-001 was published before a planned independent architecture review. The published Product remains functionally accepted, but the chronology is preserved and a post-publication compensating architecture audit remains mandatory before final Premium closure.

---

## 7. Test design techniques

AtlasBadge uses a combination of:

- positive testing;
- negative/authorisation testing;
- boundary-value analysis;
- state-transition/combination testing;
- interruption/reload/recovery testing;
- concurrency/repeated-action testing;
- exploratory testing;
- error guessing based on previous defects and architecture.

Particular attention is given to asynchronous writes, stale state, repeated clicks, destructive actions, public/private boundaries, status combinations, derived counters and authority-to-resource races.

For Premium Hardening, named acceptance-oracle inventories are retained when a finding has multiple race/lifecycle cases. A green aggregate test count cannot replace a missing original oracle. Concurrency tests must place deterministic barriers at the semantic boundary being claimed; PA3-15 specifically requires the pause after authentication/logical authorisation and physical-key selection but before byte consumption.

---

## 8. Functional and regression coverage

### 8.1 Feature validation

Every implementation/correction receives direct validation against the accepted rule and affected dependencies.

A status or Wishlist change may require coverage of UI state, Firestore private state, public projection, reload persistence, counters, visit order independence, achievements, Profile presentation and privacy.

A visit-photo lifecycle change may additionally require exact allocation identity, release idempotency/staleness, durable recovery after reload, current owner/public reference validation, full-quota recovery, physical-object/logical-state separation, immutable byte identity, strict absent/present-valid/present-invalid locator handling, request/body/query contracts and revocation/account-lifecycle gates.

### 8.2 Core regression

Core regression covers authentication, authenticated navigation, map access, travel-status persistence, visits/memories, personal Profile, public Profile, privacy boundaries and high-value lifecycle operations.

### 8.3 Impact-based expansion

Regression expands when a change reaches persistence, Rules, cache/listener behaviour, calculations, geography, achievements, privacy, authentication, responsive UI or account deletion.

### 8.4 Retesting

A correction is complete only when:

- the original failure is no longer reproducible;
- the expected result is achieved;
- affected regression passes;
- the evidence is credible;
- remaining limitations are understood/accepted.

---

## 9. Smoke and Production validation

Most functional validation is completed before Production.

Production validation is controlled and uses disposable QA data only. It focuses on behaviours whose release confidence depends on the deployed frontend, Firebase configuration/Rules or real integration.

For a release that changes frontend and/or Firebase Rules, the process is:

```text
Git push
→ verify Vercel Production READY at expected SHA
→ deploy approved Firestore/Storage Rules-only change where separately required
→ confirm source/Rules parity where applicable
→ run focused Production validation when proportionate
```

A READY frontend deployment is not enough when the security-rule layer changed.

AB-EV-033 followed this exact gate before validating Wishlist/private-public projection in Production.

Presentation-only/authenticated-state checkpoints may close as **Production technical PASS + local/Emulator visual PASS** when manufacturing Production state would add avoidable risk and server/routing/isolation behaviour can be verified safely without mutation. C45D–C45J use this evidence distinction where applicable; C45I and C45J/FIX1/FIX2 additionally received final Test Lead Production visual approval without exercising destructive account or private-data operations.

For PA-001/PA-002 publication, exact Git/Vercel SHA metadata proved deployment identity after comprehensive local/Emulator qualification; destructive/authenticated Production mutation was deliberately not repeated merely to prove the same lifecycle against real user data. PA-003 adds a separate post-publication gate: the exact approved commit→`origin/main`→Vercel deployment→Production alias chain was verified, localized page/static assets and safe rejected API paths were smoked, bounded runtime logs were checked and zero intentional Firebase mutation occurred. This is publication/runtime evidence, not a Production race-test claim.

---

## 10. Automation strategy

### 10.1 Versioned automated coverage

Playwright, Vitest and Firebase Rules tests are permanent, versioned quality assets.

The browser E2E suite includes high-value flows such as:

- authentication/navigation;
- status persistence and rapid mutation;
- Wishlist membership/privacy/order;
- public-profile source/privacy;
- geographic counters/map interaction;
- manual visit order;
- responsive behaviour;
- achievement unlock/notification/localization where affected;
- visit-photo owner/public access and active-quota affected paths;
- Clear Map/account lifecycle affected areas.

### 10.2 Firebase Emulator isolation

Normal browser E2E persistence tests use an isolated Firebase Emulator environment rather than real Firebase.

The AB-EV-033 baseline uses:

```text
Application: 127.0.0.1:3100
Auth Emulator: 9099
Firestore Emulator: 8080
Project: demo-atlasbadge-web
```

Later photo/stateful checkpoints also start the Storage Emulator when required.

The configuration forces Emulator targets, uses a dedicated E2E Next.js runtime/build, excludes Firebase-real Production specs and fails fast if real Firebase traffic is detected.

Validated runs recorded `realFirebaseRequests=0`. PA-002/PA-003 final C44 affected regression again recorded `realFirebaseRequests=0`; this is Emulator evidence and must not be misreported as a Production network-count claim.

When one Rules engine depends on another service's state, the dedicated Rules suite is supplemented by a combined-service smoke under the actual Product Emulator project. PA-003 used this to validate Storage CREATE against Firestore account/allocation state under `demo-atlasbadge-web` rather than assuming the dedicated Rules project proved cross-service configuration parity.

### 10.3 Controlled Production automation

Production-specific browser validation is separate from Emulator regression.

It may use controlled QA accounts/data only, must be explicitly approved, and must not silently expand into destructive operations beyond the authorised scope.

Temporary Production-only specs/artefacts are removed after use unless intentionally promoted to permanent regression assets.

### 10.4 Automation quality rules

Tests must avoid false confidence from:

- arbitrary `.first()` / `nth()` selectors when strict semantic targeting is possible;
- assertions coupled to implementation-specific Firestore wire endpoints instead of functional behaviour;
- unauthenticated private reads used as if they were valid owner snapshots;
- offline toggling used as a substitute for semantics already covered deterministically elsewhere;
- hardcoded app ports that bypass the isolated test runtime;
- stale product counts/labels after an approved rule change;
- directly seeded derived achievement state that bypasses required reconciliation;
- replacing a named acceptance criterion with a superficially similar weaker test;
- relying on an implementation helper test when the real Product path does not call that helper;
- treating malformed-present security identity as equivalent to genuine field absence;
- pre-buffering bytes before a race barrier and then claiming the test proves the live authority-to-stream boundary.

---

## 11. Non-functional quality coverage

### 11.1 Security and privacy

Current coverage includes authenticated-route protection, revocation-aware protected photo tokens, account-state gates, Firestore/Storage Rules, account isolation, private/public profile transitions, sanitised `publicProfiles` projection, public Wishlist privacy, direct identifier/URL attempts and account deletion.

AB-EV-033 validated zero private viewer reads in the focused Production Profile flow and absence of forbidden private place fields in the public projection. AB-EV-053 confirms that localized achievement presentation does not expand the `{ unlockedAt, sequence }` public achievement metadata contract or make the public Profile depend on the authenticated locale provider. AB-EV-067 adds current-reference validation for visit-photo reads so retained physical objects cannot bypass logical owner/public removal. AB-EV-068 adds immutable per-photo object identity for new uploads, write-once/create-only Storage semantics, fail-closed present-invalid locator handling, cross-account/current-reference isolation and exact-byte race evidence.

This is not an independent penetration test or security certification.

### 11.2 Performance and stability

Performance is assessed through user-facing responsiveness, repeated interaction, map behaviour and targeted responsive/stability tests.

No formal load/SLA baseline exists yet. Quantitative performance targets remain a planned improvement and PA-011 requires a measured baseline before optimisation.

### 11.3 Compatibility

Current evidence includes Microsoft Edge on Windows, Chrome/Android and automated desktop/mobile viewports. Broader Firefox/Safari/iPhone/macOS coverage remains an assessment gap.

### 11.4 Accessibility

A scoped WCAG 2.2 AA technical baseline has been executed (AB-EV-017), with later interaction regression extending keyboard/control/dialog/responsive coverage.

C45H adds localized loading/ARIA presentation, toast keyboard activation, close-control accessible naming and stable achievement anchors to the affected regression baseline. C45N extends localized selector accessible-current-state coverage to German/de-DE without changing the broader certification boundary.

This does **not** constitute formal accessibility certification or complete assistive-technology coverage.

---

## 12. Test data approach

Testing uses controlled QA accounts and disposable data.

Emulator tests use isolated demonstration-project data. Production tests use only approved QA accounts and must not modify non-QA accounts.

Rich manual/visual fixtures that induce derived Product state must be prepared through the normal Product/domain lifecycle where practical or explicitly run the same reconciliation helpers before handoff. Fixture readiness includes checking dependent surfaces whose invariants consume that derived state; a fixture shortcut must not be used as justification to weaken a Product assertion.

Credentials, tokens, private account details and sensitive payloads are not committed to the public QA portfolio.

---

## 13. Test evidence and defect handling

Evidence depth is proportional to risk, complexity, repetition need, release importance and investigation difficulty.

Evidence may include execution summaries, command output, screenshots, structured narratives, commits, deployments, test counts, exact candidate hashes and Test Lead decisions.

A material defect record should preserve:

1. expected behaviour;
2. observed behaviour;
3. risk/severity/priority;
4. root cause where confirmed;
5. correction;
6. direct retest;
7. affected-area regression;
8. release/Production result;
9. residual risk.

The Evidence Register provides the public traceability index.

### 13.1 Documentation structure governance

The `docs/` directory contains the fixed living control set `01`–`10`. A feature/change increment does not receive a new standalone traceability document under `docs/`. Its durable product/risk/scope/plan/lesson changes are merged into the existing control documents, while selected execution/defect/release traceability is published under `evidence/`.

A new file under `docs/` requires explicit Test Lead approval.

---

## 14. High-level approval conditions

An implementation is approved only when:

- expected behaviour is validated;
- relevant negative/exploratory scenarios pass;
- no release-blocking defect remains;
- required corrections pass direct retest;
- required affected regression passes;
- repository/test-quality blockers are resolved;
- required independent review has completed when it is part of the approved gate;
- the final approved artefact is the same artefact later committed/published;
- residual risk is reviewed by the Test Lead.

After deployment, release sign-off additionally requires expected-version verification and Production validation proportional to the change.

---

## 15. Release decision and residual risk

Release outcomes are:

| Outcome | Meaning |
|---|---|
| Approved | Evidence is satisfactory and residual risk acceptable. |
| Approved with accepted risk | A known limitation remains but is understood/accepted. |
| Blocked | Evidence shows unacceptable risk or insufficient confidence. |

Privacy exposure, unauthorised private reads, material data corruption/loss or deployment-parity failure are release-blocking until corrected or explicitly resolved by an appropriate control.

The final release decision belongs to the Test Lead.

---

## 16. Current constraints and planned improvements

### Confirmed constraints

- Browser/device coverage is not comprehensive.
- There is no independent security/penetration test.
- Formal multi-user load/stress testing has not been performed.
- Quantitative performance targets are not established.
- CI execution policy for the versioned regression suite can be expanded further.
- Formal accessibility certification/native assistive-technology coverage is not claimed.
- PA-001 AC-017 full browser UI → API → Admin Emulator reload proof remains carried to PA-007 / Final Premium RC.
- The compensating post-publication PA-001 architecture audit remains a governance requirement before final Premium Hardening closure.
- Physical visit-photo Storage garbage collection is intentionally deferred; PA-002/PA-003 logical access, quota and immutable-byte correctness do not depend on immediate deletion.
- The existing Rules alias's undeclared/cached `tsx` dependency remains PA-006A deterministic-tooling scope.
- PA-008 retains account-deletion origin-allowlist hardening.
- The legacy Clear Map fixture/test suite debt remains distinct from the authoritative passing server/client regression and is not a PA-003 failure.

### Planned improvements / Premium sequence

1. continue remaining Premium Hardening in Test Lead-approved order, including PA-006A deterministic tooling, PA-007/AC-017 server-boundary/project-safety closure and PA-008 account-deletion origin policy;
2. complete the PA-001 compensating post-publication architecture audit before final Premium Hardening closure;
3. establish measurable performance baselines before PA-011 optimisation decisions;
4. expand browser/device coverage where risk justifies it;
5. continue accessibility validation with assistive-technology evidence;
6. maintain Emulator fail-fast protections as Firebase/Next.js configuration changes;
7. keep checkpointed regression, immutable resource-identity checks and exact-byte approval traceable so saved execution time never becomes hidden coverage loss.

---

## 17. Strategy review triggers

Review this strategy when authentication, travel-status/Wishlist rules, persistence/concurrency, Firestore/Storage Rules/data model, public projection/privacy, account deletion, visit-photo lifecycle/Admin boundaries or physical-object identity, geographic catalogue, achievements/localized presentation, Emulator architecture, CI, supported browsers/devices, accessibility targets or release process change materially.

---

## 18. Related portfolio documents

- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `docs/10-lessons-learned.md`
- `GOVERNANCE-ERRATA.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/regression/ab-ev-052-c45g-deep-country-visit-editor-localization.md`
- `evidence/v1.0/regression/ab-ev-053-c45h-badges-achievements-localization.md`
- `evidence/v1.0/regression/ab-ev-054-c45i-profile-edit-localization.md`
- `evidence/v1.0/regression/ab-ev-055-c45j-public-profile-localization-and-fixes.md`
- `evidence/v1.0/regression/ab-ev-056-c45k-geographic-localization.md`
- `evidence/v1.0/regression/ab-ev-057-c45l-password-reset-action-localization.md`
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

## Current Premium Hardening strategy checkpoint

The current Product publication anchor is `7616e8ca455f1d2e72a67e4dd839c9c302f98656`, deployed automatically to Vercel Production as `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` (`READY`, production, exact SHA) and accepted after bounded post-publication verification.

PA-001 and its UTF-8 test-oracle repair are published under AB-EV-066; AC-017 and the compensating post-publication architecture audit remain explicit carry-forwards. PA-002 is closed under AB-EV-067 with all original acceptance criteria independently PROVEN before publication. PA-003 is closed under AB-EV-068 with PA3-01 through PA3-15 accepted, F08/PA3-15 immutable-byte race evidence, 258/258 Rules, full S6 qualification and safe Production verification. Remaining Premium work is sequenced separately. This checkpoint is not the final V1.0 Go-Live decision.