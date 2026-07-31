# Smoke Test Evidence

This directory contains selected evidence from AtlasBadge V1.0 smoke testing.

Evidence may include:

* smoke testing after intermediate deployments;
* Microsoft Edge production smoke;
* Google Chrome production smoke;
* Samsung Galaxy S20 FE production smoke;
* portrait and landscape mobile checks;
* `pt-BR` production smoke;
* `en-GB` production smoke;
* authentication and essential navigation checks;
* final smoke after the production reset.

Each smoke evidence item should identify:

* deployed version or commit;
* execution date;
* environment;
* browser and device;
* language;
* scenarios covered;
* overall result;
* defects or limitations found;
* Test Lead decision or required follow-up.

Smoke evidence must not contain real user data or sensitive authentication information.

## Available evidence

| Evidence | Scope | Environment | Status |
|---|---|---|---|
| [AB-EV-009 — Final dashboard and persistence Production smoke](./ab-ev-009-final-dashboard-and-persistence-production-smoke.md) | Deployment readiness, essential routes, 251-place selector, flags, Footer GitHub, registered-place filters, metric parity, controlled persistence, relogin and test-data restoration | Vercel Production, Microsoft Edge on Windows | Passed — approved by Test Lead; QR-01 remains Current gap due to broader persistence scope |
| [AB-EV-013 — QR-04 real-time synchronisation and concurrency Production validation](./ab-ev-013-qr-04-real-time-sync-production-smoke.md) | Runtime real-time listener, OCC, 14 permanent tests, two-tab propagation, conflict recovery, cross-tab logout and test-data restoration | Vercel Production and local automated regression (2026-07-31) | Passed — approved by Test Lead; QR-04 moved to Regression risk |
