# AtlasBadge V1.0 System Test Plan

**Document status:** Initial completed planning version
**Execution status:** Not started
**Product:** AtlasBadge
**Target release:** V1.0
**Document owner:** Test Lead/Product Owner
**Last updated:** 22 July 2026

---

## 1. Document purpose

This plan defines how the AtlasBadge V1.0 system-test cycle will be prepared, executed, controlled, evidenced and concluded.

It converts the existing product, risk, strategy, scope, environment, defect and reporting decisions into a concrete release-validation sequence. It does not replace those documents, contain every detailed test case or claim that planned tests have already passed.

The plan remains active until the approved Release Candidate has completed final regression, deployment, production smoke, final data reset, clean-start validation and Test Lead release assessment.

---

## 2. Test-plan status and control

This is the initial completed planning baseline. AtlasBadge remains under active development, so the plan is change-controlled rather than frozen prematurely.

A material update is required when:

* mandatory V1.0 scope changes;
* a risk changes state or priority;
* a material defect changes required coverage;
* an environment, browser, device or language decision changes;
* a new build invalidates previous results;
* the Release Candidate changes;
* an exception affects deployment or release.

Each material update must identify the reason, affected risks and tests, results that remain valid, results that require re-execution and the Test Lead decision.

### 2.1 Documentation alignments required

Before the final V1.0 cycle:

1. the language baseline in the preceding scope and environment documents must be aligned to `pt-BR` and `en-GB`;
2. QR-02 must be changed from **Current gap** to **Regression risk**, reflecting the validated V1.0 data model and permanent regression coverage;
3. QR-01 must be reassessed against the current persistence rollback and error-handling controls; it should remain a current gap unless evidence proves that the complete risk statement has been mitigated across applicable write flows;
4. the Product Overview status should be aligned with its Completed portfolio status.

This plan records the approved execution decisions without silently rewriting the preceding documents.

---

## 3. Relationship with the QA portfolio

This plan applies:

* [Product Overview](01-product-overview.md) for product context and journeys;
* [Quality Risk Analysis](02-quality-risk-analysis.md) for risk priorities and states;
* [Test Strategy](03-test-strategy.md) for quality governance and testing principles;
* [Test Scope](04-test-scope.md) for included, conditional, deferred and excluded areas;
* [Entry and Exit Criteria](05-entry-exit-criteria.md) for progression and blocking rules;
* [Test Environments](06-test-environments.md) for execution contexts and limitations;
* [Defect Management](07-defect-management.md) for defect control;
* [Metrics and Reporting](08-metrics-and-reporting.md) for quality gates, readiness and reporting;
* [V1.0 Test Evidence](../evidence/v1.0/README.md) for evidence organisation and publication controls.

Where documents disagree, the inconsistency must be recorded and resolved explicitly before the affected release decision.

---

## 4. Product and release context

AtlasBadge is a published web application being prepared for its first official V1.0 release.

The system includes authentication, account and profile management, an interactive travel map, country and territory selection, travel statuses, visits, memories, notes, counters, continent progress, badges, achievements, public profiles, account deletion, localisation, responsiveness, privacy and security controls.

The integrated test item includes the browser application and its interaction with Firebase Authentication, Cloud Firestore, Firebase Storage, Firebase Admin SDK operations, local cache, Vercel deployment, geographic data and localisation resources.

Previous incremental testing is useful input but does not replace final integrated release coverage.

---

## 5. Test items and version identification

### 5.1 Development baseline

Every material execution record must identify, where applicable:

* branch;
* full Git commit SHA;
* local or deployed application context;
* Vercel deployment corresponding to the commit;
* Firebase project or emulator identifier;
* relevant configuration;
* browser and version;
* device, resolution and orientation;
* language;
* execution date;
* test-data state.

The normal branch is `main`, but the branch name alone is not sufficient because its contents change.

### 5.2 Release Candidate

A release candidate will use a readable identifier such as:

```text
V1.0-RC1
```

The identifier must map to one full commit SHA, one approved deployment, the applicable Firebase configuration, the approved language scope and the required evidence set.

A changed source revision normally creates `RC2`, `RC3` and so on.

### 5.3 Version-to-deployment control

Before production validation, the deployment checkpoint must compare:

* tested commit;
* Test Lead-approved commit;
* deployed commit or Vercel-linked source revision;
* version-match result;
* any approved exception.

A technically successful Vercel deployment does not itself prove product approval.

### 5.4 Changes after execution begins

A change requires proportional rebaseline:

1. identify the new commit or deployment;
2. analyse affected features, integrations and risks;
3. identify which previous results remain valid;
4. invalidate results that may be affected;
5. perform direct validation;
6. perform retest when correcting a defect;
7. perform impact-based regression;
8. update traceability, evidence and checkpoints;
9. decide whether a new Release Candidate is required.

Unaffected results may be carried forward only when the impact analysis supports that decision.

---

## 6. Test objectives

The V1.0 cycle will provide evidence to determine whether AtlasBadge can be released safely within the declared scope and limitations.

