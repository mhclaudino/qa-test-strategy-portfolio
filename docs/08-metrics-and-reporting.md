# AtlasBadge Metrics and Reporting

## 1. Document purpose

This document defines how AtlasBadge quality information is collected, interpreted, reported, and used to support testing, deployment, and release decisions.

It establishes:

* which quality information is useful;
* why that information is collected;
* which decisions it supports;
* which sources provide the data;
* how data quality is controlled;
* how small samples and incomplete information are presented;
* how risks, tests, defects, quality gates, environments, deployments, and evidence are reported;
* how release readiness is summarised;
* how exceptions and residual risks are communicated;
* how operational reporting differs from public portfolio evidence;
* which metrics are deliberately not used.

The purpose of quality reporting is not to create the largest possible set of measurements. Its purpose is to give the Test Lead enough reliable and contextual information to make defensible decisions.

This document applies to:

* incremental development;
* change-level validation;
* defect correction and retesting;
* regression testing;
* controlled deployments;
* production smoke testing;
* AtlasBadge V1.0 release preparation;
* future releases;
* selected public portfolio reporting.

> **Document status:** Initial completed version based on the current AtlasBadge quality process, the existing QA portfolio documents, and the metrics and reporting decisions confirmed by the Test Lead.

---

## 2. Relationship with the QA portfolio

This document should be read together with:

* [Product Overview](01-product-overview.md);
* [Quality Risk Analysis](02-quality-risk-analysis.md);
* [Test Strategy](03-test-strategy.md);
* [Test Scope](04-test-scope.md);
* [Entry and Exit Criteria](05-entry-exit-criteria.md);
* [Test Environments](06-test-environments.md);
* [Defect Management](07-defect-management.md);
* [V1.0 Test Evidence](../evidence/v1.0/README.md).

These documents have different responsibilities:

* the Product Overview defines the product and its users;
* the Quality Risk Analysis defines what may fail and why it matters;
* the Test Strategy defines how quality is governed;
* the Test Scope defines what requires validation;
* the Entry and Exit Criteria define when work may begin, continue, or conclude;
* the Test Environments document defines the available execution contexts and limitations;
* the Defect Management document defines how actual failures are recorded and controlled;
* this document defines how the resulting quality information is interpreted and communicated.

This document does not replace:

* individual test results;
* defect records;
* risk records;
* detailed evidence;
* the future System Test Plan;
* the future Test Summary Report.

The future `docs/09-system-test-plan.md` will apply the indicators and checkpoints defined here to the formal AtlasBadge V1.0 test cycle.

The future `reports/test-summary-report.md` will use this reporting model to provide the final release-level quality assessment.

---

## 3. Metrics and reporting principles

AtlasBadge quality reporting follows these principles.

### 3.1 Every indicator must support a decision

A metric or reported item must exist because it helps answer a practical question, such as:

* Is the implementation ready for Test Lead validation?
* Can testing continue?
* Is a quality risk sufficiently covered?
* Is a correction ready for retesting?
* Has the necessary regression been completed?
* Can the approved version be deployed?
* Did the production smoke test pass?
* Is the release ready?
* What residual risk remains?
* Is the available evidence sufficient?
* Has a quality-gate exception been accepted?

An indicator that does not support a decision, investigation, or useful review should not be collected merely because it is measurable.

### 3.2 Counts require context

A raw count does not represent quality by itself.

For example:

* more defects found may reflect deeper testing rather than worse product quality;
* fewer defects found may reflect insufficient coverage;
* more tests may reflect duplication rather than improved confidence;
* fewer tests may still provide stronger coverage when they target higher risks;
* a high pass rate may hide important unexecuted scenarios;
* zero recorded defects does not prove that no defects exist.

Reports must explain what was covered, what was not covered, and why the result matters.

### 3.3 Metrics do not replace risk assessment

Quality decisions must consider:

* the importance of the affected journey;
* severity and priority of defects;
* the state of High quality risks;
* environment suitability;
* evidence quality;
* unexecuted scenarios;
* known limitations;
* accepted residual risk;
* cumulative impact.

A positive numerical result cannot compensate for a material unresolved risk.

### 3.4 Quality is not reduced to one score

AtlasBadge will not use a single overall quality score or readiness percentage.

Authentication, privacy, data integrity, accessibility, performance, regression, and evidence cannot be combined responsibly through arbitrary weights.

Release readiness may be summarised as `Green`, `Amber`, or `Red`, but the colour must be supported by facts, limitations, risks, and a formal Test Lead decision.

### 3.5 Small samples must not create false precision

Percentages, averages, and trends may be misleading when based on small or inconsistent samples.

For small samples, reports should prefer formats such as:

```text
1 of 2 tests passed
2 High risks reviewed; 1 remains open
3 defects recorded, including 1 release blocker
```

The denominator and absolute count must remain visible when a percentage is used.

### 3.6 Facts, interpretation, and decisions remain separate

Reports should distinguish:

* **facts:** what was executed or observed;
* **interpretation:** what those facts mean in context;
* **decision:** what the Test Lead approved, rejected, blocked, or accepted.

A tool result is a fact source. It is not automatically a release decision.

### 3.7 Metrics must not evaluate individual productivity

Metrics must not be used to rank or judge people, developers, testers, or AI tools by:

* number of commits;
* number of defects;
* number of test cases;
* number of commands executed;
* lines of code;
* speed of defect closure;
* quantity of automation.

