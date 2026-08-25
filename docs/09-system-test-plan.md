# AtlasBadge V1.0 System Test Plan

**Document status:** Active / change-controlled  
**Execution status:** Incremental system testing in progress; latest baseline reviewed through AB-EV-035  
**Product:** AtlasBadge  
**Target release:** V1.0  
**Document owner:** Test Lead/Product Owner  
**Last updated:** 25 August 2026

---

## 1. Document purpose

This plan defines how the AtlasBadge V1.0 system-test cycle is prepared, executed, controlled, evidenced and concluded.

It converts the product, risk, strategy, scope, environment, defect and reporting decisions into a concrete release-validation model. It is change-controlled as the product progresses toward the official V1.0 release.

Incremental feature/release approvals do not automatically mean the entire V1.0 product has completed final release assessment. They do, however, create valid checkpoints that may be carried forward when later changes do not invalidate them.

---

## 2. Test-plan control

A material update is required when:

- mandatory V1.0 scope changes;
- a quality risk changes state/priority;
- a material defect changes required coverage;
- an environment/browser/device decision changes;
- Firebase/Rules/public projection architecture changes;
- a new implementation invalidates an approved checkpoint;
- the Release Candidate changes;
- an exception affects deployment or release.

Each update should identify what changed, affected risks/tests, which previous results remain valid, which require re-execution and the Test Lead decision.

AB-EV-033 established the current checkpointed regression baseline; AB-EV-034 and AB-EV-035 demonstrate proportional follow-up validation without unnecessary full-suite repetition.

---

## 3. Relationship with the QA portfolio

This plan applies:

- [Product Overview](01-product-overview.md)
- [Quality Risk Analysis](02-quality-risk-analysis.md)
- [Test Strategy](03-test-strategy.md)
- [Test Scope](04-test-scope.md)
- [Entry and Exit Criteria](05-entry-exit-criteria.md)
- [Test Environments](06-test-environments.md)
- [Defect Management](07-defect-management.md)
- [Metrics and Reporting](08-metrics-and-reporting.md)
- [Lessons Learned](12-lessons-learned.md)
- [V1.0 Evidence Register](../evidence/v1.0/evidence-register.md)

Where documents disagree, the inconsistency must be resolved before the affected release decision.

---

## 4. Product and release context

AtlasBadge includes authentication/account lifecycle, an interactive travel map, six travel statuses, detailed visits/memories, Wishlist management, Manual Visit Order, geographic counters, achievements, private/public profiles, public sanitised projections, account deletion, responsiveness, localisation, privacy and security controls.

The integrated test item includes the browser application plus Firebase Authentication, Cloud Firestore, Firestore Rules, Firebase Storage where relevant, local cache/state, Firebase Emulators, Vercel deployment and geographic data.

The C35 product rule defines **Visited + Passed through** as compatible cumulative statuses. Individual travel occurrences remain represented by `RegisteredVisit`; adding the second status does not by itself create another visit.

Previous incremental testing is valid evidence when impact analysis confirms that a later change has not invalidated it.

---

## 5. Test item and version identification

Every material execution record should identify where applicable:

- branch and full Git SHA;
- application target (local Emulator runtime or Production);
- Vercel deployment corresponding to the SHA;
- Firebase project/Emulator identifier;
- relevant Rules version/deployment;
- browser/device/viewport;
- execution date;
- test-data state.

The branch name `main` alone is not sufficient evidence because its content changes.

### Current release anchors

The latest sequence is:

```text
AB-EV-033 runtime/security baseline:
7bbdb9402145523f6a2f36d41cc74e55479cc664
Vercel: dpl_HEKQuz6MAXiW413m6cqnH25zWrRg — READY

AB-EV-034 QR-01 evidence-only closure:
66cffbc933710f2b9f4ba007c5726ebc2857ac82
test(profile): cover flagSortOrder failed-write recovery
No runtime deployment required for the technical closure.

C35 runtime requirement correction:
29c7ac63748fb823899fb77cdb6ee91bb6194b1f
fix(status): allow visited and passed through coexistence
Vercel: dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv — READY / production
```

C35 did not alter Firestore Rules, Firebase configuration or database schema.

