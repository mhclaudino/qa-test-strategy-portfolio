# AtlasBadge Metrics and Reporting

## 1. Document purpose

This document defines how AtlasBadge quality information is collected, interpreted and communicated to support testing, publication, deployment and release decisions.

The goal is not to maximise the number of metrics. It is to provide the Test Lead with reliable, contextual evidence about risk, execution, defects/findings, quality gates, environments, versions, residual risk and readiness.

> **Document status:** Completed and maintained through AB-EV-067. Reporting now includes the seven-locale closure, Node 24 technical RC, PA-001 governance/test-oracle reconciliation and PA-002 exact-oracle/final-byte/independent-approval/publication chain. Product `49abb003a9b495f3be43b4e30ea4b36dcfb8ceca` and Vercel `dpl_FTp3iCiARWMSejRv12FcHtaMcWeR` are the current published Premium Hardening anchors. Final Premium closure and final V1.0 Go Live are not claimed.

---

## 2. Relationship with the portfolio

Read together with:

- `docs/01-product-overview.md`
- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/09-system-test-plan.md`
- `docs/10-lessons-learned.md`
- `GOVERNANCE-ERRATA.md`
- `evidence/v1.0/evidence-register.md`

The Evidence Register is the public index. Detailed operational logs/scratch output remain outside the public portfolio unless sanitised and intentionally promoted.

---

## 3. Reporting principles

### 3.1 Every indicator supports a decision

Useful questions include:

- Is the implementation ready for Test Lead validation?
- Is the current environment valid/safe?
- Is a quality risk sufficiently covered?
- Is a correction ready for retest/regression?
- Is a named acceptance criterion actually proven?
- Is the candidate the same artefact that was independently reviewed?
- Can commit/push/deployment proceed?
- Does the deployed SHA match the approved commit?
- What remains unexecuted or accepted as residual risk?

### 3.2 Counts require context

Raw totals do not equal quality. A high pass count can hide a missing Critical oracle; fewer tests can provide stronger confidence when they target the right risk.

Always retain meaningful denominators and scope. Examples:

```text
13 / 13 quota-lifecycle Emulator tests PASS
10 / 10 slot-server Emulator tests PASS
10 / 10 route Emulator tests PASS
6 / 6 Context ordering tests PASS (mock persistence boundaries)
```

Do not flatten these into “39 real Firestore tests”.

### 3.3 Facts, interpretation and decision are distinct

```text
FACT: command/test/deployment/hash result
INTERPRETATION: what the fact means for risk/coverage
DECISION: Test Lead approval, accepted residual risk or block
```

An agent/tool result is evidence input, not release authority.

### 3.4 Missing information remains visible

Use explicit states such as:

- `NOT EXECUTED`
- `BLOCKED`
- `INCONCLUSIVE`
- `NOT APPLICABLE`
- `NOT VERIFIED`
- `COMPLETE WITH LIMITATIONS`

Missing information is never silently converted to PASS or zero.

### 3.5 No single quality score

AtlasBadge does not use an overall readiness percentage, defect-density target, automation-percentage target, pass-rate target or productivity score.

A readiness colour may summarise a decision, but it must be supported by the underlying risks, blockers, evidence and Test Lead disposition.

### 3.6 Warning/test totals are not identity

For final qualification, a total may stay constant while the underlying warning or acceptance cases change.

Therefore:

- lint comparison uses warning identity (`file`, rule, semantic message) where the total changes or masking is possible;
- acceptance reporting preserves the authoritative named-oracle matrix;
- deleted/remapped acceptance tests are reported as coverage loss even if the aggregate suite remains green.

PA-002 demonstrated all three situations.

---

## 4. Quality-reporting layers

| Layer | Main audience | Purpose |
|---|---|---|
| Operational | Test Lead / authorised agents | Investigation, correction and detailed execution control |
| Change/finding checkpoint | Test Lead / reviewer | Decide whether the change can move to review/publication |
| Deployment checkpoint | Test Lead | Verify approved commit, remote SHA and deployment identity/status |
| Release-level summary | Test Lead / future collaborators | Consolidate risks, gates, residuals and final recommendation |
| Public portfolio evidence | Recruiters / QA leadership / public readers | Demonstrate risk-based QA, governance and decisions without exposing secrets/private data |

---

## 5. Data sources

Current sources include:

- GitHub commits/branches/issues;
- Product and Portfolio documents;
- named acceptance matrices;
- Vitest/Playwright/Firebase Emulator results;
- Firestore/Storage Rules results;
- TypeScript/ESLint/build/diff checks;
- candidate/staged/commit hashes;
- Vercel deployment metadata/source SHA/status;
- manual/exploratory Test Lead observations;
- sanitised screenshots/evidence records;
- independent Codex review decisions.

Automatically produced results may still be stale, tied to the wrong bytes, incomplete or based on an invalid oracle. Source and artefact identity are therefore part of the evidence.

---

## 6. Core indicators

### 6.1 Risk status

Report:

- Critical/High risks and current state;
- Current gaps versus Regression risks;
- accepted residuals;
- carry-forwards and their closure trigger;
- whether a Premium finding is open, blocked, independently approved, published or deferred.

A risk is not “gone” merely because one implementation closes its current defect.

### 6.2 Acceptance-oracle completeness

For lifecycle/concurrency/security work, report the authoritative requirement IDs and one of:

- `PROVEN`
- `PARTIAL`
- `MISSING`
- `WRONG ORACLE`

Approval requires the defined threshold for that finding. PA-002 required all original PA2 criteria to be `PROVEN`.

### 6.3 Test execution

Report exact layers separately, including PASS/FAIL/SKIP/BLOCKED and environment.

For PA-002 final independent approval:

```text
Focused lifecycle: 24 / 24 PASS
Requested PA-002 run: 39 / 39 PASS
  quota lifecycle: 13 / 13
  slot server: 10 / 10
  route: 10 / 10
  Context ordering: 6 / 6
