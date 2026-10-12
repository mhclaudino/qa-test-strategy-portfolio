# AtlasBadge Entry and Exit Criteria

## 1. Document purpose

This document defines the entry, exit, approval and blocking criteria used throughout AtlasBadge testing and release validation.

It establishes:

* when an implementation is ready for Test Lead validation;
* when testing of a change can be considered complete;
* when a defect is ready for retesting and closure;
* when regression testing is sufficiently complete;
* when a release is ready for deployment;
* when deployment or launch must be blocked;
* when production smoke testing is complete;
* when AtlasBadge V1.0 is ready for real users;
* how unexecuted scenarios, known limitations and residual risks are assessed;
* who has authority to approve or block each stage.

The criteria are intentionally proportional to risk. They are designed to support reliable quality decisions without requiring the same evidence, formality or test depth for a cosmetic correction and a change affecting authentication, privacy or persistent user data.

> **Document status:** Maintained through AB-EV-067. AB-EV-060 closed the final seven-locale visual/Production acceptance; AB-EV-061–065 established release-readiness and the Node 24 technical RC baseline. Premium Hardening then published PA-001/AB-EV-066 and PA-002/AB-EV-067. The current criteria include independent pre-publication review where required, exact final-byte integrity and publication-chain verification. PA-003 is the next active finding; final V1.0 Go Live is not yet approved.

---

## 2. Relationship with other portfolio documents

This document should be read together with:

* [AtlasBadge Product Overview](01-product-overview.md);
* [AtlasBadge Quality Risk Analysis](02-quality-risk-analysis.md);
* [AtlasBadge Test Strategy](03-test-strategy.md);
* [AtlasBadge Test Scope](04-test-scope.md);
* [AtlasBadge System Test Plan](09-system-test-plan.md);
* [AtlasBadge Lessons Learned](10-lessons-learned.md);
* [Governance Errata](../GOVERNANCE-ERRATA.md).

The Product Overview defines the product context and main user journeys.

The Quality Risk Analysis identifies what could fail, why it matters and which areas require the strongest attention.

The Test Strategy defines how testing is directed, executed and governed.

The Test Scope defines the product areas, scenario groups, priorities and remaining mandatory coverage for V1.0.

The System Test Plan defines the current release-validation sequence and Premium Hardening checkpoints.

This document defines the practical quality gates used to determine whether testing or delivery may begin, continue, conclude or proceed to the next stage.

It does not replace detailed test cases, exploratory charters, defect records, execution evidence or release reports.

---

## 3. Core principles

### 3.1 Risk-proportional criteria

Entry and exit criteria are applied according to:

* the priority and impact of the affected area;
* the reach of the change;
* technical and functional dependencies;
* the possibility of data loss or corruption;
* privacy and access-control consequences;
* the importance of the affected user journey;
* regression risk;
* implementation complexity;
* available evidence;
* the consequences of an incorrect approval.

A contained visual correction may require direct validation, a brief regression and a concise approval record.

A change affecting authentication, Firestore persistence, privacy, account deletion, identity, travel-status architecture, visit-photo/server ownership or an essential user journey requires stronger technical evidence, deeper testing, broader regression and explicit residual-risk review.

### 3.2 Meaningful confidence rather than artificial completion

Testing is not considered complete merely because every planned item has been marked as executed.

Testing is considered sufficiently complete when:

* the required depth matches the risk;
* relevant behaviour has been validated;
* defects, stale oracles, fixture problems and process gaps have been assessed;
* the required regression has been completed;
* remaining uncertainty is understood;
* no unacceptable risk remains.

Conversely, a large number of successful checks does not justify approval when an important named acceptance criterion remains unproven.

### 3.3 Incremental validation

AtlasBadge is tested incrementally.

Each implementation is validated when it becomes sufficiently complete and testable. Development normally proceeds to the next change after the current change has received direct validation and an appropriate regression.

Completed parts of a larger feature may be tested before the entire feature is finished. This early validation does not represent approval of the complete feature.

A previous green checkpoint is retained unless a later change invalidates what it proved.

### 3.4 Separation between technical readiness and quality approval