The purpose is product and process quality, not productivity surveillance.

### 3.8 Missing information must remain visible

A missing result is not a pass.

Reports must use explicit states such as:

* `Not Collected`;
* `Unavailable`;
* `Not Verified`;
* `Not Applicable`;
* `Partially Available`;
* `Insufficient Evidence`.

A relevant missing result may change readiness to `Amber` or `Red`.

### 3.9 AI-generated information requires review

AI-assisted tools may produce:

* command results;
* implementation reports;
* summaries;
* proposed interpretations;
* defect hypotheses;
* draft checkpoints.

Their output must still be checked against the underlying evidence.

The Test Lead retains responsibility for:

* indicator selection;
* interpretation;
* readiness assessment;
* quality-gate exceptions;
* residual-risk acceptance;
* deployment approval;
* release recommendation.

---

## 4. Audiences and decisions

AtlasBadge quality reporting has four communication layers.

### 4.1 Operational record

**Audience**

* Test Lead/Product Owner;
* authorised implementation support;
* future authorised collaborators.

**Purpose**

To preserve detailed information required for investigation, correction, validation, and decision-making.

**Typical content**

* technical results;
* GitHub Issues;
* implementation details;
* defect evidence;
* test outputs;
* commit references;
* quality-gate results;
* investigation notes;
* known limitations;
* corrective actions.

Operational records must still exclude credentials, tokens, personal data, and unnecessary sensitive information.

### 4.2 Change or deployment summary

**Audience**

* Test Lead;
* implementation process;
* technical reviewer where applicable.

**Purpose**

To determine whether a change can proceed to the next quality gate.

**Typical decisions**

* continue local development;
* return the change for correction;
* begin retesting;
* begin impact-based regression;
* authorise push;
* authorise deployment;
* require additional evidence;
* accept or reject a limited exception.

### 4.3 Release-level summary

**Audience**

* Test Lead/Product Owner;
* future collaborators;
* authorised QA Managers or Test Managers.

**Purpose**

To support the V1.0 release recommendation.

**Typical decisions**

* `Approved`;
* `Approved with accepted residual risk`;
* `Blocked`.

### 4.4 Public portfolio evidence

**Audience**

* recruiters;
* QA Managers;
* Test Managers;
* public portfolio readers.

**Purpose**

To demonstrate:

* risk-based thinking;
* test leadership;
* defect analysis;
* release decision-making;
* evidence review;
* responsible use of metrics;
* transparent treatment of limitations.

Public reporting must be sanitised and must not expose:

* personal data;
* private user information;
* credentials;
* tokens;
* private configuration;
* unnecessary internal code;
* identifiable payloads;
* sensitive URLs.

---

## 5. Reporting levels

AtlasBadge uses four proportional reporting levels.

### 5.1 Change-level checkpoint

A concise checkpoint is produced after a change with material quality impact.

It records:

* the change or objective;
* affected risks;
* validation performed;
* defects;
* quality gates;
* limitations;
* evidence;
* Test Lead decision;
* next action.

It is not a full release report.

### 5.2 Deployment checkpoint

A deployment checkpoint records:

* approved commit or hash;
* deployed commit or hash;
* version correspondence;
* required quality gates;
* accepted exceptions;
* deployment result;
* production smoke result;
* rollback or containment information;
* Test Lead decision.

### 5.3 Release-level report

The V1.0 release-level assessment will consolidate:

* scope;
* risk status;
* test execution;
* regression;
* defects;
* quality gates;
* environments;
* non-functional coverage;
* evidence;
* limitations;
* accepted risks;
* readiness;
* final recommendation.

The detailed release report will be created later as:

```text
reports/test-summary-report.md
```

### 5.4 Portfolio-level view

The public portfolio view contains selected, sanitised information showing:

* the quality problem or risk;
* the evidence considered;
* the decision supported;
* the coverage performed;
* significant defects or exceptions;
* residual risk;
* outcome;
* lessons learned.

It does not reproduce the entire operational record.

---

## 6. Data sources and classification

A reported result must identify or make clear its source.

### 6.1 Current data sources

Current or expected sources include:

* GitHub Issues;
* Git commits and commit hashes;
* portfolio documents;
* risk records;
* defect records;
* test scripts;
* Firebase Emulator results;
* Firestore Rules test results;
* TypeScript checks;
* ESLint results;
* build results;
* version-control checks;
* Vercel deployment status;
* manual QA notes;
* exploratory-test notes;
* browser and device records;
* sanitised screenshots;
* production smoke evidence;
* files stored under `evidence/v1.0/`.

### 6.2 Automatically collected data

Examples include:

* command exit result;
* automated-test result;
* TypeScript result;
* ESLint result;
* build result;
* emulator-test output;
* deployment status;
* commit hash.

Automatically produced data is not automatically correct. It may still be:

* incomplete;
* stale;
* associated with the wrong commit;
* misinterpreted;
* produced by an invalid test;
* unavailable.

### 6.3 Manually recorded data

Examples include:

* exploratory observations;
* visual results;
* browser and device coverage;
* usability findings;
* evidence review;
* residual-risk assessment;
* known limitations;
* Test Lead decisions.

Manual data requires consistent definitions and adequate evidence.

### 6.4 Derived or interpreted data

Examples include:

* release readiness;
* cumulative defect impact;
* change in risk status;
* sufficiency of regression;
* acceptability of an environment gap;
* evidence completeness;
* release recommendation.