---

## 6. System-test objectives

The V1.0 cycle provides evidence that AtlasBadge can be released safely within the declared scope and limitations.

Objectives include:

- validate registration/authentication/session/account lifecycle;
- confirm correct user ownership and isolation;
- validate all approved status/Wishlist combinations;
- verify reliable persistence, failed-write recovery, reload and concurrency behaviour;
- protect visits/memories/ordering from unintended loss or artificial duplication;
- confirm the canonical geographic/counter model;
- confirm private/public Profile projection and sanitisation;
- confirm public Profile/Wishlist remains read-only;
- validate achievement chronology and public metadata;
- validate complete account deletion/cleanup;
- assess declared localisation, browser/device, responsive and accessibility scope;
- verify release/deployment parity;
- support a defensible Test Lead release recommendation.

---

## 7. Mandatory critical path

Release-blocking coverage includes:

- application availability;
- authentication/verification/recovery/linking/session/logout;
- owner identity and protected routes;
- travel-status/Wishlist persistence and compatibility;
- visits/memories/order/concurrency;
- rejected-write recovery where risk-relevant;
- privacy/user isolation;
- public projection whitelisting/sanitisation;
- public/private transitions;
- account deletion;
- core counters/geographic model;
- achievements where affected;
- responsive usability of critical controls;
- Vercel/Firestore Rules parity when Rules change.

---

## 8. Test levels and execution layers

### 8.1 Static / build quality

Depending on affected code:

- TypeScript;
- lint;
- `git diff --check`;
- Next.js production build when production code/build configuration is affected and the relevant checkpoint is invalidated.

### 8.2 Unit/domain/component tests

Vitest protects stable business/component behaviour.

Important recent checkpoints:

```text
AB-EV-033 integrated checkpoint:
359 passed / 15 skipped / 0 failed

C35 full domain/component checkpoint after statusRules change:
373 / 373 PASS

C35 focused QR-24 status/history checkpoint:
22 / 22 PASS

AB-EV-034 focused flagSortOrder recovery:
1 / 1 PASS
```

### 8.3 Firestore Rules

Rules validate owner-only private access, public projection boundaries and approved data schemas.

Latest AB-EV-033 Rules checkpoint:

```text
226 / 226 PASS
```

AB-EV-034 and C35 did not modify Rules, so this checkpoint remains valid.

### 8.4 Browser E2E — Emulator

Normal persistence/privacy E2E uses:

```text
App: 127.0.0.1:3100
Auth: 9099
Firestore: 8080
Project: demo-atlasbadge-web
```

The suite fails fast against unexpected real Firebase traffic.

C35 specifically restored the changed browser contract with one focused scenario:

```text
Visited + Passed-through coexistence/persistence/reload/deselection: 1 / 1 PASS
realFirebaseRequests=0
```

### 8.5 Manual/exploratory QA

Used for usability, visual behaviour, edge conditions, unexpected interaction sequences and Test Lead sign-off.

C35 received Test Lead manual QA before commit approval, including coexistence, memory/counter behaviour and independent status handling.

### 8.6 Production validation

Executed only after the expected source revision and applicable Firebase Rules are deployed/aligned.

Production testing uses controlled QA accounts/data and only the authorised scope.

For C35, release verification confirmed the exact runtime SHA reached a `READY` Production Vercel deployment and the Production root returned HTTP 200. No separate authenticated post-deployment functional smoke is claimed in AB-EV-035.

---

## 9. Checkpointed incremental regression

### Principle

A previously green checkpoint remains valid unless a later change directly invalidates what it proved.

Examples:

- E2E-only stale locator cleanup does not invalidate full unit/Rules checkpoints;
- a persistence change invalidates affected persistence tests and relevant static/build gates;
- a Rules change invalidates Rules/release-parity coverage;
- a responsive CSS change requires affected responsive/manual/E2E validation;
- formatting performed after a gate invalidates only the gate required to verify the final file form unless semantics changed.

This is not reduced coverage by omission: the carried-forward result and reason must remain explicit.

### Recent applications