The **designated Product implementation agent** may:

* implement a Product change;
* execute technical checks;
* report that the implementation is ready for validation;
* identify technical limitations;
* provide test results and factual handoff evidence;
* implement a defect correction;
* recommend approval or blocking.

Gemini/Antigravity is the default Product implementation agent in the current AtlasBadge workflow. ChatGPT coordinates requirements/risk/governance and owns QA Portfolio documentation/evidence. Codex performs independent review when the current gate requires it. The Test Lead owns final approval/publication decisions. Generic instructions such as “fix it”, “finish it” or “do everything” do not silently change those actor boundaries.

The Test Lead decides:

* whether the change is ready to enter functional validation;
* which scenarios and regression depth are required;
* whether the available evidence is sufficient;
* whether a defect is closed;
* whether residual risk is acceptable;
* whether a deployment or launch is approved or blocked.

Technical completion does not automatically represent quality approval.

### 3.5 Independent review and exact-artifact approval

Where independent review is an approved publication gate:

* the implementation candidate remains uncommitted/unpublished until the review is complete;
* the exact final candidate bytes are frozen after all implementation/test edits;
* required gates execute against those bytes;
* no later source/test/rules edit is allowed without impact analysis and requalification;
* approval applies to the named acceptance criteria and exact candidate, not merely an aggregate green count;
* staging, commit, remote SHA and deployment source must preserve the approved bytes.

PA-001 records a historical ordering deviation: original publication preceded the planned independent architecture review. The chronology remains transparent and a compensating **post-publication** audit is required before final Premium Hardening closure. PA-002 demonstrates the corrected pre-publication control.

---

## 4. Decision states

The Test Lead uses three formal decision states.

| Decision                                 | Meaning                                                                                                                                                               |
| ---------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Approved**                             | The required validation has been completed, no unacceptable risk remains and the change or release may proceed.                                                       |
| **Approved with accepted residual risk** | The required validation has been completed and a known limitation or defect remains, but its impact is understood, recorded and explicitly accepted by the Test Lead. |
| **Blocked**                              | Testing, deployment or launch cannot safely proceed because the implementation, evidence, environment, unresolved defects, missing review gate or remaining risk are unacceptable. |

`Approved with accepted residual risk` must not be used to hide:

* a Critical defect;
* data loss or corruption risk;
* privacy exposure;
* unauthorised access;
* an essential-flow failure;
* misleading privacy or security behaviour;
* insufficient evidence for a material high-risk change;
* a required independent review that has not occurred;
* an unknown final artefact that differs from what was validated.

---

## 5. Entry criteria for Test Lead validation

An implementation, correction or completed part of a feature may enter Test Lead validation when the following applicable conditions are satisfied.

### 5.1 Implementation readiness

* The intended change or testable part of the change has been implemented.
* The relevant behaviour is available in the intended test environment.
* The application compiles and operates sufficiently for meaningful validation.
* No technical failure prevents reliable execution of the required scenarios.
* Preliminary technical checks have been completed in proportion to the risk.
* Known warnings, failed checks or limitations have been disclosed.
* Dirty/untracked Product state that may affect evidence is inventoried.

A completely warning-free technical execution is not required for every change.

Warnings or failed checks may be temporarily accepted when they are unrelated to the change, do not prevent reliable validation and do not introduce relevant risk.

A warning, failed check or known technical problem blocks entry when it:

* relates directly to the implemented behaviour;
* may invalidate the test results;
* prevents the required scenarios from being executed;
* introduces material data, privacy, security or reliability risk;
* makes the implementation insufficiently stable for useful testing.

### 5.2 Implementation information

The implementation handover should normally include:

* a summary of what was changed;
* the technical checks executed;
* relevant results;
* known limitations, warnings or unresolved problems.

For Critical and High-impact changes, it should also identify:

* the expected impact;
* affected features, components or dependencies;
* relevant quality risks;
* files or technical areas changed when this information helps the Test Lead assess regression risk;
* named acceptance criteria/oracles where the change is race/lifecycle heavy;
* environment/Firebase isolation evidence where destructive or stateful tests are involved.

