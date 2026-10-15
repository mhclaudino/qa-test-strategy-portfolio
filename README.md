# QA Test Strategy Portfolio

A practical, risk-based Quality Assurance portfolio using **AtlasBadge** as a real-world case study.

This repository demonstrates how I approach test strategy, quality risks, test planning, exploratory testing, defect management, automation, release readiness, production validation and continuous process improvement as a Test Lead.

> **Current case-study status — 15 October 2026:** documentation is reconciled through **AB-EV-068 / PA-003**. PA-001, PA-002 and PA-003 are published Premium Hardening checkpoints; PA-003 is **CLOSED / PUBLISHED / PRODUCTION VERIFIED**. The current AtlasBadge Product publication anchor is `7616e8ca455f1d2e72a67e4dd839c9c302f98656`, deployed to Vercel Production as `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` (`READY`, exact SHA). Final Premium Hardening closure and final V1.0 Go Live are separate future decisions and are **not** claimed here.

## Case study: AtlasBadge

AtlasBadge is an interactive travel platform where users can track places they have visited or want to visit, assign cumulative travel statuses, maintain detailed visit history and memories, attach a representative visit photo, monitor geographic progress and achievements, manage a private/public Wishlist and publish a read-only traveller profile.

The application creates realistic QA challenges involving:

- authentication and account lifecycle;
- persistent user data, failure recovery and concurrency;
- travel-status and Wishlist rules;
- interactive maps and geographic counters;
- private/public profile projection and per-memory privacy;
- bounded visit-photo quota, server-owned lifecycle and immutable resource identity;
- responsive behaviour and accessibility;
- localization across seven supported locales;
- Firebase Authentication, Firestore, Storage and Rules;
- exact-SHA Git/Vercel publication and Production verification.

## My involvement

My responsibilities within the AtlasBadge project include:

- Product and feature definition;
- requirements analysis and clarification;
- quality-risk identification;
- manual and exploratory testing;
- test strategy, scope and acceptance-oracle design;
- defect investigation, classification and reporting;
- regression strategy and checkpoint management;
- release-readiness and residual-risk assessment;
- AI-assisted delivery coordination and independent-review governance;
- Production validation planning;
- continuous improvement of the product and QA process.

## Quality approach

The strategy documented in this repository is based on the following principles:

- prioritise testing according to business and technical risk;
- begin quality discussions during requirement and feature definition;
- combine structured testing with exploratory investigation;
- focus automation on stable, repeatable and valuable scenarios;
- maintain traceability across **requirement → risk → implementation → test/oracle → finding/defect → evidence → commit → deployment → Test Lead decision**;
- preserve a green checkpoint unless a later change invalidates what it proved;
- distinguish Product Defects from stale automation, fixture problems, environment failures, unsupported theories and requirement corrections;
- preserve named acceptance oracles rather than relying on aggregate test counts;
- bind high-risk publication approval to exact candidate bytes and exact deployed SHA;
- use protected Emulator evidence for destructive/race scenarios and proportional non-destructive Production verification when real mutation would add avoidable risk;
- communicate residual risk explicitly rather than hiding gaps behind a green headline.

## Portfolio deliverables

The `docs/` directory is intentionally limited to the ten living control artefacts `01`–`10`. Change-level execution evidence belongs under `evidence/`.

| Document | Purpose | Status |
|---|---|---|
| [Product Overview](docs/01-product-overview.md) | Product, users, capabilities, data and current Premium checkpoint | Maintained through AB-EV-068 |
| [Quality Risk Analysis](docs/02-quality-risk-analysis.md) | Risk identification, prioritisation and residual-risk state | Maintained through AB-EV-068 |
| [Test Strategy](docs/03-test-strategy.md) | Risk-based validation, automation, review and publication approach | Maintained through AB-EV-068 |
| [Test Scope](docs/04-test-scope.md) | Covered areas, depth, deferrals and regression boundaries | Maintained through AB-EV-068 |
| [Entry and Exit Criteria](docs/05-entry-exit-criteria.md) | Validation, deployment and launch gates | Maintained through AB-EV-068 |
| [Test Environments](docs/06-test-environments.md) | Local/Emulator/Production environments and safety controls | Maintained through AB-EV-068 |
| [Defect Management](docs/07-defect-management.md) | Defect/finding classification, lifecycle and closure | Maintained through AB-EV-068 |
| [Metrics and Reporting](docs/08-metrics-and-reporting.md) | Decision-oriented quality indicators and evidence reporting | Maintained through AB-EV-068 |
| [System Test Plan](docs/09-system-test-plan.md) | V1.0 system-test and Premium Hardening execution control | Maintained through AB-EV-068 |
| [Lessons Learned](docs/10-lessons-learned.md) | Reusable QA/delivery rules derived from evidence | Active; LL-01–LL-79 |
| [V1.0 Test Evidence](evidence/v1.0/README.md) | Sanitised public evidence by purpose | In progress / maintained |
| [Evidence Register](evidence/v1.0/evidence-register.md) | Authoritative public evidence index | Maintained through AB-EV-068 |
| Exploratory Test Charters | Focused exploratory missions | Planned |
| Sample Test Cases | Selected risk-based examples | Planned |
| Test Summary Report | Final V1.0 release-level quality summary | Planned |

