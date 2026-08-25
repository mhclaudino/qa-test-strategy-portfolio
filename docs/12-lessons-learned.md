# AtlasBadge Lessons Learned

## 1. Purpose

This document records reusable lessons learned from AtlasBadge V1.0 product development and quality validation.

It is deliberately not a diary of every defect, test run or implementation decision. A lesson is included only when it changes how future work should be planned, implemented, tested, evidenced or released.

The emphasis is on reducing avoidable work while preserving risk-based confidence and traceability.

> **Document status:** Active / maintained. Initial baseline consolidated from AtlasBadge V1.0 work through AB-EV-035.

---

## 2. How lessons are applied

Each lesson is expressed as:

- **Observation** — what repeatedly caused risk, rework or wasted effort;
- **Working rule** — the process decision adopted for future work;
- **Benefit** — why the rule improves quality or efficiency.

Lessons do not replace the Test Strategy, Quality Risk Analysis or System Test Plan. They explain why specific working rules exist and provide a feedback loop for continuous improvement.

---

## 3. Avoiding unnecessary validation effort

### LL-01 — Do not rerun a green checkpoint unless a later change invalidated what it proved

**Observation:** AtlasBadge accumulated strong unit, Rules, browser, build and Production checkpoints. Re-running every suite after every change created unnecessary execution time and occasionally generated unrelated noise.

**Working rule:** Before re-executing a checkpoint, identify the later change that invalidated the previous result. If no relevant dependency changed, carry the result forward and record the reason.

**Benefit:** Less repetitive testing without lowering confidence.

### LL-02 — State the invalidated checkpoint before selecting the next test

**Observation:** “Run everything to be safe” is not a risk-based test strategy.

**Working rule:** Impact analysis precedes execution. State what changed, which evidence became stale and which smallest test can restore confidence.

**Benefit:** Test effort remains proportional to actual risk.

### LL-03 — Use the smallest test layer that can prove the changed behaviour

**Observation:** Unit/domain tests are faster and more deterministic for business rules, while E2E is valuable only when browser/integration behaviour must be demonstrated.

**Working rule:** Prefer unit/domain coverage for rule matrices; add integration/E2E only for boundaries that cannot be proven below that layer. Do not duplicate the entire unit matrix in Playwright.

**Benefit:** Faster feedback, easier diagnosis and lower maintenance cost.

### LL-04 — A small correction must not automatically become a full regression campaign

**Observation:** C35 changed one central status rule and required focused domain coverage plus one browser persistence path, not a new full Playwright campaign.

**Working rule:** Expand regression only when static impact analysis or focused failures show broader invalidation.

**Benefit:** More time is spent investigating meaningful risk rather than exercising unrelated functionality.

### LL-05 — Run formatting and auto-fixes before final quality gates

**Observation:** `eslint --fix`, whitespace cleanup or other file changes performed after a PASS make the final artefact different from the one that was validated, even when semantics did not change.

**Working rule:** Edit → format/fix → test → final static gates → audit. Any later file modification must be explicitly classified for checkpoint impact.

**Benefit:** Avoids unnecessary micro-gates and ambiguity over which file version actually passed.

### LL-06 — Do not treat an empty diff as evidence for an untracked file

**Observation:** `git diff` does not prove the content of a file that is not yet tracked.

**Working rule:** Inspect untracked files directly or compare them read-only against an empty baseline. Never report an empty tracked diff as proof that an untracked test is clean.

**Benefit:** Prevents false pre-commit confidence.

---

## 4. Requirement and product-definition lessons

### LL-07 — Distinguish Product Defect from Requirement Correction

**Observation:** C35 was implemented according to the original product rule, but the rule itself was later recognised as conceptually wrong.

**Working rule:** When implementation matches the approved requirement, classify a later business-rule change as a requirement/scope correction rather than inflating defect counts.

**Benefit:** Defect metrics remain meaningful and engineering accountability remains fair.

### LL-08 — Validate temporal user journeys, not only static status matrices

**Observation:** The original `Passed through` versus `Visited` exclusivity looked plausible as a static matrix but failed real travel-history sequences such as airport connection → later visit → later connection.

**Working rule:** For historical/cumulative states, requirements must be challenged with realistic event sequences and reverse transitions before exclusivity rules are accepted.

**Benefit:** Product rules better match real user behaviour.

### LL-09 — Separate long-lived status from individual occurrence/history

**Observation:** A travel status describes accumulated history for a place; `RegisteredVisit` describes individual occurrences. Treating a second compatible status as a new occurrence would create artificial visits and incorrect counters.

**Working rule:** Status changes must not create new `RegisteredVisit` records unless the user actually records another occurrence.

**Benefit:** Counters, memories and chronology remain semantically correct.

### LL-10 — Reuse an adequate model before creating schema or migration work

**Observation:** C35 could have been over-designed with per-visit status types, new collections or migrations. Existing statuses plus `RegisteredVisit` already represented the requirement.