A detailed list of every modified line is not mandatory for every change. More detailed technical information may be required when the change is complex, high-risk or difficult to assess from external behaviour alone.

### 5.3 Expected behaviour

The expected behaviour must be sufficiently clear to support a reliable approval decision.

When the expected behaviour is incomplete, ambiguous or changes during implementation:

* exploratory investigation may continue;
* evidence may be collected;
* the implementation may be compared with related product rules;
* final approval remains blocked until the expected behaviour is clarified;
* affected tests must be adjusted and executed against the confirmed expectation.

The Test Lead must not approve a change by silently assuming the most likely intended behaviour.

A green automated test is not authoritative when it protects a superseded or malformed oracle.

### 5.4 Environment and data readiness

The accounts, configurations, data and environmental conditions required for the selected scenarios should be available.

When a required condition is unavailable:

* unaffected scenarios may continue;
* dependent scenarios remain pending;
* the missing coverage is recorded;
* approval is blocked when the gap leaves relevant risk unresolved.

Environmental unavailability does not automatically remove a scenario from scope.

For Emulator tests, the effective project/endpoints and real-request fail-fast must be observable; `localhost` alone is not evidence of Firebase isolation.

### 5.5 Entry authority

The designated Product implementation agent or delivery process may report that a change is technically ready.

The Test Lead accepts or rejects entry into functional validation after considering:

* implementation stability;
* available information;
* known risks;
* preliminary results;
* environmental readiness;
* whether meaningful and trustworthy validation is possible.

Compilation alone does not automatically satisfy the entry criteria.

---

## 6. Entry criteria proportional to impact

### 6.1 Low-impact change

A low-impact change may enter validation with:

* the change available locally;
* successful basic technical execution;
* a concise description of the change;
* no indication of wider impact.

A change must not remain Low when it affects data, privacy, accessibility, localisation, responsive behaviour, navigation or an essential flow.

### 6.2 Medium-impact change

A Medium-impact change should enter validation with:

* the implemented behaviour available;
* relevant technical checks completed;
* affected integration points identified;
* suitable test data available;
* known limitations disclosed.

### 6.3 High-impact change

A High-impact change should enter validation with:

* a clear implementation summary;
* expected impact and affected areas identified;
* relevant technical checks completed;
* risks and limitations disclosed;
* stable behaviour sufficient for broad functional and dependency testing;
* stronger evidence where required;
* explicit acceptance criteria and the current oracle for important lifecycle/concurrency paths.

### 6.4 Critical-impact change

A Critical-impact change should enter validation only when:

* the implementation is technically stable enough for deep validation;
* affected identities, data flows, permissions and dependencies are understood;
* relevant technical checks have passed or any exception has been explicitly assessed;
* suitable accounts and data states are available;
* destructive, recovery, isolation and unauthorised-access scenarios can be exercised;
* the Test Lead accepts that the implementation is ready for Critical-level validation.

A complete production build is not mandatory before every small implementation.

It is expected for:

* relevant high-impact or Critical changes where build behaviour may be affected;
* final regression;
* release preparation;
* any situation in which a development-only execution would provide insufficient confidence.

---

## 7. Exit criteria for feature or change validation

A change or feature may be considered complete from a testing perspective when all applicable conditions below are satisfied.

### 7.1 Functional completion

* The confirmed expected behaviour has been validated.
* Relevant positive scenarios have been executed.
* Relevant negative, boundary, state-transition, concurrency or exploratory scenarios have been executed according to risk.
* Dependent calculations, persistence, profiles, maps, counters, badges, server routes or other affected behaviours have been checked where applicable.
* The implementation behaves consistently after refresh, navigation or a later session where persistence/recovery is relevant.
* Named acceptance criteria remain mapped to executable evidence; a similar green test is not accepted as replacement without requirement review.

### 7.2 Defect and correction status

* Defects identified during validation have been assessed.
* Required corrections have been implemented.
* Corrected defects have passed direct retesting.
* Regression of affected areas has been completed.
* Any remaining defect or limitation has been evaluated as residual risk.
* Test-oracle, fixture, environment and governance issues are classified separately rather than inflated into Product defects.

