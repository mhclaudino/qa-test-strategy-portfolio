# AB-EV-022 — Rapid Status Last-Intent and Activation-Chronology Closure

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-022 |
| Related defect | AB-DEF-007 |
| Related risks | QR-01; QR-04; QR-16; QR-18; QR-19; QR-22 |
| Product | AtlasBadge V1.0 |
| Evidence type | Concurrency defect correction and permanent regression |
| Owner | Test Lead/Product Owner |
| Correction commit | `3ee18c8` — `fix(status): persist explicit intents and activation chronology` |
| Decision | Passed — defect closed; QR-04 remains Regression risk |

## 2. Purpose

This evidence records the correction of a rapid same-session status race in which a later explicit user intent could be lost while optimistic state, confirmed snapshots and queued work were being reconciled.

The defect was separate from the earlier false external-conflict defect: the key failure here was loss of the user's latest intended state.

## 3. Approved rule

The desired active state is captured once when the user acts. Queued/replayed work must apply that explicit state rather than re-toggle relative to a later state. Local intents are replayable and idempotent, the latest valid local intent wins, genuine stale writes remain protected by OCC, and activation chronology must match the final confirmed state.

## 4. Defect mechanism

The previous queue stored a relative toggle mutator. During rebase or retry, replaying that mutator could invert a state again instead of reproducing the user's original intent. A stale rollback path could also make an older confirmed state dominate a later valid action.

## 5. Correction

The queue was changed to explicit replayable `setStatus(status, active)` intent semantics. `desiredActive` is captured once at click time and reused during replay, rebase and retry. The approved `statusRules` remain the authority for automatic compatibility consequences.

## 6. Activation chronology and retry classification

Status activation chronology is validated against the final persisted intent rather than an intermediate optimistic transition.

One transient Firestore `FAILED_PRECONDITION` during rapid transaction work was classified as an **expected recovered SDK retry**, not a product defect: the SDK retried, the outer operation resolved, no user-visible error remained, and final UI, backend and reload state were correct.

## 7. Permanent validation

Coverage retains five independent race repetitions, ON/OFF/ON stress, last-intent-wins, Firestore confirmation, reload parity, activation chronology, nationality interactions and genuine external-conflict protection. The final integrated gate containing this correction completed **119/119 E2E Passed**.

## 8. Production position

The controlled Production validator later confirmed status activation/removal/reactivation, UI/Firestore parity and reload persistence; see AB-EV-024.

## 9. Traceability

```text
AB-DEF-007
→ relative toggle replay
→ later local intent could be lost
→ explicit idempotent setStatus intent
→ activation chronology retained
→ repeated race / reload / Firestore regression
→ Production parity through AB-EV-024
→ AB-EV-022
→ QR-04 Regression risk
```

## 10. Final conclusion

Rapid same-session status changes now preserve the explicit latest valid user intent and remain protected by permanent concurrency and reload regression coverage.
