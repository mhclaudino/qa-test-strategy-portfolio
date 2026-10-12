# AtlasBadge Defect Management

## 1. Document purpose

This document defines how AtlasBadge identifies, classifies, records, prioritises, investigates, corrects, retests, regresses, defers, accepts, reopens and closes defects and defect-like findings.

It applies to incremental development, Premium Hardening, controlled deployment/Production validation and V1.0 release preparation.

The process is intentionally proportional. Significant quality failures receive formal traceability; test-oracle, fixture, environment, governance and technical-task findings are kept distinct from Product Defects so defect metrics remain meaningful.

> **Document status:** Completed and maintained through AB-EV-067. The current examples include AB-DEF-022 closure, PA-001 review-order governance deviation and UTF-8 test-oracle repair, and PA-002 Product lifecycle/concurrency defects versus test, recovery and scope-hygiene findings. Actor ownership follows `GOVERNANCE-ERRATA.md`.

---

## 2. Relationship with other portfolio documents

Read together with:

- `docs/01-product-overview.md`
- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `docs/10-lessons-learned.md`
- `GOVERNANCE-ERRATA.md`
- `evidence/v1.0/evidence-register.md`

Quality-risk priority, defect severity, defect priority and test-scope priority are related but not interchangeable.

---

## 3. Core principles

1. **Observed behaviour is compared with an approved expectation.** Undefined requirements or later desired behaviour are not automatically Product Defects.
2. **Root cause is not required to confirm a defect.** It is required only when the claim is made that a cause is confirmed.
3. **Severity and priority remain independent.**
4. **Documentation depth is proportional to risk and traceability value.**
5. **Implementation completion is not closure.** Direct retest, affected regression and Test Lead decision are still required.
6. **Evidence outranks confidence.** A plausible AI/developer explanation is not a confirmed cause without a verifiable chain.
7. **Lower-severity items may combine into release-blocking risk.**
8. **Production response begins with user/data protection and containment.**
9. **Sensitive information is sanitised before evidence is published.**
10. **The Test Lead owns final classification, severity/priority, residual-risk, deployment and closure decisions.**
11. **A green test can protect the wrong requirement.** Stale/malformed oracles are repaired as test evidence rather than forcing Product behaviour to match them.
12. **A governance deviation is not automatically a Product Defect.** Preserve the real chronology and correct the control forward.

---

## 4. Classification boundaries

### 4.1 Product Defect

An implemented AtlasBadge behaviour that violates an approved requirement, business rule, acceptance criterion, security/privacy/data-integrity condition or established Product behaviour.

Examples include persistence loss, privacy exposure, broken auth, wrong counters, inaccessible essential controls, incorrect public projection, server lifecycle failures or concurrency lost updates.

### 4.2 Test-oracle / automation defect

The Product is correct against the current requirement but the automated expectation, locator, fixture assumption or test flow is stale, malformed or weaker than the required oracle.

Examples:

- C45 localization tests that retained superseded labels;
- PA-001 route tests that expected mojibake while runtime returned correct UTF-8;
- PA-002 temporary deletion/remapping of original PA2-06/PA2-07 acceptance cases.

A test-oracle defect does not increase Product-defect metrics.

### 4.3 Test-data / fixture defect

Prepared QA state violates Product invariants even though valid Product state behaves correctly.

Example: a rich C45G Emulator fixture created earned achievements without running required metadata reconciliation; data-only repair restored dependent surfaces.

### 4.4 Environment / infrastructure incident

Execution is invalid or blocked by environment rather than Product behaviour, such as unavailable Emulators, port conflicts, wrong Firebase target, runtime/dependency transport failure or browser-session/origin mismatch.

### 4.5 Configuration / release-parity issue

Application, Firebase Rules, runtime or deployment configuration differs from the approved/required state. If this reaches Production and harms users, it may still be managed with Production-defect urgency even when the technical cause is configuration.

### 4.6 Governance / process deviation

An approved actor, review, publication or evidence-control rule was violated while the resulting Product may still be technically valid.

