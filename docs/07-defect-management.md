# AtlasBadge Defect Management

## 1. Document purpose

This document defines the defect-management approach used for AtlasBadge.

It establishes how potential defects are:

* identified;
* reviewed;
* classified;
* recorded;
* prioritised;
* investigated;
* corrected;
* retested;
* submitted to regression;
* deferred or accepted as residual risk;
* reopened;
* closed;
* communicated;
* considered in deployment and release decisions.

The process applies to incremental development, local quality validation, regression testing, production validation, preparation for AtlasBadge V1.0, and future releases.

The approach is intentionally proportional to the product and delivery model. It provides professional traceability and release governance without introducing unnecessary administrative stages or roles that do not exist in the AtlasBadge workflow.

> **Document status:** Initial completed version based on the current AtlasBadge implementation, the existing portfolio documents, and the defect-management decisions confirmed by the Test Lead.

---

## 2. Relationship with other portfolio documents

This document expands the defect-management principles already established in the portfolio.

It does not replace:

* the product and business context defined in [Product Overview](01-product-overview.md);
* the quality risks and test priorities defined in [Quality Risk Analysis](02-quality-risk-analysis.md);
* the overall validation, regression, evidence, and quality-ownership model defined in [Test Strategy](03-test-strategy.md);
* the functional and non-functional coverage defined in [Test Scope](04-test-scope.md);
* the conditions for testing, retesting, regression, deployment, production smoke testing, and release approval defined in [Entry and Exit Criteria](05-entry-exit-criteria.md);
* the environment, test-data, production, and evidence controls defined in [Test Environments](06-test-environments.md).

The Quality Risk Analysis evaluates what might fail and how strongly each risk should influence test coverage. This document governs actual suspected or confirmed failures.

Quality-risk priority, defect severity, defect priority, and test-scope priority are related but distinct concepts:

* **quality-risk priority** determines the strength and sequencing of preventive test coverage;
* **defect severity** describes the impact of a materialised failure;
* **defect priority** determines the urgency and order of corrective action;
* **test-scope priority** determines the depth and importance of planned validation.

These classifications must not be used interchangeably.

---

## 3. Defect-management principles

AtlasBadge defect management follows these principles:

1. **Observed behaviour must be compared with an agreed expectation.**
   A difference of opinion, undefined requirement, or newly desired capability is not automatically a product defect.

2. **Confirmation of a defect does not require a known root cause.**
   A defect may be confirmed when evidence shows that the product diverges from the expected behaviour. Root-cause investigation may follow.

3. **Severity and priority remain independent.**
   Severity describes impact. Priority describes urgency and correction order.

4. **Documentation is proportional to risk and traceability needs.**
   Significant failures require formal records and stronger evidence. A minor issue found, corrected, and fully validated within the same low-risk execution may remain in the execution notes.

5. **Implementation completion is not defect closure.**
   A reported correction must pass direct retesting, applicable regression, residual-risk assessment, and Test Lead validation.

6. **Evidence takes precedence over technical confidence.**
   A plausible explanation, including one produced by an AI tool, is not automatically a confirmed root cause.

7. **Defect decisions are risk-based rather than purely numerical.**
   A combination of lower-severity defects may create enough cumulative risk to block a release.

8. **Production response begins with user and data protection.**
   Containment may precede full investigation when the active impact requires immediate control.

9. **Sensitive information is sanitised before being recorded or shared.**
   Operational convenience does not justify exposing personal data, credentials, tokens, private content, or unnecessary security details.

10. **Final quality authority remains with the Test Lead.**
    AI-assisted tools and the technical delivery process may investigate, implement, execute tests, and recommend decisions, but they do not independently approve risk, deployment, release, or closure.

---

## 4. Definitions and classification boundaries

### 4.1 Product defect

A product defect is an implemented AtlasBadge behaviour that diverges from an approved requirement, business rule, expected result, safety condition, or established product behaviour.

A product defect may be functional or non-functional and may involve:

* authentication or authorisation;
* persistence or data integrity;
* privacy;
* travel statuses and their combinations;
* visits, memories, and notes;
* calculations, statistics, badges, or achievements;
* map interaction;
* personal or public profiles;
* account deletion;
* performance;
* compatibility;
* accessibility;
* usability;
* error handling;
* user feedback.

A root cause in configuration, infrastructure, or a third-party integration may still result in a product defect when the deployed product fails for users. Root-cause category and user-facing classification are assessed separately.

### 4.2 Environment incident

An environment incident is a failure caused by the local or test environment without sufficient evidence that the released product behaviour is defective.

Examples may include:

* a corrupted local build cache;
* a temporary Turbopack cache problem;
* unavailable local services;
* emulator startup failure;
* temporary network instability;
* browser extension interference;
* stale local assets;
* test-machine resource exhaustion.

An environment incident is not counted as a product defect unless investigation shows that the product is expected to handle the condition and fails to do so.

### 4.3 Configuration issue

A configuration issue is caused by an incorrect, incomplete, incompatible, or outdated setting.

Examples include:

* an incorrect environment variable;
* an unsupported local configuration;
* a missing emulator connection;
* a deployment setting that differs from the approved version.

When a configuration issue reaches users or creates unsafe production behaviour, it must be managed with the same urgency and evidence controls as a production defect even if its technical root cause is configuration.

