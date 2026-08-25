# AtlasBadge V1.0 Test Evidence

This directory contains selected test evidence produced during the quality validation of AtlasBadge V1.0.

The evidence is organised by testing purpose rather than by test tool.

The central [Evidence Register](evidence-register.md) links material risks, defects, investigations, environments, builds, evidence status and Test Lead decisions to selected public artefacts.

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

Environment/browser/device/configuration evidence, including Firebase Emulator and Playwright execution context.

### `smoke`

Selected Production/release smoke evidence includes:

- [AB-EV-009 — Final dashboard and persistence Production smoke](smoke/ab-ev-009-final-dashboard-and-persistence-production-smoke.md)
- [AB-EV-013 — QR-04 real-time synchronisation and concurrency](smoke/ab-ev-013-qr-04-real-time-sync-production-smoke.md)
- [AB-EV-014 — QR-11 username case-normalisation](smoke/ab-ev-014-qr-11-username-case-normalisation-production-smoke.md)
- [AB-EV-024 — Production release validation and harness hardening](smoke/ab-ev-024-production-release-validation-and-harness-hardening.md)
- [AB-EV-028 — Map and profile parity](smoke/ab-ev-028-map-profile-parity-production-validation.md)
- [AB-EV-030 — Profile map to earned-flag navigation](smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md)

### `regression`

Selected quick/affected-area/integrated regression evidence includes:

- [QR-02 — Visit-history preservation](regression/qr-02-visit-history-preservation.md)
- [AB-EV-008 — QR-01 historical map persistence mitigation](regression/qr-01-map-persistence-and-dashboard-regression.md)
- [AB-EV-011 — QR-06 character-limit closure](regression/qr-06-character-limits-closure.md)
- [AB-EV-012 — QR-08 password-policy closure](regression/qr-08-password-policy-closure.md)
- [AB-EV-015 — QR-13 immediate username reuse](regression/qr-13-immediate-username-reuse-accepted-behaviour.md)
- [AB-EV-016 — QR-24 Passed through detailed-visit workflow](regression/qr-24-passed-through-detailed-visit-workflow.md)
- [AB-EV-017 — QR-40 accessibility technical baseline](regression/qr-40-accessibility-technical-baseline.md)
- [AB-EV-018 — QR-39 responsive/touch/constrained-device baseline](regression/qr-39-responsive-touch-constrained-device-baseline.md)
- [AB-EV-019 — Status persistence/OCC closure](regression/ab-ev-019-status-persistence-and-occ-regression.md)
- [AB-EV-020 — Responsive navigation/card-paint stability](regression/ab-ev-020-responsive-navigation-and-card-painting-stability.md)
- [AB-EV-021 — V1.0 release hardening/integrated regression](regression/ab-ev-021-v1-release-hardening-and-integrated-regression.md)
- [AB-EV-022 — Rapid status last-intent/chronology](regression/ab-ev-022-status-last-intent-and-activation-chronology.md)
- [AB-EV-023 — Achievement chronology/World Completion/notification reliability](regression/ab-ev-023-achievement-chronology-world-completion-and-notification-reliability.md)
- [AB-EV-025 — Badge Unlock surface consistency](regression/ab-ev-025-badge-unlock-surface-and-visual-polish.md)
- [AB-EV-027 — UK coverage/counter-integrity observation](regression/ab-ev-027-uk-coverage-and-geographic-counter-integrity.md)
- [AB-EV-029 / AB-DEF-012 — Geographic counter integrity](defects/ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-EV-031 — Dashboard selected-place sorting](regression/ab-ev-031-dashboard-selected-place-sorting.md)
- [AB-EV-033 — Wishlist/public-profile release hardening](regression/ab-ev-033-wishlist-public-profile-release-hardening.md)
- [AB-EV-034 — QR-01 failed-write recovery closure](regression/ab-ev-034-qr-01-failed-write-recovery-closure.md)
- [AB-EV-035 — C35 Visited + Passed-through coexistence](regression/ab-ev-035-c35-visited-passed-coexistence.md)

AB-EV-034 is the current closure record for the previous QR-01 `Current gap`; QR-01 now remains as a `Regression risk`. AB-EV-035 records a requirement correction rather than a Product Defect and explicitly states its Production evidence boundary.

### `defects`

Selected examples include persistence, privacy, authentication, responsive, concurrency, achievement and geographic-integrity defects. The [Evidence Register](evidence-register.md) is the authoritative public index for IDs and decisions.

The Production-validation cycles also found test-harness defects involving target selection, authentication setup, stale accessible names, search assumptions, optimistic-state synchronisation and locator scope. These are QA-process evidence rather than Product Defects.

### `production-reset`

Evidence related to the final removal of test accounts and test data before the official V1.0 launch.

## Evidence standards

Evidence should provide enough context to understand what was tested, the environment, expected and observed results, status, defects or limitations, retest/regression, relevant build/deployment and the Test Lead decision.

Screenshots are not required for every execution. A textual record is acceptable when command/build/commit, environment, result and decision are clear.

Historical evidence is preserved with the status that was true when it was produced. Later evidence may supersede the current risk state without rewriting the earlier record.

## Screenshot and log sanitisation policy

Original screenshots, recordings and raw logs remain private. Public evidence must not expose identifiers, authentication payloads, e-mail addresses, credentials, API keys, `.env` values, cookies, personal notes/memories, private URLs, identifiable payloads, sensitive cloud configuration or unnecessary private source code.

Raw AI-assisted logs are not public evidence. Only verified summaries and relevant sanitised outputs may be published.

## Evidence selection

Not every item produced during testing is published. The Test Lead selects evidence that demonstrates risk-based testing, supports decisions, illustrates defect/requirement-change management, demonstrates release readiness and is safe for a professional public portfolio.
