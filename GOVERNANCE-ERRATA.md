# AtlasBadge AI Role-Routing Governance Errata

**Status:** Active / authoritative for actor ownership and review-order deviations  
**Date:** 12 October 2026  
**Owner:** Test Lead / Product Owner  
**Applies to:** AtlasBadge Product + `qa-test-strategy-portfolio`

## 1. Why this errata exists

The AtlasBadge workflow used in project conversations has a stable role split:

- ChatGPT coordinates, analyses, reviews, prepares implementation prompts and owns QA documentation/evidence;
- Gemini/Antigravity performs Product implementation and Product automated-test/config changes;
- Codex performs independent review/audit when that gate is required;
- the Test Lead gives final approval.

The versioned repository instructions drifted from that operating model. Product `AGENTS.md` explicitly allowed Codex to edit code and perform technical work directly. Portfolio control documents used generic wording such as “AI-assisted tools may implement a change”, without distinguishing the coordinator from the designated implementation agent.

That ambiguity created a repeatable process failure: tool capability was mistaken for actor authority. Premium Hardening later exposed a second governance risk: a technically valid Product change can still violate the intended quality workflow if publication happens before the planned independent review.

## 2. Corrected authoritative interpretation

Effective immediately, actor ownership is determined before capability.

| Work type | Default owner | Coordinator may do directly? | Antigravity may do directly? |
|---|---|---:|---:|
| Requirement/risk/acceptance analysis | ChatGPT coordinator | Yes | Input only |
| Product source code | Antigravity | **No** | Yes |
| Product automated tests | Antigravity | **No** | Yes |
| Product runtime/build/test config | Antigravity | **No** | Yes |
| Firebase Rules/backend implementation | Antigravity | **No** | Yes when scoped |
| Read-only Product investigation/diff audit | ChatGPT coordinator / Codex | Yes | Yes when tasked |
| Independent pre-publication review | Codex when required by the current gate | Review only | No |
| Antigravity implementation prompt | ChatGPT coordinator | Yes | N/A |
| QA Portfolio control docs | ChatGPT coordinator | Yes | **No** |
| Evidence/defect documentation | ChatGPT coordinator | Yes | Facts/handoff only |
| Defect closure/residual-risk acceptance | Test Lead | Record only | No |
| Final manual/visual PASS | Test Lead | Record only after explicit decision | No |
| Release/deployment approval | Test Lead | Audit/report | No independent authority |

## 3. Documents affected by the clarification

The ten living control documents were reviewed for role-routing relevance.

- `docs/01-product-overview.md` — no actor-routing contradiction requiring change.
- `docs/02-quality-risk-analysis.md` — no actor-routing contradiction requiring change.
- `docs/03-test-strategy.md` — generic AI governance wording must be read under the actor matrix above.
- `docs/04-test-scope.md` — no actor-routing contradiction requiring change.
- `docs/05-entry-exit-criteria.md` — statements that AI tools may implement refer to the designated implementation agent, not the coordinator.
- `docs/06-test-environments.md` — no actor-routing contradiction requiring change.
- `docs/07-defect-management.md` — statements that AI-assisted tools may implement refer to the designated implementation agent; closure remains Test Lead-owned.
- `docs/08-metrics-and-reporting.md` — AI-produced implementation reports remain valid data sources, but role ownership follows this matrix.
- `docs/09-system-test-plan.md` — LL-66 preflight remains mandatory; before that preflight, the actor-routing gate determines who is permitted to mutate which repository/files.
- `docs/10-lessons-learned.md` — LL-66 remains mandatory but is insufficient by itself to prevent role drift or review-order drift; this errata adds both actor-ownership and review-order gates.

For actor ownership and Premium Hardening review ordering, this errata plus each repository's root `AGENTS.md` supersedes older generic wording.

## 4. Mandatory workflow state machine

Every new task starts with exactly one classification:

```text
PRODUCT_IMPLEMENTATION
QA_DOCUMENTATION
READ_ONLY_AUDIT
TEST_LEAD_DECISION
MIXED
```

Routing:

```text
PRODUCT_IMPLEMENTATION
→ ChatGPT read-only analysis/preflight
→ ChatGPT bounded Antigravity prompt
→ Antigravity implementation + technical handoff
→ Codex independent review when the current gate requires it
→ Test Lead manual/final decision where required
→ authorised publication
→ ChatGPT documentation/evidence reconciliation
```