The objectives are to:

* validate the principal registration, authentication, session and account-lifecycle journeys;
* confirm that identity and owned data remain associated with the correct user;
* validate all approved status rules, combinations and automatic transitions;
* verify reliable persistence, restoration and trustworthy failed-write behaviour;
* protect visits, memories, notes and other user-created data from unintended loss or corruption;
* confirm map, catalogue, counters, continents, badges and achievements remain consistent;
* confirm public profiles are read-only and expose only intended information;
* validate complete account deletion and absence of residual public or stored data;
* validate essential behaviour in `pt-BR` and `en-GB`;
* assess the declared desktop, mobile and conditional tablet sample;
* assess essential accessibility against the WCAG 2.2 Level AA reference without claiming formal conformity;
* assess performance and perceived fluency without inventing numerical service levels;
* confirm that the approved and deployed versions match;
* confirm the final empty production state and clean-start journey;
* support a defensible Test Lead release recommendation.

The objective is meaningful release confidence, not exhaustive proof of every theoretical condition.

---

## 7. Scope under execution

### 7.1 Mandatory critical path

Release-blocking coverage includes:

* application access;
* registration, authentication, verification, recovery, linking, session and logout;
* account identity, ownership and protected routes;
* status selection, compatibility, persistence and failure behaviour;
* visits, memories and notes;
* privacy and user isolation;
* personal and public profiles;
* account deletion;
* production reset and clean start.

### 7.2 High-risk coverage

High-risk coverage includes:

* map, search and geographical catalogue;
* country, territory, visit and continent calculations;
* badges and achievements;
* local cache and Firestore consistency;
* Firebase Rules and Storage ownership;
* localisation;
* status-selection performance;
* responsiveness;
* essential accessibility;
* required browser and device coverage;
* the planned permanent Playwright suite and its safety controls.

### 7.3 Regression coverage

Permanent regression includes:

* the primary status-persistence failure;
* failed-write handling and rollback;
* required place identity in persisted payloads;
* QR-02 visit and memory preservation;
* status normalisation;
* account deletion;
* public/private profile boundaries;
* counters and achievements;
* previous material defects selected by impact.

### 7.4 Conditional coverage

Conditional coverage includes:

* Redmi Pad 2 checks when available;
* Vercel Preview only if formally adopted and assessed;
* per-memory privacy only if implemented for V1.0;
* production-only scenarios that cannot be proven adequately elsewhere;
* new coverage required by scope changes, defects or risks.

### 7.5 Deferred or excluded

Deferred or excluded from the V1.0 release requirement:

* photo per visit;
* generated Instagram Story;
* per-memory privacy when not completed before the Release Candidate;
* future badges beyond the final V1.0 set;
* broad Safari, Firefox, iPhone, iPad and macOS validation;
* formal accessibility certification;
* professional penetration testing;
* formal load and stress testing;
* invented quantitative performance targets;
* Playwright execution in CI;
* administrative functions not present in the product;
* real customer data;
* translation of user-authored content.

---

## 8. Risk-based prioritisation

Coverage is organised into five execution layers:

1. **Mandatory critical path** — essential journeys, data, privacy and destructive operations.
2. **High-risk coverage** — integrations, calculations, geographic integrity, profiles, localisation and performance-sensitive areas.
3. **Regression coverage** — previously corrected or disproved risks requiring permanent protection.
4. **Exploratory and conditional coverage** — unusual states, interruptions, secondary devices and changing functionality.
5. **Lower-priority coverage** — isolated visual or textual refinements with limited consequence.

A lower-priority activity must not displace unresolved higher-priority risk.

### 8.1 Treatment by risk state

| Risk state         | Execution treatment                                                                                 |
| ------------------ | --------------------------------------------------------------------------------------------------- |
| Current gap        | Correction, containment, explicit scope decision or accepted limitation is required.                |
| Regression risk    | Repeatable coverage must be linked to the release baseline.                                         |
| Assessment gap     | Evidence must be generated or the characteristic remains explicitly unproven.                       |
| Future risk        | Enters execution only if the related feature enters V1.0 scope.                                     |
| Accepted behaviour | Intended behaviour and its consequences must be validated.                                          |
| Deferred           | Reason, residual risk, target or review trigger must be recorded.                                   |
| Open question      | Investigation may continue, but approval of the affected behaviour remains blocked until clarified. |

A quality risk does not automatically require its own test case. One asset may cover several related risks.

---

## 9. Test approach

The cycle combines:

* incremental validation while development remains active;
* positive, negative and boundary testing;
* state-transition and combination testing;
* interruption and recovery testing;
* exploratory testing and error guessing;
* manual functional and visual validation;
* automated tests with assertions;
* automated technical checks;
* tool-assisted inspection;
* defect retesting;
* impact-based regression;
* final integrated regression;
* controlled production smoke;
* final reset and clean-start validation.

Testing depth, evidence and regression reach are proportional to impact, likelihood, affected dependencies and the consequence of an incorrect release decision.

---

## 10. Test cycles and execution sequence