### 4.4 Known limitation

A known limitation is an understood restriction of the current product or technical solution that does not violate the behaviour currently promised.

A limitation must not be used to disguise:

* a broken acceptance criterion;
* an unsafe condition;
* a missing mandatory V1.0 capability;
* a regression;
* a behaviour previously represented as supported.

### 4.5 Enhancement

An enhancement is a proposed change that adds or improves capability, usability, presentation, or efficiency without correcting a failure against the current expectation.

A desired feature that was never part of the agreed behaviour is an enhancement, not a Low-severity defect.

### 4.6 Technical task

A technical task is internal engineering work such as:

* refactoring;
* dependency maintenance;
* tooling;
* test infrastructure;
* logging improvement;
* code organisation;
* build optimisation;
* automation maintenance.

It may reduce quality risk but does not represent a confirmed user-facing defect by itself.

### 4.7 Quality risk

A quality risk is a credible possibility of future failure that has not yet materialised as an observed product defect.

Quality risks are managed in the Quality Risk Analysis. They may be linked to defect records when a failure materialises.

### 4.8 Inconclusive test result

A test result is inconclusive when the available evidence is insufficient to determine whether:

* the product passed;
* the product failed;
* the test expectation was correct;
* the environment was reliable;
* the automated result was valid.

An inconclusive result is not automatically passed, failed, or classified as a product defect. It requires investigation proportional to the potential impact.

### 4.9 Not a defect

`Not a Defect` applies when investigation confirms that the observed behaviour:

* matches the approved expectation;
* results from incorrect use;
* reflects an explicitly accepted product rule;
* is outside the supported scope;
* is a product question rather than an implementation failure.

### 4.10 Duplicate

A duplicate is an item already represented by an existing defect record.

The duplicate record should reference the original item and preserve any new evidence that expands the known conditions, impact, or reproducibility.

---

## 5. Initial investigation and formal confirmation

An unexpected result should be reviewed before it is treated as a confirmed product defect.

The initial review should establish, where applicable:

* the expected behaviour;
* the observed behaviour;
* the environment;
* the affected version, commit, or deployment;
* the user or account state;
* the relevant test data;
* whether the result can be reproduced;
* whether a known configuration or environment incident explains the result;
* whether the expectation itself is current and correct;
* whether sufficient evidence exists to continue investigation.

The item may remain `Under Review` while these questions are unresolved.

A root cause is not required before confirmation. A defect may move to `Confirmed` when there is sufficient evidence that the product is involved and the observed behaviour diverges from an approved expectation.

---

## 6. When formal defect recording is required

A formal defect record is mandatory when any of the following applies:

* the defect is found in production;
* severity is `Critical` or `High`;
* priority is `P0` or `P1`;
* the defect affects deployment, release, or acceptance criteria;
* data integrity, authentication, authorisation, privacy, security, or account isolation may be affected;
* an essential user journey is blocked or unreliable;
* the defect requires material investigation;
* the defect is recurrent, intermittent, or difficult to reproduce;
* a correction requires explicit retest or regression tracking;
* the defect will be deferred;
* residual risk may be accepted;
* the issue requires containment, rollback, or a hotfix;
* several lower-severity defects may create a release-blocking combined impact;
* the history will provide meaningful traceability for a future release or quality review.

A `Medium` or `Low` issue may remain only in execution notes when all of the following are true:

* it is isolated and well understood;
* it is corrected during the same execution;
* direct validation confirms the correction;
* no meaningful regression risk remains;
* it does not affect release readiness;
* it is not recurrent;
* it does not involve data, privacy, access, security, or essential behaviour;
* a permanent record would not add useful traceability.

If such an issue recurs, expands in impact, remains unresolved, or becomes relevant to a release decision, a formal defect record must be created.

---

## 7. Operational source of truth and identifiers

### 7.1 Source of truth

GitHub Issues in the AtlasBadge product repository are the operational source of truth for formally recorded defects.

Temporary information may exist in:

* AI-assisted investigation conversations;
* implementation reports;
* local test notes;
* console output;
* generated validation reports.

However, relevant conclusions and decisions must be consolidated into the official defect record.

The public QA portfolio contains only selected, sanitised examples. It is not the complete operational defect backlog.

### 7.2 Defect identifier

Each formal defect uses a stable identifier in the following format:

```text
AB-DEF-###
```

Example:

```text
[AB-DEF-001] Country status selection is not applied or persisted
```

The GitHub Issue number continues to exist, but `AB-DEF-###` is the portable identifier used across:

* evidence;
* test notes;
* risk references;
* commits or pull requests;
* retest results;
* regression results;
* release decisions;
* public sanitised case studies.

The identifier must not contain the area or release because these may change while the defect identity remains the same.

---

## 8. Defect record requirements

The defect record uses a two-layer structure: information required at initial registration and information added as the lifecycle progresses.

A field named `Extended description` is not used.

### 8.1 Required at initial registration

