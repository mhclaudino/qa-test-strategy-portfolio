# AtlasBadge Test Strategy

## 1. Document purpose

This document defines the overall testing strategy for AtlasBadge and how quality is directed and governed during V1.0 development.

It covers risk prioritisation, test design, regression, evidence, AI-assisted execution, automation, release approval and residual risk.

> **Document status:** Completed and maintained through AB-EV-051. The current strategy includes versioned Playwright/Firebase Emulator regression, checkpointed incremental validation, controlled real-backend/Production validation, explicit environment/runtime parity checks, real-browser acceptance and fixed living-document governance.

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

Its main quality challenges include asynchronous persistence, optimistic state, concurrency, interdependent statuses, detailed visits/memories, Wishlist privacy/order, geographic catalogue integrity, public/private profile projection, achievements, responsive map/UI behaviour and account lifecycle.

The strategy should be read together with the [Product Overview](01-product-overview.md) and [Quality Risk Analysis](02-quality-risk-analysis.md).

---

## 4. Quality ownership and governance

AI-assisted development/testing tools may contribute implementation summaries, technical findings, scripted validation, defect investigation, test execution and evidence.

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
Test Lead commit/push approval
        ↓
Deployment parity verification
        ↓
Focused Production validation
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

This reduces wasted execution time while retaining traceable risk-based coverage.

### 6.2 Fail-fast classification

When a test fails, the first step is classification:

- **product defect** — implementation/behaviour violates the accepted rule;
- **test defect/stale expectation** — automation no longer represents the accepted rule or produces false evidence;
- **environment/infrastructure issue** — execution cannot prove product behaviour;
- **inconclusive** — evidence is insufficient.

Test defects must not be presented as product defects, and product defects must not be hidden by weakening automation.

AB-EV-033 demonstrates this model: three real product defects were assigned AB-DEF IDs, while stale selectors, unauthenticated test reads and obsolete protocol/offline assumptions were treated as test-maintenance debt.

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

Particular attention is given to asynchronous writes, stale state, repeated clicks, destructive actions, public/private boundaries, status combinations and derived counters.

---

## 8. Functional and regression coverage

### 8.1 Feature validation

Every implementation/correction receives direct validation against the accepted rule and affected dependencies.

A status or Wishlist change may require coverage of UI state, Firestore private state, public projection, reload persistence, counters, visit order independence, achievements, Profile presentation and privacy.

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

For a release that changes both frontend and Firestore Rules, the process is:

```text
Git push
→ verify Vercel Production READY at expected SHA
→ deploy approved Firestore Rules-only change
→ confirm frontend/Rules parity
→ run focused Production validation
```

A READY frontend deployment is not enough when the security-rule layer changed.

AB-EV-033 followed this exact gate before validating Wishlist/private-public projection in Production.

---

## 10. Automation strategy

### 10.1 Versioned automated coverage

Playwright, Vitest and Firebase Rules tests are now permanent, versioned quality assets.

The browser E2E suite includes high-value flows such as:

- authentication/navigation;
- status persistence and rapid mutation;
- Wishlist membership/privacy/order;
- public-profile source/privacy;
- geographic counters/map interaction;
- manual visit order;
- responsive behaviour;
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

The configuration forces Emulator targets, uses a dedicated E2E Next.js runtime/build, excludes Firebase-real Production specs and fails fast if real Firebase traffic is detected.

Validated runs recorded `realFirebaseRequests=0`.

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
- stale product counts/labels after an approved rule change.

---

## 11. Non-functional quality coverage

### 11.1 Security and privacy

Current coverage includes authenticated-route protection, Firestore Rules, account isolation, private/public profile transitions, sanitised `publicProfiles` projection, public Wishlist privacy, direct identifier/URL attempts and account deletion.

AB-EV-033 validated zero private viewer reads in the focused Production Profile flow and absence of forbidden private place fields in the public projection.

This is not an independent penetration test or security certification.

### 11.2 Performance and stability

Performance is assessed through user-facing responsiveness, repeated interaction, map behaviour and targeted responsive/stability tests.

No formal load/SLA baseline exists yet. Quantitative performance targets remain a planned improvement.

### 11.3 Compatibility

Current evidence includes Microsoft Edge on Windows, Chrome/Android and automated desktop/mobile viewports. Broader Firefox/Safari/iPhone/macOS coverage remains an assessment gap.

### 11.4 Accessibility

A scoped WCAG 2.2 AA technical baseline has been executed (AB-EV-017), with later interaction regression extending keyboard/control/dialog/responsive coverage.

This does **not** constitute formal accessibility certification or complete assistive-technology coverage.

---

## 12. Test data approach

Testing uses controlled QA accounts and disposable data.

Emulator tests use isolated demonstration-project data. Production tests use only approved QA accounts and must not modify non-QA accounts.

Credentials, tokens, private account details and sensitive payloads are not committed to the public QA portfolio.

---

## 13. Test evidence and defect handling

Evidence depth is proportional to risk, complexity, repetition need, release importance and investigation difficulty.

Evidence may include execution summaries, command output, screenshots, structured narratives, commits, deployments, test counts and Test Lead decisions.

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

### Planned improvements

1. expand browser/device coverage;
2. add/strengthen CI execution for stable gates;
3. establish measurable performance baselines;
4. continue accessibility validation with assistive-technology evidence;
5. maintain the Emulator fail-fast protections as Firebase/Next.js configuration changes;
6. keep checkpointed regression traceable so saved execution time never becomes hidden coverage loss.

---

## 17. Strategy review triggers

Review this strategy when authentication, travel-status/Wishlist rules, persistence/concurrency, Firestore Rules/data model, public projection/privacy, account deletion, geographic catalogue, achievements, Emulator architecture, CI, supported browsers/devices, accessibility targets or release process change materially.

---

## 18. Related portfolio documents

- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `docs/10-lessons-learned.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
