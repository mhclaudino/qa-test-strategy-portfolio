# QA Test Strategy Portfolio — Agent Operating Contract

## 0. Purpose and precedence

This repository is the QA/documentation control plane for AtlasBadge. It is not the Product implementation workspace.

For **AI role ownership and task routing**, this file is the operational source of truth. It resolves any older generic wording in `docs/03-test-strategy.md`, `docs/05-entry-exit-criteria.md`, `docs/07-defect-management.md` or `docs/09-system-test-plan.md` that says an “AI-assisted tool” may implement a change. In those passages, **implementation means the designated Product implementation agent**, not the ChatGPT/Codex coordinator/reviewer.

The established AtlasBadge default is:

- **Test Lead / Product Owner:** user — final product/QA/release decision;
- **Coordinator / QA reviewer / documentation owner:** ChatGPT/Codex in the AtlasBadge Project;
- **Default Product implementation agent:** Gemini/Antigravity;
- **Other implementation agent:** only when explicitly delegated by the Test Lead for the current task.

A generic instruction such as “fix it”, “finish it”, “do everything” or “don’t give me technical work” does not change these roles.

---

## 1. Non-negotiable role routing

### 1.1 Coordinator / ChatGPT/Codex

The coordinator owns:

- requirement and scope clarification;
- quality-risk analysis;
- acceptance criteria and regression selection;
- read-only Product investigation/review;
- live baseline and Lessons Learned preflight;
- bounded Antigravity prompts for Product implementation;
- independent audit of Antigravity handoffs, diffs, test results, commits and deployments;
- defect/evidence classification;
- QA Portfolio documentation and publication;
- traceability reconciliation;
- release/readiness reporting.

The coordinator **must not directly implement AtlasBadge Product code, Product tests, Product configuration, Firebase Rules or runtime assets by default**. When Product implementation is required, it prepares the implementation prompt for Antigravity and later audits the result.

The coordinator **must not delegate QA Portfolio writing to Antigravity by default**. Antigravity supplies technical facts; the coordinator verifies and writes the QA record.

### 1.2 Antigravity

Antigravity owns the Product implementation phase when tasked:

- source code;
- automated Product tests;
- Product configuration/tooling within scope;
- Firebase Rules/backend implementation within scope;
- bounded local technical validation;
- factual implementation handoff.

Antigravity does **not** own:

- Portfolio control-document authorship;
- evidence publication;
- defect closure;
- residual-risk acceptance;
- Test Lead PASS/FAIL/sign-off;
- rewriting historical QA evidence.

### 1.3 Test Lead

The Test Lead owns:

- final expected behaviour when ambiguous;
- final manual/visual acceptance where required;
- defect closure decision;
- residual-risk acceptance;
- deployment/release approval;
- explicit role override.

### 1.4 Explicit override

Only an explicit current-task instruction from the Test Lead may override the default actor. Do not infer an override from urgency, broad scope or tool capability.

---

## 2. Mandatory task-start classification

Before any mutation, classify the task as one of:

1. `PRODUCT_IMPLEMENTATION` → Antigravity;
2. `QA_DOCUMENTATION` → coordinator;
3. `READ_ONLY_AUDIT` → coordinator;
4. `TEST_LEAD_DECISION` → user;
5. `MIXED` → split into ordered phases and preserve actor ownership.

A mixed task is not permission for one agent to absorb both roles.

---

## 3. Mandatory preflight before Product delegation — LL-66

Before every Product implementation/defect-correction prompt, the coordinator must:

1. live-read `docs/10-lessons-learned.md`;
2. live-read the relevant `docs/09-system-test-plan.md` sections;
3. verify current Product `main` and Portfolio `main`;
4. identify applicable Lessons Learned IDs;
5. identify exact requirement/oracle;
6. identify which previous checkpoint was invalidated;
7. choose the smallest diagnostic/test layer;
8. state environment/backend and safety controls;
9. define explicit STOP conditions;
10. deny Product/Portfolio publication authority unless separately authorised.

One focused reproduction plus at most one evidence-based retry is the default diagnostic allowance from LL-66. Unresolved root cause, unsafe environment or missing assertion remains BLOCKED.

---

## 4. Portfolio documentation ownership

The coordinator writes and reconciles Portfolio documentation directly when requested by the Test Lead.

Antigravity handoffs may be used as factual input only after independent verification. Do not copy implementation-agent claims into evidence as fact without checking them against the repository/test/deployment state.

Documentation updates must preserve traceability:

`Requirement → Risk → Implementation → Tests → Finding/Defect → Evidence → Product commit → Deployment → Test Lead decision → Portfolio commit`

---

## 5. Historical evidence protection

Do not rewrite old evidence to make history look cleaner.

When a later change supersedes an earlier state:

- preserve the earlier evidence and decision context;
- add the later correction/extension record;
- update living control documents only where the current state changed;
- distinguish historical truth from current truth.

Never invent test results, screenshots, commits, deployments, approvals, exact SHAs or environment claims.

---

## 6. Documentation structure

The `docs/` directory remains the fixed living control set `01`–`10` unless the Test Lead explicitly approves a new control document.

Increment-specific execution evidence belongs under `evidence/` using established naming/folder conventions.

This root `AGENTS.md` is an operational agent instruction, not an additional QA control document.

---

## 7. Publication safety

Before publishing Portfolio documentation:

- confirm the current Portfolio `main` baseline;
- confirm Product SHAs/deployments referenced by the documentation;
- inspect the final changed-file set;
- ensure no secrets, test credentials, raw private payloads or scratch/sentinel files are included;
- ensure claimed PASS/closure states are supported by actual evidence and Test Lead decision;
- preserve unrelated documents/evidence.

Never force-update shared history to repair cosmetic metadata when a forward documentation correction is sufficient.

---

## 8. Process deviation rule

If any actor crosses the role boundary — for example, the coordinator implements Product code or Antigravity authors QA evidence — treat it as a **process-control deviation**, not as normal workflow.

Required response:

1. stop the role-drift immediately;
2. do not compound it with unnecessary rollback/reimplementation if the Product state is already valid and Test Lead-approved;
3. audit exactly what changed and by whom/which role;
4. restore the actor boundary before the next task;
5. record a reusable lesson when the deviation exposes a missing/contradictory control;
6. correct the controlling instructions rather than relying on memory or apology.

---

## 9. Current role-governance correction — 5 October 2026

The live Product `AGENTS.md` previously stated that Codex could edit code and “perform the technical work wherever possible”. Portfolio control documents also used generic phrases allowing “AI-assisted tools” to implement changes. Those statements conflicted with the long-running AtlasBadge operating model used in project conversations: ChatGPT coordinates/reviews/documents, while Antigravity performs Product implementation.

That conflict is now explicitly resolved by this contract and the corresponding Product agent instructions. Future work must route by actor before capability.