| Field                      | Requirement                                                                          |
| -------------------------- | ------------------------------------------------------------------------------------ |
| Defect ID                  | Stable `AB-DEF-###` identifier.                                                      |
| Title                      | Concise description of the failure and affected behaviour.                           |
| Date identified            | Date on which the unexpected behaviour was first recorded.                           |
| Status                     | Current lifecycle state.                                                             |
| Classification             | Product defect or current provisional classification.                                |
| Area                       | Primary affected product area.                                                       |
| Environment                | Local, emulator, production, browser, device, or other relevant context.             |
| Detected during            | Activity or test phase in which the issue was found.                                 |
| Affected version or commit | Identifiable application state, commit, build, or deployment.                        |
| Preconditions              | Required when state, account, data, configuration, or sequence affects reproduction. |
| Steps to reproduce         | Clear and repeatable actions when reproduction is possible.                          |
| Expected result            | Approved behaviour or acceptance expectation.                                        |
| Actual result              | Observed behaviour.                                                                  |
| Reproducibility            | Always, frequent, intermittent, once, or currently not reproducible.                 |
| Severity                   | `Critical`, `High`, `Medium`, or `Low`.                                              |
| Priority                   | `P0`, `P1`, `P2`, or `P3`.                                                           |
| Release impact             | Current effect on testing, deployment, release, or launch readiness.                 |
| Evidence                   | Sanitised screenshots, logs, test results, recordings, or equivalent references.     |

`Detected by` may be recorded when useful, but it is not mandatory. Discovery may involve the Test Lead, exploratory testing, an automated test, an AI-assisted tool, or a combination of these.

### 8.2 Conditional or lifecycle information

The following information is added when relevant or when it becomes available:

* related quality risks;
* related requirement or acceptance criterion;
* related test case, checklist, exploratory session, or execution;
* workaround;
* containment;
* technical investigation;
* working hypothesis;
* suspected cause;
* confirmed root cause;
* root-cause evidence;
* correction implemented;
* correction commit or pull request;
* technical checks performed;
* retest coverage;
* retest result and date;
* regression coverage;
* regression result;
* affected or target release;
* status changes;
* number of reopens;
* residual risk;
* deferral justification;
* accepted-risk justification;
* final decision;
* closure reason;
* closed by;
* closure date.

A field may be marked `Not applicable` when that decision is deliberate. It should not be left ambiguous merely because the information was not collected.

### 8.3 Traceability expectations

Every formal defect must include:

* affected area;
* environment;
* identifiable version, commit, build, or deployment;
* evidence;
* severity;
* priority;
* release impact.

Where applicable, it should also link to:

* relevant quality risks;
* requirements or acceptance criteria;
* the test execution that revealed the failure;
* the correction commit or pull request;
* the target release;
* retest evidence;
* regression evidence;
* related defects or incidents.

Traceability must be more explicit for:

* `Critical` and `High` defects;
* `P0` and `P1` defects;
* production defects;
* release blockers;
* deferred defects;
* accepted residual risks.

The absence of a formal test case does not prevent defect registration. Reproduction steps, a checklist, or an exploratory-test reference may provide sufficient initial traceability.

---

## 9. GitHub Issues implementation model

AtlasBadge uses a hybrid model.

### 9.1 Structured issue content

The Issue body contains the detailed record, including:

* expected and observed behaviour;
* environment;
* reproduction;
* evidence;
* investigation;
* correction;
* retest;
* regression;
* residual risk;
* final decision.

### 9.2 Labels

Labels should be limited to classifications that provide practical filtering value.

An initial useful set may include:

```text
type:defect

severity:critical
severity:high
severity:medium
severity:low

priority:p0
priority:p1
priority:p2
priority:p3

area:authentication
area:data
area:map
area:profile

production
release-blocker
```

Additional labels should be created only when they support real operational use.

Detailed investigation, evidence, lifecycle history, and final decisions must not be reduced to labels.

### 9.3 Lifecycle tracking

Lifecycle status may initially be recorded in the structured Issue body.

A future GitHub Project may provide dedicated fields for status, priority, area, release, and backlog views. This is a planned improvement and is not represented as an already implemented capability.

---

## 10. Severity model

Severity measures the intrinsic impact of the defect. It does not determine correction order by itself.

The classification considers:

* nature and intensity of the impact;
* data sensitivity;
* reversibility;
* affected journey;
* reach;
* frequency;
* availability and reliability of a workaround;
* accessibility;
* compatibility;
* user trust;
* ability to continue safely.

The number of users affected is relevant but does not determine severity alone. An unauthorised exposure affecting one account may be more severe than a visual issue affecting every account.

### 10.1 Severity levels

| Severity     | Definition and typical conditions                                                                                                                                                                                                                                                                                                                                               |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Critical** | Extreme or unacceptable impact. Examples include unauthorised access; exposure of personal or private data; severe or irreversible loss or corruption of relevant data; critical authentication or authorisation failure; modification or deletion of another user's data; broad product unavailability; or complete failure of an essential journey without a safe workaround. |
| **High**     | Significant functional or non-functional impact. Examples include a core capability being unavailable or unreliable; silent persistence rejection; major regression in a critical area; severe performance degradation; a substantial accessibility barrier; or an impractical or fragile workaround.                                                                           |
| **Medium**   | Limited or recoverable functional impact. Examples include an affected secondary flow, a less frequent scenario, a recoverable inconsistency without exposure, moderate performance or compatibility degradation, or a usability or accessibility problem with an acceptable workaround.                                                                                        |
| **Low**      | Reduced impact. Examples include cosmetic inconsistency, minor text or alignment problems, small animation or spacing defects, limited usability deviation, or rare behaviour with minimal consequence and no meaningful effect on data, privacy, access, essential journeys, or release confidence.                                                                            |

