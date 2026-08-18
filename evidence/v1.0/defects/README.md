# Defect Evidence

This directory contains selected defect examples from the AtlasBadge V1.0 quality process.

The purpose is to demonstrate:

- defect identification;
- risk assessment;
- clear communication;
- expected and observed behaviour;
- prioritisation;
- correction follow-up;
- retesting;
- regression decisions;
- final quality assessment.

A defect evidence item may include:

- defect title;
- description;
- preconditions;
- reproduction steps;
- expected result;
- observed result;
- affected environment;
- browser or device;
- screenshots;
- severity and priority;
- correction summary;
- retest evidence;
- regression scope;
- final status.

This directory does not need to contain every project defect.

Only representative examples with professional portfolio value should be published.

## Current public records

- [AB-DEF-001 — Country status selection is not applied or persisted](status-persistence-failure.md)
- [QR-03 — Private cache remains after explicit logout](qr-03-private-cache-after-logout.md)
- [QR-05 — Legacy memory editor persisted text on every keystroke](qr-05-legacy-memory-keystroke-persistence.md)
- [AB-EV-010 / QR-07 — Retry-safe account deletion](qr-07-account-deletion-retry-safe.md)
- [QR-10 — Wrong identity during access-method linking](qr-10-wrong-identity-account-linking.md)
- [AB-DEF-002 — Production frontend and Firestore Rules were deployed out of sync](production-deployment-parity-failure.md)
- [AB-DEF-003 — Production CSS not applied in the affected Android browser](android-css-cascade-layer-compatibility.md)
- [AB-DEF-004 — Rapid same-session status changes triggered a false external conflict](rapid-status-mutation-false-conflict.md)
- [AB-DEF-005 / AB-EV-019 — Clear Map achievement metadata reconciliation race](ab-def-005-clear-map-achievement-reconciliation-race.md)

See the central [V1.0 Evidence Register](../evidence-register.md) for evidence status, related risks and Test Lead decisions.

Real personal data, credentials, tokens, private configuration and sensitive project information must be removed or masked.
