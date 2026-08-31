# QA Test Strategy Portfolio

A practical, risk-based Quality Assurance portfolio using AtlasBadge as a real-world case study.

This repository demonstrates how I approach test strategy, quality risks, test planning, exploratory testing, release readiness, defect management, quality reporting and continuous process improvement as a Test Lead.

> **Project status:** This portfolio and the AtlasBadge application are both under active development. Documentation evolves under change control as new features, risks, defects, requirement corrections and release evidence are produced.

## Case study: AtlasBadge

AtlasBadge is an interactive travel platform where users can track places they have visited or want to visit, assign cumulative travel statuses, maintain detailed visit history, monitor progress, manage a private/public Wishlist and build a public traveller profile.

The application provides realistic testing challenges involving:

- user authentication and account management;
- persistent user data and failed-write recovery;
- travel status and Wishlist management;
- interactive maps and user interfaces;
- public/private profile projection and privacy;
- responsive behaviour across devices;
- data integrity and synchronisation;
- accessibility and usability;
- third-party services and deployment integration.

## My involvement

My responsibilities within the AtlasBadge project include:

- Product and feature definition;
- Requirements analysis and clarification;
- Quality risk identification;
- Manual and exploratory testing;
- Workflow and usability validation;
- Defect investigation and reporting;
- Regression strategy and checkpoint management;
- Release readiness assessment;
- AI-assisted development coordination;
- Continuous improvement of the product and testing process.

## Quality approach

The strategy documented in this repository is based on the following principles:

- prioritise testing according to business and technical risk;
- begin quality discussions during requirement and feature definition;
- combine structured testing with exploratory investigation;
- focus automation on stable, repetitive and valuable scenarios;
- maintain traceability between risks, requirements, tests, defects, evidence and releases;
- preserve green checkpoints unless a later change invalidates what they proved;
- distinguish Product Defects from stale automation, infrastructure failures and requirement corrections;
- communicate quality status in a concise and decision-oriented way;
- treat quality as a shared responsibility across delivery.

## Portfolio deliverables

| Document | Purpose | Status |
|---|---|---|
| [Product Overview](docs/01-product-overview.md) | Describe the product, users, features and business context | Completed / maintained |
| [Quality Risk Analysis](docs/02-quality-risk-analysis.md) | Identify and prioritise product and project risks | Completed / maintained |
| [Test Strategy](docs/03-test-strategy.md) | Define the overall testing approach and responsibilities | Completed / maintained |
| [Test Scope](docs/04-test-scope.md) | Define what will and will not be tested | Completed / maintained |
| [Entry and Exit Criteria](docs/05-entry-exit-criteria.md) | Define conditions for starting and completing testing | Completed |
| [Test Environments](docs/06-test-environments.md) | Describe environments, test data, dependencies and Emulator isolation/readiness | Completed / maintained |
| [Defect Management](docs/07-defect-management.md) | Define defect reporting, severity, priority and triage | Completed |
| [Metrics and Reporting](docs/08-metrics-and-reporting.md) | Define useful quality indicators and reporting methods | Completed |
| [System Test Plan](docs/09-system-test-plan.md) | Define the V1.0 system-level execution and release-validation plan | Completed / maintained |
| [Lessons Learned](docs/10-lessons-learned.md) | Convert recurring product/QA experience into reusable efficiency and quality rules | Active / maintained |
| Exploratory Test Charters | Provide focused exploratory testing missions | Planned |
| Sample Test Cases | Demonstrate selected functional and risk-based tests | Planned |
| Test Summary Report | Demonstrate release-level quality reporting | Planned |
| [V1.0 Test Evidence](evidence/v1.0/README.md) | Organise environment, smoke, regression, defect and reset evidence | In Progress |

The `docs/` directory is intentionally limited to the ten living control artefacts `01`–`10`. Increment-specific traceability must be distributed into those existing documents; a new change-level file must not be created under `docs/` without explicit Test Lead approval. New incremental records belong under `evidence/` when evidence is required.

## Recent quality traceability

Recent evidence includes:

- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity: 252 / 195 / 57](evidence/v1.0/defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-030 — Profile map to earned-flag navigation](evidence/v1.0/smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)
- [AB-EV-031 — Dashboard selected-place sorting](evidence/v1.0/regression/ab-ev-031-dashboard-selected-place-sorting.md)
- [AB-EV-032 / AB-DEF-013 — Manual visit order and rapid-visit concurrency](evidence/v1.0/defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)
- [AB-EV-033 — Wishlist, public-profile projection and release hardening](evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md)
- [AB-EV-034 — QR-01 failed-write recovery closure](evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md)
- [AB-EV-035 — C35 Visited + Passed-through coexistence](evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md)
- [AB-EV-036 / AB-DEF-017 — Wishlist atomic settings save and order integrity](evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md)
- [AB-EV-037 / AB-DEF-018 — Clear Map atomic generation reset](evidence/v1.0/defects/ab-ev-037-clear-map-atomic-generation-reset.md)
- [AB-EV-038 — Manual QA Environment Contract](evidence/v1.0/environments/ab-ev-038-manual-qa-environment-contract.md)
- [AB-EV-039 — C39 Individual memory privacy](evidence/v1.0/regression/ab-ev-039-c39-individual-memory-privacy.md)
- [AB-EV-040 — C40 Manual memory ordering](evidence/v1.0/regression/ab-ev-040-c40-manual-memory-ordering.md)
- [AB-EV-041 — C41 Public memories from earned flags](evidence/v1.0/regression/ab-ev-041-c41-public-memory-flag-modal.md)
- [AB-EV-042 — C42 Editable visit names](evidence/v1.0/regression/ab-ev-042-editable-visit-names.md)
- [AB-EV-043 — C43 Visual identity alignment](evidence/v1.0/regression/ab-ev-043-visual-identity-alignment.md)
- [AB-EV-044 — C44 One photo per RegisteredVisit production closure](evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md)
- [AB-EV-045 — C45A Localization routing foundation](evidence/v1.0/regression/ab-ev-045-c45a-localization-routing-foundation.md)
- [AB-EV-046 — C45B Public Home localization and language selector](evidence/v1.0/regression/ab-ev-046-c45b-public-home-localization-and-language-selector.md)