### 7.3 Regression status

* The rapid core regression has been completed where applicable.
* Regression has been expanded according to impact and dependencies.
* No unacceptable regression remains.
* Any newly identified defect outside the original scope has been recorded and assessed.
* Previously green checkpoints are rerun only when a later change invalidated them.

A newly identified defect does not automatically reject or approve the original change. Its impact, relationship to the implementation and contribution to combined release risk must be assessed.

### 7.4 Pending or unexecuted scenarios

Testing may be considered complete with a scenario unexecuted when the scenario is:

* not applicable;
* technically impossible in the available environment;
* dependent on production-only behaviour;
* consciously deferred after risk analysis.

The reason, impact and remaining uncertainty must be known.

A pending scenario must not be used to hide unacceptable risk.

For an unexecuted Critical or High-priority scenario, the Test Lead must consider:

* why execution was not possible;
* whether alternative evidence exists;
* whether related scenarios provide sufficient confidence;
* the potential impact of failure;
* whether post-deployment validation is possible and safe;
* whether the remaining uncertainty is acceptable.

Approval is blocked when confidence is insufficient.

PA-001 AC-017 is an example of an explicit bounded carry-forward: it does not reopen PA-001 functionality but remains required under PA-007 / Final Premium RC.

### 7.5 Scope changes

When part of the originally expected scope is removed or deferred:

* the revised scope must be explicitly confirmed;
* all remaining in-scope behaviour must be implemented and tested;
* removed or deferred items must be recorded;
* the reduction must not introduce unacceptable risk;
* the Test Lead must approve the revised delivery.

A feature is not complete merely because the implemented portion works when the agreed scope remains unclear.

PA-002 physical Storage deletion is an accepted example of a correctness-independent deferred cleanup: logical inaccessibility and active quota release are proven independently, and broader cleanup remains separate scope.

### 7.6 Final decision

A feature or change is complete only after the Test Lead assigns one of the following:

* `Approved`;
* `Approved with accepted residual risk`.

A `Blocked` decision means the exit criteria have not been satisfied.

---

## 8. Evidence required for approval

Evidence is proportional to risk, complexity and release importance.

Quality indicators, readiness summaries, quality-gate reporting and evidence-status rules are governed by [Metrics and Reporting](08-metrics-and-reporting.md).

It may include:

* checklists;
* exploratory notes;
* structured test cases;
* screenshots;
* expected and observed results;
* browser or Firestore inspection;
* command output;
* build results;
* Playwright results;
* exact candidate hashes;
* lint warning-identity comparison;
* independent-review decisions;
* defect evidence;
* retest results;
* regression results;
* implementation reports;
* commit/deployment SHA evidence;
* Test Lead approval records.

For Critical and High-impact changes, explicit evidence should normally be retained.

The Test Lead's explicit approval is also valid evidence of the quality decision. However, approval does not replace testing, retesting, regression, independent review or investigation that was necessary to reach that decision.

Medium and Low-impact changes may use lighter evidence when the risk and need for traceability are limited.

---

## 9. Defect correction and closure criteria

Defect severity, priority, lifecycle and triage are governed by [Defect Management](07-defect-management.md).

### 9.1 Ready for retest

A correction may be submitted for retesting when:

* the designated implementation agent reports that the correction is complete;
* a summary of the correction is available;
* potentially affected areas are identified where relevant;
* preliminary technical checks have been executed;
* known limitations are disclosed;
* the cause is described when known or useful to the regression assessment.

A complete formal root-cause analysis is not required for every defect, particularly for isolated cosmetic or low-impact problems.

### 9.2 Closure criteria

A defect may be closed only when the Test Lead confirms that:

* the original failure has been retested;
* the expected behaviour is achieved;
* the relevant regression has been completed;
* no unacceptable related failure has been introduced;
* any remaining limitation is understood;
* residual risk has either been removed or explicitly accepted.

An implementation agent may report a defect as corrected or ready for retest. It does not close the defect.

### 9.3 Failed retest

When the retest fails:

