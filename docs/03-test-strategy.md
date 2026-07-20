# AtlasBadge Test Strategy

## 1. Document purpose

This document defines the overall testing strategy for AtlasBadge.

It translates the product context ando practical decisions about:

* test prioritisation;
* test design and execution;
* regression coverage;
* test evidence;
* AI-assisted validation;
* release approval;
* residual risk;
* planned improvements to the quality process.

This strategy is intended to demonstrate how quality is directed and governed for a real product under active development. It does not attempt to duplicate the detailed product rules or quality risk register maintained in the preceding documents.

> **Document status:** Initial completed version based on the current AtlasBadge delivery and testing process. The strategy must be reviewed when the product, risk profile, architecture, supported platforms or release process changes materially.

---

## 2. Strategy objectives

The AtlasBadge test strategy aims to:

* protect user-created travel data from loss, corruption or unintended deletion;
* protect private account and travel information from unauthorised access;
* validate authentication, account-management and identity flows;
* confirm that travel statuses and their compatibility rules behave correctly;
* maintain consistency between the map, profiles, visits, memories, statistics, badges and achievements;
* identify regressions as early as possible during incremental development;
* provide evidence to support deployment decisions;
* balance test depth with the speed and scale of an independently managed product;
* apply structured testing where risk or complexity requires it;
* use exploratory testing to identify failures outside predefined scenarios;
* introduce maintainable automation where it provides repeatable value;
* communicate known limitations and residual risks honestly.

The strategy prioritises meaningful release confidence rather than attempting to demonstrate that every possible condition has been tested.

---

## 3. Strategy context

AtlasBadge is a published web product built with Next.js, React, TypeScript, Tailwind CSS, Firebase Authentication, Cloud Firestore and Vercel.

Its main quality challenges include:

* multiple authentication and account-lifecycle flows;
* asynchronous cloud persistence;
* local browser cache and fallback behaviour;
* interdependent travel statuses;
* visits and user-created memories;
* geographic catalogue integrity;
* calculations across countries, territories and continents;
* public and private profile behaviour;
* badges and dynamic achievements;
* responsive map interaction;
* compatibility and accessibility.

This strategy should be read together with:

* [AtlasBadge Product Overview](01-product-overview.md);
* [AtlasBadge Quality Risk Analysis](02-quality-risk-analysis.md).

The Product Overview defines the product context. The Quality Risk Analysis identifies and prioritises what could fail. This Test Strategy defines how those risks influence testing and release decisions.

---

## 4. Quality ownership and governance

Quality assessment is collaborative, but release accountability is clearly assigned.

AI-assisted development tools and the wider delivery process may contribute:

* implementation summaries;
* technical findings;
* preliminary checks;
* automated or scripted validation;
* defect investigation;
* remediation proposals;
* test results and supporting evidence.

The Test Lead is responsible for:

* clarifying expected behaviour;
* identifying quality risks;
* determining the required test depth;
* designing test scenarios;
* directing AI-assisted validation;
* performing manual and exploratory testing;
* reviewing test results and evidence;
* assessing defects and residual risk;
* defining the necessary regression coverage;
* approving or blocking deployment.

The delivery process may recommend that a change is ready, but the **Test Lead retains the final deployment decision**.

Use of AI does not transfer quality ownership to the tool. Test scope, expected results, risk interpretation and final quality judgement remain the responsibility of the Test Lead.

---

## 5. Risk-based testing approach

Testing depth is determined by the risk introduced or affected by each change.

The assessment considers:

* potential impact on users;
* likelihood of failure;
* reach of the affected behaviour;
* technical dependencies;
* risk of data loss or corruption;
* privacy or access-control consequences;
* importance of the affected user journey;
* likelihood of regression;
* availability of a safe workaround;
* quality and completeness of the available evidence.

The risk priorities defined in the Quality Risk Analysis guide:

* which tests are executed first;
* which areas receive the deepest coverage;
* how much evidence is required;
* how wide the regression should be;
* whether a defect can be accepted;
* whether a deployment must be blocked.

The highest test priority is given to changes affecting:

1. data integrity, Firestore persistence and recovery;
2. privacy and account isolation;
3. authentication and account lifecycle;
4. status compatibility and automatic transitions;
5. visit, country, territory and continent calculations;
6. geographic catalogue and map integrity;
7. public-profile access and read-only behaviour;
8. achievements and badges;
9. compatibility, usability, performance and accessibility.