| Phase | Purpose                                               | Main gate or output                                                             |
| ----: | ----------------------------------------------------- | ------------------------------------------------------------------------------- |
|     1 | Plan, scope, risk and environment readiness           | Pre-test decision                                                               |
|     2 | Targeted validation of remaining V1.0 development     | Change-level decision                                                           |
|     3 | Integrated system smoke                               | Stable baseline for wider execution                                             |
|     4 | Risk-based functional execution                       | Critical and High results                                                       |
|     5 | Integration and data-integrity validation             | Persistence, ownership and calculation evidence                                 |
|     6 | Non-functional validation                             | Compatibility, responsiveness, accessibility, localisation and fluency evidence |
|     7 | Exploratory execution                                 | Findings, risks, defects and questions                                          |
|     8 | Defect correction and retesting                       | Retest decision                                                                 |
|     9 | Impact-based and mandatory regression                 | Regression checkpoint                                                           |
|    10 | Release Candidate formation and freeze                | `V1.0-RC#` authorised                                                           |
|    11 | Final regression against the candidate                | Pre-deployment recommendation                                                   |
|    12 | Production deployment                                 | Version match                                                                   |
|    13 | Production smoke                                      | Post-deployment decision                                                        |
|    14 | Final production reset                                | Empty-state evidence                                                            |
|    15 | Clean-start validation and validation-account removal | Post-reset gate                                                                 |
|    16 | Release assessment                                    | Test Lead recommendation                                                        |

A phase may repeat after a correction, new baseline, environment failure, new material risk or insufficient evidence.

---

## 11. Entry, suspension, resumption and completion

### 11.1 Entry

System execution may begin when:

* the baseline and intended scope are identifiable;
* expected behaviour is sufficiently clear;
* the application is stable enough for meaningful testing;
* applicable technical checks have completed;
* required environments, accounts and data are available;
* material limitations are disclosed;
* the Test Lead accepts entry.

### 11.2 Suspension

The affected phase must be suspended or restricted when:

* a P0 blocks the primary journey;
* a Critical privacy, security or authorisation failure exists;
* material data loss, corruption or cross-user contamination is observed;
* authentication or an essential environment is unavailable;
* the wrong build is under test;
* evidence is unreliable;
* a repeated pipeline failure prevents trustworthy assessment;
* a defect invalidates dependent results;
* expected behaviour is materially undefined;
* a destructive scenario cannot be executed safely.

Independent unaffected testing may continue when isolation is clear and the Test Lead approves it.

### 11.3 Resumption

Resumption requires:

* correction, containment or reliable isolation of the blocking condition;
* identified new baseline;
* stable environment and suitable data;
* identification of invalidated results;
* defined retest and regression;
* Test Lead acceptance.

### 11.4 Completion

The V1.0 cycle is sufficiently complete when:

* mandatory Critical and High coverage has received the required depth;
* the final Release Candidate is identified;
* required manual, exploratory, automated and technical activities have completed;
* required retests and regression have completed;
* production smoke, reset and clean-start validation have completed;
* evidence is traceable;
* defects, exceptions, limitations and residual risks are understood;
* no prohibited risk remains;
* the Test Lead can issue a defensible recommendation.

A pass percentage alone does not establish completion.

---

## 12. Environment allocation

| Context                             | Main use                                                                                | Principal restriction                            |
| ----------------------------------- | --------------------------------------------------------------------------------------- | ------------------------------------------------ |
| Local Next.js application           | Functional validation, exploration, retest, quick regression and localisation           | Currently uses the real Firebase project         |
| Firebase Emulator Suite             | Rules, unauthorised contexts, destructive backend checks and repeatable technical tests | Not a complete staging environment               |
| Real Firebase project `atlas-badge` | Controlled integration before launch                                                    | Shared by local and production                   |
| Vercel Preview                      | Only if formally assessed later                                                         | Not currently a confirmed QA environment         |
| Vercel Production                   | Deployment verification, smoke, public profile and approved production-dependent checks | Not the primary functional test environment      |
| Clean production state              | Final reset and clean-start validation                                                  | Must be protected from uncontrolled local writes |

Destructive and unsafe negative scenarios should use the Emulator wherever practical.

A production-dependent destructive scenario requires controlled disposable data, defined cleanup, no real user data and Test Lead approval.

After the final reset:

* local persistence testing against the real project is prohibited;
* emulator-mode automation must fail rather than fall back silently;
* technical protection must distinguish local, emulator and production modes.

---

## 13. Browser and device coverage

| Coverage                           | Configuration                                               | V1.0 use                                          |
| ---------------------------------- | ----------------------------------------------------------- | ------------------------------------------------- |
| Primary desktop                    | Windows, Microsoft Edge 150+, physical `1600 × 900`         | Broad manual execution and accessibility baseline |
| Mandatory secondary desktop        | Windows, Google Chrome 150+                                 | Required compatibility and final smoke            |
| Primary mobile                     | Samsung Galaxy S20 FE, Chrome 150+, portrait and landscape  | Mobile functional, responsive and smoke coverage  |
| Conditional tablet                 | Redmi Pad 2, Chrome 150+ where available, both orientations | Additional non-blocking coverage                  |
| Automated primary                  | Chromium through Playwright                                 | Repeatable coverage after safe implementation     |
| Selected automated release browser | Installed Edge or Chrome                                    | Selected release execution                        |

