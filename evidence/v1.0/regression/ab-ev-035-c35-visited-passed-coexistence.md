# AB-EV-035 — C35 Visited + Passed Through Coexistence

## Evidence summary

**Evidence ID:** AB-EV-035  
**Change:** C35 — Visited and Passed through coexistence  
**Classification:** Requirement / scope correction; no Product Defect  
**Primary risks:** QR-16 status compatibility; QR-24 Passed through workflow  
**Supporting risks:** QR-18, QR-19, QR-21, QR-22 and QR-23  
**AtlasBadge commit:** `29c7ac63748fb823899fb77cdb6ee91bb6194b1f` — `fix(status): allow visited and passed through coexistence`  
**Vercel deployment:** `dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv` — Production `READY`  
**Test Lead decision:** Requirement correction approved after automated validation and manual QA

---

## 1. Requirement correction

The original product rule treated **Visited** and **Passed through** as mutually exclusive. The implementation and earlier QR-24 tests correctly reflected that approved rule.

The Product Owner/Test Lead later identified that the rule itself did not represent realistic travel history. A person may:

- pass through an airport and later visit the country;
- visit a country and later pass through it during another journey;
- live or be born in a place and still have a later transit occurrence there.

C35 therefore reclassifies **Passed through** as cumulative historical information that may coexist with **Visited**. Because **Lived / Live there** and **Born there** imply **Visited**, `passed` may also coexist with those statuses.

This was a requirement/scope correction, not a Product Defect: the product had been implementing the previously approved rule.

---

## 2. Domain-model decision

No new persistence model was required.

The existing model already separates:

- long-lived place status in `statuses`; and
- individual travel occurrences in `registeredVisits`.

Adding the second compatible status must therefore **not** create another `RegisteredVisit` or increment `visitsCount`. A new occurrence is created only through the existing visit-add workflow.

No new Firestore field, collection, migration, per-visit status type or Rules change was introduced.

---

## 3. Implementation

Primary production change:

```text
src/lib/statusRules.ts
```

The correction:

- preserves `passed` during status normalisation when `visited`, `lived` or `born` is active;
- stops `visited`, `lived` and `born` activation from deleting `passed`;
- stops `passed` activation from deleting `visited`, `lived` or `born`;
- preserves the existing Wishlist incompatibility rules for proper physical-presence statuses;
- leaves independent status deselection intact where no existing dependency blocks it.

No unrelated production behaviour was redesigned.

---

## 4. Updated automated contract

Earlier QR-24 unit expectations that enforced mutual exclusion became stale when the requirement changed.

`src/lib/passedThrough.qr24.test.ts` was updated to prove:

- Passed through → Visited retains both statuses;
- Visited → Passed through retains both statuses;
- combined status normalisation retains both;
- existing `registeredVisits` and memories are preserved in both activation directions;
- `visitsCount` does not increase merely because a second compatible status is added;
- either status can be deselected independently when no other dependency prevents the operation.

Final focused QR-24 result:

```text
22 / 22 PASS
```

The broader Vitest checkpoint was executed because `statusRules.ts` is central domain logic:

```text
373 / 373 PASS
```

---

## 5. Focused browser/persistence validation

The existing Emulator status-persistence E2E was extended with one proportional C35 scenario rather than duplicating the full unit matrix.

The focused test proved:

1. activate Passed through;
2. activate Visited;
3. both controls remain active in the UI;
4. Firestore Emulator contains `passed=true` and `visited=true`;
5. selecting the second status does not create an artificial visit-count increment;
6. reload restores both statuses;
7. deselecting Passed leaves Visited active;
8. re-enabling Passed and deselecting Visited leaves Passed active.

Result:

```text
Focused C35 Playwright: 1 / 1 PASS
realFirebaseRequests=0
```

This confirms the intended path across UI → application mutation flow → Firestore Emulator → reload without using real Firebase for the controlled persistence scenario.

---

## 6. Static and repository gates

Final gates:

```text
TypeScript: PASS
ESLint changed files: 0 errors / 0 warnings
git diff --check: PASS
Focused QR-24 Vitest: 22 / 22 PASS
Full Vitest: 373 / 373 PASS
Focused C35 Playwright: 1 / 1 PASS
```

The final working tree before commit contained only:

```text
src/lib/statusRules.ts
src/lib/passedThrough.qr24.test.ts
e2e/status-persistence-regression.spec.ts
```

Test Lead manual QA also passed the corrected coexistence behaviour before commit approval.

---

## 7. Commit and deployment

Approved commit:

```text
29c7ac63748fb823899fb77cdb6ee91bb6194b1f
fix(status): allow visited and passed through coexistence
```

Post-push repository state was clean with `HEAD == origin/main` and `0/0` ahead/behind.

Vercel Git deployment was verified independently:

```text
Deployment: dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv
Target: production
State: READY
Git SHA: 29c7ac63748fb823899fb77cdb6ee91bb6194b1f
```

The Production root also returned HTTP `200` after deployment.

No Firestore Rules deployment, Firebase configuration deployment or database migration was required.

**Evidence boundary:** no separate authenticated post-deployment functional Production smoke is claimed by this record. The functional sign-off consists of focused Emulator automation plus Test Lead manual QA; the release evidence confirms the expected C35 runtime commit reached a READY Production deployment and the Production root remained available.

---

## 8. Decision

**C35 — APPROVED.**

The corrected V1.0 product rule is:

> **Visited and Passed through are compatible cumulative historical statuses. Adding the second status does not itself create another travel occurrence.**

QR-16 and QR-24 remain regression risks protected by the updated permanent tests. Counter/history risks remain protected by the explicit no-artificial-visit rule and the existing `RegisteredVisit` model.

No Product Defect ID was created.

---

## 9. Traceability

```text
C35 requirement correction
→ QR-16 / QR-24 (+ QR-18/19/21/22/23 support)
→ src/lib/statusRules.ts
→ QR-24 domain tests
→ focused Emulator persistence E2E
→ TypeScript / ESLint / Vitest / Playwright PASS
→ Test Lead manual QA PASS
→ commit 29c7ac63748fb823899fb77cdb6ee91bb6194b1f
→ Vercel Production dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv READY
→ requirement correction approved
```

A reusable process lesson from C35 is retained in `docs/10-lessons-learned.md`: temporal user journeys should be validated before historical statuses are declared mutually exclusive, and an adequate existing domain model should be reused before introducing schema complexity.