**AB-EV-034:** the write-path audit identified only `flagSortOrder` as the remaining Partial QR-01 path. One deterministic component failure/recovery test closed the gap; no runtime deployment or unrelated regression rerun was required.

**C35/AB-EV-035:** central `statusRules.ts` changed, justifying focused QR-24 tests and one full Vitest checkpoint. Browser impact was restored through one focused Emulator E2E; a full Playwright campaign was not required because no wider browser contract was invalidated.

The efficiency rules are retained in `docs/12-lessons-learned.md`.

---

## 10. Defect and requirement-change handling

A failed scenario is classified before correction as:

- product defect;
- stale/test-automation defect;
- infrastructure/environment issue;
- inconclusive.

A later requirement correction is not counted as a Product Defect when the implementation correctly matched the previously approved product rule.

C35 is the reference example: QR24-UT-11/12 were valid tests for the old rule and became stale only after Product Owner/Test Lead redefinition. They were updated without inventing a Product Defect ID.

---

## 11. Pre-release repository quality gate

Before commits are approved, the candidate is audited for:

- unexpected tracked/untracked files;
- scratch/debug artefacts;
- secrets/credentials;
- accidental Production/test configuration changes;
- stale skips/`.only`/debugger/logging;
- diff integrity;
- coherent commit grouping.

Auto-fix/formatting should occur before final validation whenever possible so that the final committed artefact is the artefact that passed the quality gates.

---

## 12. Release and deployment gate

For releases that alter Firestore Rules:

```text
1. Test Lead approves commits.
2. Create controlled commits.
3. Test Lead approves push.
4. Push to main.
5. Verify Vercel READY at expected SHA.
6. Test Lead explicitly approves Rules deployment.
7. Deploy only firestore:rules.
8. Confirm success and no unrelated Firebase resource deployment.
9. Start focused Production validation.
```

For application-only changes such as C35, a Rules deploy is not performed when Rules did not change.

No force push/rebase/deploy shortcut is used to bypass this gate.

---

## 13. Latest Production/release evidence

AB-EV-033 remains the latest broad authenticated Production validation and recorded Wishlist/public projection, privacy, desktop/mobile and release-parity PASS results.

Later release evidence:

```text
AB-EV-034:
test-only QR-01 coverage closure — no runtime deployment required

AB-EV-035 / C35:
Commit: 29c7ac63748fb823899fb77cdb6ee91bb6194b1f
Vercel Production: dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv
State: READY
Production root: HTTP 200
Firestore Rules/schema/config: unchanged
```

The absence of an additional authenticated Production functional smoke for C35 is stated explicitly rather than inferred from deployment readiness.

---

## 14. Evidence and traceability

Material system-test evidence must connect:

```text
Requirement / rule
→ Quality risk
→ Implementation
→ Automated/manual tests
→ Defect(s) or requirement correction, if applicable
→ Retest / regression
→ Commit
→ Deployment
→ Production result
→ Test Lead decision
```

The central public index is `evidence/v1.0/evidence-register.md`.

Current recent evidence is AB-EV-033 through AB-EV-035.

---

## 15. Exit and release assessment

An increment may be approved when:

- required tests/gates pass;
- no unacceptable Critical/High defect remains;
- privacy/security/data-integrity boundaries are acceptable;
- repository quality is clean;
- source/deployment/Rules parity is proven where relevant;
- required Production validation is complete or its limitation explicitly accepted;
- residual risks are retained in the risk register.

The official V1.0 final release assessment additionally depends on completion/acceptance of the remaining mandatory scope, including localisation/compatibility/performance or final reset/clean-start activities as applicable.

The final release decision belongs to the Test Lead/Product Owner.

---

## 16. Current limitations carried into later V1.0 work

- QR-01 is now a Regression risk rather than a Current gap.
- Browser/device coverage is not comprehensive.
- Quantitative performance targets are not established.
- Formal accessibility certification/native assistive-technology coverage is not claimed.
- No independent penetration/security audit or formal load test has been completed.
- Remaining V1.0 localisation/final release activities must be assessed separately.
- C35 has deployment-readiness verification but no separately claimed authenticated post-deployment functional smoke.

---

## 17. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
- `docs/12-lessons-learned.md`