Exact browser versions should be captured in environment evidence.

The declared sample does not establish comprehensive Safari, Firefox, iPhone, iPad, macOS, multi-device, multi-resolution or older-browser compatibility. No universal compatibility claim may be made.

---

## 14. Language coverage

The approved V1.0 language scope is:

* Brazilian Portuguese (`pt-BR`) — primary language and fallback;
* British English (`en-GB`) — secondary language.

Coverage includes applicable navigation, authentication, statuses, visits, memories, notes, profiles, public pages, counters, badges, errors, confirmations, dynamic text, dates, plurals, accessibility labels, truncation and layout.

Testing must include:

* automatic browser or device selection;
* supported and unsupported preferences;
* fallback to `pt-BR`;
* manual selection;
* persistence after refresh and later sessions;
* missing-translation fallback;
* mixed-language detection;
* accents, special characters and long text.

Broad functional regression is performed in `pt-BR`. Essential journeys and localisation-specific checks are performed in `en-GB`. Final production smoke includes both languages, without multiplying both across every browser and device combination.

User-authored content is not automatically translated.

---

## 15. Test-data strategy

Test data must be artificial or personally controlled, disposable, scenario-oriented, sanitised before publication and removed when no longer required.

Representative states include:

* new empty account;
* unverified and verified e-mail/password accounts;
* Google account;
* linked authentication methods;
* multiple users for isolation checks;
* account with statuses, visits, memories and notes;
* public and private profiles;
* account with badge and achievement conditions;
* account targeted for deletion;
* countries never used and places with existing history;
* boundary, invalid and unauthorised data in the Emulator.

Relevant boundaries include zero and one visit, repeated increment/decrement, empty required values, defined text limits, invalid identifiers, invalid status combinations, stale state and deleted-account behaviour.

Before launch, all Authentication identities, Firestore records, Storage files, usernames and public profiles are disposable test data. No official or demonstration account will remain after the final reset.

---

## 16. Test-design techniques and assets

AtlasBadge uses a hybrid model:

| Asset                     | Use                                                                        |
| ------------------------- | -------------------------------------------------------------------------- |
| Structured test case      | Critical, complex, destructive or highly repeatable behaviour              |
| Checklist                 | Smoke, quick regression, readiness and recurring release checks            |
| Exploratory charter       | Usability, interruption, unusual sequences and assessment gaps             |
| Combination matrix        | Status rules, permissions, languages and selected environment combinations |
| Automated test            | Stable repeatable behaviour with assertions                                |
| Automated technical check | TypeScript, lint, build, repository and deployment checks                  |
| Tool-assisted inspection  | Logs, DevTools, Firebase, browser storage and deployment analysis          |

Planned supporting assets:

* `test-assets/risk-to-test-traceability.md`;
* `test-assets/exploratory-test-charters.md`;
* `test-assets/sample-test-cases.md`.

Suggested identifiers:

* `QR-##` — quality risk;
* `AB-DEF-###` — defect;
* `AB-TC-###` — structured test case;
* `AB-CHK-###` — checklist;
* `AB-EXP-###` — exploratory charter;
* `AB-AUT-###` — automated test;
* `AB-EV-###` — evidence item.

Identifiers support traceability and must not be used to inflate counts.

---

## 17. Manual, automated and tool-assisted testing

### Manual test

Executed and evaluated by the Test Lead for user journeys, visual behaviour, physical-device checks, usability and release smoke.

### Exploratory test

Executed against a mission or charter and records risks, data, observations, defects, questions, coverage, limitations and next actions.

### Automated test

A repeatable script with defined assertions and an identifiable result. Current or planned examples include Rules tests, persistence or mutation tests, account-deletion tests and Playwright scenarios.

### Automated technical check

TypeScript, ESLint, build, repository checks, environment checks, deployment status and version verification are quality-gate inputs, not functional test-case counts.

### Tool-assisted inspection

Developer Tools, console, network, browser storage, Firebase Console, Vercel, Git history and AI-assisted analysis support diagnosis and evidence but do not replace Test Lead judgement.

### Playwright condition

The planned permanent Playwright suite is not treated as available until implemented and validated.

Persistence scenarios must not run unless the browser application is explicitly connected to Authentication, Firestore and Storage Emulators, emulator mode is mandatory, silent fallback is prevented, hosts and ports are verified, data preparation and cleanup are controlled, and a safety check proves that no test data reached the real project.

---

## 18. Functional coverage

Detailed cases will be maintained separately. System execution will cover these condition groups:

### Access and authentication

* public access and navigation;
* registration, login and invalid credentials;
* e-mail verification and stale verification state;
* password recovery;
* Google authentication;
* session persistence and logout;
* protected routes;
* linking, cancellation, retry and same-UID data preservation;
* unavailable or deleted accounts.