## Current Premium Hardening traceability

### PA-001 — Clear Map server boundary

[AB-EV-066 — PA-001 Clear Map server-boundary premium hardening](evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md)

- Clear Map protected-state mutation moved to an authenticated server/Admin transaction.
- The historical reset/generation contract and server-managed photo-slot protection are preserved.
- A later UTF-8 test-oracle-only repair changed tests, not runtime behaviour.
- PA-001 is published/functionally closed.
- AC-017 browser UI → API → Admin all-Emulator reload proof remains a PA-007 / Final Premium RC carry-forward.
- The missed pre-publication independent architecture review remains a transparently labelled compensating post-publication governance item.

### PA-002 — Active visit-photo quota lifecycle

[AB-EV-067 — PA-002 active visit-photo quota lifecycle premium hardening](evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md)

- The free quota means at most **10 active logical persisted visit photos**, not historical reservations.
- Logical photo/visit removal releases exact active capacity and immediately invalidates obsolete owner/public access.
- Release failures persist exact retry identity across reload.
- Stale cleanup cannot remove a newer reservation that reused a slot.
- Reconciliation preserves concurrent edits and other pending work.
- All original PA2 acceptance criteria were independently proven before publication.
- Physical Storage deletion remains correctness-independent deferred cleanup.

### PA-003 — Photo Admin/server-route hardening

[AB-EV-068 — PA-003 photo Admin/server-route lifecycle and input-contract premium hardening](evidence/v1.0/regression/ab-ev-068-pa003-photo-admin-server-route-hardening.md)

PA-003 extends the photo lifecycle boundary with:

- strict mutation/input/request contracts;
- revocation-aware protected authentication and account-lifecycle gates;
- verified-email reservation policy;
- canonical owner/public read identity and fail-closed legacy compatibility;
- explicit distinction between **absent / present-valid / present-invalid** persisted locator state;
- immutable physical identity for new photo versions:

```text
users/{uid}/visit-photo-objects/{slotId}/{photoRef}.webp
```

- write-frozen legacy mutable photo namespace;
- deterministic authorization/resource race proof with the barrier before byte consumption;
- cross-service Firestore+Storage Rules smoke in the Product Emulator project;
- exact candidate-byte, Git and Vercel publication traceability;
- bounded post-publication verification with **zero intentional Production Firebase mutation**.

Final PA-003 qualification included:

```text
PA3-01 through PA3-15: accepted
Immutable race suite: 7 / 7 PASS
Serial five-suite Emulator group: 90 / 90 PASS
Rules: 258 / 258 PASS, 0 failed, 0 skipped
Full Vitest: 989 PASS / 123 environment-gated SKIPPED / 0 FAIL
C44 affected Edge regression: 1 / 1 PASS; realFirebaseRequests=0
Build: PASS
TypeScript: PASS
ESLint: 0 errors / 30 warnings
Product: 7616e8ca455f1d2e72a67e4dd839c9c302f98656
Vercel: dpl_B842NnXHecrGNhcQAfpkC8CgyVhj — READY / production / exact SHA
Post-publication: PASS
```

**Final status: PA-003 CLOSED / PUBLISHED / PRODUCTION VERIFIED.**

## Recent release and quality evidence

The complete index is the [Evidence Register](evidence/v1.0/evidence-register.md). Recent milestones include:

- [AB-EV-055 — C45J Public Profile localization and fixes](evidence/v1.0/regression/ab-ev-055-c45j-public-profile-localization-and-fixes.md)
- [AB-EV-056 — C45K geographic localization](evidence/v1.0/regression/ab-ev-056-c45k-geographic-localization.md)
- [AB-EV-057 — C45L password-reset localization](evidence/v1.0/regression/ab-ev-057-c45l-password-reset-action-localization.md)
- [AB-EV-058 / AB-DEF-021 — C45M authenticated language selector and Production hotfix](evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md)
- [AB-EV-059 — C45N German V1.0 localization extension](evidence/v1.0/regression/ab-ev-059-c45n-german-localization-extension.md)
- [AB-EV-060 / AB-DEF-022 — C45N final localization closure](evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md)
- [AB-EV-061 — V1.0 release-readiness audit](evidence/v1.0/regression/ab-ev-061-v1-release-readiness-audit.md)
- [AB-EV-062 — Final V1.0 Release Candidate regression charter](evidence/v1.0/regression/ab-ev-062-final-v1-release-candidate-regression-charter.md)
- [AB-EV-063 — RC lockfile reproducibility recovery](evidence/v1.0/regression/ab-ev-063-rc-lockfile-reproducibility-recovery.md)
- [AB-EV-064 — Node 24 LTS upgrade charter](evidence/v1.0/regression/ab-ev-064-c46-node24-lts-upgrade-charter.md)
- [AB-EV-065 — Node 24 final RC qualification and Production deployment](evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md)
- [AB-EV-066 — PA-001 Clear Map server-boundary premium hardening](evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md)
- [AB-EV-067 — PA-002 active visit-photo quota lifecycle premium hardening](evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md)
- [AB-EV-068 — PA-003 photo Admin/server-route premium hardening](evidence/v1.0/regression/ab-ev-068-pa003-photo-admin-server-route-hardening.md)