Derived information must identify the facts on which the interpretation is based.

### 6.5 Estimated data

Estimates may be used only when they are necessary and explicitly labelled.

An estimate must not be presented as:

* an observed result;
* a measured value;
* a confirmed fact;
* a passed gate.

### 6.6 Unavailable or uncollected data

When information does not exist, the report must state:

* what is missing;
* why it is missing;
* whether it matters;
* whether alternative evidence exists;
* how it affects confidence.

---

## 7. Data-quality controls

A metric without a clear definition and traceable source must not be reported as fact.

### 7.1 Required controls

Each indicator should define, where applicable:

* unit of analysis;
* scope;
* measurement period;
* source;
* result state;
* treatment of reruns;
* treatment of retests;
* treatment of partial executions;
* treatment of inconclusive results;
* treatment of duplicates;
* treatment of historical corrections;
* treatment of process exceptions.

### 7.2 Test execution rules

* A rerun does not create a new test case.
* A rerun does not erase the original result.
* A retest is distinct from the original execution.
* Regression execution is distinct from direct retesting.
* A partially executed test is not reported as fully executed.
* A blocked test is not reported as failed unless the product itself caused the failure and the expected result was not achieved.
* An inconclusive result is neither passed nor failed.
* A test that is no longer valid must not be included as successful coverage.
* Automated assertions must not be presented as separate business test cases solely to increase counts.

### 7.3 Defect-data rules

* Duplicate records do not increase the number of unique defects.
* Severity changes must preserve the previous value in the record history.
* Priority changes must preserve the previous value in the record history.
* Reopened defects retain their original identity.
* Environment incidents must not be counted as product defects.
* Enhancements and technical tasks must not be counted as defects.
* A rejected implementation does not automatically represent a product defect.
* A production escape must be based on evidence that the defect existed in a deployed version.

### 7.4 Historical corrections

When historical information is corrected:

* the reason must be recorded;
* the previous interpretation must not be silently erased when it influenced a decision;
* affected summaries must be updated;
* the correction date should be identifiable where material.

### 7.5 Missing data

Missing data must not be converted to:

* zero;
* passed;
* no defects;
* no risk;
* complete coverage.

---

## 8. Small-sample interpretation

AtlasBadge currently has a limited volume of structured QA data.

This affects how results should be communicated.

### 8.1 Preferred presentation

For small samples, reports should use:

* absolute counts;
* visible denominators;
* itemised risks;
* named blockers;
* descriptive interpretation;
* explicit limitations.

Examples:

```text
1 of 2 targeted scenarios passed.
```

```text
Two High risks were reviewed. One has sufficient regression evidence; one remains an assessment gap.
```

```text
One defect was found, but it blocks the primary user journey.
```

### 8.2 Percentages

A percentage may be used only when:

* the denominator is clear;
* the included items are comparable;
* the sample is sufficient for the decision;
* absolute values remain visible;
* the percentage does not hide a blocker;
* the definition is repeatable.

A fixed universal minimum sample size is not required.

The Test Lead determines whether the result is interpretable in context.

### 8.3 Averages

Averages should not be used when:

* the sample is too small;
* extreme values are decision-relevant;
* the underlying cases are not comparable;
* the average hides a blocked or failed condition.

Where used, distributions or individual significant values should remain visible.

### 8.4 Trends

A trend may be reported only when:

* the indicator definition is unchanged;
* scope is comparable;
* the data source is consistent;
* the measurement periods or releases are comparable;
* process changes are disclosed;
* the available sample supports responsible interpretation.

Otherwise, use:

```text
Insufficient comparable data to establish a trend.
```

An increase in recorded defects after expanding test coverage must not automatically be described as declining product quality.

---

## 9. Indicator model

Indicators are divided into four groups.

### 9.1 Core indicators

Core indicators are used at the relevant checkpoints because they directly support quality, deployment, or release decisions.

They include:

* High quality-risk status;
* test-execution status;
* release blockers;
* open defects by severity and priority;
* direct retest status;
* required regression status;
* required smoke status;
* quality-gate compliance;
* environment and version integrity;
* evidence sufficiency;
* accepted risk and process exceptions;
* release readiness.

### 9.2 Conditional indicators

Conditional indicators are used only when sufficient data and a useful decision exist.

They may include:

* defects by area;
* defects by environment;
* defects by detection phase;
* recurring root-cause categories;
* reopened defects;
* production escapes;
* automation reliability;
* flaky or disabled tests;
* performance measurements;
* accessibility findings;
* compatibility findings;
* comparison between releases.

### 9.3 Descriptive information

Some information should remain descriptive rather than numerical.

Examples include:

* untested browsers or devices;
* accepted limitations;
* environment restrictions;
* evidence gaps;
* quality-gate exceptions;
* production-only scenarios;
* residual uncertainty;
* reason for an inconclusive result;
* rejected technical approach;
* rollback decision.

### 9.4 Not currently used

The following are not currently used as AtlasBadge quality indicators:

* single quality score;
* numerical release-readiness percentage;
* raw test-case total as a quality measure;
* overall pass-rate target;
* automation percentage target;
* defect density;
* lines of code;
* number of commits;
* individual productivity metrics;
* mean time to repair;
* average defect-resolution time;
* SLA compliance;
* maximum permitted defect count.