### Profile and username

* creation, validation, uniqueness and case handling;
* username change and old-username release;
* display name, biography and social links;
* persistence;
* personal and public URL resolution;
* invalid or unavailable usernames.

### Map and catalogue

* loading, zoom, pan, pointer and touch interaction;
* search, aliases, labels and flags;
* supported and unsupported place identifiers;
* UK constituent nations, territories, limited-recognition entries and Antarctica;
* consistency between map, selected card, stored record, profile and statistics.

### Travel statuses

* all six statuses;
* selection and deselection;
* valid and prohibited combinations;
* automatic dependent selection and incompatible removal;
* repeated and rapid transitions;
* stale-state normalisation;
* persistence, refresh and later-session restoration;
* failed-write handling and rollback;
* effect on visits, memories, map, counters, badges and profiles.

### Visits, memories and notes

* zero and initial visit state;
* increment, decrement and lower bound;
* total-visit calculation;
* creation, edit and deletion of memories;
* duration, autosave and general note;
* status transitions;
* QR-02 preservation behaviour;
* private treatment of user-created detail.

### Statistics, badges and achievements

* country, territory, visit and continent totals;
* physical-presence qualification;
* prevention of duplicate conquered-place counts;
* dynamic achievement locking and unlocking;
* United Kingdom achievement;
* final V1.0 badge set and visual refinement;
* personal/public consistency.

### Public profile and privacy

* private-by-default behaviour;
* public/private transitions;
* authenticated and unauthenticated access;
* read-only behaviour;
* no unauthorised modification;
* map and statistics consistency;
* exclusion of private memories and notes;
* invalid username and deleted-account behaviour.

### Account deletion

* supported authentication methods and linked identities;
* recent authentication;
* cancellation, retry and partial failure;
* removal from Authentication, Firestore and Storage;
* username release;
* public-profile removal;
* repeated attempts and residual records.

---

## 19. Integration and data-integrity coverage

The principal persistence chain is:

```text
User action
→ UI state
→ local cache where applicable
→ Firestore write
→ stored record
→ refresh or later session
→ reconstructed map, profile and statistics
```

A visual update alone is not sufficient proof of persistence.

Coverage includes:

* rejected or delayed writes;
* rollback or trustworthy failure feedback;
* cache fallback;
* later reload;
* repeated action after failure;
* ownership and cross-user isolation;
* account switching;
* consistency between stored place data, map, card, counters, continents, badges and profiles;
* refresh, logout, later session, rapid actions and practical two-tab exposure;
* account deletion across all integrated stores.

The current absence of a formal multi-tab or multi-device conflict-resolution model remains a residual risk unless changed.

---

## 20. Non-functional coverage

### Performance and fluency

Assessment is qualitative and comparative. It covers page and map loading, zoom, pan, status-selection feedback, map/counter update, repeated rapid interaction, navigation, modals and profile rendering.

Developer Tools may support investigation. No numerical target or universal guarantee will be invented without a validated baseline.

### Responsiveness

Validate primary desktop, Samsung portrait and landscape, essential actions, map, navigation, forms, modals, text wrapping, scrolling, touch targets and conditional tablet behaviour.

### Accessibility

Use WCAG 2.2 Level AA as the reference for an essential baseline covering keyboard navigation, focus, accessible names, labels, errors, contrast, status identification beyond colour, selected zoom/resizing checks, touch targets and continued access to essential actions.

This is not a formal conformity audit or certification.

### Compatibility

Report combinations executed, not executed, defects, limitations and residual risk within the declared sample.

### Usability and resilience

Explore clarity of status meaning, feedback, destructive-action clarity, terminology, recovery, mobile interaction, refresh, temporary connectivity failure, stale state, retry, invalid routes, unavailable profiles and partial deletion failure.

### Localisation quality

Validate terminology, fallback, plurals, dates, accents, long text, truncation, mixed language, responsive layout and system messages.

---

## 21. Security and privacy validation

Coverage includes:

* unauthenticated, authenticated, unverified, logged-out and deleted-account states;
* owner and non-owner access;
* direct identifier manipulation;
* Firestore and Storage Rules;
* public/private profile boundaries;
* read-only public behaviour;
* absence of private memories and notes from unauthorised presentation or responses;
* immediate effect of changing profile visibility;
* sanitised errors;
* complete account deletion;
* reset and evidence privacy.

No published evidence may expose passwords, tokens, API keys, cookies, `.env` data, real e-mail addresses, unmasked identifiers, private usernames, personal data or sensitive configuration.

An active and uncontained risk involving unauthorised access, private-data exposure, severe data loss, severe corruption or critical authentication failure blocks release.

---

## 22. Regression model

Regression is layered:

1. smoke after each meaningful change;
2. direct affected-area regression;
3. impact-based regression;
4. targeted regression after defect correction;
5. permanent regression for material previous risks;
6. mandatory critical-path regression before relevant deployment;
7. final broad regression against one identified Release Candidate;
8. production smoke.