A `Critical` severity normally creates a deployment or release blocker, but the blocking decision is still recorded separately.

### 10.2 Recent practical example

The recent country-status failure is classified as **High severity** because:

* status buttons accepted interaction but did not apply a visible state;
* persistence did not occur;
* the user received no error feedback;
* new and existing accounts were affected;
* the primary travel-recording journey was blocked.

It is not classified as `Critical` because the available evidence did not demonstrate:

* unauthorised access;
* privacy exposure;
* irreversible data loss;
* corruption of another user's data;
* critical authentication failure.

---

## 11. Priority model

Priority determines the urgency and order of corrective action.

Priority considers:

* severity;
* active production impact;
* release timing;
* frequency;
* affected users and journeys;
* related quality risks;
* workaround;
* dependencies;
* regression reach;
* cost of delay;
* whether continued testing remains meaningful or safe.

### 11.1 Priority levels

| Priority               | Definition                                                                                                                                                                                                                                                                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **P0 — Immediate**     | Requires immediate containment or correction. It normally interrupts lower-priority work. Typical conditions include active unsafe production behaviour, risk to privacy or data, a blocked essential journey without a safe workaround, a release or deployment that cannot continue, or a severe regression in the current version. |
| **P1 — High priority** | Must be addressed before the next relevant release or before dependent work continues. It applies when the defect threatens acceptance criteria, stability, release confidence, accessibility, user trust, or creates a high cost of delay.                                                                                           |
| **P2 — Planned**       | Should be corrected through planned backlog or release work. Impact is moderate, risk is controlled, and continued testing or release preparation remains possible.                                                                                                                                                                   |
| **P3 — Low priority**  | May be addressed when capacity permits or during a later refinement. Impact and cost of delay are low, and no material risk remains.                                                                                                                                                                                                  |

Priority may change when new evidence affects:

* reach;
* frequency;
* workaround;
* dependencies;
* target release;
* production exposure;
* cumulative risk.

The Test Lead owns the final priority decision.

### 11.2 Severity versus priority

Severity and priority must not be automatically mapped.

Examples include:

* a `High` defect may be `P0` when it blocks the AtlasBadge primary journey immediately before release;
* a `Critical` defect isolated in an unreleased development branch may still require immediate containment but may have a different operational sequence from an active production incident;
* a `Low` visual or wording defect may become `P1` when it materially affects a public demonstration, accessibility, legal clarity, or V1.0 presentation;
* a `Medium` defect with a safe workaround may remain `P2`;
* several `Medium` or `Low` defects may collectively create a release blocker.

The recent country-status failure is classified as **High / P0**.

---

## 12. Defect lifecycle

### 12.1 Main lifecycle

```text
New
  ↓
Under Review
  ↓
Confirmed
  ↓
In Progress
  ↓
Ready for Retest
  ↓
Ready for Regression
  ↓
Awaiting Test Lead Validation
  ↓
Closed
```

### 12.2 Lifecycle states

| Status                            | Meaning                                                                                                                                             |
| --------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **New**                           | The unexpected behaviour has been reported but not yet reviewed sufficiently.                                                                       |
| **Under Review**                  | Expected behaviour, evidence, classification, environment, and reproducibility are being assessed.                                                  |
| **Confirmed**                     | Sufficient evidence shows that the product diverges from the approved expectation. Root cause may still be unknown.                                 |
| **In Progress**                   | Investigation, correction, or both are active. Hypothesis, evidence, confirmed cause, and implementation must remain distinguishable in the record. |
| **Ready for Retest**              | The implementation is reported as complete and the technical readiness conditions have been met. This is not proof that the defect is fixed.        |
| **Ready for Regression**          | Direct retesting has passed and the correction is ready for the regression coverage defined by the Test Lead.                                       |
| **Awaiting Test Lead Validation** | Applicable regression and evidence collection are complete, and the item is ready for the final quality decision.                                   |
| **Closed**                        | The Test Lead has confirmed that the applicable closure criteria are satisfied.                                                                     |

### 12.3 Alternative states and outcomes

| Status or outcome    | Meaning                                                                                                                                                            |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Retest Failed**    | Direct retesting did not confirm the correction. The item returns to active correction.                                                                            |
| **Reopened**         | A previously corrected or closed defect has reappeared, remained partially unresolved, or received new evidence that invalidates the previous closure.             |
| **Deferred**         | The defect remains valid, but correction has been deliberately postponed.                                                                                          |
| **Duplicate**        | The failure is already represented by another defect record.                                                                                                       |
| **Cannot Reproduce** | Reasonable investigation did not reproduce or sufficiently confirm the reported behaviour. This does not prove that the original observation was false.            |
| **Not a Defect**     | Investigation confirms that the behaviour matches the approved expectation or belongs to another classification.                                                   |
| **Accepted Risk**    | The unresolved exposure has been explicitly assessed and accepted by the Test Lead under the applicable release rules. It does not mean the product was corrected. |

