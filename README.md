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
| [Test Environments](docs/06-test-environments.md) | Describe environments, test data, dependencies and Emulator isolation | Completed / maintained |
| [Defect Management](docs/07-defect-management.md) | Define defect reporting, severity, priority and triage | Completed |
| [Metrics and Reporting](docs/08-metrics-and-reporting.md) | Define useful quality indicators and reporting methods | Completed |
| [System Test Plan](docs/09-system-test-plan.md) | Define the V1.0 system-level execution and release-validation plan | Completed / maintained |
| [C31–C32 Production Traceability](docs/10-c31-c32-production-traceability.md) | Connect counter integrity and Profile map navigation to risks/tests/evidence/release | Completed |
| [C33–C34 Production Traceability](docs/11-c33-c34-production-traceability.md) | Connect dashboard/manual ordering, concurrency and birthplace integrity | Completed |
| [Lessons Learned](docs/12-lessons-learned.md) | Convert recurring product/QA experience into reusable efficiency and quality rules | Active / maintained |
| Exploratory Test Charters | Provide focused exploratory testing missions | Planned |
| Sample Test Cases | Demonstrate selected functional and risk-based tests | Planned |
| Test Summary Report | Demonstrate release-level quality reporting | Planned |
| [V1.0 Test Evidence](evidence/v1.0/README.md) | Organise environment, smoke, regression, defect and reset evidence | In Progress |

The numbered documents are intentionally maintained as living control artefacts rather than creating a new top-level strategy document for every increment. Material increments are captured through evidence and traceability records.

## Recent quality traceability

Recent evidence includes:

- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity: 252 / 195 / 57](evidence/v1.0/defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-030 — Profile map to earned-flag navigation](evidence/v1.0/smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)
- [AB-EV-031 — Dashboard selected-place sorting](evidence/v1.0/regression/ab-ev-031-dashboard-selected-place-sorting.md)
- [AB-EV-032 / AB-DEF-013 — Manual visit order and rapid-visit concurrency](evidence/v1.0/defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)
- [AB-EV-033 — Wishlist, public-profile projection and release hardening](evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md)
- [AB-EV-034 — QR-01 failed-write recovery closure](evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md)
- [AB-EV-035 — C35 Visited + Passed-through coexistence](evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md)

AB-EV-034 closes the last documented QR-01 coverage gap and changes its current state from `Current gap` to `Regression risk`. AB-EV-035 records a Product Owner/Test Lead requirement correction: Visited and Passed through are compatible cumulative historical statuses, while `RegisteredVisit` remains the individual-occurrence model.

The portfolio also records significant true Product Defects separately from stale automation or requirement corrections so defect metrics are not artificially inflated.

## Lessons learned and efficiency

[AtlasBadge Lessons Learned](docs/12-lessons-learned.md) consolidates reusable rules developed through the V1.0 work. Key themes include:

- do not rerun a green checkpoint without a concrete invalidation reason;
- use the smallest test layer capable of proving the changed behaviour;
- perform format/auto-fix work before final gates;
- preserve historical evidence rather than rewriting past decisions;
- validate temporal user journeys before imposing mutually exclusive historical states;
- treat optimistic UI separately from confirmed persistence;
- use actual Firestore commit boundaries when assessing atomicity;
- keep Emulator isolation observable and fail-fast.

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
│   ├── 10-c31-c32-production-traceability.md
│   ├── 11-c33-c34-production-traceability.md
│   └── 12-lessons-learned.md
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