Rules: 242 / 242 PASS
C44 Edge: 1 / 1 PASS; realFirebaseRequests=0
Clear Map: 8 / 8 PASS
Full Vitest: 911 PASS / 67 SKIPPED / 0 FAIL
Vitest files: 125 PASS / 10 SKIPPED
```

Context ordering tests use mocked persistence boundaries; this limitation is part of the report rather than hidden by the aggregate count.

### 6.4 Static/build quality

Report:

- TypeScript result;
- ESLint errors/warnings and relevant warning-identity delta;
- production build result;
- `git diff --check`;
- relevant dependency/runtime/tooling limitations.

PA-002 final lint evidence is:

```text
baseline HEAD: 38 warnings
approved candidate: 37 warnings
errors: 0
PA-002-created warnings: 0
```

The one legitimate removed warning is recorded by identity rather than celebrated as generic “lint improvement”.

### 6.5 Environment safety

Report effective application origin, Firebase project and Emulator endpoints where relevant.

`realFirebaseRequests=0` is valid only for the protected Emulator run that actually reached the guard/assertion. It must not be extrapolated to Production.

### 6.6 Independent review

Where required, report:

- exact candidate scope/hash set reviewed;
- reviewer decision (`APPROVE`, `CHANGES REQUIRED`, `BLOCKED`);
- findings/closure state;
- whether review occurred before publication.

PA-001 contains a review-order governance deviation and open compensating **post-publication** audit. PA-002 received final independent APPROVE before publication.

### 6.7 Final-byte integrity

For high-risk Premium publication, report the exact chain:

```text
approved working bytes
→ staged bytes
→ committed tree bytes
→ GitHub main SHA
→ Vercel Production source SHA
```

Any mismatch invalidates the assumption that the deployed artefact is the independently approved artefact.

### 6.8 Deployment identity

Report commit, parent when relevant, subject/scope, remote branch SHA, Vercel deployment ID, target, READY/ERROR state and source SHA.

Current PA-002 publication evidence:

```text
Product commit: 49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
Parent: d0116967fc389353191c11e8865f20e38b9d5835
Subject: fix(visit-photo): enforce active quota lifecycle
Vercel: dpl_FTp3iCiARWMSejRv12FcHtaMcWeR
Target: production
State: READY
Source SHA: 49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
```

READY is deployment evidence, not by itself functional Production PASS.

---

## 7. Defect and finding reporting

Follow `docs/07-defect-management.md`.

Keep separate counts/classifications for:

- Product Defects;
- Premium findings;
- test-oracle/automation defects;
- fixture defects;
- environment/infrastructure incidents;
- release-control blocks;
- governance/process deviations;
- accepted limitations/residual risk.

Do not inflate Product-defect metrics with stale tests, fixture reconciliation, workstation interruptions or review-order errors.

A single High/P0 defect may be more important than many Low items; severity/priority/affected journey remain visible.

---

## 8. Quality-gate reporting

A gate may be:

- `PASS`
- `FAIL`
- `BLOCKED`
- `NOT EXECUTED`
- `NOT APPLICABLE`
- `NOT VERIFIED`
- `FAIL — EXCEPTION ACCEPTED` where explicitly authorised

An accepted exception does not rewrite a failed gate as PASS.

A pipeline containing a required unresolved failure is not described as fully green.

---

## 9. Evidence completeness

Use:

- `Complete`
- `Complete with Limitations`
- `Incomplete`
- `Not Applicable`
- `Not Verified`

Completeness means sufficient evidence for the decision, not maximum screenshot/log volume.

For a material Premium finding, evidence should normally connect:

```text
finding / requirement
→ quality risks
→ implementation
→ acceptance oracles
→ test layers
→ defects/test/process findings
→ remediation/retest
→ independent review
→ Test Lead decision
→ commit/push
→ deployment exact SHA/status
→ residuals / next scope
```

---

## 10. Readiness summary

Readiness remains a concise contextual signal, not a calculated score.

### Red

Examples:

- Critical/P0 uncontained issue;
- privacy/data-integrity release blocker;
- required acceptance criterion missing/partial;
- invalid/unsafe environment;
- required independent review missing;
- candidate/deployed bytes do not match approval;
- insufficient evidence for a material change.

### Amber

No Red condition, but explicit conditions/residuals/limitations remain before the next gate or final release decision.

### Green

All mandatory gates for the current checkpoint are satisfied, evidence is sufficient, residuals are accepted and the Test Lead approves progression.

A finding-level Green does not automatically mean final V1.0 Go Live Green.

---

## 11. Current Premium Hardening reporting checkpoint

### PA-001 / AB-EV-066

Report separately:

- functional implementation `b0fef1d...` and exact READY deployment;
- AC-017 as a bounded PA-007/Final RC carry-forward;
- missed pre-publication Codex architecture review as governance deviation;
- compensating **post-publication** audit still open;
- UTF-8 test-oracle-only repair `d011696...`, independently reviewed/published;
- no false claim that the hotfix changed runtime Product behaviour.

### PA-002 / AB-EV-067

Report:

- all original PA2 criteria `PROVEN`;
- exact lifecycle/concurrency/retry/current-reference semantics;
- final independent Codex `APPROVE` on frozen bytes;
- final gates and limitations by layer;
- 38→37 lint delta by identity, 0 new warnings;
- Product `49abb003...` → GitHub main → Vercel `dpl_FTp3...` READY exact SHA;
- no authenticated Production mutation required for the publication proof;
- physical Storage deletion intentionally deferred;
- PA-003 next;
- cached undeclared `tsx` follow-up under PA-006A.

PA-002 being Production READY does not equal final Premium Hardening or final V1.0 Go Live approval.

---

## 12. Change checkpoint template

```markdown
## Change / finding checkpoint