Environment incidents, configuration issues, enhancements, technical tasks, known limitations, quality risks, and inconclusive results are classifications. When appropriate, the defect Issue may be closed or redirected to the relevant backlog, risk register, or execution record while preserving the investigation history.

### 12.4 Investigation within `In Progress`

`In Investigation` is not a mandatory separate status.

Investigation and implementation may both occur under `In Progress`, provided that the defect record separates:

* working hypothesis;
* suspected cause;
* evidence collected;
* confirmed root cause;
* correction implemented;
* remaining uncertainty.

A technically plausible hypothesis must never be presented as a confirmed cause merely because implementation has started.

---

## 13. Triage and decision-making

Triage is proportional and continuous rather than dependent on a fixed meeting.

### 13.1 Timing

Triage occurs:

* immediately for production defects;
* immediately for `Critical`, `High`, `P0`, and `P1` defects;
* immediately when testing, deployment, or release may be blocked;
* during normal backlog review for lower-impact defects;
* again whenever new evidence changes impact, reach, urgency, workaround, or release relevance.

### 13.2 Triage inputs

Triage reviews:

* expected and observed behaviour;
* available evidence;
* reproducibility;
* affected area and environment;
* severity;
* priority;
* related quality risks;
* reach and frequency;
* workaround or containment;
* effect on dependent tests;
* deployment impact;
* release impact;
* need for further investigation;
* correction, deferral, or accepted-risk options.

### 13.3 Decision authority

AI-assisted tools and the technical delivery process may:

* provide investigation results;
* suggest severity or priority;
* identify affected components;
* recommend containment;
* estimate regression reach;
* propose corrective work.

The Test Lead:

* confirms the classification;
* assigns or changes severity and priority;
* determines whether work may continue;
* defines the required retest and regression;
* approves deferral or accepted risk;
* blocks or approves deployment;
* makes the final closure decision.

Conflicting technical recommendations are resolved through evidence, reproducibility, quality risks, and the established entry and exit criteria.

---

## 14. Investigation and root-cause evidence

### 14.1 Investigation expectations

Investigation should consider, where relevant:

* exact expected and observed behaviour;
* application version, commit, or deployment;
* browser, device, viewport, and operating system;
* authentication and account state;
* existing data state;
* sequence and timing of actions;
* console, network, application, and service logs;
* Firestore reads and writes;
* Firebase Security Rules;
* local cache;
* asynchronous state transitions;
* error propagation;
* third-party service behaviour;
* recent implementation changes;
* related defects or known risks.

Investigation must distinguish:

* observation;
* evidence;
* hypothesis;
* suspected cause;
* confirmed root cause.

### 14.2 Silent failures

When the interface fails without visible feedback, investigation should explicitly review:

* rejected asynchronous operations;
* promises that are not handled correctly;
* empty or overly broad error handlers;
* client state updated before or after persistence;
* missing rollback;
* rejected Firestore operations;
* validation or Security Rules failures;
* absent or misleading user feedback;
* logs that contain the failure but are not surfaced appropriately.

### 14.3 Intermittent problems

For intermittent behaviour, investigation may vary:

* timing;
* action speed;
* connection state;
* reload sequence;
* account state;
* data volume;
* browser or device;
* tab or session count;
* repeated execution.

A high-impact issue must not be discarded solely because it cannot be reproduced immediately.

### 14.4 Non-reproducible results

Before using `Cannot Reproduce`, the Test Lead considers:

* original evidence;
* original environment;
* version changes since discovery;
* reasonable reproduction attempts;
* possible cache or configuration influence;
* potential recurrence;
* impact if the failure is real;
* whether observation in future executions is appropriate.

The outcome may be:

* continued investigation;
* observation in later testing;
* confirmation as an intermittent defect;
* reclassification as an environment incident;
* `Cannot Reproduce`;
* accepted residual risk where eligibility and evidence justify that decision.

Absence of reproduction is not automatic evidence of absence.

### 14.5 Root-cause confirmation

A confirmed root cause requires a verifiable evidence chain connecting:

1. the observed failure;
2. the technical mechanism;
3. the responsible condition or change;
4. removal or control of that mechanism;
5. the corrected result.

Suitable evidence may include:

* controlled reproduction;
* logs or rejected requests;
* sanitised payload comparison;
* code inspection linked to the failure;
* Security Rules validation;
* a test that fails before the correction and passes afterwards;
* an isolated change or rollback;
* direct retest;
* removal of the failure when the responsible condition is removed.

When evidence remains incomplete, the record must use wording such as:

* `working hypothesis`;
* `suspected cause`;
* `investigation pending`.

Reproduction in production is not required when doing so would be unsafe or unnecessary to establish the cause.

---

## 15. Correction readiness

A defect may move to `Ready for Retest` only when the implementation is technically ready for independent quality validation.

The record must contain, where applicable:

* an objective description of the correction;
* affected files, components, services, or rules;
* confirmed cause or updated hypothesis;
* initial impact analysis;
* technical checks performed;
* build result;
* lint and type-check results;
* relevant unit, integration, rule, or orchestration-test results;
* known limitations;
* updated reproduction or retest instructions;
* confirmation that unrelated changes are identified;
* an environment in which the Test Lead can execute the retest.

