# QA Test Strategy Portfolio

A practical, risk-based Quality Assurance portfolio using AtlasBadge as a real-world case study.

This repository demonstrates how I approach test strategy, quality risks, test planning, exploratory testing, release readiness, defect management, and quality reporting as a Test Lead.

> **Project status:** This portfolio and the AtlasBadge application are both under active development. The documentation evolves under change control as new features, risks, defects and release evidence are produced.

## Case study: AtlasBadge

AtlasBadge is an interactive travel platform where users can track places they have visited or want to visit, assign travel statuses, monitor their progress, manage a private/public Wishlist, and build a public traveller profile.

The application was selected as the case study because it provides realistic testing challenges involving:

- User authentication and account management
- Persistent user data
- Travel status and Wishlist management
- Interactive maps and user interfaces
- Public/private profile projection and privacy
- Responsive behaviour across devices
- Data integrity and synchronisation
- Accessibility and usability
- Third-party services and integrations

## My involvement

My responsibilities within the AtlasBadge project include:

- Product and feature definition
- Requirements analysis and clarification
- Quality risk identification
- Manual and exploratory testing
- Workflow and usability validation
- Defect investigation and reporting
- Regression testing
- Release readiness assessment
- AI-assisted development coordination
- Continuous improvement of the product and testing process

## Quality approach

The strategy documented in this repository is based on the following principles:

- Prioritise testing according to business and technical risk
- Begin quality discussions during requirement and feature definition
- Combine structured testing with exploratory investigation
- Focus automation on stable, repetitive, and valuable scenarios
- Maintain clear traceability between risks, requirements, tests, defects, evidence and releases
- Communicate quality status in a concise and decision-oriented way
- Treat quality as a shared responsibility across the delivery process

## Portfolio deliverables

| Document | Purpose | Status |
|---|---|---|
| [Product Overview](docs/01-product-overview.md) | Describe the product, users, features, and business context | Completed / maintained |
| [Quality Risk Analysis](docs/02-quality-risk-analysis.md) | Identify and prioritise product and project risks | Completed / maintained |
| [Test Strategy](docs/03-test-strategy.md) | Define the overall testing approach and responsibilities | Completed / maintained |
| [Test Scope](docs/04-test-scope.md) | Define what will and will not be tested | Completed / maintained |
| [Entry and Exit Criteria](docs/05-entry-exit-criteria.md) | Define conditions for starting and completing testing | Completed |
| [Test Environments](docs/06-test-environments.md) | Describe environments, test data, dependencies and Emulator isolation | Completed / maintained |
| [Defect Management](docs/07-defect-management.md) | Define defect reporting, severity, priority, and triage | Completed |
| [Metrics and Reporting](docs/08-metrics-and-reporting.md) | Define useful quality indicators and reporting methods | Completed |
| [System Test Plan](docs/09-system-test-plan.md) | Define the V1.0 system-level test execution and release validation plan | Completed |
| [C31–C32 Production Traceability](docs/10-c31-c32-production-traceability.md) | Connect the counter-integrity correction and Profile map navigation to requirements, risks, tests, evidence, commits and Production sign-off | Completed |
| [C33–C34 Production Traceability](docs/11-c33-c34-production-traceability.md) | Connect dashboard sorting, manual visit order, rapid-visit concurrency, birthplace integrity, tests, defects and Production sign-off | Completed |
| Exploratory Test Charters | Provide focused exploratory testing missions | Planned |
| Sample Test Cases | Demonstrate selected functional and risk-based tests | Planned |
| Test Summary Report | Demonstrate release-level quality reporting | Planned |
| [V1.0 Test Evidence](evidence/v1.0/README.md) | Organise selected environment, smoke, regression, defect, and production-reset evidence | In Progress |

The permanent numbered documents are intentionally kept concise. Later feature/release history is integrated into the living strategy/risk/environment documents and into the evidence register rather than creating a new top-level document for every increment.

## Test evidence

Selected and sanitised evidence from the AtlasBadge V1.0 quality process is organised in the [V1.0 Test Evidence](evidence/v1.0/README.md) directory.

Recent Production traceability includes:

- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity: 252 / 195 / 57](evidence/v1.0/defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-030 — Profile map to earned-flag navigation](evidence/v1.0/smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)
- [AB-EV-031 — Dashboard selected-place sorting](evidence/v1.0/regression/ab-ev-031-dashboard-selected-place-sorting.md)
- [AB-EV-032 / AB-DEF-013 — Manual visit order and rapid-visit concurrency closure](evidence/v1.0/defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)
- [AB-EV-033 — Wishlist, public-profile projection and release hardening](evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md)

AB-EV-033 also records the closure of three product defects found during affected-area regression:

- [AB-DEF-014 — Public place projection missing for normal public statuses](evidence/v1.0/defects/ab-def-014-public-place-projection-missing.md)
- [AB-DEF-015 — Public achievement metadata was not synchronised](evidence/v1.0/defects/ab-def-015-public-achievement-metadata-not-synchronised.md)
- [AB-DEF-016 — Mobile dashboard control grid collapsed to zero width](evidence/v1.0/defects/ab-def-016-mobile-dashboard-grid-collapse.md)

The evidence structure includes environment/configuration verification, smoke evidence, affected-area and integrated regression, selected defect examples and final production-reset evidence.

Only material appropriate for a public professional portfolio is committed. Personal data, credentials, tokens, private environment variables, raw test-account payloads and sensitive configuration are not published.

## Current and planned repository structure

```text
qa-test-strategy-portfolio/
│
├── README.md
│
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
│   └── 11-c33-c34-production-traceability.md
│
├── evidence/
│   └── v1.0/
│       ├── README.md
│       ├── evidence-register.md
│       ├── environments/
│       ├── smoke/
│       ├── regression/
│       ├── defects/
│       └── production-reset/
│
├── test-assets/
│   ├── exploratory-test-charters.md
│   └── sample-test-cases.md
│
└── reports/
    └── test-summary-report.md
```

## Portfolio boundaries

This repository is a professional portfolio and learning project.

It does not contain:

- Confidential employer or client documentation
- Production credentials or access tokens
- Real customer information
- Proprietary test cases from previous employers
- Private business or personal user data

All examples are based on AtlasBadge, controlled test information, and general Quality Assurance practices.

## About me

I am a Test Lead based in Northern Ireland, United Kingdom, with experience in QA strategy, enterprise testing, project coordination, regulated environments, Oracle E-Business Suite, and cross-functional delivery.

[Connect with me on LinkedIn](https://www.linkedin.com/in/mhclaudino/)
