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
| [QR-01 — Map persistence mitigation and dashboard regression](./qr-01-map-persistence-and-dashboard-regression.md) | Confirmed-cache separation, rollback, retry, canonical metrics, filters, flags and affected-area UI regression | Local, Firebase Emulators, Microsoft Edge and production build | Local scope approved — QR-01 remains Current gap; Production confirmation pending |