### Scope
- Finding/change:
- Product base/candidate:
- Affected risks:
- Authoritative acceptance criteria:

### Validation
- Test layers/results:
- Environment:
- Safety/isolation:
- Static/build gates:

### Findings
- Product defects:
- Test/oracle/fixture/environment/governance findings:
- Retest/regression:

### Independent review
- Required?:
- Candidate hash/scope:
- Decision:

### Evidence and residuals
- Evidence ID/link:
- Limitations/carry-forwards:

### Test Lead decision
- Approved / Approved with accepted residual risk / Blocked
- Next action:
```

---

## 13. Deployment checkpoint template

```markdown
## Deployment checkpoint

### Approved artefact
- Base SHA:
- Approved candidate hashes:
- Independent review:

### Publication
- Commit SHA / parent / subject:
- Staged/committed byte verification:
- Remote main SHA:

### Deployment
- Vercel deployment ID:
- Target/state:
- Source SHA:
- Alias:

### Production validation
- Functional Production smoke executed?:
- If not, why not?:
- Real Firebase mutation?:

### Residuals
- Accepted limitation:
- Follow-up finding:

### Test Lead decision
- Publication result:
```

---

## 14. Public portfolio reporting

Public evidence should demonstrate decisions and risk control, not volume.

Include enough context to understand what was tested, which environment/version, important defects/findings, final decision and residuals. Exclude credentials, UIDs/e-mails, private user data, action links, tokens/cookies, `.env`/service-account values, raw AI logs, private screenshots/payloads and unnecessary private source excerpts.

Historical evidence remains truthful to its original decision state. Later evidence supersedes current state without rewriting earlier facts.

---

## 15. Metrics deliberately not used as quality targets

AtlasBadge does not currently target:

- overall pass-rate percentage;
- automation percentage;
- number of tests;
- number of defects;
- defect density;
- average/mean repair time;
- commit count;
- lines of code;
- AI command count;
- individual productivity metrics.

Performance thresholds may be introduced only after PA-011 establishes a repeatable baseline and an approved decision need.

---

## 16. Reporting cadence

Reporting is event-driven. Produce/update checkpoints when a material change/finding reaches validation, a Critical/High/P0/P1 issue appears, a required gate/review changes state, publication/deployment occurs, Production validation completes, residual risk is accepted or a release decision is required.

Do not produce repetitive “all green” reports when no checkpoint changed.

---

## 17. Roles

- **Tools/automation:** produce facts/results.
- **Antigravity/Gemini:** Product implementation/test/config handoff; no independent approval; no QA Portfolio ownership by default.
- **Codex:** independent review/audit when required; no publication authority.
- **ChatGPT coordinator:** requirements/risk/governance coordination and QA Portfolio documentation/evidence reconciliation.
- **Test Lead/Product Owner:** selects/accepts indicators, resolves ambiguity, accepts residual risk, approves publication/deployment/release.

---

## 18. Current constraints and planned improvements

Current limitations include manual consolidation, incomplete CI/dashboard automation, incomplete browser/device matrix, no formal performance SLA, no independent penetration test and no formal accessibility certification/native-AT matrix.

Planned improvements may include machine-readable gate summaries, risk/test/defect/evidence linkage, CI capture, performance baselines, stronger automation-health reporting and the final V1.0 Test Summary Report.

PA-006A owns deterministic test-tooling follow-up for the cached undeclared `tsx` dependency. PA-003 is the next active Product hardening service.

---

## 19. Review triggers

Review this model when risk definitions, test environments, defect process, actor ownership, independent-review/publication gates, evidence structure, CI, supported browser/device scope, performance baselines or release criteria change materially.

---

## 20. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
