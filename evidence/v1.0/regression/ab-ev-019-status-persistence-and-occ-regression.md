# AB-EV-019 — Status Persistence and Optimistic-Concurrency Regression Closure

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-019 |
| Related risks and defects | QR-01; QR-04; QR-16; QR-18; QR-19; QR-22; AB-DEF-005 |
| Product | AtlasBadge V1.0 |
| Evidence type | Defect correction, Firestore reconciliation and permanent E2E regression |
| Owner | Test Lead/Product Owner |
| Execution period | 2026-08-01 to 2026-08-05 |
| Runtime correction | `ff8d13ab923c988cf8f7d459681e9e251f34cf17` |
| Final approved Production baseline | `982091c` |
| Final decision | Passed — AB-DEF-005 closed; QR-04 retained as Regression risk |

## 2. Purpose

This evidence records the correction and closure of a Production regression in which a permitted country status appeared optimistically and then disappeared after synchronisation.

The evidence extends the existing QR-04 concurrency baseline with permanent coverage for a single valid status intent, confirmed Firestore state, subscription reconciliation and reload parity.

## 3. Approved rule

The approved AtlasBadge rule is:

- **Visited + Nationality** is a valid combination;
- a selected permitted status must remain active;
- optimistic UI and Firestore must converge;
- a later snapshot must not erase a confirmed local intent;
- reload must reproduce the persisted state;
- `visitsCount`, `RegisteredVisit` and memories must not change unless required by the selected status.

## 4. Defect traceability

The public defect record is:

- [AB-DEF-005 — Added status disappeared after optimistic synchronisation](../defects/status-added-disappears-after-sync.md).

Selected screenshots show:

- the valid status immediately after selection;
- the same status missing after synchronisation.

## 5. Correction

Commit `ff8d13ab923c988cf8f7d459681e9e251f34cf17` corrected the optimistic-concurrency race and status-persistence path.

The correction ensures that:

- queued work is evaluated against current confirmed state;
- a single intent is retained;
- successful local state converges with Firestore and subscription output;
- rollback does not remove a later or confirmed intent;
- true external conflicts remain protected.

## 6. Permanent automated validation

| Suite | Result |
|---|---|
| Mutation orchestrator | 7/7 Passed |
| TravelMapContext QR-04 | 14/14 Passed |
| TravelMapContext general | 3/3 Passed |
| Status-persistence E2E | 2/2 Passed |
| Rapid-mutation E2E | 11/11 Passed |
| TypeScript | Passed |
| Lint | Passed |
| Next.js production build | Passed |
| `git diff --check` | Passed |

The E2E path verifies UI state, Firestore state and reload state rather than relying only on the absence of an error message.

## 7. Production validation

The Test Lead approved the corrected behaviour in Production:

- the status remained selected after at least five seconds;
- reload retained the same state;
- a second country also retained a permitted status;
- no false external conflict appeared;
- visits, counters and memories remained correct.

## 8. Risk decision

QR-04 remains a **Regression risk** because future changes to optimistic state, task queuing, snapshots or OCC could reintroduce the failure.

QR-01 remains a **Current gap** only for equivalent failure, reload and recovery coverage outside the registered-place persistence paths already evidenced.

## 9. Traceability

```text
AB-DEF-005
→ valid Visited + Nationality rule
→ optimistic state displayed
→ stale reconciliation removed the new status
→ mutation and reconciliation correction ff8d13a
→ 2 status-persistence E2E scenarios
→ 11 rapid-mutation E2E scenarios
→ Firestore and reload parity
→ Production retest approved
→ AB-EV-019
→ QR-04 Regression risk
```

## 10. Final conclusion

The status-persistence regression is corrected, protected by permanent automation and approved in Production.
