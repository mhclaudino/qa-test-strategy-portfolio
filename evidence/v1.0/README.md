# AtlasBadge V1.0 Test Evidence

This directory contains selected test evidence produced during the quality validation of AtlasBadge V1.0.

The evidence is organised by testing purpose rather than by test tool.

The central [Evidence Register](evidence-register.md) links material risks, defects, investigations, environments, builds, evidence status and Test Lead decisions to the selected public artefacts.

## Directory structure

```text
evidence/
└── v1.0/
    ├── README.md
    ├── evidence-register.md
    ├── environments/
    ├── smoke/
    ├── regression/
    ├── defects/
    └── production-reset/
```

## Evidence categories

### `environments`

Evidence related to environments, browsers, devices, resolutions and configurations used during testing, including browser/device information, desktop and mobile evidence, Firebase Emulator verification and Playwright environment verification.

### `smoke`

Evidence from Production and release smoke testing.

Current public record:

- [AB-EV-009 — Final dashboard and persistence Production smoke](smoke/ab-ev-009-final-dashboard-and-persistence-production-smoke.md)
- [AB-EV-013 — QR-04 real-time synchronisation and concurrency Production validation](smoke/ab-ev-013-qr-04-real-time-sync-production-smoke.md)
- [AB-EV-014 — QR-11 username case-normalisation Production validation](smoke/ab-ev-014-qr-11-username-case-normalisation-production-smoke.md)
- [AB-EV-024 — Production release validation and harness hardening](smoke/ab-ev-024-production-release-validation-and-harness-hardening.md)
- [AB-EV-028 — Map and profile parity Production validation](smoke/ab-ev-028-map-profile-parity-production-validation.md)
- [AB-EV-030 — Profile map to earned-flag navigation](smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)

### `regression`

Selected evidence from quick regression, affected-area regression and final V1.0 regression.

Current public record:

- [QR-02 — Visit-history preservation](regression/qr-02-visit-history-preservation.md)
- [AB-EV-008 — QR-01 map persistence and dashboard regression](regression/qr-01-map-persistence-and-dashboard-regression.md)
- [AB-EV-011 — QR-06 character-limit closure](regression/qr-06-character-limits-closure.md)
- [AB-EV-012 — QR-08 password-policy closure](regression/qr-08-password-policy-closure.md)
- [AB-EV-015 — QR-13 immediate username reuse accepted behaviour](regression/qr-13-immediate-username-reuse-accepted-behaviour.md)
- [AB-EV-016 — QR-24 Passed through detailed-visit workflow](regression/qr-24-passed-through-detailed-visit-workflow.md)
- [AB-EV-017 — QR-40 accessibility technical baseline](regression/qr-40-accessibility-technical-baseline.md)
- [AB-EV-018 — QR-39 responsive, touch and constrained-device baseline](regression/qr-39-responsive-touch-constrained-device-baseline.md)
- [AB-EV-019 — Status persistence and optimistic-concurrency regression closure](regression/ab-ev-019-status-persistence-and-occ-regression.md)
- [AB-EV-020 — Responsive navigation, layout density and country-card paint stability](regression/ab-ev-020-responsive-navigation-and-card-painting-stability.md)
- [AB-EV-021 — V1.0 release hardening and integrated regression](regression/ab-ev-021-v1-release-hardening-and-integrated-regression.md)
- [AB-EV-022 — Rapid status last-intent and activation-chronology closure](regression/ab-ev-022-status-last-intent-and-activation-chronology.md)
- [AB-EV-023 — Achievement chronology, World Completion and notification reliability](regression/ab-ev-023-achievement-chronology-world-completion-and-notification-reliability.md)
- [AB-EV-025 — Badge Unlock surface consistency and visual polish](regression/ab-ev-025-badge-unlock-surface-and-visual-polish.md)
- [AB-EV-027 — United Kingdom coverage rule and follow-up counter-integrity observation](regression/ab-ev-027-uk-coverage-and-geographic-counter-integrity.md)
- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity closure](defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-031 — Dashboard selected-place sorting](regression/ab-ev-031-dashboard-selected-place-sorting.md)

### `defects`

Selected examples of defects identified, investigated, corrected and retested.

Current public records:

- [AB-DEF-001 — Country status selection is not applied or persisted](defects/status-persistence-failure.md)
- [QR-03 — Private cache remains after explicit logout](defects/qr-03-private-cache-after-logout.md)
- [QR-05 — Legacy memory editor persisted text on every keystroke](defects/qr-05-legacy-memory-keystroke-persistence.md)
- [AB-EV-010 / QR-07 — Retry-safe account deletion](defects/qr-07-account-deletion-retry-safe.md)
- [QR-10 — Wrong identity during access-method linking](defects/qr-10-wrong-identity-account-linking.md)
- [AB-DEF-002 — Production frontend and Firestore Rules were deployed out of sync](defects/production-deployment-parity-failure.md)
- [AB-DEF-003 — Production CSS not applied in the affected Android browser](defects/android-css-cascade-layer-compatibility.md)
- [AB-DEF-004 — Rapid same-session status changes triggered a false external conflict](defects/rapid-status-mutation-false-conflict.md)
- [AB-DEF-005 — Added status disappeared after optimistic synchronisation](defects/status-added-disappears-after-sync.md)
- [AB-DEF-006 — Country cards became visually empty during rapid scrolling](defects/transient-empty-country-cards-during-scroll.md)
- [AB-DEF-007 — Rapid status changes could lose the latest local intent](defects/rapid-status-last-intent-lost.md)
- [AB-DEF-008 — World Complete achievement was mathematically unattainable](defects/world-complete-achievement-unattainable.md)
- [AB-DEF-009 — Achievement unlock re-entered the full-page application loader](defects/full-page-loader-during-achievement-unlock.md)
- [AB-DEF-010 — Achievement popup could be lost during a reconciliation race](defects/achievement-popup-lost-during-reconciliation-race.md)
- [AB-DEF-011 / AB-EV-026 — Clear Map achievement metadata reconciliation race](defects/ab-ev-026-clear-map-achievement-reconciliation-race.md)
- [AB-DEF-012 / AB-EV-029 — Geographic counter integrity: 252 / 195 / 57](defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-DEF-013 / AB-EV-032 — Manual visit order and rapid-visit concurrency closure](defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)

The Production-validation cycle also found test-harness defects involving target selection, authentication setup, stale accessible names, search-surface assumptions, optimistic-state synchronisation and locator scope. These are documented as QA-process evidence in AB-EV-024 rather than being assigned product-defect IDs.

### `production-reset`

Evidence related to the final removal of test accounts and test data before the official V1.0 launch.

## Evidence standards

Evidence should provide enough context to understand what was tested, the environment, expected and observed results, status, defects or limitations, retest/regression, relevant build or deployment, and the Test Lead decision.

Screenshots are not required for every execution. A textual record is acceptable when command/build/commit, environment, result and decision are clear.

## Screenshot and log sanitisation policy

Original screenshots, recordings and raw logs remain private. Public evidence must not expose identifiers, authentication payloads, e-mail addresses, credentials, API keys, `.env` values, cookies, personal notes/memories, private URLs, identifiable payloads, sensitive cloud configuration or unnecessary private source code.

Raw Antigravity or other AI-assisted logs are not public evidence. Only verified summaries and relevant sanitised outputs may be published.

## Evidence selection

Not every item produced during testing must be published. The Test Lead selects evidence that demonstrates the testing approach, supports decisions, provides traceability, illustrates defect management/retesting, demonstrates release readiness and is safe for a public professional portfolio.
