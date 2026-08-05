# Regression Test Evidence

This directory contains selected evidence from AtlasBadge V1.0 regression testing.

Evidence may cover:

- quick regression after an implementation;
- affected-area regression after a correction;
- regression following defect retesting;
- release regression;
- final V1.0 regression;
- browser, device and language coverage;
- automated regression evidence when the Playwright suite becomes available.

Each regression record should identify:

- scope of the regression;
- reason for execution;
- version or commit tested;
- environment;
- browser and device;
- scenarios or areas covered;
- passed, failed or blocked results;
- defects identified;
- unexecuted scenarios;
- residual risk;
- Test Lead conclusion.

Only selected and sanitised evidence suitable for the public portfolio should be committed.

## Available evidence

| Evidence | Scope | Environment | Status |
|---|---|---|---|
| [V1 Social Profiles and Birthplace Integrity — Local Validation](./v1-social-profiles-and-birthplace-integrity-local-validation.md) | Social profile ordering, GitHub restrictions, Footer versioning, single-birthplace integrity, Firestore Rules and concurrency | Local, Firebase Emulator and production build | Local Approved — Production Validation Pending |
| [QR-01 — Map persistence mitigation and dashboard regression](./qr-01-map-persistence-and-dashboard-regression.md) | Confirmed-cache separation, rollback, retry, canonical metrics, filters, flags and affected-area UI regression | Local, Firebase Emulators, Microsoft Edge, production build and AB-EV-009 Production cross-reference | Scoped path approved in Production — QR-01 remains Current gap only for broader persistence coverage |
| [AB-EV-011 — QR-06 character-limit closure](./qr-06-character-limits-closure.md) | Closure of the missing-limit gap and permanent boundary, persistence and layout regression decision | Local validation and Vercel Production; detailed operational artefacts retained privately | Approved — QR-06 moved to Regression risk |
| [AB-EV-012 — QR-08 password-policy closure](./qr-08-password-policy-closure.md) | Approved 15-character minimum, passphrase compatibility, permanent boundary and Firebase-response tests | Local code audit, Vitest, directed lint, production build and authorised Firebase policy confirmation | Passed — QR-08 moved to Regression risk |
| [AB-EV-015 — QR-13 immediate username reuse](./qr-13-immediate-username-reuse-accepted-behaviour.md) | Immediate release after username change, no alias/redirect, containment audit and permanent accepted-behaviour regression | Local repository audit, 12 focused tests, 121-test full suite, lint and production build | Passed — QR-13 remains Accepted behaviour |
| [AB-EV-016 — QR-24 Passed through detailed-visit workflow](./qr-24-passed-through-detailed-visit-workflow.md) | First passage, multiple passages, detailed memory, explicit Save, status transitions, privacy and metric integrity | Static implementation audit, 18 focused tests, 139-test full suite, lint and production build | Passed — QR-24 moved to Regression risk |
| [AB-EV-017 — QR-40 accessibility technical baseline](./qr-40-accessibility-technical-baseline.md) | WCAG 2.2 AA technical baseline for critical V1.0 flows, focus management, semantics, contrast, reduced motion, zoom and reflow | Vitest, Playwright/Axe, Edge Windows, Chrome Android and Vercel Production | Passed — QR-40 moved to Regression risk |
| [AB-EV-018 — QR-39 responsive, touch and constrained-device baseline](./qr-39-responsive-touch-constrained-device-baseline.md) | Responsive viewports, touch, orientation, network/CPU constraints, Android CSS correction and rapid-status concurrency correction | Local development/production builds, Firebase Emulators, Playwright, Edge Windows, Chrome Android and Vercel Production | Passed — QR-39 moved to Regression risk; QR-04 extended |
| [AB-EV-019 — Status persistence and OCC regression closure](./ab-ev-019-status-persistence-and-occ-regression.md) | Single-intent status persistence, Firestore convergence, subscription reconciliation, reload parity and OCC preservation | Firebase Emulators, Playwright, Edge/Chromium and Vercel Production | Passed — AB-DEF-005 closed; QR-04 remains Regression risk |
| [AB-EV-020 — Responsive navigation and country-card paint stability](./ab-ev-020-responsive-navigation-and-card-painting-stability.md) | Responsive menu breakpoints, floating-surface standard, layout density and rapid-scroll painting stability | Playwright responsive regression, Chromium desktop/mobile and Vercel Production | Passed — AB-DEF-006 closed; QR-39 and QR-40 remain Regression risks |