* the defect remains open or is reopened;
* the new observed behaviour is recorded;
* further correction is requested;
* the necessary regression is reassessed.

### 9.4 Defect no longer reproducible

When a previously confirmed defect can no longer be reproduced, it is not automatically closed.

The Test Lead considers:

* the original conditions;
* available evidence;
* reasonable reproduction attempts;
* changes introduced since the defect was found;
* the potential for recurrence;
* the impact if the defect returns.

The defect may then be:

* closed after sufficient confidence is obtained;
* kept open;
* placed under observation;
* recorded as a known residual risk.

A known code correction is useful evidence but is not the only possible basis for closure.

---

## 10. Regression entry and exit criteria

### 10.1 Entry into regression

Regression may begin when:

* direct validation of the change has produced sufficiently stable results;
* the affected product areas and dependencies have been identified;
* blocking defects that would invalidate the regression have been corrected or isolated;
* the build or local execution is stable enough for meaningful regression;
* suitable data and accounts are available.

Regression may continue around a known defect when unaffected scenarios can still be executed with confidence.

Regression should be paused or restricted when the defect:

* invalidates subsequent results;
* blocks the main test path;
* creates unsafe data or privacy conditions;
* prevents reliable distinction between expected and defective behaviour.

### 10.2 Rapid core regression exit

The rapid core regression is complete when:

* the relevant essential journeys have been exercised;
* no major breakage has been identified;
* any failure has been assessed;
* affected areas have been added to broader regression where necessary.

### 10.3 Impact-based regression exit

Impact-based regression is complete when:

* coverage reflects the risk and reach of the implementation;
* affected features and dependencies have been exercised;
* defects found during regression have been assessed;
* required corrections have passed retesting;
* relevant regression gaps are justified;
* no unacceptable residual risk remains;
* the Test Lead approves the result.

It is not necessary to execute every theoretical scenario when the selected coverage provides sufficient confidence.

### 10.4 Final V1.0 regression

The final V1.0 regression may begin when:

* the mandatory V1.0 implementation/Premium Hardening scope is sufficiently complete for an integrated candidate;
* the application is stable enough for broad integrated validation;
* known blockers from incremental testing have been resolved;
* the required environments, accounts, data and Playwright suite are available;
* the Test Lead approves commencement of the final cycle.

AB-EV-065 established the technical RC baseline. PA-001 and PA-002 are later published Premium Hardening closures and therefore form part of the eventual final Premium RC integration state. Entry to the ultimate final cycle must account for the remaining Premium sequence, not merely reuse the pre-hardening green state as final Go Live evidence.

The final regression is complete when:

* the broad scope defined in the Test Scope has been executed;
* Critical and High-risk product areas have received the required depth;
* the permanent Playwright suite has been executed and analysed;
* required manual, exploratory, visual, localisation, compatibility and accessibility coverage has been completed;
* reported corrections have passed retesting;
* affected areas have passed regression;
* known limitations and residual risks have been reviewed;
* PA-001 AC-017 and governance carry-forward are closed or explicitly resolved under their approved final gates;
* no launch-blocking risk remains.

### 10.5 Changes during final regression

Any change introduced after the final regression begins requires:

* impact analysis;
* direct validation;
* retesting where applicable;
* regression proportional to the affected areas;
* review of previous results that may no longer be reliable.

A high-impact change may require broad repetition or partial restart of the final regression.

---

## 11. Playwright result criteria

A Playwright failure does not automatically block the release without investigation.

Each failure must be assessed to determine whether it represents:

* a genuine product defect;
* an inconclusive result;
* an environmental failure;
* an unstable test;
* an outdated test;
* an incorrect automated expectation.

A genuine defect or unresolved inconclusive result in a relevant area blocks approval when it leaves unacceptable risk.

A demonstrably invalid, outdated or unstable automated test may be corrected or explicitly justified before the release decision.

The suite is not merely informational, but automated results do not replace the Test Lead's analysis and final judgement.

Emulator Playwright evidence must not be called isolated/safe if the relevant run did not establish its intended backend or if it completed with real Firebase traffic.

---

## 12. Deployment entry and approval criteria

