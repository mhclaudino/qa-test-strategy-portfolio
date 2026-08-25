# Defect Evidence

This directory contains selected defect examples from the AtlasBadge V1.0 quality process.

The purpose is to demonstrate defect identification, risk assessment, expected/observed behaviour, prioritisation, correction follow-up, retesting, regression decisions and final quality assessment.

Only representative examples with professional portfolio value should be published. Real personal data, credentials, tokens, private configuration and sensitive project information must be removed or masked.

## Current public records

- [AB-DEF-001 — Country status selection is not applied or persisted](status-persistence-failure.md)
- [QR-03 — Private cache remains after explicit logout](qr-03-private-cache-after-logout.md)
- [QR-05 — Legacy memory editor persisted text on every keystroke](qr-05-legacy-memory-keystroke-persistence.md)
- [AB-EV-010 / QR-07 — Retry-safe account deletion](qr-07-account-deletion-retry-safe.md)
- [QR-10 — Wrong identity during access-method linking](qr-10-wrong-identity-account-linking.md)
- [AB-DEF-002 — Production frontend and Firestore Rules were deployed out of sync](production-deployment-parity-failure.md)
- [AB-DEF-003 — Production CSS not applied in the affected Android browser](android-css-cascade-layer-compatibility.md)
- [AB-DEF-004 — Rapid same-session status changes triggered a false external conflict](rapid-status-mutation-false-conflict.md)
- [AB-DEF-005 — Added status disappeared after optimistic synchronisation](status-added-disappears-after-sync.md)
- [AB-DEF-006 — Country cards became visually empty during rapid scrolling](transient-empty-country-cards-during-scroll.md)
- [AB-DEF-007 — Rapid status changes could lose the latest local intent](rapid-status-last-intent-lost.md)
- [AB-DEF-008 — World Complete achievement was mathematically unattainable](world-complete-achievement-unattainable.md)
- [AB-DEF-009 — Achievement unlock re-entered the full-page application loader](full-page-loader-during-achievement-unlock.md)
- [AB-DEF-010 — Achievement popup could be lost during a reconciliation race](achievement-popup-lost-during-reconciliation-race.md)
- [AB-DEF-011 / AB-EV-026 — Clear Map achievement metadata reconciliation race](ab-ev-026-clear-map-achievement-reconciliation-race.md)
- [AB-DEF-012 / AB-EV-029 — Geographic counter integrity: 252 / 195 / 57](ab-ev-029-geographic-counter-integrity-252-195-57.md)
- [AB-DEF-013 / AB-EV-032 — Manual visit order and rapid-visit concurrency closure](ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md)
- [AB-DEF-014 — Public place projection missing for normal public statuses](ab-def-014-public-place-projection-missing.md)
- [AB-DEF-015 — Public achievement metadata was not synchronised](ab-def-015-public-achievement-metadata-not-synchronised.md)
- [AB-DEF-016 — Mobile dashboard control grid collapsed to zero width](ab-def-016-mobile-dashboard-grid-collapse.md)

See the central [V1.0 Evidence Register](../evidence-register.md) for evidence status, related risks and Test Lead decisions.
