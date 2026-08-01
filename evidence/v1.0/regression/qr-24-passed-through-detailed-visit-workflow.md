# AB-EV-016 — QR-24 Passed Through Detailed-Visit Workflow Closure

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-016 |
| Related quality risks | QR-24; affected-area regression for QR-01, QR-02, QR-04, QR-05, QR-18, QR-19, QR-20, QR-22 and QR-23 |
| Product | AtlasBadge V1.0 |
| Evidence type | Product-rule confirmation and permanent automated regression |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-07-31 |
| Final decision | Passed — QR-24 moved to Regression risk |

## 2. Purpose

This evidence closes an outdated quality-risk gap concerning the **Passed through** status.

The initial risk statement assumed that **Passed through** contributed to presence and visit totals without offering an equivalent detail or memory workflow.

The implementation audit confirmed that the current product already provides the complete approved workflow. No runtime correction was required. Permanent automated tests were added to prevent the rule from being misunderstood or regressed.

## 3. Approved product rule

**Passed through** represents genuine physical presence, such as an airport connection or short transit through a place.

The approved behaviour is:

- selecting **Passed through** with no previous physical visit creates the first visit;
- that visit is represented by a `RegisteredVisit`;
- the first `RegisteredVisit` is not artificial: it represents the physical passage selected by the user;
- the user may record multiple passages through the same place;
- each passage may contain date, duration and memory details;
- the detailed `VisitEditor` is the correct editor for this status;
- `generalNote` is reserved for memory content without a detailed physical visit;
- typing changes only local draft state;
- persistence occurs only through the explicit **Save** action;
- repeated passages increase **Total Visits**;
- the place continues to count only once as conquered;
- memories remain private;
- **Visited** and **Passed through** remain mutually incompatible;
- approved status transitions preserve visit history and memories.

## 4. Technical traceability

| Area | Reference |
|---|---|
| Physical-presence classification | `src/lib/statusRules.ts` |
| Initial visit creation and visit-state transitions | `src/lib/visitRules.ts` |
| Detailed visit and memory interface | `src/components/CountryActionCard.tsx` |
| Visit mutation and increment controls | `src/context/TravelMapContext.tsx` |
| Total Visits and conquered-place calculations | `src/lib/stats.ts` |
| Permanent QR-24 coverage | `src/lib/passedThrough.qr24.test.ts` |
| Test-only commit | `cf5382d` — `test(visits): cover Passed through workflow for QR-24` |

The audit baseline was commit `751e3f6`.

No production file was changed during the QR-24 closure. The full test-commit SHA remains available in the private product repository and Git history.

## 5. Permanent automated validation

| ID | Scenario | Result |
|---|---|---|
| QR24-UT-01 | Activate **Passed through** without previous physical presence. | Passed — exactly one visit is created. |
| QR24-UT-02 | Inspect the initial passage. | Passed — a corresponding `RegisteredVisit` exists. |
| QR24-UT-03 | Add another passage. | Passed — `visitsCount` increases correctly. |
| QR24-UT-04 | Add multiple passages. | Passed — all occurrences remain registered. |
| QR24-UT-05 | Save a memory for one passage. | Passed — the note remains attached to its `RegisteredVisit`. |
| QR24-UT-06 | Type memory content without saving. | Passed — persisted state is not changed. |
| QR24-UT-07 | Open the details workflow for **Passed through**. | Passed — `VisitEditor` remains available. |
| QR24-UT-08 | Inspect the primary memory model. | Passed — `generalNote` is not used as the main passage editor. |
| QR24-UT-09 | Recalculate **Total Visits**. | Passed — every registered passage is included. |
| QR24-UT-10 | Recalculate conquered places. | Passed — the place is counted once. |
| QR24-UT-11 | Change **Passed through** to **Visited**. | Passed — the incompatible status is removed. |
| QR24-UT-12 | Change **Visited** to **Passed through**. | Passed — the incompatible status is removed. |
| QR24-UT-13 | Perform a status transition with existing visit details. | Passed — visit history is preserved. |
| QR24-UT-14 | Remove physical-presence status. | Passed — the approved preservation rule remains intact. |
| QR24-UT-15 | Compare personal-map and public-profile metrics. | Passed — calculations use the same canonical data. |
| QR24-UT-16 | Inspect memory publication behaviour. | Passed — memories remain private. |
| QR24-UT-17 | Exercise persistence rollback and retry coverage. | Passed — confirmed state remains consistent. |
| QR24-UT-18 | Combine **Passed through** with an allowed status. | Passed — existing compatibility rules remain active. |

Execution summary:

- focused QR-24 tests: **18/18 Passed**;
- complete Vitest suite: **139/139 Passed**;
- directed ESLint validation: **Passed**;
- global lint: **Passed without a new QR-24 violation**;
- Next.js production build: **Passed**;
- `git diff --check`: **Passed**;
- staged change contained only `src/lib/passedThrough.qr24.test.ts`;
- HEAD and `origin/main` were aligned after push;
- working tree was clean;
- no temporary, environment or sensitive file was committed.

## 6. Deployment position

Commit `cf5382d` adds automated regression coverage only.

It does not alter runtime code, Firebase Rules, product configuration or Production data. A new functional Production smoke was therefore not required for QR-24 closure.

The runtime behaviour documented here was already present in the approved product and was verified through the static implementation audit and the permanent automated suite.

## 7. Risk decision

QR-24 is no longer a `Current gap`.

It is retained as **Regression risk** because a future change could:

- stop creating the initial passage;
- incorrectly route **Passed through** to `generalNote`;
- remove the detailed editor;
- prevent multiple passages;
- count a place more than once as conquered;
- exclude passages from **Total Visits**;
- expose private passage memories;
- delete visit history during a status transition;
- permit an incompatible **Visited + Passed through** combination.

## 8. Traceability

```text
QR-24
→ Passed through represents physical presence
→ first passage creates one RegisteredVisit
→ detailed VisitEditor
→ explicit Save
→ multiple passages permitted
→ canonical Total Visits
→ one conquered place
→ private memories
→ safe status transitions
→ no runtime correction required
→ 18 permanent automated tests
→ commit cf5382d
→ 139-test full regression
→ AB-EV-016
→ QR-24 Regression risk
```

## 9. Final conclusion

The current **Passed through** workflow matches the Product Owner-approved AtlasBadge V1.0 rule.

The former gap was a documentation and coverage gap rather than a product defect. Permanent automated protection is now present, and no further runtime or release action remains open for QR-24.
