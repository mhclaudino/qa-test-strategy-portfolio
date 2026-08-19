# Smoke Test Evidence

This directory contains selected evidence from AtlasBadge V1.0 smoke testing.

Smoke evidence identifies the deployed version or commit, environment, browser/device, covered scenarios, overall result, limitations and Test Lead decision. It must not contain real user data or sensitive authentication information.

## Available evidence

| Evidence | Scope | Environment | Status |
|---|---|---|---|
| [AB-EV-009 — Final dashboard and persistence Production smoke](./ab-ev-009-final-dashboard-and-persistence-production-smoke.md) | Deployment readiness, essential routes, flags, filters, metric parity, controlled persistence, relogin and restoration | Vercel Production, Microsoft Edge on Windows | Passed — approved by Test Lead |
| [AB-EV-013 — QR-04 real-time synchronisation and concurrency Production validation](./ab-ev-013-qr-04-real-time-sync-production-smoke.md) | Real-time listener, OCC, two-tab propagation, conflict recovery, cross-tab logout and restoration | Vercel Production and local automated regression | Passed — QR-04 moved to Regression risk |
| [AB-EV-014 — QR-11 username case-normalisation Production validation](./ab-ev-014-qr-11-username-case-normalisation-production-smoke.md) | Canonical lowercase identity, collision prevention, public lookup, links and restoration | Vercel Production and local automated regression | Passed — QR-11 Regression risk |
| [AB-EV-024 — Production release validation and harness hardening](./ab-ev-024-production-release-validation-and-harness-hardening.md) | Dedicated Production runner, target safety, status flow, Clear Map, reload and reconquest | Vercel/Firebase Production, Microsoft Edge | Passed — canonical Production validation established |
| [AB-EV-028 — Map and profile parity Production validation](./ab-ev-028-map-profile-parity-production-validation.md) | Micro-marker scaling, canonical read-only Profile map, tab-width parity and map-surface parity | Local Playwright/Vitest/build gates and Vercel Production | Passed — C27–C30 Production approved |
| [AB-EV-030 — Profile map to earned-flag navigation](./ab-ev-030-profile-map-to-earned-flag-navigation.md) | Read-only map click-to-flag navigation, deterministic country targeting, highlight, sorting, micro-markers and mobile | Firebase Emulators, Playwright, local manual QA and Vercel Production | Passed — C32 FIXED / PRODUCTION PASS |