For high-risk corrections, the readiness evidence should also include:

* a test or controlled demonstration that fails before and passes after the correction, where viable;
* rollback or containment considerations;
* confirmation that errors and user feedback do not expose sensitive information.

The Test Lead may reject readiness when:

* implementation is incomplete;
* evidence is insufficient;
* technical checks failed or were not assessed;
* the scope expanded without impact analysis;
* the correction cannot be tested meaningfully;
* known limitations were not disclosed.

`Ready for Retest` means that the technical delivery process reports readiness. It does not mean that the defect is resolved.

---

## 16. Retesting

Every reported correction receives direct retesting against the original failure.

Retest coverage includes, where applicable:

* the original scenario;
* equivalent preconditions and data;
* new and existing accounts;
* visual state;
* actual persistence;
* reload and return-session behaviour;
* the main positive scenario;
* directly related negative scenarios;
* relevant boundary conditions;
* error handling;
* rollback;
* preservation of existing data;
* repeated execution for intermittent failures;
* the environments relevant to the confirmed impact.

The retest passes only when:

* the expected behaviour is achieved;
* the original failure is no longer present;
* the correction works under the relevant conditions;
* no direct new consequence invalidates the correction;
* limitations are documented;
* evidence is sufficient.

A failed or partial retest results in `Retest Failed` and return to active correction through `Reopened` or `In Progress`.

Retesting does not replace regression.

---

## 17. Regression after correction

Regression is based on the impact and reach of the correction.

The Test Lead considers:

* root cause;
* changed files and components;
* shared services and functions;
* data that is read, written, deleted, or recalculated;
* authentication and authorisation rules;
* local cache;
* error handling;
* related user journeys;
* responsive and visual dependencies;
* related quality risks;
* severity and priority;
* previous regression history;
* proximity to release.

Every formal correction receives at least:

1. approved direct retesting;
2. regression of the directly affected areas and dependencies;
3. smoke coverage of the essential flows defined by the Test Strategy.

Regression is expanded for:

* `Critical` or `High` defects;
* `P0` or `P1` defects;
* production defects;
* cross-cutting changes;
* authentication, privacy, persistence, or destructive operations;
* shared business rules;
* changes close to release;
* corrections with previous regression side effects;
* uncertain implementation reach.

A complete regression is reserved for release milestones or changes whose reach justifies broad execution.

The implementation tool may recommend regression coverage and execute scripted checks. The Test Lead defines the required scope and approves the result.

---

## 18. Reopening and closure

### 18.1 Reopening criteria

A defect is reopened when:

* the original scenario still fails;
* the correction is incomplete;
* the failure reappears under the original or equivalent conditions;
* the identified cause was not actually removed;
* rollback or error handling fails;
* persistence remains incorrect;
* a direct consequence invalidates the correction;
* new evidence materially expands the original impact;
* a previously accepted assumption is disproved.

A reopened defect retains its original identifier and history.

Severity and priority must be reassessed when the new evidence changes impact or urgency.

### 18.2 Closure criteria

A defect may be closed only when the Test Lead confirms that:

* direct retesting passed;
* required regression passed;
* no unacceptable related regression remains;
* required evidence is attached or referenced;
* cause and correction are documented where applicable;
* known limitations are understood;
* residual risk was removed or explicitly accepted;
* the final decision is recorded.

A development-only defect may be closed after complete local validation when no production deployment is necessary to prove the result.

A production defect or production-dependent correction additionally requires:

* controlled deployment;
* production smoke testing;
* confirmation of containment or resolution in the affected environment.

Implementation completion, compilation, or an AI-generated report is insufficient for closure.

---

## 19. Deferral and accepted risk

### 19.1 Deferred

`Deferred` means that the defect remains valid but correction has been postponed.

The record must include:

* reason for deferral;
* current impact;
* residual risk;
* workaround or containment;
* affected release;
* target release, backlog position, or review trigger;
* dependencies;
* confirmation that applicable entry and exit criteria remain satisfied;
* Test Lead decision.

Deferral does not mean that the risk has been accepted indefinitely.

### 19.2 Accepted Risk

`Accepted Risk` means that the unresolved exposure has been explicitly evaluated and accepted for a defined context.

The record must include:

* reason for acceptance;
* affected users and journeys;
* residual exposure;
* available workaround or containment;
* evidence that related areas remain safe;
* affected release or operational context;
* time, condition, or trigger for review;
* Test Lead decision.

### 19.3 Ineligible production risks

The following are not accepted for production while active and uncontained:

* unauthorised access;
* exposure of personal or private data;
* severe data loss or corruption;
* critical authentication or authorisation failure;
* modification or deletion of another user's data;
* known privacy violation;
* an unusable essential journey without a safe workaround;
* behaviour that prevents safe operation or reliable determination of data persistence.

A `Critical` defect blocks release.

A `High` defect may be deferred or accepted only exceptionally when:

* the impact is limited and understood;
* containment is reliable;
* no prohibited risk is involved;
* essential behaviour remains safe;
* the entry and exit criteria remain satisfied;
* the Test Lead explicitly approves the decision.

Several lower-severity defects may be rejected when their combined residual risk is unacceptable.

---

## 20. Production defects