Defect dates remain in the operational defect records for traceability, but time-based defect metrics are not currently calculated or reported.

---

## 10. Indicator-definition model

Formal indicators should contain enough information to prevent ambiguous interpretation.

Where applicable, each indicator should identify:

* **Indicator name**
* **Purpose and decision supported**
* **Definition or data source**
* **Scope and unit**
* **Interpretation**
* **Limitations**
* **Required response**
* **Reporting classification**

Reporting classifications may include:

* operational;
* change-level;
* deployment;
* release;
* public portfolio.

Frequency and ownership may be defined for the indicator group rather than repeated for every item.

Descriptive information does not require a complete indicator definition.

---

## 11. Quality-risk reporting

Quality-risk reporting must show the state of important risks rather than only the total number of risk records.

### 11.1 Core risk information

Reports should identify, where relevant:

* risks by priority;
* current state;
* High risks without sufficient coverage;
* risks covered by manual testing;
* risks protected by reusable automation;
* risks validated locally;
* risks with production evidence;
* accepted risks;
* deferred risks;
* assessment gaps;
* open questions;
* regression risks;
* changes in risk state.

### 11.2 Risk-coverage interpretation

A general risk-coverage percentage must not be the primary quality indicator.

It may be misleading because:

* risks do not have equal impact;
* one High risk may matter more than several Low risks;
* some risks require deeper evidence;
* manual and automated coverage provide different confidence;
* a risk may have partial coverage;
* coverage may be outdated after a change.

When a percentage is used, the report must also show:

* the risk set included;
* the priority distribution;
* coverage criteria;
* High risks separately;
* gaps and limitations.

### 11.3 Risk-state changes

A risk state may change after:

* implementation;
* investigation;
* direct testing;
* defect discovery;
* regression coverage;
* production validation;
* product-scope change;
* accepted limitation;
* final data reset.

The reason and evidence for the change must be recorded.

A changed risk record is a quality outcome, but it should not automatically be converted into a percentage or score.

### 11.4 Required response

An unresolved High risk requires explicit review before release.

It must be identified as one of the following:

* sufficiently mitigated by testing and evidence;
* reduced through a technical or product control;
* accepted with documented residual risk;
* deferred with a defined limitation and review trigger;
* release blocker.

---

## 12. Test-execution reporting

### 12.1 Result states

Test execution may use:

* `Planned`;
* `Passed`;
* `Failed`;
* `Blocked`;
* `Not Run`;
* `Inconclusive`;
* `Partially Executed`.

### 12.2 Execution type

Reports should distinguish:

* initial execution;
* rerun;
* retest;
* rapid core regression;
* impact-based regression;
* final regression;
* smoke;
* exploratory session;
* manual execution;
* automated execution.

### 12.3 Required context

Execution reporting should include, where relevant:

* scope;
* environment;
* browser;
* device;
* viewport or orientation;
* language;
* application version or commit;
* test data condition;
* result;
* defect or issue identified;
* limitations;
* evidence.

### 12.4 Interpretation

A high pass rate does not establish sufficient coverage when:

* important tests were not run;
* High risks remain untested;
* the environment was unsuitable;
* the executed tests were outdated;
* the denominator excludes blocked or inconclusive items;
* the release blocker is contained within a small failed subset.

Reports must highlight:

* failures;
* blockers;
* unexecuted critical coverage;
* inconclusive results;
* coverage gaps.

### 12.5 Required response

* `Failed` requires defect assessment or test-correction assessment.
* `Blocked` requires cause and impact review.
* `Inconclusive` requires investigation or alternative evidence.
* `Partially Executed` requires explicit remaining scope.
* `Not Run` requires justification when the test is relevant to readiness.

---

## 13. Defect reporting

Defect reporting uses the classifications and lifecycle defined in [Defect Management](07-defect-management.md).

### 13.1 Core defect indicators

Core reporting should identify:

* open defects by severity;
* open defects by priority;
* open `Critical` defects;
* open `P0` defects;
* release blockers;
* defects awaiting retest;
* failed retests;
* reopened defects;
* deferred defects;
* accepted risks;
* production escapes;
* defects preventing exit-criteria completion.

### 13.2 Conditional defect indicators

When sufficient data exists, reporting may include:

* defects by area;
* defects by environment;
* defects by detection phase;
* defects by detection source;
* recurring technical causes;
* reopen patterns;
* production-escape patterns;
* comparison between releases.

### 13.3 Interpretation

The total defect count must not be used alone.

A single `High / P0` defect may be more relevant than many Low defects.

Reports must explain:

* affected journey;
* release impact;
* workaround or containment;
* residual risk;
* required corrective action.

### 13.4 Time-based defect information

Operational dates continue to be recorded for traceability.

The following are not currently reported as metrics:

* average resolution time;
* mean time to repair;
* target time by severity;
* SLA compliance;
* time-based productivity comparison.

These indicators may be reviewed in the future if:

* a meaningful baseline exists;
* the process is stable;
* the measurement supports a real decision;
* the result is not used to assess individual productivity.

---

## 14. Quality-gate compliance

### 14.1 Applicable gates

Depending on the change, quality gates may include:

* targeted functional testing;
* automated tests;
* Firebase Emulator tests;
* Firebase Rules tests;
* TypeScript;
* ESLint;
* build;
* version-control checks;
* security review;
* evidence review;
* regression;
* approved commit verification;
* deployment verification;
* production smoke.