AB-EV-034 closes the last documented QR-01 coverage gap and changes its current state from `Current gap` to `Regression risk`. AB-EV-035 records a Product Owner/Test Lead requirement correction: Visited and Passed through are compatible cumulative historical statuses, while `RegisteredVisit` remains the individual-occurrence model.

AB-EV-036 records a true Product Defect found through atomicity analysis: one Wishlist Save could use independent Firestore commits and leave partial persisted state. The final correction replaces per-place Wishlist ordering writes with root `wishlistOrder`, keeps the maximum combined operation at or below 253 document writes, proves rejected-batch atomicity in the Firestore Emulator and closes the defect after controlled Vercel/Firestore Rules deployment plus Test Lead Production validation.

AB-EV-037 extends that data-integrity work to the destructive Clear Map lifecycle. The old implementation could be blocked by the new root Wishlist metadata and also used separate public/private commit boundaries. C37 rejects naive 504-write chunking, introduces generation-based public projection invalidation, performs the logical Clear Map reset in one <=253-write batch, protects stale public data at the Rules/query boundary and closes AB-DEF-018 after aligned application/Rules deployment and Test Lead Production PASS.

AB-EV-039 establishes explicit per-memory privacy with sanitised `publicMemories`; AB-EV-040 adds independent `memoryOrder` presentation metadata while preserving `registeredVisits` identity/history; AB-EV-041 connects those contracts to the public Profile through an earned-flag modal and a single public data source for owner/viewer rendering. AB-EV-042 adds optional editable visit names while preserving stable visit identity, explicit Save, memory order and sanitised public labels. AB-EV-043 aligns the cross-product visual language to Atlas Gold/Black/Warm White while retaining semantic status, feedback, data-visualisation and third-party colours. AB-EV-044 closes C44: one representative photo per `RegisteredVisit`, a hard free quota of 10 active visit photos per user, bounded Storage slots, owner/public server-mediated reads, replacement/removal lifecycle cleanup and Production approval after real-backend/runtime hardening. AB-EV-045 closes C45A: six explicit public Home locale routes, root locale resolution, `next-intl` foundation, preservation of the root `[username]` public-profile contract and Next.js security hardening before Production publication. AB-EV-046 closes C45B: translated public Home/Hero/Header/Footer for all six V1.0 locales, desktop/mobile language selection, cookie persistence, locale-specific metadata/document language and authenticated-Home selector parity. C41–C45B reached Production and final Test Lead approval.

The portfolio records true Product Defects separately from stale automation, environment/Rules parity failures and requirement corrections so defect metrics are not artificially inflated.

## Lessons learned and efficiency

[AtlasBadge Lessons Learned](docs/10-lessons-learned.md) consolidates reusable rules developed through the V1.0 work. Key themes include:

- do not rerun a green checkpoint without a concrete invalidation reason;
- use the smallest test layer capable of proving the changed behaviour;
- perform format/auto-fix work before final gates;
- preserve historical evidence rather than rewriting past decisions;
- validate temporal user journeys before imposing mutually exclusive historical states;
- treat optimistic UI separately from confirmed persistence;
- use actual Firestore commit boundaries when assessing atomicity;
- quantify maximum write cardinality before accepting a batch redesign;
- verify application/Firestore Rules parity before investigating permission failures;
- treat Emulator services, test identity and the Test Lead browser session as separate readiness gates;
- use one canonical manual-QA browser origin;
- review write path, read path and confirmed in-memory state whenever a source of truth changes;
- ensure ordering tests connect user reorder state to persisted and later-rendered order;
- separate atomic logical invalidation from non-critical physical garbage collection when destructive operations exceed backend transaction limits;
- expose destructive-operation failure feedback instead of leaving confirmation UI apparently frozen;
- keep public Profile rendering on the sanitised public projection even when the owner views their own Profile;
- diagnose stale legacy projection data before adding compensating code or migrations;
- keep AI-agent work bounded to one finishable objective per checkpoint and fit task size to available model/runtime quota;
- distinguish local environment readiness from Production/Vercel configuration instead of inferring one from the other;
- prove real Firebase Admin/Storage/runtime integration separately from Emulator success when a feature depends on those boundaries;
- reuse an explicitly referenced existing UI pattern before inventing a new layout.

This is part of the QA strategy rather than a retrospective appendix: lessons are converted into standing working rules for later AtlasBadge changes.

## Test evidence

Selected and sanitised evidence is organised in [V1.0 Test Evidence](evidence/v1.0/README.md). The central [Evidence Register](evidence/v1.0/evidence-register.md) is the authoritative public index of evidence IDs, related risks/defects, environments, commits, decisions and artefacts.

Only material appropriate for a public professional portfolio is committed. Personal data, credentials, tokens, private environment variables, raw test-account payloads and sensitive configuration are not published.

## Current and planned repository structure

```text
qa-test-strategy-portfolio/
│
├── README.md
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