The minimum release regression includes:

* public access;
* authentication and account loading;
* protected routes;
* map, search and selection;
* status rules and persistence;
* visits, memories and notes;
* profile and public profile;
* counters and essential achievements;
* privacy and ownership;
* account deletion;
* `pt-BR` and essential `en-GB`;
* required desktop and mobile sample;
* applicable automated and technical checks.

Retest proves the original failure is corrected. Regression evaluates related behaviour. A passed retest alone does not close the defect.

---

## 23. Defect management during execution

Formal defects use `AB-DEF-###` and follow the lifecycle defined in Defect Management.

The record must identify the affected area, environment, baseline, expected and actual result, reproducibility, severity, priority, release impact and evidence. `Extended description` is not used.

Immediate triage is required for:

* Critical or High defects;
* P0 or P1 defects;
* production defects;
* release blockers;
* privacy, security or data-integrity failures;
* failures that invalidate dependent results.

The Test Lead confirms classification, severity, priority, continuation, retest, regression, deferral, accepted risk and closure.

A Critical defect blocks release. A High defect may be deferred or accepted only exceptionally with explicit containment, evidence and Test Lead approval. Implementation completion, compilation or an AI-generated report does not close a defect.

---

## 24. Traceability

The required chain is:

```text
Quality risk
→ feature or journey
→ test condition
→ case, checklist, charter or automated item
→ environment and data
→ build or Release Candidate
→ result
→ defect where applicable
→ evidence
→ quality gate
→ release decision
```

A simple Markdown matrix is sufficient:

| Risk ID | Feature/journey | Test condition | Asset ID | Priority | Environment | Build/RC | Result | Defect | Evidence | Release relevance |
| ------- | --------------- | -------------- | -------- | -------- | ----------- | -------- | ------ | ------ | -------- | ----------------- |

Rules:

* one asset may cover several related risks;
* asset existence does not prove execution;
* rerun, retest and regression remain distinct;
* Critical and High risks require stronger linkage;
* failed, unexecuted or unverified conditions must remain visible;
* exceptions and accepted risks must link to evidence and the Test Lead decision.

---

## 25. Quality gates

The plan uses these checkpoints:

1. pre-test;
2. pre-retest;
3. pre-regression;
4. pre-deployment;
5. post-deployment;
6. pre-release;
7. post-reset.

Applicable gates may include functional testing, automated tests, Emulator and Rules tests, TypeScript, ESLint, build, repository checks, security review, evidence review, regression, approved-commit verification, deployment verification, production smoke and reset verification.

### 25.1 Gate results

This plan uses the established reporting terms:

* `Passed`;
* `Failed`;
* `Not Applicable`;
* `Not Executed`;
* `Result Not Verified`.

The interview term **Not proven** maps to `Result Not Verified`.

An accepted exception is recorded as:

```text
Failed — Exception Accepted
```

The failure remains visible. An accepted exception is not a pass and does not make the pipeline fully green.

---

## 26. Metrics and reporting checkpoints

Reporting is event-driven and decision-oriented.

Use absolute counts, visible denominators, named risks, blockers, material defects, gate states, evidence completeness, environment coverage and explicit limitations. Percentages are used only when their denominator and interpretation are meaningful.

| Checkpoint        | Decision supported                                       |
| ----------------- | -------------------------------------------------------- |
| Change-level      | Whether the change may progress                          |
| Defect/retest     | Whether correction passed, failed or requires regression |
| Deployment        | Whether the approved version may be deployed             |
| Regression        | Whether coverage and remaining risk are sufficient       |
| Release Candidate | Whether the build may enter freeze                       |
| Production smoke  | Whether the deployment may remain active                 |
| Final release     | Formal V1.0 recommendation                               |
| Post-reset        | Whether production is clean and ready                    |

Each material checkpoint separates:

```text
Facts
Interpretation
Decision
```

Readiness may be `Red`, `Amber` or `Green`. Formal decisions remain `Approved`, `Approved with accepted residual risk` or `Blocked`.

---

## 27. Evidence plan

Selected public evidence is stored under:

```text
evidence/
└── v1.0/
    ├── environments/
    ├── smoke/
    ├── regression/
    ├── defects/
    └── production-reset/
```

An evidence record should identify, where applicable:

* ID or title;
* date;
* purpose;
* related risk, test or defect;
* environment;
* browser, device, resolution and orientation;
* language;
* commit, build, Release Candidate or deployment;
* data state;
* expected and observed result;
* result;
* limitations;
* Test Lead conclusion;
* attachments.

Permitted formats include Markdown, sanitised screenshots, sanitised logs, automated output, technical reports and commit or deployment references.

Screenshots are required only when they add useful proof.

Suggested naming:

```text
YYYY-MM-DD_<category>_<id-or-area>_<environment>_<result>.<ext>
```

Example:

```text
2026-07-22_regression_AB-TC-012_edge-windows_passed.md
```

Only sanitised, professionally useful evidence is committed. Operational evidence may be more extensive than public portfolio evidence.

---