A feature working through its main expected journey is not, by itself, sufficient evidence for release.

---

## 6. Incremental validation workflow

AtlasBadge is tested incrementally. Each implementation is validated before development continues to the next significant change.

The current workflow is:

```text
Implementation
    ↓
Preliminary technical checks
    ↓
Local functional validation
    ↓
Focused regression
    ↓
Defect correction, where required
    ↓
Retesting and affected-area regression
    ↓
Test Lead approval
    ↓
Deployment
    ↓
Production smoke test
    ↓
Production-only validation, where required
```

Testing begins when:

* implementation is reported as complete;
* the application builds and runs without blocking errors;
* the changed behaviour is available in the local environment;
* preliminary checks have been performed;
* a summary of the implementation is available;
* no obvious technical failure prevents meaningful validation.

The Test Lead then validates the implemented behaviour and performs a rapid regression of the product's most important capabilities.

Development proceeds only after the Test Lead is satisfied that the feature, correction and affected areas are sufficiently stable.

---

## 7. Test design and execution techniques

Testing combines structured functional validation with active attempts to stress and break the implemented behaviour.

The techniques used include:

### 7.1 Positive testing

Expected user journeys are validated using valid data and supported actions.

Examples include:

* successful authentication;
* applying a supported status;
* creating a memory;
* saving profile information;
* loading persisted travel data;
* opening an authorised public profile.

### 7.2 Negative testing

Invalid, prohibited or unauthorised actions are attempted.

Examples include:

* invalid credentials;
* unauthorised access to private pages;
* prohibited status combinations;
* invalid field values;
* modification attempts from a public profile;
* access attempts against another user's data.

### 7.3 Boundary-value analysis

Minimum, maximum, empty and near-limit values are tested where relevant.

This includes counters, text fields, required values and actions close to business-rule boundaries.

### 7.4 State-transition and combination testing

AtlasBadge contains behaviours whose results depend on the current state.

Testing therefore covers:

* applying a status;
* removing a status;
* replacing an incompatible status;
* automatic selection of a dependent status;
* automatic removal of an incompatible status;
* repeated transitions between supported states;
* combinations that are valid individually but problematic when applied in sequence.

### 7.5 Interruption and recovery testing

Flows may be interrupted through actions such as:

* refreshing the page;
* navigating away;
* logging out;
* losing connectivity;
* returning in a later session;
* repeating an action after an apparent failure.

The purpose is to evaluate whether the interface, local cache and cloud data remain consistent.

### 7.6 Exploratory testing

Exploratory sessions are used to investigate behaviour beyond predetermined steps.

The Test Lead varies:

* navigation order;
* data combinations;
* speed of interaction;
* repeated actions;
* device orientation or viewport;
* interruption points;
* account state;
* existing volume of user data.

### 7.7 Error guessing

Previous defects, technical architecture, user behaviour and QA experience are used to anticipate likely failure points.

Particular attention is given to:

* asynchronous saving;
* stale state;
* repeated clicks;
* destructive actions;
* incompatible status transitions;
* counters derived from multiple records;
* public and private data boundaries.

---

## 8. Functional and regression coverage

### 8.1 Feature validation

Each new implementation or correction receives direct validation against its expected behaviour.

Coverage is expanded according to the affected risks and dependencies rather than being limited to the changed screen or component.

For example, a status change may require validation of:

* status compatibility;
* map representation;
* visit count;
* persisted Firestore data;
* restored state after refresh;
* country and territory totals;
* continent statistics;
* badges and achievements;
* public-profile representation.

### 8.2 Core rapid regression

A rapid regression is performed after each implementation.

The recurring core covers:

* login;
* account behaviour;
* map availability and interaction;
* selectable places;
* memories;
* personal and public profile behaviour.

This core regression is intentionally concise. It is designed to identify major breakage without requiring a full product regression after every small implementation.

### 8.3 Impact-based regression

The regression is expanded when a change affects or could indirectly affect:

* travel-status rules;
* visits and counters;
* persistence;
* local cache;
* statistics;
* geographic data;
* badges;
* achievements;
* privacy;
* authentication;
* responsive behaviour.