**Working rule:** Before adding fields, collections or migrations, test whether the existing domain model can express the rule without ambiguity.

**Benefit:** Smaller implementation, lower migration risk and less regression surface.

---

## 5. Automation and evidence lessons

### LL-11 — A green automated test can protect the wrong requirement

**Observation:** QR24-UT-11/12 correctly enforced the old mutual-exclusion rule and therefore became stale when the requirement changed.

**Working rule:** When a requirement changes, classify affected failures as stale automation before modifying product behaviour. A green test is evidence only for the specification encoded in it.

**Benefit:** Automation supports product truth instead of becoming an obstacle to legitimate change.

### LL-12 — Never weaken a product criterion merely to make automation pass

**Observation:** AtlasBadge encountered stale locators, protocol assumptions, localhost assumptions and incomplete fixtures during release hardening.

**Working rule:** Correct the test when the automation is wrong; correct the product when the product is wrong. Do not lower the acceptance criterion to satisfy a stale script.

**Benefit:** Test suites retain credibility.

### LL-13 — E2E should prove integration boundaries, not reproduce every unit assertion

**Observation:** C35 required one browser scenario proving UI → persistence → reload and independent deselection. The rule matrix remained better protected by domain tests.

**Working rule:** E2E scenarios must demonstrate high-value cross-layer behaviour and avoid duplicating exhaustive lower-layer permutations.

**Benefit:** Lower E2E execution time and maintenance cost with stronger signal.

### LL-14 — Persistence evidence must distinguish optimistic state from confirmed state

**Observation:** Several AtlasBadge persistence investigations showed that an interface can look correct before Firestore confirms the write.

**Working rule:** High-risk persistence tests must identify what is optimistic, what is confirmed, when cache is updated and what survives reload/relogin.

**Benefit:** Prevents false confidence from transient UI state.

### LL-15 — Failed-write coverage must prove recovery, not merely an exception

**Observation:** QR-01 was not closed by proving that a Promise could reject. Closure required evidence that the UI rolled back/reconciled, confirmed cache remained clean, reload did not resurrect rejected state and retry remained safe.

**Working rule:** Failed-write acceptance criteria include failure visibility/reconciliation, confirmed-state integrity, reload behaviour and retry/idempotency where relevant.

**Benefit:** Reduces silent data-loss risk.

### LL-16 — Emulator isolation must be observable

**Observation:** Browser tests intended for Firebase Emulators previously risked environment ambiguity.

**Working rule:** Emulator E2E must fail fast on unexpected real Firebase traffic and report the effective backend. `realFirebaseRequests=0` is retained as useful evidence when available.

**Benefit:** Destructive/failure scenarios remain controlled and repeatable.

---

## 6. Architecture, release and documentation lessons

### LL-17 — Judge atomicity by real commit boundaries, not by the UI action

**Observation:** One Save button or one `Promise.all()` can still represent multiple independent Firestore commits. A visually single action is not automatically atomic.

**Working rule:** Data-consistency analysis must identify the actual transaction/batch boundaries and classify partial-persistence risk before calling a behaviour corruption or a defect.

**Benefit:** More accurate risk analysis and better targeted fault-injection tests.

### LL-18 — Publish evidence after the technical result is stable, while preserving historical records

**Observation:** Updating portfolio documents during investigation created avoidable rework, while rewriting old evidence would erase the decision context that existed at the time.

**Working rule:** Investigation → implementation → focused validation → affected regression → Test Lead decision → commit/push → deployment/Production evidence → documentation. Historical evidence remains unchanged; later evidence records the new state.

**Benefit:** Clear chronology, less documentation churn and defensible traceability.

---

## 7. Standing efficiency rules

The following compact rules apply to future AtlasBadge work:

1. One problem/risk at a time unless a shared root cause is proven.
2. Explain checkpoint invalidation before re-running a large suite.
3. Prefer focused deterministic tests over broad repetitive execution.
4. Finish formatting/fixes before final gates.
5. Do not reopen approved product decisions without new evidence or an explicit requirement change.
6. Do not classify infrastructure or stale automation as Product Defects.
7. Keep commits semantically small enough to audit and roll back.
8. Test-only commits do not require runtime deployment validation merely because repository HEAD changed.
9. Production-source changes require proportional release validation; Rules deploys remain separately authorised.
10. The Test Lead makes quality decisions and final sign-off; mechanical evidence preparation should be automated wherever possible.

---

## 8. Review triggers

Update this document when a project event produces a reusable change in working practice, especially when it:

- prevents repeated unnecessary testing;
- changes how risk is classified;
- exposes a recurring automation weakness;
- improves persistence/recovery validation;
- reduces release or documentation rework;
- identifies an architecture decision that should become a standing rule.

Do not add a lesson merely because an isolated defect occurred.

---

## 9. Related documents and evidence

- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