## 28. Release Candidate control

A build may become `V1.0-RC1` when:

* mandatory V1.0 functionality is implemented;
* no planned functional change remains before release;
* the application is stable enough for final regression;
* applicable technical gates have completed;
* system smoke has passed;
* release blockers are resolved;
* Critical risks have sufficient planned or completed coverage;
* environment and commit are identifiable;
* required assets, data and evidence are available;
* mandatory Playwright and safety controls are available;
* residual risks are documented;
* the Test Lead authorises freeze.

After freeze:

* only approved corrections enter;
* each correction receives retest and impact-based regression;
* a changed source revision receives a new RC identifier;
* results from a previous candidate are not attributed to the new candidate without justified carry-over.

The candidate may proceed to deployment only after final regression, evidence review, version control and Test Lead approval.

---

## 29. Deployment and production smoke

Deployment requires:

* identified Release Candidate;
* approved commit;
* required pre-deployment gates completed or explicitly dispositioned;
* required regression completed;
* rollback or containment understood;
* Test Lead authorisation.

After deployment, record the Vercel deployment, deployed commit, approved commit, version match, configuration and exceptions.

The final production smoke includes:

* public access;
* authentication and account access;
* authenticated navigation;
* map loading;
* place selection;
* core status interaction;
* persistence-dependent behaviour;
* personal and public profiles;
* read-only and privacy behaviour;
* logout or session termination;
* `pt-BR` and `en-GB`;
* Edge on Windows;
* Chrome on Windows;
* Samsung Galaxy S20 FE in portrait and landscape.

The Redmi Pad 2 is additional when available.

A serious production failure may require blockage, rollback, containment or a controlled hotfix. A hotfix still requires version control, retest, regression, approval and renewed smoke.

---

## 30. Final production reset and clean-start validation

The final reset occurs only after the candidate has passed required validation, production deployment and smoke, disposable data is no longer needed, and the Test Lead authorises the operation.

Sequence:

1. record authorisation, deployment and Firebase project;
2. capture a sanitised pre-reset state;
3. decide whether a temporary technical backup is necessary;
4. remove all disposable Authentication identities;
5. remove user, profile and username records;
6. remove places, statuses, visits, memories and notes;
7. remove Storage content;
8. verify tombstones and residual records;
9. verify that no test profile or username remains accessible;
10. verify that cache or fallback does not repopulate the backend;
11. confirm that Authentication, Firestore and Storage are empty of test content;
12. create one disposable new account;
13. execute clean-start smoke;
14. confirm no preloaded travel or profile data;
15. delete the validation account and all related data;
16. reconfirm the final empty state;
17. publish sanitised evidence;
18. complete the post-reset gate.

Current data does not require preservation as official content. A temporary backup may support safe rollback or investigation but must not become a demo dataset or prevent the required empty state.

The reset is not executed during creation of this plan.

---

## 31. Deliverables

The cycle will produce or support:

* this System Test Plan;
* risk-to-test traceability;
* exploratory charters;
* selected sample test cases;
* checklists and combination matrices;
* automated and technical results;
* environment and execution records;
* defect, retest and regression records;
* production-smoke evidence;
* production-reset evidence;
* quality checkpoints;
* Test Summary Report;
* Test Lead release recommendation.

This plan does not claim that the remaining deliverables already exist.

---

## 32. Roles and responsibilities

### Test Lead/Product Owner

Responsible for scope, risk, expected behaviour, test depth, manual and exploratory execution, evidence review, defect decisions, regression selection, exceptions, Release Candidate freeze, deployment approval, reset approval, residual-risk assessment and final recommendation.

### AI-assisted implementation process

May analyse code, implement approved changes, execute commands, maintain scripts, run technical and automated checks, investigate defects, collect evidence and draft reports.

It must not approve its own work, hide failed gates, close defects independently, accept residual risk, authorise deployment or issue the Test Lead recommendation.

### Tools and services

GitHub, Vercel, Firebase, browsers, the Emulator Suite, Playwright and local scripts provide source identity, deployment identity, execution results, logs and evidence. Tools do not make quality decisions.

---

## 33. Milestones and sequencing

The plan uses sequence-based milestones rather than invented dates.

| Milestone                             | Completion signal                                                                   |
| ------------------------------------- | ----------------------------------------------------------------------------------- |
| M1 — Plan and documentation readiness | Plan approved and documentation mismatches recorded                                 |
| M2 — Mandatory development completion | Required V1.0 functionality ready                                                   |
| M3 — Targeted validation              | Remaining changes validated and blockers addressed                                  |
| M4 — System execution                 | Required functional, integration, non-functional and exploratory coverage completed |
| M5 — Stabilisation                    | Material defects retested and regression completed                                  |
| M6 — Release Candidate                | `V1.0-RC#` identified and frozen                                                    |
| M7 — Final regression                 | Candidate regression and evidence review completed                                  |
| M8 — Deployment                       | Approved candidate deployed and matched                                             |
| M9 — Production smoke                 | Required smoke completed                                                            |
| M10 — Reset and clean start           | Empty state and clean start proved                                                  |
| M11 — Release decision                | Recommendation issued                                                               |