The regression depth is therefore proportional to the risk and reach of the change.

### 8.4 Retesting

Every reported correction is directly retested against the original failure.

A correction is not considered complete merely because the implementation has changed. The Test Lead must confirm that:

* the original defect can no longer be reproduced;
* the expected result is now achieved;
* no relevant regression has been introduced;
* any remaining limitation is understood.

---

## 9. Smoke and production validation

The primary functional validation is performed in the local development environment.

After every deployment, a production smoke test confirms that the published application remains operational.

The production smoke test focuses on essential availability and user journeys, including relevant checks across:

* public application access;
* authentication;
* authenticated navigation;
* map loading;
* core profile access;
* persistence-dependent behaviour;
* public-profile availability.

Complete scenarios are executed exclusively in production only when the relevant behaviour cannot be reproduced adequately in the local environment.

Production testing uses controlled accounts and disposable data. It must not depend on or alter real customer information.

The current operating model does not rely on a separate dedicated QA or staging environment. This increases the importance of local validation, controlled production checks and clear release gates.

---

## 10. AI-assisted testing and automation strategy

### 10.1 Current AI-assisted validation

AI-assisted testing already forms part of the AtlasBadge development process.

The Test Lead defines:

* what must be tested;
* the relevant risks;
* the scenarios to execute;
* required data and preconditions;
* expected results;
* evidence required;
* regression expectations.

AI tools may then:

* execute commands;
* inspect implementation behaviour;
* perform scripted browser interactions;
* run technical checks;
* investigate failures;
* report results;
* retain selected test artefacts.

These tests are currently generated and executed mainly for individual implementations. Some are retained for reuse, while others are temporary.

This provides valuable automated assistance, but it does not yet constitute a fully versioned, consistently repeatable regression suite.

### 10.2 Planned Playwright automation

Playwright with TypeScript has been selected as the proposed primary framework for a maintainable automated test suite.

The first automation layer should prioritise stable, repetitive and high-value scenarios such as:

* public application smoke testing;
* e-mail and password authentication;
* authenticated access;
* logout;
* core map interaction;
* travel-status persistence;
* refresh and state recovery;
* personal-profile access;
* public-profile read-only behaviour;
* selected privacy controls.

Automated tests should:

* use controlled test accounts;
* create identifiable disposable data;
* clean up data created during execution;
* avoid dependency on execution order;
* produce understandable failure evidence;
* remain aligned with current product rules;
* be reviewed when business behaviour changes.

The future execution model — on demand, before deployment or through continuous integration — remains to be defined.

Automation is intended to support, not replace:

* exploratory testing;
* usability assessment;
* risk analysis;
* visual evaluation;
* human release judgement.

---

## 11. Non-functional quality coverage

### 11.1 Security and privacy

Authentication, authorisation, account lifecycle and privacy controls have been validated through positive and negative functional testing.

Current coverage includes:

* protection of authenticated pages;
* unauthorised access attempts;
* isolation between user accounts;
* public and private profile separation;
* e-mail verification;
* password recovery;
* Google and password method linking;
* identity and data preservation during method linking;
* account deletion;
* Firestore access restrictions;
* manipulation of direct URLs and identifiers;
* confirmation that public profiles remain read-only;
* confirmation that private content is not intentionally exposed.

This coverage is considered proportionate to the current stage of the product.

It does not constitute:

* an independent security audit;
* formal penetration testing;
* external certification;
* a guarantee that no security vulnerability exists.

Any identified privacy, unauthorised-access, data-exposure, corruption or material data-loss risk is unacceptable in production.

### 11.2 Performance and stability

Performance is currently assessed through targeted exploratory testing and observation of user-facing responsiveness.

Testing has included:

* map loading;
* map zoom and movement;
* map interaction fluidity;
* general interface responsiveness;
* repeated interactions;
* stability during normal use;
* desktop and mobile behaviour.

Previous map-fluidity problems were treated as defects, corrected and retested.

A perceptible delay is currently observed when selecting travel statuses. This has been identified through visual and practical observation but has not yet been measured quantitatively.

The delay is therefore recorded as:

* a known performance concern;
* an area requiring baseline measurement;
* an open investigation into interface, calculation and persistence timing.