A change or release may be approved for deployment when:

* its applicable feature/finding exit criteria have been satisfied;
* required defect corrections have passed retesting;
* required regression has been completed;
* build and technical release checks appropriate to the change have passed;
* Playwright/Emulator/Rules results have been assessed where required;
* known limitations have been reviewed;
* no Critical defect remains open;
* any accepted High or lower defect meets the residual-risk criteria;
* production-only scenarios have been identified;
* the exact approved implementation version is known;
* any required independent pre-publication review is complete;
* the Test Lead assigns `Approved` or `Approved with accepted residual risk`.

### 12.1 Version and byte integrity

The commit/build deployed must correspond to the version approved by the Test Lead.

For high-risk Premium changes, approval may additionally require:

```text
approved working candidate hashes
=
staged hashes
=
committed tree hashes
=
GitHub main exact commit
=
Vercel Production source SHA
```

When the deployed version differs from the approved version:

* the differences must be identified;
* their impact must be analysed;
* previous approval does not automatically cover the new changes;
* proportional direct validation, retesting and regression must be performed;
* new Test Lead approval is required when the differences may affect the result.

A technical statement that the differences are minor is not sufficient by itself.

### 12.2 Production-only validation

A deployment may proceed when a scenario cannot be reproduced adequately in the local environment only when:

* all safe and relevant local validation has been completed;
* the reason for production-only execution is understood;
* the risk has been assessed;
* no unacceptable privacy, data or security exposure is introduced;
* controlled accounts and disposable data are used;
* production validation is mandatory immediately after deployment when it is part of the approved gate;
* the Test Lead approves the controlled deployment.

Production is not treated as the normal primary testing environment.

Conversely, a stateful/destructive scenario does not have to be re-run against real Production merely because the deployment is READY when comprehensive Emulator evidence already proves the behaviour and Production mutation would add avoidable risk. The non-execution limitation must be explicit.

---

## 13. Deployment blocking criteria

Deployment must be blocked when any individual issue or combination of issues creates unacceptable risk.

Blocking conditions include:

* a known Critical defect;
* actual or credible data loss;
* data corruption;
* privacy exposure;
* unauthorised access;
* failure of an essential user journey;
* an unresolved significant regression;
* unreliable behaviour that prevents safe use;
* misleading privacy or security information;
* insufficient evidence for a material High or Critical-risk change;
* inability to distinguish whether user data was saved correctly;
* an untested condition whose potential impact is unacceptable;
* unresolved ambiguity in essential expected behaviour;
* a required independent review not completed;
* final candidate bytes that differ from the independently approved bytes;
* a deployed build that has not received the necessary validation;
* combined lower-severity defects whose overall effect makes the release unsafe or untrustworthy.

A workaround does not make a known Critical defect acceptable for deployment.

---

## 14. Acceptance of High and lower-severity defects

A High defect may be considered for acceptance only on a case-by-case basis.

It must not be accepted when it involves:

* data loss or corruption;
* privacy exposure;
* security or unauthorised access;
* an essential-flow failure;
* misleading privacy or security behaviour;
* unacceptable user harm or loss of trust.

Acceptance requires:

* understood impact and reach;
* a sufficiently reliable workaround where relevant;
* evidence that related areas remain safe;
* a clear reason why delivery value exceeds the remaining risk;
* a proportional record of the limitation;
* explicit Test Lead approval.

Medium or Low defects may be accepted when:

* their impact is limited;
* they can reasonably be corrected later;
* they do not compromise essential behaviour;
* combined residual risk remains acceptable.

---

## 15. Residual-risk record

An accepted residual risk should have a proportional record containing:

* the known defect, limitation or unexecuted condition;
* the understood impact;
* the reason for acceptance;
* relevant restrictions, workaround or follow-up;
* the Test Lead's decision.

A separate formal report is not required for every minor accepted risk.

Relevant risks involving privacy, exposure, corruption or loss of user data are not accepted for production.

Current material carry-forwards include PA-001 AC-017, the PA-001 compensating post-publication architecture audit, PA-002 deferred physical Storage cleanup and PA-006A deterministic Rules-tooling declaration.

