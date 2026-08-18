# [AB-DEF-005] Clear Map achievement metadata reconciliation race

**Evidence ID:** AB-EV-019  
**Evidence status:** Production approved; public narrative complete  
**Classification:** Intermittent data-integrity / reconciliation race  
**Severity:** High  
**Priority:** P0 during investigation  
**Related quality risks:** QR-04, QR-30 and scoped QR-01 persistence integrity  
**Target release:** AtlasBadge V1.0  
**Final status:** Closed after deterministic race protection, confirmed-snapshot gating and repeated Production validation

## 1. Evidence purpose

This record documents an intermittent AtlasBadge Clear Map defect in which achievement metadata could be recreated after the underlying place collection had already been cleared.

The evidence was selected for the public QA portfolio because it demonstrates:

- investigation of an intermittent race condition rather than a simple deterministic UI defect;
- separation of place persistence from achievement-metadata persistence;
- controlled use of Firebase Emulator tests to reproduce concurrency;
- Production validation against the deployed build;
- distinction between a benign Firestore optimistic-concurrency retry and a genuine functional failure;
- iterative root-cause refinement when the first correction proved incomplete.

Raw account identifiers, complete Firestore payloads, credentials, private paths and AI-assisted investigation logs are excluded.

## 2. Initial symptom

During `validate-clear-map` Production validation, the map could become empty while achievement `A1` briefly reappeared and `nextAchievementUnlockSequence` advanced.

The important observation was that the failure could occur even though the final `places` subcollection was empty.

This meant that the defect was not simply "Clear Map failed to delete a place".

## 3. First confirmed race: background place normalisation

The first investigation identified a race between:

1. a background normalisation write for an existing place;
2. Clear Map deleting all place documents;
3. the normalisation transaction retrying after the delete;
4. the original upsert semantics recreating the missing place.

The correction introduced an `updateOnly` option for background normalisation.

If the document no longer exists when the transaction retries, the background normalisation becomes an atomic no-op instead of recreating deleted state.

### Implementation reference

```text
84b49376b2cad2a483cca1cc094a30d4864ccd0b
fix(clear-map): prevent normalization from restoring deleted places
```

The deterministic Emulator test covered:

- `updateOnly` update of an existing document;
- `updateOnly` no-op for a missing document;
- legitimate creation by a normal user mutation;
- concurrent delete followed by transaction retry;
- final confirmation that the deleted place is not resurrected.

Test-only instrumentation was kept out of Production code; the deterministic pause/intercept remained isolated to the test layer.

## 4. Production retest and reopened evidence

The first correction passed Production validation, but a later repeated Clear Map run produced new evidence:

- `places = 0`;
- `A1` reappeared transiently;
- the sequence advanced;
- no place document was recreated.

That result invalidated the assumption that the original place-normalisation race was the only write path involved.

C23 was therefore reopened rather than being treated as permanently closed without new evidence.

## 5. Second confirmed race: achievement metadata hydration queue

The second investigation instrumented the timing of:

- startup hydration;
- cached Firestore snapshots;
- server-confirmed snapshots;
- achievement reconciliation;
- Clear Map;
- achievement metadata sequence allocation.

The failing chain was confirmed as:

```text
page reload
→ initial cache-only snapshot is empty
→ destructive achievement reconciliation is queued
→ server-confirmed snapshot contains the existing place
→ achievement recreation is queued
→ Clear Map queues the final delete
→ queued reconciliation briefly recreates A1 and consumes a new sequence
```

The place collection remained empty after Clear Map. The problematic write was against achievement metadata on the user record.

## 6. Final correction

Persistent/destructive reconciliation was changed so that cache-only startup snapshots can hydrate local/UI state but cannot become authoritative input for achievement-metadata writes.

The first server-confirmed snapshot becomes the authoritative persistence source.

The listener explicitly supports metadata-state transition so reconciliation is not dependent on an accidental document-content change.

### Implementation reference

```text
56742f35d7fdacae13455638998c87e91a93e3bf
fix(reconciliation): ignore unconfirmed cache for metadata writes
```

The correction preserves:

- UI hydration;
- normal Firestore listener behaviour;
- legitimate user mutations;
- empty real-account reconciliation;
- Clear Map semantics;
- achievement chronology;
- offline/cache rendering expectations.

## 7. Firestore HTTP 400 investigation

During the Clear Map investigation, the browser occasionally observed:

```text
POST /documents:commit
HTTP 400
FAILED_PRECONDITION
stored version does not match required base version
```

The response body proved that this event was optimistic-concurrency contention caused by a stale base version.

The Firestore transaction retried and completed successfully; the final functional state remained correct.

The Production E2E harness was therefore hardened to classify only this exact signature as expected transaction contention.

### Classifier reference

```text
5a45eddeafd5035bf10b2f4026dadca20dd37332
test(e2e): classify expected firestore transaction contention
```

The classifier remains fail-closed:

- unrelated HTTP 400 responses remain failures;
- `PERMISSION_DENIED` remains a failure;
- `INVALID_ARGUMENT` remains a failure;
- unrelated `FAILED_PRECONDITION` remains a failure;
- malformed/unreadable bodies remain failures;
- unrelated console errors remain failures.

The expected contention is still logged for observability rather than silently ignored.

## 8. Validation

Validation included:

| Validation | Result |
|---|---|
| TypeScript | Passed |
| Lint | Passed |
| Production build | Passed |
| Full Vitest regression | Passed |
| Dedicated Emulator race tests | Passed |
| Production Guard | Passed |
| `validate-production` | Passed |
| `validate-clear-map` | Passed |
| Repeated valid Clear Map Production runs after C25 deployment | 3/3 Passed |

Final repeated Production runs confirmed:

```text
places after Clear Map = 0
A1 after Clear Map = absent
places resurrected = no
phantom A1 = no
unexpected sequence increment from phantom reconciliation = no
```

## 9. Test Lead decision

**Closed and approved for AtlasBadge V1.0.**

The Test Lead accepted the final behaviour after:

- the deployed C25 commit was objectively confirmed as the active Production deployment;
- three valid Production Clear Map runs passed;
- no phantom achievement was observed;
- no place was resurrected;
- no unexpected sequence increment was caused by reconciliation;
- the known OCC response was separately qualified and did not mask real errors.

## 10. Residual regression risk

Permanent regression coverage should retain:

- cache-only versus server-confirmed snapshot handling;
- achievement metadata reconciliation;
- Clear Map with pending/listener activity;
- transaction retries;
- sequence monotonicity;
- no resurrection of places or achievements;
- fail-closed E2E network observability.

## 11. Traceability

| Item | Reference |
|---|---|
| Defect | AB-DEF-005 |
| Evidence | AB-EV-019 |
| Related risks | QR-04, QR-30, scoped QR-01 |
| Initial place-race correction | `84b49376b2cad2a483cca1cc094a30d4864ccd0b` |
| Production contention classifier | `5a45eddeafd5035bf10b2f4026dadca20dd37332` |
| Final snapshot-authority correction | `56742f35d7fdacae13455638998c87e91a93e3bf` |
| Production result | Passed |
| Decision | Closed; retained in regression coverage |