Production defects follow the same core lifecycle but require an initial assessment focused on active user and data protection.

### 20.1 Initial assessment

The Test Lead assesses:

* current user impact;
* data integrity;
* privacy;
* authentication and authorisation;
* affected journey;
* reach and frequency;
* possibility of continued propagation;
* workaround;
* need to pause related deployments or development;
* containment options.

A production defect is not automatically `Critical / P0`. Severity and priority are based on its actual impact and urgency.

### 20.2 Response sequence for serious production defects

The expected sequence is:

1. protect users and data;
2. contain the active impact;
3. decide between rollback, temporary disablement, or controlled hotfix;
4. preserve sanitised evidence where safe;
5. investigate the cause;
6. implement and technically verify the correction;
7. execute direct retesting;
8. execute impact-based regression;
9. obtain Test Lead deployment approval;
10. deploy the approved correction;
11. execute production smoke testing;
12. confirm resolution or containment;
13. complete a post-incident review when warranted.

Containment may occur before root cause is known.

A direct hotfix without full prior investigation is reserved for situations in which it is demonstrably safer than leaving the active failure in place. It still requires subsequent evidence, retesting, regression, and approval.

### 20.3 Post-incident review triggers

A proportional post-incident review is required when the defect involves:

* data, privacy, or security impact;
* material unavailability;
* prolonged silent failure;
* rollback;
* a regression that escaped existing controls;
* repetition of a previously known cause;
* a significant test, deployment, or monitoring gap;
* material loss of release confidence.

AtlasBadge does not currently claim a formal 24/7 incident operation or response-time SLA.

---

## 21. AI-assisted investigation and evidence review

AI-assisted tools may support:

* code inspection;
* log analysis;
* reproduction;
* test execution;
* technical comparison;
* hypothesis generation;
* root-cause investigation;
* implementation;
* test creation;
* impact-analysis suggestions;
* evidence preparation;
* implementation and validation reports.

AI-generated output must be reviewed as evidence, not accepted as authority.

An AI tool may not independently:

* confirm the final classification;
* assign the final severity or priority;
* declare a root cause confirmed without a verifiable evidence chain;
* determine that regression is complete;
* approve deferral;
* accept residual risk;
* approve deployment or release;
* close a defect.

Reports must distinguish:

* what was inspected;
* what was executed;
* what was observed;
* what remains assumed;
* what failed;
* what passed;
* what was not tested.

A confident narrative without reproducible or inspectable support is not sufficient evidence.

---

## 22. Evidence and privacy

### 22.1 Acceptable evidence

Evidence may include:

* cropped screenshots;
* screen recordings;
* relevant logs;
* error messages;
* browser console or network results;
* automated-test output;
* rule-test output;
* before-and-after comparison;
* environment, version, and commit details;
* minimal technical excerpts;
* reduced and sanitised payload structures.

### 22.2 Information that must be removed or replaced

Defect records and portfolio evidence must not expose:

* real UID values;
* personal e-mail addresses;
* real names or profile details;
* tokens;
* cookies;
* credentials;
* API keys;
* secrets;
* private or signed URLs;
* authentication data;
* private visit, note, or memory content;
* identifiers that unnecessarily allow a user to be correlated;
* unnecessary implementation details that increase security exposure.

Replacement values may include:

```text
<REDACTED_UID>
test-user@example.invalid
<REDACTED_TOKEN>
<REDACTED_PRIVATE_URL>
```

### 22.3 Sanitisation rules

* Evidence is sanitised before it enters the defect record.
* AI-generated reports follow the same sanitisation rules.
* The public portfolio does not receive raw operational evidence.
* Public evidence is a sanitised reconstruction or carefully selected subset.
* When sanitisation removes details necessary for investigation, the record may state that restricted evidence exists without publishing it.
* Test data must remain controlled and disposable.
* Evidence quality does not justify retaining unnecessary test accounts or personal data.

---

## 23. Development, deployment, and release blocking rules

Blocking is assessed separately for development, deployment, and release.

### 23.1 Development or test blocking

Work in the affected area may be paused when:

* the defect invalidates subsequent results;
* dependent tests cannot proceed meaningfully;
* continued activity may worsen loss, corruption, or exposure;
* the behaviour prevents distinction between expected and defective results;
* the failure is not sufficiently contained.

Independent work may continue when separation is understood and safe.

### 23.2 Deployment blocking

Deployment is blocked when:

* a `Critical` defect remains unresolved;
* active privacy, security, access, or data-integrity risk remains uncontained;
* required technical checks failed or were not assessed;
* required retesting or regression is incomplete;
* the approved implementation version cannot be identified;
* rollback or containment is inadequate for a high-risk change;
* evidence is insufficient to establish safe operation.

### 23.3 Release blocking

Release is blocked when:

* a `Critical` defect remains open;
* a `High` defect affects an essential journey, data, authentication, privacy, or mandatory acceptance criteria;
* a `P0` remains without approved containment;
* entry or exit criteria are not met;
* an unresolved inconclusive result leaves unacceptable risk;
* required regression is incomplete;
* a combination of lower-severity defects creates an unsafe, unreliable, inaccessible, or untrustworthy experience;
* residual risk has not been explicitly assessed.

The Test Lead retains the final decision, but the decision must be supported by evidence and the established release criteria.

