# QA Portfolio — Gemini / Antigravity Boundary

Read `AGENTS.md` first. It is authoritative for actor ownership.

In the default AtlasBadge workflow, Gemini/Antigravity is the **Product implementation agent**, not the QA Portfolio author.

When working from an AtlasBadge implementation prompt:

- change Product code/tests/config only within the approved scope;
- run the bounded technical validation;
- return factual results, changed files, Git state, risks and documentation impact to the coordinator;
- do not edit or publish this QA Portfolio unless the Test Lead explicitly delegates that documentation task in the current instruction;
- do not claim defect closure, residual-risk acceptance, Production approval or Test Lead sign-off.

The coordinator/ChatGPT verifies the implementation handoff and owns QA Portfolio documentation/evidence reconciliation. The Test Lead gives final approval.