Not every gate applies to every change.

Applicability is determined by risk, scope, and the relevant Entry and Exit Criteria.

### 14.2 Gate results

A gate result may be:

* `Passed`;
* `Failed`;
* `Not Applicable`;
* `Not Executed`;
* `Result Not Verified`.

A failed gate may additionally carry the disposition:

```text
Failed — Exception Accepted
```

`Exception Accepted` does not mean that the gate passed.

### 14.3 Result rules

* `Passed` requires verifiable evidence.
* `Failed` remains failed even when continuation is authorised.
* `Not Applicable` requires a reason.
* `Not Executed` must not be reported as passed.
* `Result Not Verified` applies when execution is claimed but evidence is insufficient.
* A partially successful command set must not be summarised as fully green.

### 14.4 Pipeline summary

A pipeline summary must distinguish:

* gates passed;
* gates failed;
* gates not executed;
* gates not applicable;
* accepted exceptions;
* final Test Lead decision.

The word `green` must not be used for a pipeline containing an unresolved failed gate.

---

## 15. Exceptions and overrides

A quality-gate override is a controlled decision about continuation.

It does not rewrite the technical result.

### 15.1 Required exception information

An accepted exception must record:

* failed gate or unmet rule;
* technical evidence;
* exception type;
* affected artefact or process;
* impact;
* residual risk;
* reason for continuation;
* scope of authorisation;
* permitted action;
* corrective action, where required;
* review trigger;
* Test Lead approval.

### 15.2 Permitted decision scope

An exception must state whether it authorises:

* continued local validation;
* push;
* deployment;
* release.

Authorisation at one stage does not automatically authorise the next stage.

### 15.3 Exception classifications

Exceptions may be classified as:

* product quality issue;
* code-quality issue;
* test-artefact quality issue;
* environment limitation;
* process non-compliance;
* evidence gap.

### 15.4 Restrictions

An override must not be used to accept an active and uncontained risk involving:

* unauthorised access;
* personal-data exposure;
* serious privacy failure;
* severe data corruption or loss;
* critical authentication or authorisation failure;
* unsafe destructive behaviour;
* an unusable essential journey without a safe workaround.

### 15.5 Recurrence

Repeated exceptions involving the same gate or cause require process review.

An exception must not become an undocumented permanent alternative to compliance.

---

## 16. Automation and regression health

Automation health is assessed by usefulness, reliability, and risk protection.

It is not assessed primarily by test count or automation percentage.

### 16.1 Relevant information

Reports may include:

* available suites;
* purpose of each suite;
* latest relevant execution;
* pass and fail results;
* blocked tests;
* flaky tests;
* disabled or skipped tests;
* outdated tests;
* risks protected by automation;
* regressions detected;
* tests added after significant defects;
* critical gaps that remain manual.

### 16.2 Automation percentage

Automation percentage is not a target.

It may be shown only when:

* the denominator is defined;
* scope is comparable;
* risk priority is visible;
* the result is not used as a quality score;
* the limitations are stated.

### 16.3 Test reliability

A failing automated test must be investigated to determine whether it represents:

* product defect;
* valid regression;
* environment failure;
* unstable test;
* outdated test;
* incorrect expected result;
* inconclusive result.

An unstable or outdated test must not be counted as successful product coverage.

### 16.4 Regression protection

A new reusable test is valuable when it:

* protects an important risk;
* prevents recurrence of a confirmed defect;
* validates a stable business rule;
* reduces repetitive manual effort;
* produces understandable evidence.

Its value does not depend on increasing a general automation percentage.

---

## 17. Environment and deployment reporting

### 17.1 Environment information

Reports should identify, where applicable:

* local application environment;
* Firebase Emulator context;
* Vercel production;
* persistence destination;
* browser;
* browser version;
* device;
* operating system;
* viewport;
* orientation;
* language;
* relevant environment limitation.

### 17.2 Version integrity

Deployment reporting must identify:

* commit or hash tested;
* commit or hash approved;
* commit or hash deployed;
* whether they match;
* any difference introduced after approval;
* additional validation required because of a difference.

A deployment cannot inherit approval automatically when the deployed version differs from the approved version.

### 17.3 Deployment result

Deployment reporting should include:

* deployment state;
* relevant quality-gate results;
* accepted exceptions;
* production smoke result;
* production-only test result;
* rollback or containment status;
* final Test Lead decision.

### 17.4 Environment incidents

Environment incidents must be recorded separately from product defects.

Examples include:

* local cache corruption;
* temporary Turbopack cache behaviour;
* unavailable emulator service;
* temporary network interruption;
* browser extension interference;
* unavailable Vercel or Firebase service.

An environment incident may affect test confidence or readiness without increasing the product-defect count.

### 17.5 Environmental limitations

The absence of coverage on Safari, Firefox, iPhone, iPad, macOS, or other unavailable combinations must remain visible.

It must not be interpreted as confirmed compatibility.

---

## 18. Non-functional quality reporting

Non-functional quality is reported by dimension.

AtlasBadge does not use a combined non-functional score.

### 18.1 Relevant dimensions

Where applicable, reports may cover:

* performance;
* accessibility;
* responsiveness;
* compatibility;
* usability;
* privacy;
* security;
* resilience.

### 18.2 Required information

Each reported dimension should identify:

* scope evaluated;
* method;
* environment;
* observed result;
* defects or gaps;
* quality risk;
* limitation;
* decision supported.

