# AB-DEF-010 — Achievement popup could be lost during a reconciliation race

## Defect summary

| Field | Value |
|---|---|
| Defect ID | AB-DEF-010 |
| Evidence ID | AB-EV-023 |
| Related risks | QR-30; QR-04 |
| Environment | Firebase Emulators; multi-session Playwright; Vercel Production |
| Severity | Medium |
| Release impact | Release blocker because a qualifying action could persist without expected achievement feedback |
| Status | Closed after notification-authority correction and Production reconquest retest |
| Correction commit | `8887d2b23a3e459e9d5954bdfb63cd0383c984fb` |

## Expected result

A local action that causes a new acquisition or reconquest must show the Badge popup exactly once in that action session after final metadata confirmation. Remote tabs and reloads must not replay it historically.

## Observed result

In Production, the qualifying action succeeded, new achievement metadata/sequence persisted and the Badges UI showed the achievement earned, but an observer installed before the action confirmed the popup never entered the action tab DOM.

## Root cause

Notification delivery relied only on `transactionResult.newlyUnlockedIds`. If another reconciler persisted first, the local retry saw the metadata already present, returned an empty new-ID list and exited without enqueuing a notification.

## Correction

Persistence authority remains Firestore transaction plus final metadata. Notification authority now combines a locally caused not-earned → earned candidate with final confirmed metadata. Dedupe remains `uid:achievementId:sequence`; hydration, login, reload and remote-only snapshots do not create historical notifications.

## Permanent validation

Coverage includes another reconciler winning first, local retry returning an empty transaction-level list, action tab exactly-one popup, remote tab zero popup, relock/reconquest with a new sequence, reload without historical replay and zero full-page-loader reappearance.

## Final decision

```text
AB-DEF-010 closed
Notification delivery is correlated with the local earned transition and final persisted acquisition metadata
QR-30 remains Regression risk
```
