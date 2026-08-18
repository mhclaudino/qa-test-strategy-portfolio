# AB-DEF-007 — Rapid status changes could lose the latest local intent

## Defect summary

| Field | Value |
|---|---|
| Defect ID | AB-DEF-007 |
| Evidence ID | AB-EV-022 |
| Related risks | QR-01; QR-04; QR-16; QR-18; QR-19; QR-22 |
| Environment | Firebase Auth/Firestore Emulators; Playwright concurrency regression; later Production parity through AB-EV-024 |
| Severity | High |
| Status | Closed after explicit-intent correction, repeated concurrency regression and Production validation |
| Correction commit | `3ee18c8` |

## Expected result

Rapid status changes must finish in the latest valid explicit state requested by the user. Rebase, retry or rollback must not re-toggle against a later state and invert that intent.

## Observed result

A sequence such as **Want to visit ON → Visited ON** could finish without the user's final intended state after queue replay/reconciliation.

## Root cause

The queue stored a relative toggle mutator. Replaying it against a different confirmed state could produce a different result from the state originally requested. A stale rollback path could compound the problem.

## Correction

Status mutations were converted to explicit replayable intents carrying the desired final active state. The desired state is captured once and stays idempotent during rebase, retry and replay. `statusRules` still controls compatibility consequences.

## Permanent validation

Coverage includes repeated original-race executions, ON/OFF/ON stress, last-intent-wins, Firestore confirmation, reload parity, activation chronology and genuine external-conflict protection.

## Final decision

```text
AB-DEF-007 closed
QR-04 remains Regression risk with explicit-intent and rapid-mutation coverage
```