### 18.3 Performance

Performance results may be numerical only when there is:

* a defined interaction;
* a repeatable method;
* a known environment;
* an approved baseline or threshold;
* sufficient context.

Perceived responsiveness may be described when no quantitative baseline exists, but it must not be presented as a measured performance result.

### 18.4 Accessibility

Accessibility reporting may include:

* manual findings;
* keyboard behaviour;
* focus behaviour;
* semantics;
* contrast;
* screen-size behaviour;
* colour-independent communication;
* automated-tool findings;
* WCAG 2.2 AA assessment results when available.

An automated accessibility score does not prove WCAG conformance.

### 18.5 Compatibility and responsiveness

Compatibility reports must state the combinations actually tested.

Untested combinations remain limitations, not implicit passes.

### 18.6 Privacy and security

Privacy and security are reported through:

* controls validated;
* risks assessed;
* defects found;
* unauthorised scenarios tested;
* evidence available;
* residual risk.

They must not be reduced to a generic percentage.

---

## 19. Evidence completeness

Evidence completeness means that the available material is sufficient to support the quality decision.

It does not mean that the largest possible number of screenshots or logs has been collected.

### 19.1 Evidence states

Evidence may be classified as:

* `Complete`;
* `Complete with Limitations`;
* `Incomplete`;
* `Not Applicable`;
* `Not Verified`.

### 19.2 Completeness criteria

Depending on the checkpoint, evidence may need to identify:

* risk or requirement;
* environment;
* version or commit;
* expected result;
* observed result;
* test outcome;
* defect;
* retest;
* regression;
* quality gate;
* deployment;
* smoke result;
* Test Lead decision.

### 19.3 Evidence percentage

An evidence-completeness percentage may be used only when an objective and proportional checklist exists.

Otherwise, use the evidence state and a narrative explanation.

### 19.4 Public evidence

Public portfolio evidence is a sanitised subset of operational evidence.

A public item may be considered complete for portfolio purposes even when sensitive operational details are deliberately omitted, provided that:

* the decision remains understandable;
* the evidence is not misleading;
* the omission is acknowledged where relevant;
* private information is not exposed.

---

## 20. Baselines, targets, thresholds, and trends

### 20.1 Mandatory thresholds

Thresholds are reserved for genuine safety and readiness conditions.

The current mandatory conditions include:

* zero open `Critical` release defects;
* zero uncontained `P0` release blockers;
* zero active and uncontained privacy, access-control, or severe data-integrity risks;
* required regression passed;
* required production smoke passed;
* required environment available;
* approved and deployed commit identity verified;
* mandatory evidence available;
* applicable Entry and Exit Criteria satisfied;
* no unresolved release blocker.

### 20.2 Guidelines

Guidelines may include:

* testing depth proportional to risk;
* High defects addressed before release unless an allowed exception is approved;
* meaningful evidence retained for High-impact changes;
* important risks supported by repeatable regression coverage.

A guideline requires judgement and is not an automatic mathematical gate.

### 20.3 Baselines

Baselines may be established for:

* suite duration;
* selected interaction performance;
* defect distribution;
* automation reliability;
* browser or device execution time;
* recurring quality-gate results.

A baseline describes the current state. It is not automatically a target.

### 20.4 Trend-only information

Trend-only information may include:

* automation growth;
* recurring technical causes;
* performance evolution;
* repeated gate exceptions;
* defect distribution between releases;
* expansion of risk coverage.

Trend claims require comparable data.

### 20.5 No current target

No current target is defined for:

* overall pass rate;
* automation percentage;
* defect density;
* total number of tests;
* total number of defects;
* average resolution time;
* mean time to repair;
* commit volume;
* lines of code.

---

## 21. Release-readiness reporting

AtlasBadge uses `Green`, `Amber`, and `Red` as a concise readiness summary.

The colour is not a score and is not calculated through an average.

### 21.1 Red

`Red` takes precedence when any of the following applies:

* an open `Critical` defect exists;
* a `P0` remains without approved containment;
* a prohibited active risk exists;
* a release blocker exists;
* required regression failed or is incomplete;
* mandatory smoke failed;
* an essential environment is unavailable;
* evidence is insufficient for a safe decision;
* the approved and deployed versions do not correspond;
* exit criteria are not satisfied;
* the Test Lead does not recommend progression.

### 21.2 Amber

`Amber` applies when:

* no Red condition exists;
* limitations remain;
* accepted risks exist;
* a quality-gate exception exists;
* coverage is partial but may be sufficient under defined conditions;
* an action remains pending before the next decision;
* progression is possible only under explicit conditions.

`Amber` does not mean nearly Green.

It means that the conditions and residual risks require explicit explanation.

### 21.3 Green

`Green` applies when:

* applicable mandatory gates are satisfied;
* no release blocker remains;
* required regression passed;
* required smoke passed;
* evidence is sufficient;
* residual risks are assessed and acceptable;
* environment and version integrity are confirmed;
* the Test Lead recommends progression.

### 21.4 Relationship with formal decisions

The formal decisions remain:

* `Approved`;
* `Approved with accepted residual risk`;
* `Blocked`.

Typical relationships are:

| Readiness | Possible formal decision                                            |
| --------- | ------------------------------------------------------------------- |
| Green     | Approved                                                            |
| Amber     | Approved with accepted residual risk, or Blocked pending conditions |
| Red       | Blocked                                                             |