PA-001 is the current example: commit/push occurred before the planned independent architecture review. The correct response is transparent chronology plus a compensating **post-publication** audit, not a fictional retroactive pre-publication review or automatic Product rollback.

### 4.7 Known limitation / accepted residual

An understood restriction that does not violate the currently promised behaviour and whose residual risk is explicitly recorded.

PA-002 physical Storage deletion is an example: logical access and quota correctness are independently proven, while physical cleanup remains deferred.

### 4.8 Enhancement / technical task

Future or internal work that does not correct a current Product failure. Examples include broader CI/tooling hardening, observability, performance baselines and unreachable-code cleanup.

### 4.9 Quality risk

A credible possibility of future failure that has not yet materialised as an observed defect. Managed in `docs/02-quality-risk-analysis.md`.

### 4.10 Inconclusive

Evidence is insufficient to establish pass/fail, correct expectation or valid environment. Inconclusive does not equal PASS.

### 4.11 Not a Defect / Requirement Correction

Use when behaviour matches the approved baseline or when the approved requirement later changes. C35 is the historical requirement-correction example.

---

## 5. Initial investigation

Before confirmation, establish where applicable:

- approved expected behaviour / acceptance oracle;
- actual behaviour;
- affected Product commit/deployment;
- environment/browser/device and Firebase project/backend;
- account/data preconditions;
- reproducibility and timing;
- relevant source/network/Firestore/Storage evidence;
- whether the automation/fixture/environment could explain the result;
- whether the requirement itself changed.

The item may remain `Under Review` until enough evidence exists.

For delegated AI investigation, LL-66 applies: one focused reproduction plus at most one evidence-based retry before STOP/BLOCKED and rescoping.

---

## 6. When a formal AB-DEF record is required

A formal Product defect record is expected when any of the following applies:

- Production impact;
- Critical/High severity or P0/P1 priority;
- data integrity, authentication, authorisation, privacy or security impact;
- essential journey blocked/unreliable;
- release/acceptance criteria affected;
- material investigation/retest/regression required;
- recurrence/intermittency;
- containment/rollback/hotfix/defer/accepted risk required;
- the history materially improves future traceability.

A minor issue may remain in execution evidence when it is isolated, corrected within the same bounded work, fully validated, not release-relevant and unrelated to data/privacy/security/essential behaviour.

Premium Architecture findings use their PA identifiers as the authoritative finding IDs. They do not require artificial AB-DEF numbering merely because implementation/review uncovers sub-findings. A genuine independently useful Product defect may still receive an AB-DEF ID when warranted.

---

## 7. Operational source of truth and identifiers

GitHub Issues in the Product repository are the operational source of truth for formally recorded Product defects when an Issue is created.

Stable public defect IDs use:

```text
AB-DEF-###
```

Premium hardening findings use:

```text
PA-###
```

The QA Portfolio publishes only selected sanitised evidence. Raw AI logs, credentials, private payloads and scratch files are not public evidence.

---

## 8. Minimum defect record

Initial registration normally includes:

| Field | Requirement |
|---|---|
| ID/title | Stable identifier and concise failure statement |
| Date/status/classification | Current lifecycle and provisional/final class |
| Area/environment | Product area plus browser/device/backend context |
| Affected version | Commit/build/deployment |
| Preconditions/reproduction | State and steps where relevant |
| Expected / actual | Approved oracle and observation |
| Reproducibility | Always/frequent/intermittent/once/not reproducible |
| Severity / priority | Impact and urgency kept separate |
| Release impact | Effect on current validation/publication/launch |
| Evidence | Sanitised logs/screenshots/test/network/DB evidence |

Lifecycle information may later add root-cause evidence, correction, commit, retest/regression, reopens, residual risk, deployment result and final Test Lead decision.

---

## 9. Severity