---

## 24. Roles and responsibilities

### 24.1 Test Lead and Product Owner responsibilities

The Test Lead/Product Owner is responsible for:

* clarifying expected behaviour;
* confirming whether the item is a product defect;
* assigning and reviewing severity;
* assigning and reviewing priority;
* determining formal-record requirements;
* defining retest coverage;
* defining regression coverage;
* evaluating release impact;
* approving deferral;
* accepting or rejecting residual risk;
* approving or blocking deployment;
* ordering rollback, containment, or corrective action;
* closing or reopening the defect;
* maintaining consistency with the portfolio documents.

### 24.2 Technical implementation responsibilities

The developer or AI-assisted implementation process is responsible for:

* technical investigation;
* presenting hypotheses and supporting evidence;
* identifying affected code and dependencies;
* implementing the correction;
* executing preliminary technical checks;
* identifying known limitations;
* reporting implementation readiness;
* providing sufficient information for retest;
* supporting impact-based regression;
* reporting uncertainty honestly.

### 24.3 Separation of execution and approval

The same person may coordinate multiple operational activities, and an AI tool may perform substantial technical work.

However:

* technical execution does not equal quality approval;
* implementation completion does not equal correction verification;
* test execution does not equal result acceptance;
* deployment does not equal release readiness.

The Test Lead retains final quality judgement.

---

## 25. Communication and decision records

The GitHub Issue is the official record of the defect and its decisions.

It contains or references:

* classification;
* severity;
* priority;
* release impact;
* containment;
* investigation;
* correction;
* retest;
* regression;
* residual risk;
* deployment decision;
* final closure decision.

For production defects, `Critical`, `High`, `P0`, `P1`, and release blockers:

* communication occurs immediately within the active delivery workflow;
* related work is paused when required;
* containment and blocking decisions are highlighted;
* important classification or impact changes are recorded promptly.

AI conversations and implementation reports may support live investigation, but material conclusions must be transferred to the Issue.

No formal response-time SLA is claimed while one has not been defined.

---

## 26. Relationship with metrics and reporting

This document defines which defect data should be available. It does not define final KPIs, targets, formulas, dashboards, or trend interpretation.

Defect records should allow future collection of:

* identification date;
* confirmation date;
* severity;
* priority;
* affected area;
* environment;
* detection phase;
* detection source or type;
* affected release;
* meaningful status changes;
* date ready for retest;
* retest result;
* number of reopens;
* final decision;
* closure date;
* production escape indication;
* root-cause or technical category;
* deferral;
* accepted risk.

The [Metrics and Reporting](08-metrics-and-reporting.md) document determines:

* which indicators are useful for AtlasBadge;
* which data volumes support meaningful interpretation;
* how to avoid misleading averages or targets;
* how defect data contributes to release reporting;
* which information should remain descriptive rather than statistical.

---

## 27. Current constraints and planned improvements

### 27.1 Current applicable process

The process can currently operate through:

* manually structured GitHub Issues;
* stable defect identifiers;
* manual severity and priority classification;
* sanitised evidence;
* Test Lead triage and approval;
* direct retesting;
* impact-based regression;
* manual status and decision tracking;
* selected public evidence in the QA portfolio.

### 27.2 Planned improvements

Planned process improvements include:

* a dedicated GitHub Issue template for defects;
* an initial controlled label set;
* a possible GitHub Project for lifecycle and backlog views;
* progressive linkage with test cases and evidence;
* greater automation of repeatable technical checks;
* more reusable automated regression coverage;
* structured defect and release reporting;
* metrics and release reporting applied according to [Metrics and Reporting](08-metrics-and-reporting.md).

These improvements are not represented as already implemented and do not require artificial deadlines.

---

## 28. Review triggers

This document and the defect-management process must be reviewed when:

* the lifecycle changes;
* the defect record structure changes;
* GitHub Issue templates or labels are introduced;
* a GitHub Project is created;
* severity or priority definitions change;
* Entry and Exit Criteria change;
* a new critical product area or quality risk is identified;
* a significant production defect occurs;
* containment, rollback, retesting, or regression proves inadequate;
* defects with similar causes recur;
* reopened or production-escaped defects indicate a pattern;
* new environments, platforms, or deployment models are introduced;
* automation changes defect evidence or investigation;
* evidence and privacy controls change;
* another portfolio document becomes inconsistent with this process.

A suitability review is also performed:

* before AtlasBadge V1.0 launch;
* after a material production incident;
* during major Test Strategy reviews;
* before future significant releases.

---

## 29. Related documents

* [Product Overview](01-product-overview.md)
* [Quality Risk Analysis](02-quality-risk-analysis.md)
* [Test Strategy](03-test-strategy.md)
* [Test Scope](04-test-scope.md)
* [Entry and Exit Criteria](05-entry-exit-criteria.md)
* [Test Environments](06-test-environments.md)
* [Metrics and Reporting](08-metrics-and-reporting.md)
* `docs/09-system-test-plan.md` — planned
* [V1.0 Test Evidence](../evidence/v1.0/README.md)
* `test-assets/exploratory-test-charters.md` — planned
* `test-assets/sample-test-cases.md` — planned
* `reports/test-summary-report.md` — planned
