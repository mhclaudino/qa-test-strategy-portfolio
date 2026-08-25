# AtlasBadge V1.0 System Test Plan

**Document status:** Active / change-controlled  
**Execution status:** Incremental system testing in progress; latest AB-EV-033 release increment Production-approved  
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

AB-EV-033 applies this approach explicitly through checkpointed incremental regression.

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
- [V1.0 Evidence Register](../evidence/v1.0/evidence-register.md)

Where documents disagree, the inconsistency must be resolved before the affected release decision.

---

## 4. Product and release context

AtlasBadge includes authentication/account lifecycle, an interactive travel map, six travel statuses, detailed visits/memories, Wishlist management, Manual Visit Order, geographic counters, achievements, private/public profiles, public sanitised projections, account deletion, responsiveness, localisation, privacy and security controls.

The integrated test item includes the browser application plus Firebase Authentication, Cloud Firestore, Firestore Rules, Firebase Storage where relevant, local cache/state, Firebase Emulators, Vercel deployment and geographic data.

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

### Latest approved increment

AB-EV-033 release anchors:

```text
Product/security commit:
276b0c9 feat(wishlist): add public wishlist and secure profile projections

E2E/infrastructure commit:
7bbdb94 test(e2e): isolate Firebase emulators and harden regression coverage

Final product SHA:
7bbdb9402145523f6a2f36d41cc74e55479cc664

Vercel Production:
dpl_HEKQuz6MAXiW413m6cqnH25zWrRg — READY
```

Firestore Rules were deployed separately using the Rules-only target before focused Production validation.

---

## 6. System-test objectives

The V1.0 cycle provides evidence that AtlasBadge can be released safely within the declared scope and limitations.

Objectives include:

- validate registration/authentication/session/account lifecycle;
- confirm correct user ownership and isolation;
- validate all approved status/Wishlist combinations;
- verify reliable persistence, reload and concurrency behaviour;
- protect visits/memories/ordering from unintended loss/corruption;
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
- Next.js production build when production code/build configuration is affected.

### 8.2 Unit/domain/component tests

Vitest protects stable business/component behaviour.

Latest preserved AB-EV-033 checkpoint:

```text
359 passed
15 skipped (environment-conditional)
0 failed
```

### 8.3 Firestore Rules

Rules validate owner-only private access, public projection boundaries and approved data schemas.

Latest AB-EV-033 checkpoint:

```text
226 / 226 PASS
```

### 8.4 Browser E2E — Emulator

Normal persistence/privacy E2E uses:

```text
App: 127.0.0.1:3100
Auth: 9099
Firestore: 8080
Project: demo-atlasbadge-web
```

The suite fails fast against unexpected real Firebase traffic. Latest relevant executions recorded `realFirebaseRequests=0`.

### 8.5 Manual/exploratory QA

Used for usability, visual behaviour, edge conditions, unexpected interaction sequences and Test Lead sign-off.

### 8.6 Production validation

Executed only after the expected source revision and applicable Firebase Rules are deployed/aligned.

Production testing uses controlled QA accounts/data and only the authorised scope.

---

## 9. Checkpointed incremental regression

### Principle

A previously green checkpoint remains valid unless a later change directly invalidates it.

Examples:

- E2E-only stale locator cleanup does not invalidate full unit/Rules checkpoints;
- a persistence change invalidates affected persistence tests and relevant static/build gates;
- a Rules change invalidates Rules/release-parity coverage;
- a responsive CSS change requires affected responsive/manual/E2E validation.

This is not reduced coverage by omission: the carried-forward result and reason must remain explicit.

### AB-EV-033 regression gate

The latest increment closed with:

```text
TypeScript: PASS
ESLint: 0 errors / 0 warnings
Vitest checkpoint: 359 passed / 15 skipped / 0 failed
Firestore Rules: 226 / 226 PASS
Wishlist E2E: 3 / 3 PASS
Clear Map Emulator: 2 / 2 PASS
Account Deletion controlled: 6 / 6 PASS
C31: PASS
C32: 2 / 2 PASS
C33: PASS
C34: 22 / 22 PASS FINAL
Remaining Playwright batch: 45 / 45 PASS
Final seven touched specs: 26 / 26 PASS
git diff --check: PASS
Quality Inventory blockers: 0
```

**INCREMENTAL REGRESSION GATE = PASS FINAL.**

---

## 10. Defect handling during the cycle

A failed scenario is classified before correction as:

- product defect;
- stale/test-automation defect;
- infrastructure/environment issue;
- inconclusive.

AB-EV-033 assigned product-defect IDs only to true product failures:

- AB-DEF-014 — missing public place projection;
- AB-DEF-015 — missing public achievement metadata projection;
- AB-DEF-016 — mobile dashboard grid collapsed to zero width.

Stale selectors, invalid test authentication assumptions and obsolete protocol/offline assertions were corrected as test-maintenance debt rather than inflated into product-defect counts.

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

AB-EV-033 found/remediated temporary `[DEBUG]` logs and their dead local variables. Final state:

```text
TypeScript: PASS
Lint: 0/0
diff-check: PASS
Secrets: CLEAN
Temp/debug: CLEAN
PRE-COMMIT AUDIT = PASS FINAL
```

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

No force push/rebase/deploy shortcut is used to bypass this gate.

---

## 13. Latest focused Production validation

AB-EV-033 recorded:

```text
Wishlist add/persist/remove: PASS
Wishlist private/public/public→private: PASS
Wishlist order persistence: PASS
visitOrder independence: PASS
Public Wishlist order: PASS
Anonymous/viewer Profile: PASS
Private viewer reads: 0
Public source: publicProfiles/{uid}
Forbidden public fields: NONE
Public achievementMetadata: PASS
Desktop: PASS
Mobile: PASS
Horizontal overflow: PASS
Modal scroll lock: PASS
Destructive operations: 0
Non-QA accounts modified: 0
```

**PRODUCTION VALIDATION = PASS.**

A temporary Production validation spec was removed afterwards and the product repository returned to a clean `HEAD == origin/main` state.

---

## 14. Evidence and traceability

Material system-test evidence must connect:

```text
Requirement / rule
→ Quality risk
→ Implementation
→ Automated/manual tests
→ Defect(s), if any
→ Retest / regression
→ Commit
→ Deployment
→ Production result
→ Test Lead decision
```

The central public index is `evidence/v1.0/evidence-register.md`.

Latest release-hardening evidence is AB-EV-033.

---

## 15. Exit and release assessment

An increment may be Production-approved when:

- required tests/gates pass;
- no unacceptable Critical/High defect remains;
- privacy/security/data-integrity boundaries are acceptable;
- repository quality is clean;
- source/deployment/Rules parity is proven;
- focused Production validation passes;
- residual risks are explicitly retained in the risk register.

The official V1.0 final release assessment additionally depends on completion/acceptance of the remaining V1.0 mandatory scope, including outstanding localisation/compatibility/performance or final reset/clean-start activities as applicable at that time.

The final release decision belongs to the Test Lead/Product Owner.

---

## 16. Current limitations carried into later V1.0 work

- QR-01 remains Current gap outside specifically validated persistence paths.
- Browser/device coverage is not comprehensive.
- Quantitative performance targets are not established.
- Formal accessibility certification/native assistive-technology coverage is not claimed.
- No independent penetration/security audit or formal load test has been completed.
- Remaining V1.0 localisation/final release activities must be assessed separately rather than inferred from AB-EV-033.

---

## 17. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/defects/ab-def-014-public-place-projection-missing.md`
- `evidence/v1.0/defects/ab-def-015-public-achievement-metadata-not-synchronised.md`
- `evidence/v1.0/defects/ab-def-016-mobile-dashboard-grid-collapse.md`