| Severity | Typical impact |
|---|---|
| **Critical** | Unauthorised access/private-data exposure, severe/irreversible corruption/loss, critical auth failure, cross-user modification/deletion, broad unavailability or essential journey failure without safe workaround |
| **High** | Significant core failure, silent persistence rejection/lost update, major regression, severe accessibility/performance failure or impractical workaround |
| **Medium** | Limited/recoverable secondary-flow or compatibility/usability impact with acceptable workaround |
| **Low** | Cosmetic/minor presentation/usability issue without meaningful data/privacy/security/core-flow impact |

Severity describes intrinsic impact, not correction order.

---

## 10. Priority

| Priority | Meaning |
|---|---|
| **P0** | Immediate containment/correction; blocks safe continuation/release |
| **P1** | Must be resolved before the next relevant release/dependent work |
| **P2** | Planned correction; risk controlled |
| **P3** | Low cost-of-delay / later refinement |

Priority considers severity, active Production exposure, release timing, frequency, reach, workaround, dependencies and whether continued testing is meaningful/safe.

---

## 11. Lifecycle

Main Product-defect lifecycle:

```text
New
→ Under Review
→ Confirmed
→ In Progress
→ Ready for Retest
→ Ready for Regression
→ Awaiting Test Lead Validation
→ Closed
```

Alternative outcomes include `Retest Failed`, `Reopened`, `Deferred`, `Duplicate`, `Cannot Reproduce`, `Not a Defect` and `Accepted Risk`.

Test-oracle, fixture, environment, technical-task and governance findings may be redirected to the appropriate evidence/process backlog instead of being forced through the Product-defect lifecycle.

---

## 12. Investigation and root-cause evidence

Investigation distinguishes:

```text
observation
→ evidence
→ hypothesis
→ suspected cause
→ confirmed root cause
```

Confirmed cause requires a verifiable chain connecting the failure, mechanism, responsible condition/change, removal/control of that mechanism and corrected result.

Suitable evidence includes before/after deterministic tests, logs/rejected requests, sanitised payload comparison, code inspection linked to reproduction, Rules/Storage behaviour, runtime A/B proof and direct retest.

If the chain is incomplete, use `working hypothesis`, `suspected cause` or `investigation pending`.

---

## 13. Retest and regression

Every reported correction receives direct retest against the original failure.

Where relevant, retest includes actual persistence, reload/session recovery, negative/boundary paths, rollback, concurrent/repeated actions and preservation of existing data.

Regression scope is selected from changed code plus semantic dependencies and quality risks. Critical/High/P0/P1, Production, cross-cutting, persistence/privacy/auth/destructive/server-boundary changes receive broader regression.

A complete repository regression is not mandatory for every correction. Checkpoints are preserved unless invalidated.

For named Premium acceptance matrices, all original oracles must remain represented. A similar test or aggregate pass count does not replace a missing named criterion.

---

## 14. Closure

A Product defect closes only when the Test Lead confirms:

- direct retest PASS;
- required affected regression PASS;
- no unacceptable related regression;
- evidence referenced;
- cause/correction recorded where applicable;
- residual risk understood/accepted;
- deployment/Production result completed where the closure depends on it.

Implementation completion, compilation or an AI-generated “fixed” statement is insufficient.

---

## 15. Deferral and accepted risk

Deferral/accepted-risk records state the limitation, impact, reason, containment/workaround, affected release/context, follow-up trigger and Test Lead decision.

Active uncontained unauthorised access, private-data exposure, severe data loss/corruption, critical auth/authorisation failure, cross-user modification or an unusable essential journey without safe workaround are not accepted for Production.

---

## 16. Production defects and hotfixes

Serious Production response is:

```text
protect users/data
→ contain
→ rollback/disable/hotfix decision
→ preserve sanitised evidence
→ investigate
→ correct
→ direct retest
→ affected regression
→ Test Lead publication approval
→ deploy exact approved version
→ Production smoke where required
→ close/post-incident review
```

C45M/AB-DEF-021 and C45N/AB-DEF-022 are recent Product-defect examples. Harness/environment/test-oracle events encountered around them were not inflated into additional defects.

---

## 17. Premium Hardening classification examples