```text
QA_DOCUMENTATION
→ ChatGPT reads live Product/Portfolio state
→ ChatGPT updates Portfolio directly
→ ChatGPT audits final diff/reference state
→ Test Lead decision recorded only when explicitly given
```

```text
MIXED
→ split into the two flows above
→ never let one actor absorb both phases merely because it has write access
```

## 5. Fail-closed mutation rule

Before any Product write, the coordinator checks the intended changed paths.

If any planned change touches Product implementation paths (`src/**`, `e2e/**`, tests, Firebase Rules, package/dependency files, runtime/build/test configuration, Product scripts/tooling or runtime assets), the coordinator must stop before writing and delegate the implementation phase to Antigravity.

The coordinator may mutate Product process-instruction documentation (`AGENTS.md`, `CLAUDE.md`, `GEMINI.md`) because those files govern actor routing rather than Product runtime behaviour.

A broad user instruction such as “fix”, “finish”, “do everything” or “do not give me technical work” does not override this boundary. It means orchestrate the correct actor and return completed work without assigning technical steps to the Test Lead.

Only an explicit current-task instruction such as “ChatGPT, implement this Product code yourself” overrides the default.

## 6. Fail-closed documentation rule

Antigravity does not author or publish QA Portfolio documents/evidence by default.

Its handoff contains factual implementation/test data. ChatGPT independently verifies those facts and owns the documentation reconciliation.

If an Antigravity prompt would ask it to update the QA Portfolio merely for convenience, the coordinator must remove that scope and keep documentation in the coordinator phase.

## 7. LL-66 integration

LL-66 still applies before every Product implementation prompt:

- live-read Lessons Learned and relevant System Test Plan;
- exact baseline/untracked inventory;
- current requirement/oracle;
- invalidated checkpoint;
- environment and Firebase isolation;
- cheapest diagnostic layer;
- bounded reproduction/retry budget;
- explicit STOP condition;
- no publication authority unless separately approved.

The role-routing gate runs **before** LL-66. LL-66 controls how delegated implementation is bounded; this errata controls who is allowed to perform each phase.

## 8. Process deviation response

If role drift or review-order drift occurs again:

1. STOP immediately.
2. Do not hide the deviation or retroactively pretend the correct actor/gate happened.
3. Audit the actual files, commits, tests and deployment impact.
4. Do not create needless rollback/reimplementation when the resulting Product state is valid, safe and Test Lead-approved; correct governance forward.
5. Restore the actor and review-order boundary before any next publication.
6. Update the controlling instruction if a new ambiguity was discovered.

A governance deviation is a process-control deviation, not automatically a Product Defect.

## 9. Root cause of the 5 October 2026 actor-routing deviation

The immediate Product hotfix for the authenticated localized Home menu was technically small and ultimately received Test Lead PASS, but the coordinator implemented it directly instead of routing it to Antigravity.

The underlying process cause was conflicting instructions: the long-running chat workflow expected Antigravity implementation, while Product `AGENTS.md` and generic Portfolio wording permitted the coordinator/AI tool to implement directly. The correction therefore targets the instruction hierarchy, not just the individual task.

No rollback is required solely to recreate the same already-approved hotfix under a different actor. Future Product implementation must use the corrected routing contract.

## 10. PA-001 independent-review ordering deviation

PA-001 introduced a separate governance deviation during Premium Hardening.

The intended control was:

```text
implementation candidate
→ independent Codex architecture review
→ Test Lead publication approval
→ commit/push/deployment
```

The actual sequence incorrectly authorised Product commit/push before the planned independent architecture review of the PA-001 implementation had occurred.

The published implementation itself subsequently passed its qualification gates and remains the accepted Product state. The governance correction is therefore **not** to fabricate a retroactive pre-publication review or to roll back/reimplement valid Product code. The required response is:

1. preserve the true chronology in AB-EV-066;
2. record the missed pre-publication gate as a process-control deviation;
3. perform a compensating **post-publication** independent architecture audit of the exact implementation delta `12a0dd53... → b0fef1d...` before final Premium Hardening closure;
4. never label that compensating audit as pre-publication evidence;
5. require independent review before publication for later Premium findings where that gate is part of the approved workflow.

PA-002 demonstrates the corrected control: multiple remediation cycles remained uncommitted/unpublished until the exact frozen candidate received final independent Codex **APPROVE**, after which the Test Lead authorised a controlled commit/push and exact-SHA Vercel verification.

This deviation remains a governance carry-forward only. It does not reopen PA-001 functional closure and does not block starting PA-003; it must be closed before the Premium Hardening programme itself is finally signed off.
