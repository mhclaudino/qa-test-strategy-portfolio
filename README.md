# QA Test Strategy Portfolio

A practical, risk-based Quality Assurance portfolio using AtlasBadge as a real-world case study.

This repository demonstrates how I approach test strategy, quality risks, test planning, exploratory testing, release readiness, defect management, and quality reporting as a Test Lead.

> **Project status:** This portfolio and the AtlasBadge application are both under active development. The documentation will evolve as new features, risks, and testing needs are identified.

## Case study: AtlasBadge

AtlasBadge is an interactive travel platform where users can track places they have visited, assign travel statuses, monitor their progress, and build a public traveller profile.

The application was selected as the case study because it provides realistic testing challenges involving:

- User authentication and account management
- Persistent user data
- Travel status management
- Interactive maps and user interfaces
- Public and private profile information
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
- Maintain clear traceability between risks, requirements, tests, and defects
- Communicate quality status in a concise and decision-oriented way
- Treat quality as a shared responsibility across the delivery process

## Portfolio deliverables

The portfolio will contain the following documents and supporting test evidence:

| Document | Purpose | Status |
|---|---|---|
| [Product Overview](docs/01-product-overview.md) | Describe the product, users, features, and business context | Completed |
| [Quality Risk Analysis](docs/02-quality-risk-analysis.md) | Identify and prioritise product and project risks | Completed |
| [Test Strategy](docs/03-test-strategy.md) | Define the overall testing approach and responsibilities | Completed |
| [Test Scope](docs/04-test-scope.md) | Define what will and will not be tested | Completed |
| [Entry and Exit Criteria](docs/05-entry-exit-criteria.md) | Define conditions for starting and completing testing | Completed |
| [Test Environments](docs/06-test-environments.md) | Describe environments, test data, and dependencies | Completed |
| Defect Management | Define defect reporting, severity, priority, and triage | Planned |
| Metrics and Reporting | Define useful quality indicators and reporting methods | Planned |
| System Test Plan | Define the V1.0 system-level test execution and release validation plan | Planned |
| Exploratory Test Charters | Provide focused exploratory testing missions | Planned |
| Sample Test Cases | Demonstrate selected functional and risk-based tests | Planned |
| Test Summary Report | Demonstrate release-level quality reporting | Planned |
| [V1.0 Test Evidence](evidence/v1.0/README.md) | Organise selected environment, smoke, regression, defect, and production-reset evidence | In Progress |

The status and links in this table will be updated as each document is created.

## Test evidence

Selected and sanitised evidence from the AtlasBadge V1.0 quality process is organised in the [V1.0 Test Evidence](evidence/v1.0/README.md) directory.

The evidence structure includes:

* environment and configuration verification;
* smoke test evidence;
* regression test evidence;
* selected defect examples;
* final production-reset and clean-start evidence.

Evidence will be added progressively as the corresponding tests are executed. Only material appropriate for a public professional portfolio will be committed. Personal data, credentials, tokens, private environment variables, and sensitive configuration must not be published.

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
│   └── 09-system-test-plan.md
│
├── evidence/
│   └── v1.0/
│       ├── README.md
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