---

## 16. Production smoke test criteria

### 16.1 Smoke entry criteria

Production smoke testing may begin when:

* deployment has completed;
* the deployed version has been identified;
* the application is accessible;
* required controlled accounts and disposable data are available where stateful execution is authorised;
* the smoke scope is defined;
* no known deployment problem makes execution unsafe.

### 16.2 Required V1.0 language coverage

The final V1.0 production smoke test requires coverage in:

* `pt-BR`;
* `en-GB`.

Both languages remain part of the required final-launch coverage even though the supported V1.0 locale set contains seven locales.

If one language cannot be executed or produces an inconclusive result, the Test Lead must assess:

* the reason;
* related localisation evidence;
* coverage already completed locally;
* whether the limitation could hide a relevant functional or linguistic failure;
* the remaining release risk.

The launch may proceed only when sufficient confidence exists and no relevant risk is being hidden. Execution in one language does not automatically replace the requirement for the other.

### 16.3 Smoke exit criteria

The production smoke test is complete when:

* essential public access has been validated;
* relevant authentication and account access have been validated;
* authenticated navigation works;
* the map and core status interaction work;
* persistence-dependent behaviour has been checked where authorised/required;
* personal-profile access works;
* public-profile access remains read-only and respects privacy;
* logout or session termination works where included;
* required language coverage has been completed or any exceptional limitation has been assessed;
* failures have been investigated;
* pending scenarios are justified;
* no unacceptable risk remains;
* the Test Lead approves the smoke result.

A minor smoke failure does not automatically reject the deployment.

The deployment may remain approved when the problem is:

* understood;
* limited in impact;
* unrelated to data, privacy, security or essential flows;
* recorded and accepted by the Test Lead.

### 16.4 Serious production failure

When production smoke testing identifies a serious failure, the Test Lead decides whether to:

* roll back;
* block the launch;
* apply a controlled direct correction.

Rollback is expected when:

* Critical risk exists;
* user data may be exposed, lost or corrupted;
* an essential flow is unavailable;
* safe operation cannot be confirmed;
* a direct correction is not clearly the safer controlled option.

A direct correction may be accepted when it is demonstrably safer and can be validated in a controlled manner.

### 16.5 New deployment after rollback or blocking

A new deployment attempt requires:

* correction of the failure;
* local retesting;
* regression of affected areas;
* updated risk analysis;
* Test Lead approval;
* a new deployment;
* a new production smoke test where required.

Compilation or implementation confirmation alone is insufficient.

---

## 17. V1.0 launch exit criteria

AtlasBadge V1.0 may be considered ready for real users only when all applicable conditions below are satisfied.

### 17.1 Product and test completion

* The mandatory V1.0 implementation/Premium Hardening scope has been completed or explicitly removed from the agreed release scope.
* All seven supported locales have received the defined incremental coverage; German final visual acceptance and AB-DEF-022 closure are recorded in AB-EV-060.
* Public-profile, map, visit-photo, responsiveness and privacy corrections have been validated incrementally; final Release Candidate regression confirms those checkpoints remain valid.
* Remaining Premium Hardening findings are closed or explicitly dispositioned under the approved release decision.
* PA-001 AC-017 has been resolved under PA-007 / Final Premium RC or explicitly accepted at the final gate with adequate alternative evidence.
* The compensating PA-001 post-publication architecture audit has been completed and recorded before Premium Hardening is finally closed.
* Final compatibility/performance/responsiveness and accessibility residual risks have been assessed.
* The permanent automated suite required by the final candidate has been executed and analysed.
* The final broad regression has been completed.

### 17.2 Release decision

* All release-blocking defects have been resolved.
* Corrected defects have passed retesting.
* Required regression has passed.
* Automated failures have been investigated.
* Unexecuted scenarios and known limitations have been assessed.
* Any acceptable residual risk has been recorded.
* The Test Lead has approved the release.

### 17.3 Deployment and production validation

* The approved final version has been deployed.
* Production smoke testing has been completed.
* Required `pt-BR` and `en-GB` coverage has been completed or any exceptional limitation has passed explicit risk assessment.
* Production-only scenarios have been executed where required.
* Any production failure has been resolved or explicitly accepted when eligible.

