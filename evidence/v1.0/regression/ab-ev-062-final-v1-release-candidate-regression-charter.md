# AB-EV-062 — Final AtlasBadge V1.0 Release Candidate regression charter

**Evidence ID:** AB-EV-062  
**Date:** 5 October 2026  
**Product:** AtlasBadge V1.0  
**Activity:** Final Release Candidate regression — execution charter  
**Entry decision:** APPROVED TO START by Test Lead on 5 October 2026  
**Current release decision:** NOT YET READY FOR GO LIVE

## 1. Purpose

This charter defines the controlled final V1.0 Release Candidate regression that follows C45N/AB-DEF-022 closure and AB-EV-061 release-readiness reconciliation.

The objective is not to rediscover the entire product from zero. AtlasBadge uses checkpoint preservation: earlier green evidence is carried forward unless the current candidate invalidated what it proved. The final cycle verifies the integrated Release Candidate, executes the permanent release gates, closes the mandatory Windows Google Chrome gap and establishes whether the exact candidate may proceed to final Production smoke.

This charter authorises **test execution and bounded diagnosis only**. It does not pre-authorise Product correction, commit, push, deployment, Firebase Rules deployment, Production mutation, destructive Production testing or Portfolio publication by the implementation agent.

## 2. Entry baseline

Product remote `main` at charter creation:

`5e9839c2d5f497710119c30d360dfe85713ab037` — `docs(process): define Antigravity implementation boundary`

Runtime-functional correction immediately preceding the process-only Product commits:

`8330b902b0e023891a262b2da837397b86966f59` — `fix(i18n): localize authenticated public Home menu`

C45N base German implementation:

`8321975158a389760c0d8d13ae7fb1036b4209c2` — `feat(i18n): add German localization`

QA Portfolio baseline before this charter is the current `main` containing AB-EV-060/061 and the reconciled V1.0 Entry/Exit Criteria.

AB-DEF-022 is CLOSED after Test Lead Production PASS on 5 October 2026.

AB-EV-061 concludes that no broad Product feature or correction is required before final regression begins.

## 3. Standing Lessons Learned controls

The final cycle applies, at minimum:

- **LL-01** — do not rerun a green checkpoint unless a later change invalidated what it proved;
- **LL-02** — identify the invalidated checkpoint before selecting the next test;
- **LL-03** — use the smallest test layer able to prove a changed/failed behavior;
- **LL-05** — final artefact must be the artefact that passed the final quality gates;
- **LL-06** — untracked files require direct inspection; an empty tracked diff is not evidence for them;
- **LL-11** — a green automated test can protect an obsolete requirement;
- **LL-12** — never weaken Product acceptance criteria merely to make automation pass;
- **LL-16** — Emulator isolation must be observable;
- **LL-18** — publish evidence only after the technical state is stable; preserve historical evidence;
- **LL-21** — verify application/Rules parity before classifying Firestore permission failures;
- **LL-22** — `localhost` does not prove local Firebase;
- **LL-23** — environment readiness includes services, identity/state and the actual browser context;
- **LL-66** — agent investigation is bounded and evidence-gated;
- **LL-67** — multiple locale controls must converge through one route/preference/document/control contract.

LL-66 is mandatory. A failure starts with one focused reproduction and at most one evidence-based retry. If root cause remains unresolved after that budget, STOP and return evidence for a new Test Lead decision; do not launch repeated full suites as discovery.

## 4. Repository preflight

Before execution, record:

- current local branch;
- local `HEAD`;
- `origin/main`;
- ahead/behind counts;
- tracked modifications;
- **all untracked paths**;
- Node version;
- npm dependency state relevant to execution.

Expected remote Product baseline is the SHA recorded above. If `origin/main` differs, STOP before testing and report the new SHA/diff relationship.

Do not reset, clean, stash, delete or overwrite unexpected local work. If the working tree contains unknown/protected changes, STOP.

## 5. Environment contract

Normal browser persistence/privacy regression uses:

- application `http://127.0.0.1:3100`;
- Auth Emulator `127.0.0.1:9099`;
- Firestore Emulator `127.0.0.1:8080`;
- Storage Emulator when required;
- Firebase project `demo-atlasbadge-web`;
- `realFirebaseRequests=0` fail-fast evidence.

The existing Playwright config uses real Microsoft Edge (`channel: msedge`). It must not be described as Google Chrome.

The mandatory Windows Google Chrome gate must use an installed **Google Chrome** binary via Playwright Chromium `channel: 'chrome'` or equivalent directly verifiable Chrome launch. Bundled Playwright Chromium is not accepted as evidence for the Chrome/Windows criterion.

A temporary Chrome-specific Playwright config may be created only as an execution artefact, must reuse the existing Emulator safety/global-setup/web-server contract, must not weaken any fail-fast control, and must be removed before the final repository audit unless the Test Lead later approves it as permanent Product test infrastructure.

## 6. Final RC static and build gates

On the unchanged candidate, execute and record exact exit codes/results for:

1. Node 22 confirmation;
2. `git diff --check`;
3. TypeScript no-emit check using the repository's current TypeScript configuration;
4. ESLint current repository gate;
5. full Vitest suite, preserving existing justified skips and reporting PASS/FAIL/SKIPPED totals;
6. geographic catalogue validator where still part of the current release gate;
7. Firestore/Storage Rules suite;
8. account-deletion technical suite if environment prerequisites are available and safe;
9. production build.

Do not auto-fix or format after a gate has passed without invalidating and rerunning the affected final-file gate.

## 7. Final RC browser regression — Firebase Emulators

Run the permanent Emulator Playwright suite against the protected `demo-atlasbadge-web` environment with Edge first, using serial execution/workers=1 as already established.

Required result reporting:

- total PASS / FAIL / SKIPPED;
- exact failed test names if any;
- `realFirebaseRequests` result;
- emulator services actually used;
- first failure step and trace/screenshot path when applicable.

The final integrated scope must retain meaningful evidence for the current Critical/High contracts, including:

- authentication/session/protected navigation;
- status persistence, rapid mutation and latest-intent convergence;
- Wishlist membership/privacy/order;
- visits/memories, explicit Save, privacy and ordering;
- visit-photo/quota/privacy boundaries where covered by permanent tests;
- public/private Profile projection and read-only viewer behavior;
- geographic counters/map interactions;
- achievement metadata/order/notification behavior;
- account/Clear Map lifecycle in Emulator-safe coverage;
- responsive/accessibility critical controls;
- seven-locale routing/presentation and locale-control convergence, including German and AB-DEF-022 regression.

Do not manufacture a second exhaustive locale × every-screen matrix when the permanent suite and carried-forward checkpoints already prove unaffected combinations.

## 8. Mandatory Google Chrome on Windows gate

After the Edge/Emulator candidate is stable, execute a representative Critical/High browser pass using **Google Chrome on Windows** against the same isolated Emulator backend.

At minimum, verify:

1. public localized Home loads and locale selector works;
2. Login/authenticated entry works with the Emulator QA identity;
3. authenticated navigation and avatar menu render correctly;
4. one country status can be selected, confirmed and retained after reload;
5. Map core interaction and selected-place presentation remain usable;
6. Profile access works;
7. public Profile remains read-only and respects the sanitised projection;
8. Badges page renders without layout/runtime failure;
9. locale switching is checked in representative `pt-BR`, `en-GB` and `de-DE` contexts, including the authenticated public-Home menu fixed by AB-DEF-022;
10. representative desktop layout has no critical overflow/hidden-control issue.

Record the actual Chrome version/channel. If Google Chrome is not installed or Playwright cannot launch the real Chrome channel, report the gate BLOCKED; do not substitute Edge or bundled Chromium and call it PASS.

## 9. Failure classification and STOP rules

For every failure, classify before changing files:

- Product defect;
- stale/incorrect test oracle;
- test-data/fixture defect;
- environment/infrastructure problem;
- release-control block;
- inconclusive.

Then use the smallest layer needed to diagnose it.

**STOP immediately and return to the coordinator/Test Lead when:**

- remote baseline changed unexpectedly;
- working tree contains unknown/protected modifications;
- any test attempts real Firebase from the Emulator campaign;
- Firebase backend/Rules identity is ambiguous;
- a Critical/High Product defect is reproduced;
- root cause remains unresolved after one focused reproduction plus one evidence-based retry;
- fixing a failure would require Product source/test/config/Rules changes;
- a test expectation appears stale but the current approved requirement is not proven;
- Google Chrome cannot be proven to be real Google Chrome;
- a destructive Production action would be required.

Do **not** implement a fix in this assignment. A confirmed correction receives a separately scoped task and actor authorisation.

## 10. Publication restrictions

This charter gives no authority to:

- commit Product changes;
- push Product branches;
- deploy Product or Firebase resources;
- alter Production data;
- perform Clear Map/account deletion in Production;
- update/publish QA Portfolio evidence.

The agent returns factual execution results to the coordinator. The coordinator independently verifies them, reconciles QA documentation and obtains the Test Lead decision.

## 11. Required final handoff

Return one concise final report containing:

- branch, HEAD, origin/main and ahead/behind;
- final tracked + untracked working-tree state;
- Node version;
- exact commands run;
- static/build/Vitest/Rules results with exit codes and counts;
- Edge Emulator Playwright PASS/FAIL/SKIPPED and `realFirebaseRequests`;
- actual Google Chrome version/channel and Chrome gate result;
- any failures with classification, first failing step and evidence path;
- carried-forward checkpoints and why they remain valid;
- files changed during this assignment — expected `NONE`; if not none, STOP status and explanation;
- explicit recommendation: `RC TECHNICAL GATES PASS`, `BLOCKED`, or `INCONCLUSIVE`.

Do not claim final V1.0 approval. Final release authority remains with the Test Lead.

## 12. Exit from this charter

A successful result means the candidate may proceed to coordinator audit and Test Lead review for the next gate: final Production smoke.

It does not itself authorise Production reset or Go Live.
