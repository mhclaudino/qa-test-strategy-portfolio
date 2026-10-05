# AtlasBadge AI Role-Routing Governance Errata

**Status:** Active / authoritative for actor ownership  
**Date:** 5 October 2026  
**Owner:** Test Lead / Product Owner  
**Applies to:** AtlasBadge Product + `qa-test-strategy-portfolio`

## 1. Why this errata exists

The AtlasBadge workflow used in project conversations has a stable role split:

- ChatGPT coordinates, analyses, reviews, prepares implementation prompts and owns QA documentation/evidence;
- Gemini/Antigravity performs Product implementation and Product automated-test/config changes;
- the Test Lead gives final approval.

The versioned repository instructions drifted from that operating model. Product `AGENTS.md` explicitly allowed Codex to edit code and perform technical work directly. Portfolio control documents used generic wording such as “AI-assisted tools may implement a change”, without distinguishing the coordinator from the designated implementation agent.

That ambiguity created a repeatable process failure: tool capability was mistaken for actor authority.

## 2. Corrected authoritative interpretation

Effective immediately, actor ownership is determined before capability.

| Work type | Default owner | Coordinator may do directly? | Antigravity may do directly? |
|---|---|---:|---:|
| Requirement/risk/acceptance analysis | ChatGPT coordinator | Yes | Input only |
| Product source code | Antigravity | **No** | Yes |
| Product automated tests | Antigravity | **No** | Yes |
| Product runtime/build/test config | Antigravity | **No** | Yes |
| Firebase Rules/backend implementation | Antigravity | **No** | Yes when scoped |
| Read-only Product investigation/diff audit | ChatGPT coordinator | Yes | Yes when tasked |
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
- `docs/09-system-test-plan.md` — LL-66 preflight remains mandatory; before that preflight, the new actor-routing gate determines who is permitted to mutate which repository/files.
- `docs/10-lessons-learned.md` — LL-66 remains mandatory but is insufficient by itself to prevent role drift; this errata adds the missing actor-ownership gate.

For actor ownership, this errata plus each repository's root `AGENTS.md` supersedes older generic wording.

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
→ ChatGPT independent audit
→ Test Lead manual/final decision where required
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

If role drift occurs again:

1. STOP immediately.
2. Do not hide the deviation or retroactively pretend the correct actor performed the work.
3. Audit the actual files, commits, tests and deployment impact.
4. Do not create needless rollback/reimplementation when the resulting Product state is valid, safe and Test Lead-approved; correct governance forward.
5. Restore the actor boundary before any next task.
6. Update the controlling instruction if a new ambiguity was discovered.

Role drift is a process-control deviation, not automatically a Product Defect.

## 9. Root cause of the 5 October 2026 deviation

The immediate Product hotfix for the authenticated localized Home menu was technically small and ultimately received Test Lead PASS, but the coordinator implemented it directly instead of routing it to Antigravity.

The underlying process cause was conflicting instructions: the long-running chat workflow expected Antigravity implementation, while Product `AGENTS.md` and generic Portfolio wording permitted the coordinator/AI tool to implement directly. The correction therefore targets the instruction hierarchy, not just the individual task.

No rollback is required solely to recreate the same already-approved hotfix under a different actor. Future Product implementation must use the corrected routing contract.