No formal response-time threshold, service-level target or load-testing baseline currently exists.

### 11.3 Compatibility

Current manual compatibility coverage includes:

| Platform        | Browser        | Device or viewport                            |
| --------------- | -------------- | --------------------------------------------- |
| Windows desktop | Microsoft Edge | 1600 × 900                                    |
| Android mobile  | Google Chrome  | Samsung Galaxy S20 FE, standard configuration |

These environments are the current primary validation baseline.

The present evidence does not demonstrate comprehensive compatibility across:

* Firefox;
* Safari;
* iPhone or iPad;
* tablets;
* macOS;
* other desktop resolutions;
* older browsers;
* a broader range of mobile devices.

Future Playwright execution across additional browser engines and viewports is proposed to increase compatibility evidence.

Physical-device testing should continue for behaviours such as:

* touch interaction;
* scrolling;
* keyboard appearance;
* responsive layout;
* device-specific navigation.

### 11.4 Accessibility

Accessibility has not yet been formally assessed and remains a known coverage gap.

**WCAG 2.2 Level AA** has been adopted as the target reference for future accessibility evaluation.

No accessibility-conformance claim will be made until the applicable criteria have been tested and supported by evidence.

The initial accessibility assessment should prioritise:

* keyboard navigation;
* logical focus order;
* visible focus;
* accessible names;
* form labels and instructions;
* contrast and readability;
* status identification without reliance on colour alone;
* browser zoom and content resizing;
* touch-target usability;
* basic screen-reader behaviour;
* reduced-motion preferences.

---

## 12. Test data approach

Testing uses controlled personal test accounts populated with disposable data.

The data may be created, changed and deleted as required to validate:

* travel-status persistence;
* visit counts;
* memories;
* profiles;
* account isolation;
* destructive operations;
* account deletion;
* recovery and re-entry;
* automated test repeatability.

Test execution must not require real customer data.

Data used by future automated tests should be:

* identifiable as test data;
* safe to remove;
* isolated from meaningful personal records;
* cleaned up when no longer needed;
* reusable only when this does not create dependency between tests.

Credentials, access tokens and private account details must not be committed to the portfolio repository.

---

## 13. Test evidence and defect handling

The level of documentation is proportional to:

* risk;
* complexity;
* need for repetition;
* impact of failure;
* release importance;
* investigation difficulty.

Evidence may include:

* checklists;
* working notes;
* structured test cases;
* screenshots;
* observed and expected results;
* implementation reports;
* validation reports;
* defect evidence;
* automated execution output.

Not every small change requires the same documentation level.

Structured cases and stronger evidence are preferred when testing:

* high-risk behaviour;
* complex status rules;
* persistence;
* authentication;
* privacy;
* destructive actions;
* calculations;
* release-critical corrections.

When a defect is identified, the Test Lead:

1. records reproducible steps;
2. records the expected result;
3. records the observed result;
4. captures supporting evidence;
5. assesses impact and priority;
6. coordinates the correction;
7. performs direct retesting;
8. performs regression across affected areas;
9. closes the defect only when the correction is verified or the residual risk is explicitly accepted.

Detailed severity, priority and triage rules will be defined separately in the Defect Management document.

---

## 14. High-level entry and approval conditions

Detailed entry and exit criteria will be maintained in a separate portfolio document.

At strategy level, an implementation is ready for testing when:

* development is complete;
* the application builds and runs;
* no blocking technical error prevents validation;
* preliminary checks have been performed;
* the behaviour is available locally;
* the implementation changes are understood.

A feature is approved only when all of the following are satisfied:

* expected behaviour has been validated;
* relevant negative and exploratory scenarios have been executed;
* no release-blocking defect remains;
* required corrections have passed retesting;
* the required regression has passed;
* known risks have been reviewed;
* residual risk is considered acceptable by the Test Lead.

After approval:

* the change may be deployed;
* production smoke testing is performed;
* production-only scenarios are executed where necessary;
* further development may continue when the deployed behaviour remains stable.

---

## 15. Release decision and residual risk

Release recommendations are based on test evidence and combined residual risk, not only on the status of the main user journey.

A deployment must be blocked when evidence indicates an unacceptable combination of:

* critical defect;
* data loss or corruption;
* privacy exposure;
* unauthorised access;
* failure of an essential user journey;
* significant unresolved regression;
* unreliable behaviour that prevents safe use;
* insufficient evidence for a high-risk change.

Several lower-severity problems may collectively create enough risk to block a release.

A minor known defect may be accepted when:

* its impact is limited;
* the main implementation or correction provides greater value;
* the defect does not compromise an essential flow;
* a reasonable workaround exists where needed;
* remediation can be planned for a later iteration;
* the remaining risk is understood.

Privacy, data exposure, corruption and material data-loss risks may remain temporarily in development while being investigated, but they are not accepted in production.

Release outcomes are classified as:

| Outcome                     | Meaning                                                                      |
| --------------------------- | ---------------------------------------------------------------------------- |
| Approved                    | Testing is satisfactory and residual risk is acceptable.                     |
| Approved with accepted risk | A minor known limitation remains, but its impact is understood and accepted. |
| Blocked                     | Evidence shows unacceptable risk or insufficient confidence for deployment.  |

The final release decision belongs to the Test Lead.

---

## 16. Current constraints, assumptions and planned improvements

### 16.1 Confirmed current constraints

* Manual browser coverage is limited to Edge on Windows and Chrome on one Android device.
* There is no fully versioned and consistently reusable Playwright regression suite yet.
* Accessibility has not been formally assessed.
* Formal multi-user load and stress testing has not been performed.
* No independent security audit or penetration test has been completed.
* Quantitative performance targets have not been established.
* The perceived status-selection delay has not yet been measured.
* Functional testing is performed primarily in the local development environment.
* Production is used for smoke testing and behaviours that cannot be reproduced adequately locally.
* AI-assisted tests are partly reusable and partly generated for individual implementations.

### 16.2 Assumptions

The current strategy assumes that:

* the local environment remains sufficiently representative for most functional validation;
* controlled accounts and disposable data remain available;
* preliminary AI-assisted checks do not replace Test Lead validation;
* production smoke testing remains possible after every deployment;
* quality risks continue to be reviewed when implementation behaviour changes.

Assumptions must be reassessed whenever evidence shows that they are no longer reliable.

### 16.3 Open questions

The following questions remain open:

* What measurable response-time target should apply to status selection?
* Which other key user interactions require quantitative performance targets?
* When should the Playwright suite run: on demand, before deployment, or through continuous integration?
* Which browsers, devices and minimum screen sizes will become officially supported?
* How will accessibility evidence and WCAG 2.2 AA findings be recorded?
* When should broader load, network-condition or scalability testing be introduced?

### 16.4 Proposed improvements

The following improvements are proposed:

1. measure the current status-selection delay and establish a baseline;
2. investigate interface, calculation and Firestore timing separately;
3. implement an initial Playwright smoke and regression suite;
4. retain automated tests as versioned repository assets;
5. define a repeatable automated-test data and cleanup process;
6. expand automated browser and viewport coverage;
7. perform an initial WCAG 2.2 AA accessibility assessment;
8. define quantitative targets for selected high-value interactions;
9. introduce stronger release-level reporting as the product and portfolio mature.

These items are planned improvements and must not be described as completed coverage until they have been implemented and evidenced.

---

## 17. Strategy review triggers

This strategy must be reviewed when:

* a new authentication method is introduced;
* account or identity-linking behaviour changes;
* the travel-status model changes;
* persistence, caching or synchronisation changes;
* the Firestore data model changes materially;
* a new privacy or sharing capability is introduced;
* the geographic catalogue changes;
* calculations, badges or achievements change;
* Playwright automation is implemented;
* continuous integration is introduced;
* supported browsers or devices change;
* accessibility testing begins;
* measurable performance targets are adopted;
* a significant production defect reveals a new failure mode;
* the Test Lead changes the release process.

---

## 18. Related portfolio documents

This strategy provides direction for:

* `docs/04-test-scope.md`;
* `docs/05-entry-exit-criteria.md`;
* `docs/06-test-environments.md`;
* `docs/07-defect-management.md`;
* `docs/08-metrics-and-reporting.md`;
* `test-assets/exploratory-test-charters.md`;
* `test-assets/sample-test-cases.md`;
* `reports/test-summary-report.md`.

Those documents will expand the operational details without changing the core principles established here.