The relationship is not an automatic mapping.

The Test Lead records both the readiness summary and the formal decision.

---

## 22. Reporting cadence

AtlasBadge reporting is event-driven rather than calendar-driven.

No daily meeting, weekly steering report, formal SLA, or 24/7 reporting operation is currently defined.

A checkpoint or update is produced when:

* a quality-relevant change is completed;
* a `Critical`, `High`, `P0`, or `P1` defect is identified;
* a relevant quality risk changes;
* a correction becomes ready for retest;
* material regression is completed;
* a quality gate fails;
* an exception is accepted;
* deployment is considered;
* deployment completes;
* production smoke completes;
* a production incident occurs;
* final regression reaches a significant milestone;
* a release decision is required.

Minor issues may remain in execution notes and be consolidated in the next relevant checkpoint.

---

## 23. Change-level checkpoint

A change-level checkpoint should remain concise.

A recommended structure is:

```markdown
## Change checkpoint

### Change
- Objective:
- Commit or version:
- Affected areas:

### Risks
- Related quality risks:
- Risk-state change:

### Validation
- Tests executed:
- Environment:
- Browser or device:
- Results:

### Defects
- New defects:
- Retested defects:
- Remaining defects:

### Quality gates
- Passed:
- Failed:
- Not executed:
- Exceptions:

### Evidence and limitations
- Evidence:
- Gaps:
- Residual risk:

### Test Lead decision
- Readiness:
- Formal decision:
- Next gate or action:
```

The checkpoint should separate:

```text
Facts
Interpretation
Decision
```

Example:

```text
Facts:
- Targeted functional tests passed.
- TypeScript and build passed.
- The whitespace check failed.

Interpretation:
- The product behaviour is acceptable.
- The test artefact or repository hygiene remains non-compliant.

Decision:
- Push authorised under a limited and recorded quality-gate exception.
```

---

## 24. Deployment checkpoint

A recommended deployment checkpoint contains:

```markdown
## Deployment checkpoint

### Approved version
- Tested commit:
- Test Lead-approved commit:

### Quality gates
- Required gates:
- Passed gates:
- Failed gates:
- Accepted exceptions:

### Deployment
- Deployed commit:
- Version match:
- Deployment result:

### Production validation
- Smoke scope:
- Smoke result:
- Production-only checks:
- Issues identified:

### Protection
- Rollback available:
- Containment available:
- Residual risk:

### Test Lead decision
- Readiness:
- Formal decision:
- Follow-up actions:
```

A deployment marked `Ready` by Vercel does not by itself represent product approval.

---

## 25. Release-level reporting

The future V1.0 Test Summary Report should include:

1. release identifier and evaluated commit;
2. purpose and scope;
3. included and excluded items;
4. quality-risk position;
5. test-execution summary;
6. direct retest summary;
7. regression summary;
8. smoke summary;
9. relevant open and closed defects;
10. release blockers;
11. quality-gate compliance;
12. accepted exceptions;
13. environments, browsers, devices, and languages;
14. non-functional quality results;
15. evidence status;
16. unexecuted scenarios;
17. known limitations;
18. deferred defects;
19. accepted risks;
20. readiness status;
21. formal Test Lead recommendation;
22. post-release conditions or actions.

The report should reference detailed sources rather than copy the contents of every portfolio document.

---

## 26. Public portfolio reporting

Public reporting should focus on decisions rather than volume.

A selected public item may contain:

* context;
* affected risk;
* expected and observed behaviour;
* relevant test coverage;
* defect severity and priority;
* quality-gate results;
* exception or limitation;
* retest and regression;
* deployment and smoke outcome;
* residual risk;
* Test Lead decision;
* lesson learned.

Public reporting must not hide meaningful failures merely to appear more successful.

A controlled and well-explained failure may demonstrate stronger QA leadership than an unsupported claim of `100% passed`.

---

## 27. Practical interpretation examples

### 27.1 Silent country-status persistence failure

The country-status incident demonstrates why a total defect count or general pass rate would be insufficient.

Relevant information includes:

* the primary journey was blocked;
* both new and existing accounts were affected;
* no persistence occurred;
* the interface did not provide failure feedback;
* the defect was assessed as `High / P0`;
* the issue blocked progression;
* the technical cause was supported by evidence;
* targeted retesting was completed;
* regression identified secondary issues;
* secondary corrections were validated;
* production smoke was completed;
* closure remained subject to Test Lead approval.

The appropriate readiness state remained `Red` until:

* the P0 condition was removed;
* direct retesting passed;
* required regression passed;
* deployment was controlled;
* smoke passed;
* sufficient evidence existed.

### 27.2 QR-02 preservation investigation

The QR-02 investigation demonstrates why risk status, defect status, and implementation history must remain separate.

The available evidence showed that:

* the current V1.0 model preserved registered visits and related information;
* the originally feared failure was not reproduced in the current model;
* an incompatible legacy count-only model existed only in disposable pre-V1.0 test data;
* the planned complete production reset removed the need for migration;
* reusable regression coverage was added.

The appropriate reporting is primarily narrative:

* current-model behaviour validated;
* legacy test-data limitation identified;
* risk state changed or scheduled for correction in the risk register;
* regression protection added;
* no production correction required.

It should not automatically be reported as:

* a product defect fixed;
* a production escape;
* a defect-resolution success rate;
* a numerical improvement in product quality.