### 17.4 Production data reset

After controlled production validation and before real users are admitted:

* disposable test accounts are deleted;
* Firebase Authentication test identities are removed;
* Firestore test data is removed;
* Storage test objects are removed where applicable;
* usernames and public profiles are cleared;
* no demonstration profile remains;
* no test visits, memories, photos, badges, statistics or orphaned records remain;
* public URLs do not expose deleted test records.

### 17.5 Clean-start confirmation

The production reset is complete only after both technical and functional confirmation.

Technical confirmation must establish that the relevant accounts and data have actually been deleted.

Functional confirmation must establish that:

* the application starts with an empty production state;
* no demonstration or preloaded user data appears;
* cache or fallback behaviour does not repopulate the database;
* the empty-state experience works;
* the first real user will begin with a clean account and no inherited data.

Executing a deletion command without verifying its result is insufficient.

### 17.6 Final V1.0 authority

The Test Lead makes the final decision that V1.0 is:

* `Approved`;
* `Approved with accepted residual risk`;
* `Blocked`.

The application being online does not by itself mean that V1.0 is ready for real users.

---

## 18. Authority matrix

| Stage                     | Product implementation / review actor                       | Test Lead                                                         |
| ------------------------- | ------------------------------------------------------------ | ----------------------------------------------------------------- |
| Implementation completion | Antigravity/Gemini reports implementation and technical readiness | Reviews whether the change may enter validation                   |
| Independent review        | Codex reviews when the gate requires it; no publication authority | Confirms/uses review result in publication decision               |
| Functional validation     | Coordinator/agents support execution, investigation and evidence collection | Defines scenarios, expected results and required depth            |
| Defect correction         | Designated implementation agent implements correction and reports readiness | Retests, assesses regression and closes/reopens the defect        |
| QA Portfolio              | ChatGPT coordinator reconciles evidence/control docs         | Reviews/finally approves decision state                           |
| Regression                | Agents support scripted/automated execution                  | Defines scope and approves completion                             |
| Residual risk             | Agents identify and describe limitations                    | Accepts or rejects residual risk                                  |
| Deployment recommendation | Agents may recommend approval or blocking                   | Formally approves or blocks deployment                            |
| Production smoke          | Agents support execution and technical investigation         | Assesses results and approves, blocks or orders corrective action |
| V1.0 launch               | Provides implementation and execution evidence               | Holds final launch authority                                      |

---

## 19. Assumptions, open questions and proposed evolution

### Assumptions

The assumptions already identified in the Product Overview, Quality Risk Analysis, Test Strategy and Test Scope remain applicable until validated or revised.

### Open/current questions

There is no open localization feature gate after AB-EV-060. Current V1.0 work is Premium Hardening.

The next active finding is PA-003 photo Admin/server-route lifecycle and input-contract hardening. Later Premium findings remain sequenced and must not be pulled into PA-003 without evidence of a direct blocker.

Separate carry-forwards remain PA-001 AC-017, the PA-001 compensating post-publication architecture audit, PA-002 physical Storage cleanup deferral and PA-006A deterministic `tsx` tooling declaration.

### Proposed evolution

As AtlasBadge and the quality process mature, these criteria may be extended to support:

* a dedicated test or staging environment if justified;
* continuous Playwright execution;
* broader compatibility gates;
* quantitative performance thresholds;
* formal accessibility conformance gates;
* additional contributors or approval roles.

These are future improvements and are not automatically current V1.0 requirements.

---

## 20. Review and maintenance

These criteria must be reviewed when there is a material change to:

* the AtlasBadge architecture;
* the authentication/data-storage/server-ownership model;
* the privacy model;
* visit-photo quota/read/release/cleanup semantics;
* the supported platforms or locales;
* the deployment process;
* the test environments;
* the automation strategy;
* the release scope;
* independent-review requirements;
* quality ownership or approval authority.

The Test Lead remains responsible for ensuring that the criteria continue to reflect the real AtlasBadge delivery and testing process.