Real target windows or dates may be added later. Development changes may move or repeat milestones through controlled rebaseline.

---

## 34. Assumptions, dependencies and constraints

### Assumptions

* all current data remains disposable until the final reset;
* `pt-BR` and `en-GB` are the approved V1.0 languages;
* mandatory product rules will be clarified before RC formation;
* suitable test accounts can be created;
* source and deployment identity remain available;
* the Test Lead remains available for execution and decisions.

### Dependencies

* AtlasBadge source and Git history;
* local Windows environment;
* Firebase Authentication, Firestore and Storage;
* Firebase Emulator Suite;
* Vercel;
* internet;
* Edge and planned Chrome desktop;
* Samsung Galaxy S20 FE;
* optional Redmi Pad 2;
* planned Playwright implementation;
* AI-assisted implementation;
* Test Lead review.

### Constraints

* no dedicated staging or QA server;
* local and production share the real Firebase project;
* no proven complete browser-app emulator integration yet;
* limited browser automation;
* no dedicated QA team;
* substantial manual execution;
* no iPhone, Safari, iPad or macOS evidence;
* limited physical-device coverage;
* no formal performance laboratory or load test;
* no formal accessibility audit;
* no test-management platform;
* no fixed business calendar;
* development remains active;
* public evidence must be sanitised.

### Open questions

Open questions affecting final badge rules, input limits, public-profile behaviour, safe Playwright/emulator integration or conditional per-memory privacy must be resolved or explicitly treated before the affected release decision.

---

## 35. Residual risks and limitations

The release assessment must consider:

* shared Firebase use before launch;
* no dedicated staging environment;
* limited browser and device sample;
* no iPhone or Safari evidence;
* limited tablet evidence;
* manual execution concentration;
* limited automation and Playwright implementation risk;
* no formal performance baseline or load test;
* no formal WCAG conformity assessment;
* local-cache exposure on shared devices;
* multi-tab or multi-device overwrite exposure;
* unresolved assessment gaps;
* incomplete documentation alignments;
* unexecuted conditional scope;
* deferred defects, accepted exceptions and unverified evidence.

A limitation does not automatically block release. The Test Lead evaluates its relationship to Critical and High risks, alternative evidence and user impact.

Active and uncontained unauthorised access, private-data exposure, severe data loss, severe corruption or critical authentication failure are not acceptable V1.0 residual risks.

---

## 36. Release recommendation

The Test Lead may issue:

1. **Recommend release** — criteria are satisfied and residual risk is acceptable.
2. **Recommend release with accepted residual risk** — release is viable with explicitly accepted limitations or exceptions.
3. **Do not recommend release** — blockers, prohibited risks or insufficient completion prevent launch.
4. **Decision deferred pending evidence** — available evidence is insufficient for a defensible recommendation.

Relationship with formal decision states:

| Recommendation                                | Typical formal state                          |
| --------------------------------------------- | --------------------------------------------- |
| Recommend release                             | Approved                                      |
| Recommend release with accepted residual risk | Approved with accepted residual risk          |
| Do not recommend release                      | Blocked                                       |
| Decision deferred pending evidence            | Blocked or no final approval pending evidence |

The mapping is not automatic.

The recommendation identifies the evaluated RC, commit and deployment, completed and incomplete scope, risk position, execution and regression results, smoke, reset, quality gates, defects, exceptions, evidence, environment coverage, non-functional findings, limitations, residual risks and follow-up actions.

Only the Test Lead issues the final recommendation.

---

## 37. Review and change control

Review this plan when scope, risks, environments, browser/device coverage, automation, the Release Candidate, reset process or release policy changes materially.

A material change record should identify:

* date;
* changed section;
* reason;
* affected risks and tests;
* affected baseline;
* required re-execution;
* Test Lead decision.

Git history provides document version history. A separate enterprise change-control system is not required.

The implementation process may propose plan changes. The Test Lead approves changes to scope, risk treatment, gates, evidence, exceptions, Release Candidate, deployment, reset and release recommendation.

---

## 38. Related documents

* [AtlasBadge Product Overview](01-product-overview.md)
* [AtlasBadge Quality Risk Analysis](02-quality-risk-analysis.md)
* [AtlasBadge Test Strategy](03-test-strategy.md)
* [AtlasBadge Test Scope](04-test-scope.md)
* [AtlasBadge Entry and Exit Criteria](05-entry-exit-criteria.md)
* [AtlasBadge Test Environments](06-test-environments.md)
* [AtlasBadge Defect Management](07-defect-management.md)
* [AtlasBadge Metrics and Reporting](08-metrics-and-reporting.md)
* [AtlasBadge V1.0 Test Evidence](../evidence/v1.0/README.md)

Future related assets:

* `../test-assets/risk-to-test-traceability.md`;
* `../test-assets/exploratory-test-charters.md`;
* `../test-assets/sample-test-cases.md`;
* `../reports/test-summary-report.md`.