Historical evidence remains unchanged when later work supersedes its decision state. Living controls and the Evidence Register describe the current state.

## Lessons learned and efficiency

[AtlasBadge Lessons Learned](docs/10-lessons-learned.md) converts recurring observations into standing QA controls. Current themes include:

- preserve valid checkpoints unless a later change invalidates them;
- select the smallest test layer capable of proving the changed behaviour;
- classify Product, test-oracle, fixture, environment and governance problems separately;
- prove persistence through confirmed state/reload, not optimistic UI alone;
- assess atomicity at real backend commit boundaries;
- keep Product and Rules/runtime environment parity observable;
- maintain explicit acceptance-oracle inventories;
- freeze exact candidate bytes before final independent approval;
- complete required independent review before publication;
- separate logical lifecycle completion from deferred physical garbage collection;
- persist exact recovery identity before fallible lifecycle work;
- apply post-await cleanup to current orchestrated state rather than stale snapshots;
- bind authorised content to immutable physical resource identity when names can be reused;
- preserve absent/present-valid/present-invalid security states across persistence;
- place concurrency barriers at the actual semantic boundary being claimed;
- qualify cross-service Rules in the Product Emulator project;
- keep Production verification proportional and non-destructive when controlled lower layers already prove stateful/race behaviour.

## Current carry-forwards

PA-001, PA-002 and PA-003 are not open findings. Remaining separately tracked work includes:

- PA-006A — deterministic tooling / cached undeclared `tsx` concern;
- PA-007 — Firebase server-boundary/project-safety work and PA-001 AC-017 closure;
- PA-008 — account-deletion origin policy hardening;
- PA-001 compensating post-publication architecture audit;
- deferred physical visit-photo Storage garbage collection;
- legacy Clear Map fixture/test debt;
- broader browser/device, native assistive-technology and quantitative performance evidence before final V1.0 release where required.

These carry-forwards do not rewrite the published PASS decisions for PA-001/002/003.

## Test evidence

Selected and sanitised evidence is organised in [V1.0 Test Evidence](evidence/v1.0/README.md). The central [Evidence Register](evidence/v1.0/evidence-register.md) is the authoritative public index of evidence IDs, related risks/findings, environments, commits, deployments, decisions and public artefacts.

Only material appropriate for a public professional portfolio is committed. Personal data, credentials, tokens, private environment variables, raw test-account payloads and sensitive configuration are not published.

## Repository structure

```text
qa-test-strategy-portfolio/
│
├── README.md
├── GOVERNANCE-ERRATA.md
├── docs/
│   ├── 01-product-overview.md
│   ├── 02-quality-risk-analysis.md
│   ├── 03-test-strategy.md
│   ├── 04-test-scope.md
│   ├── 05-entry-exit-criteria.md
│   ├── 06-test-environments.md
│   ├── 07-defect-management.md
│   ├── 08-metrics-and-reporting.md
│   ├── 09-system-test-plan.md
│   └── 10-lessons-learned.md
├── evidence/
│   └── v1.0/
│       ├── README.md
│       ├── evidence-register.md
│       ├── environments/
│       ├── smoke/
│       ├── regression/
│       ├── defects/
│       └── production-reset/
├── test-assets/
│   ├── exploratory-test-charters.md
│   └── sample-test-cases.md
└── reports/
    └── test-summary-report.md
```

## Portfolio boundaries

This repository is a professional portfolio and learning project. It does not contain confidential employer/client documentation, Production credentials/tokens, real customer information or proprietary test cases from previous employers.

All examples are based on AtlasBadge, controlled test information and general Quality Assurance practices.

## About me

I am a Test Lead based in Northern Ireland, United Kingdom, with experience in QA strategy, enterprise testing, project coordination, regulated environments, Oracle E-Business Suite and cross-functional delivery.

[Connect with me on LinkedIn](https://www.linkedin.com/in/mhclaudino/)