### PA-001

- **Premium finding:** client Clear Map ownership conflicted with later server-managed photo-slot state; implementation moved protected mutation to server/Admin transaction.
- **Governance deviation:** publication happened before planned Codex architecture review — process deviation, not automatically a Product Defect.
- **Test-oracle defect:** three route expectations contained mojibake while runtime UTF-8 was correct — test-only hotfix, no runtime correction.
- **Carry-forwards:** AC-017 browser UI→API→Admin Emulator reload proof; compensating post-publication architecture audit.

### PA-002

- **Product lifecycle defects/findings closed inside PA-002:** last durable tombstone was initially not physically cleared from the existing Firestore document; full-quota recovery path was initially unreachable; stale snapshot reconciliation could overwrite a concurrent saved edit; tested lifecycle helpers were initially disconnected from Context.
- **Test coverage defect:** original PA2-06/PA2-07 acceptance regressions were temporarily deleted; final approval required restoration of the exact original oracles.
- **Evidence/process findings:** PA2-13/14 initially proved weaker paths than claimed; lint cleanup temporarily removed unrelated baseline warnings; final gate chronology required re-freezing/requalification.
- **Recovery incident:** workstation shutdown interrupted one remediation cycle; controlled byte-for-byte recovery restored a reverted route Emulator test and protected untracked candidate files. No AtlasBadge root cause was claimed for the shutdown.
- **Final classification:** all original PA2 criteria independently PROVEN; PA-002 closed/published. Physical Storage deletion remains an accepted separate cleanup concern, not an unresolved PA-002 Product Defect.

---

## 18. AI-assisted investigation and actor ownership

AI tools may inspect, execute tests, generate hypotheses and produce factual handoffs, but actor authority is defined by `GOVERNANCE-ERRATA.md`:

- ChatGPT coordinates requirement/risk/evidence and owns QA Portfolio documentation;
- Antigravity/Gemini is the default Product implementation agent;
- Codex provides independent review when required;
- the Test Lead owns final classification, severity, priority, risk acceptance, publication and closure.

A confident narrative without reproducible or inspectable evidence is not authority.

---

## 19. Evidence and privacy

Public defect/evidence material excludes real UID/e-mail/name, tokens, cookies, passwords, API keys, service-account data, signed/private URLs, private notes/memories, unnecessary security-sensitive payloads and raw AI/scratch logs.

Use sanitised placeholders when needed, for example:

```text
<REDACTED_UID>
test-user@example.invalid
<REDACTED_TOKEN>
```

---

## 20. Deployment / release blocking

Deployment/release is blocked when evidence shows or cannot safely exclude:

- Critical defect;
- active privacy/security/data-integrity risk;
- essential-flow failure;
- unresolved significant regression;
- incomplete required retest/regression;
- unknown/mismatched approved version;
- required independent review not complete;
- final candidate bytes differing from approved bytes;
- insufficient evidence for material High/Critical scope;
- unacceptable cumulative lower-severity risk.

---

## 21. Current process constraints and follow-up

Current process can operate with GitHub Issues, stable IDs, manual severity/priority, sanitised evidence, Test Lead triage, direct retest and impact-based regression.

Planned improvements include Issue templates/labels, stronger automated reporting, CI expansion and observability. These are not represented as already implemented.

The legacy UK-selector modal remains a technical cleanup candidate, not a Product Defect.

PA-003 photo Admin/server-route lifecycle/input-contract hardening is the next active Premium finding. Its read-only audit may identify new Product defects or structural debt, but it must not pre-classify every Admin-route concern as a defect before comparing behaviour with the approved contracts.

---

## 22. Review triggers

Review this document when defect lifecycle/record structure, severity/priority definitions, actor ownership, evidence controls, independent-review/publication requirements, server-boundary architecture, test classification or release blocking rules materially change.

A suitability review also occurs before final V1.0 launch and after any material Production incident.

---

## 23. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-055-c45j-public-profile-localization-and-fixes.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
- `GOVERNANCE-ERRATA.md`