### 27.3 Rejected implementation and revert

An unsuitable implementation that fabricated visits was rejected and reverted.

This should be recorded as:

* technical review outcome;
* rejected approach;
* preserved Git history;
* evidence that the quality process prevented an unsuitable change.

It is not automatically counted as a product defect unless the defective behaviour entered a testable or deployed product state and met the formal defect criteria.

### 27.4 Quality-gate exception

In the final QR-02 pipeline:

* functional and technical tests passed;
* TypeScript passed;
* build passed;
* whitespace checks failed;
* ESLint warnings remained;
* push was authorised.

The correct reporting is:

```text
Product validation: Passed
TypeScript: Passed
Build: Passed
Whitespace checks: Failed — Exception Accepted
ESLint: Passed with warnings, or the precise tool result recorded
Overall pipeline: Not fully green
Push decision: Authorised by the Test Lead under a limited exception
```

The product result and the quality-gate result must not be merged into one misleading status.

---

## 28. Roles and responsibilities

### 28.1 Tools and automation

Tools may:

* execute commands;
* provide logs;
* provide test results;
* provide commit and deployment identifiers;
* identify technical failures;
* generate technical artefacts.

Tools do not determine release readiness independently.

### 28.2 Implementation or AI-assisted process

The implementation process may:

* execute tests and checks;
* consolidate preliminary results;
* identify limitations;
* propose indicator interpretation;
* prepare draft checkpoints;
* suggest corrective actions;
* identify missing evidence.

It must not:

* present hypotheses as confirmed facts;
* conceal failed gates;
* approve its own work;
* accept residual risk;
* approve deployment;
* approve release.

### 28.3 Test Lead/Product Owner

The Test Lead/Product Owner:

* selects indicators;
* validates definitions;
* checks sources;
* assesses data quality;
* separates facts from interpretation;
* evaluates limitations;
* determines required action;
* accepts or rejects quality-gate exceptions;
* determines readiness;
* approves reporting;
* communicates residual risk;
* approves or blocks deployment;
* provides the final release recommendation;
* prevents misleading use of metrics.

---

## 29. Privacy and publication controls

Reports and evidence must not contain:

* UID values;
* real e-mail addresses;
* private usernames;
* passwords;
* tokens;
* credentials;
* environment variables;
* private URLs;
* identifiable payloads;
* real user data;
* private medical or personal information;
* sensitive configuration;
* unnecessary private code.

Operational records, release summaries, and public evidence may contain different levels of detail, but none may expose protected information.

Public evidence must be reviewed and sanitised before publication.

---

## 30. Current constraints

The current metrics and reporting process is primarily manual.

Currently available capabilities include:

* GitHub Issues;
* Git history and commit hashes;
* scripted-test results;
* technical command results;
* Vercel deployment status;
* manual QA notes;
* sanitised evidence;
* browser and device records;
* risk register;
* defect records;
* Test Lead interpretation and approval.

The following are not currently established:

* automatic metrics dashboard;
* complete automated metrics pipeline;
* GitHub Project-based reporting;
* formal test-management tool;
* mature historical baseline;
* stable cross-release trend dataset;
* automatic Test Summary Report generation;
* comprehensive CI quality dashboard;
* formal SLA reporting;
* statistically significant defect dataset.

These limitations must not be hidden or represented as implemented capabilities.

---

## 31. Planned improvements

Potential improvements include:

* reusable change-checkpoint template;
* reusable deployment-checkpoint template;
* consistent machine-readable result output;
* stronger linkage between risks, tests, defects, and evidence;
* automated capture of commit and deployment identity;
* gradual automation of quality-gate result collection;
* reusable release-report structure;
* possible GitHub Project views;
* progressive baseline establishment;
* repeatable performance measurement for selected interactions;
* structured accessibility evidence;
* broader browser and device evidence;
* improved automation-health reporting.

These items remain planned until implemented and evidenced.

---

## 32. Review triggers

This document and its reporting model must be reviewed when:

* the Quality Risk Analysis changes materially;
* the Test Strategy changes;
* the Test Scope changes;
* the Entry and Exit Criteria change;
* the Test Environments change;
* the Defect Management process changes;
* new quality gates are introduced;
* a new browser or device becomes supported;
* a new environment is introduced;
* Playwright automation is implemented;
* continuous integration is introduced;
* the System Test Plan is created;
* the Test Summary Report is created;
* evidence structure changes;
* a significant production escape occurs;
* the same quality-gate exception recurs;
* enough data exists to establish a useful baseline;
* an indicator proves misleading or unhelpful;
* a dashboard or GitHub Project is introduced;
* AtlasBadge V1.0 reaches formal release preparation;
* a future major release begins.

---

## 33. Related documents

* [Product Overview](01-product-overview.md)
* [Quality Risk Analysis](02-quality-risk-analysis.md)
* [Test Strategy](03-test-strategy.md)
* [Test Scope](04-test-scope.md)
* [Entry and Exit Criteria](05-entry-exit-criteria.md)
* [Test Environments](06-test-environments.md)
* [Defect Management](07-defect-management.md)
* [V1.0 Test Evidence](../evidence/v1.0/README.md)
* `docs/09-system-test-plan.md` — planned
* `test-assets/exploratory-test-charters.md` — planned
* `test-assets/sample-test-cases.md` — planned
* `reports/test-summary-report.md` — planned
