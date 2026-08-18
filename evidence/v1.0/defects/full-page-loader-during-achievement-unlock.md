# AB-DEF-009 — Achievement unlock re-entered the full-page application loader

## Defect summary

| Field | Value |
|---|---|
| Defect ID | AB-DEF-009 |
| Evidence ID | AB-EV-023 |
| Related risks | QR-30; QR-39 |
| Environment | Vercel Production; Firebase Emulators; Playwright desktop/mobile regression |
| Severity | Medium |
| Release impact | Release blocker due to repeated premium-UX interruption |
| Status | Closed after background-refresh correction and Production retest |
| Correction commit | `047974ae143d73c08b22cc1bb0e3719e1ff4c717` |

## Expected result

Earning a Badge must persist achievement state and show the unlock notification without replacing the app with **Carregando seu Atlas...**. The global loader is appropriate for first blocking hydration, not normal background updates.

## Observed result

Achievement unlock could blank the central application for several seconds while the header/footer remained visible.

## Root cause

Achievement reconciliation called `refreshProfile()`, which reused the same blocking `profileLoading` state as initial bootstrap and triggered downstream map cleanup/resubscription.

## Correction

Profile fetching now distinguishes blocking initial hydration from background refresh when a confirmed profile already exists. The prior profile remains rendered during background achievement updates.

## Permanent validation

Tests prove initial loader preservation, no blocking loader on later snapshots/unlocks, stable popup/metadata, reload stability and zero loader reappearances in Emulator and Production unlock windows.

## Final decision

```text
AB-DEF-009 closed
Initial blocking hydration retained
Background achievement refresh no longer blanks the application
```
