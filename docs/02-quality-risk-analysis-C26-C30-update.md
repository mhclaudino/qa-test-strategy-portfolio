# docs/02-quality-risk-analysis.md — required C26–C30 update

This file contains the exact edits required in the existing `docs/02-quality-risk-analysis.md`.
The existing document should remain intact apart from these targeted replacements/additions.

## 1. Document status

Replace:

```text
> **Document status:** Reviewed through AB-EV-018, including the QR-24 detailed-visit workflow, QR-40 accessibility baseline and QR-39 responsive/touch/constrained-device closure.
```

with:

```text
> **Document status:** Reviewed through AB-EV-021, including the Clear Map reconciliation-race closure, United Kingdom counter integrity, and the current map/profile parity Production baseline.
```

## 2. Persistence context

Immediately after the paragraph ending with:

```text
Rapid same-session mutations for the same place are queued and coalesced against fresh confirmed state so local actions do not trigger false external-session conflicts; true stale writes from another context remain protected by OCC through the extended AB-EV-018 coverage.
```

add:

```text
Cache-only startup snapshots may hydrate local UI state but are not authoritative input for persistent achievement-metadata reconciliation. Persistent reconciliation waits for server-confirmed snapshot state so an initial stale or empty cache cannot enqueue destructive delete/recreate work. Clear Map/listener/reconciliation race protection, sequence integrity and expected Firestore transaction-contention qualification are recorded in AB-EV-019.
```

## 3. Geographic progress model

Immediately after:

```text
The United Kingdom aggregate is not assigned its own persisted travel status. England, Scotland, Wales, and Northern Ireland are selectable independently.
```

add:

```text
The current V1.0 progress totals are **195 conceptual countries**, **56 territories and entities**, and **251 selectable places**. The four United Kingdom constituents each contribute independently to territory/entity progress. When all four are present, they also satisfy the virtual United Kingdom country and achievement rule. The country and territory/entity metrics therefore overlap for this case and are not required to partition the places total.
```

## 4. QR-25 risk statement

Replace:

```text
| QR-25 | The `195 countries`, `251 selectable places`, `52 territories and entities`, and `8 continent display groups` may be represented inconsistently across screens and statistics. | Regression risk | 4 | 3 | 12 | High |
```

with:

```text
| QR-25 | The `195 countries`, `251 selectable places`, `56 territories and entities`, and `8 continent display groups` may be represented inconsistently across screens and statistics. | Regression risk | 4 | 3 | 12 | High |
```

## 5. QR-04 applied decision

Replace the existing QR-04 applied-decision bullet with:

```text
- **QR-04 — Regression risk:** Firestore real-time synchronisation, confirmed-state reconciliation, OCC conflict handling, listener cleanup and two-tab Production behaviour were approved through AB-EV-013. AB-EV-018 adds permanent rapid same-session mutation, deterministic recovery and genuine external-conflict coverage. AB-EV-019 adds cache-only snapshot gating, achievement-reconciliation race protection and fail-closed qualification of the known optimistic-concurrency transaction retry.
```

## 6. Add applied geographic decisions

Immediately after the QR-25–QR-30 table and before `### 5.5 Public profile and sharing`, add:

```text
### 5.4.1 Applied geographic and map decisions

- **QR-18 / QR-25 / QR-29 — Regression risk:** AB-EV-020 confirms the V1.0 totals of 251 selectable places, 195 conceptual countries and 56 territories/entities. Each UK constituent contributes individually to territory/entity progress, while all four together additionally credit the United Kingdom country and achievement.
- **QR-26 — Regression risk:** AB-EV-021 confirms the canonical dashboard map, geographic anchoring and micro-marker zoom scaling in the final Production release.
- **QR-30 — Regression risk:** AB-EV-019 closes the observed achievement-metadata reconciliation race and confirms no phantom achievement or sequence increment after Clear Map.
```

## 7. QR-39 applied decision

Replace:

```text
- **QR-39 — Regression risk:** responsive reflow, touch, constrained network/CPU, production CSS delivery and Android Production behaviour were approved through AB-EV-018.
```

with:

```text
- **QR-39 — Regression risk:** responsive reflow, touch, constrained network/CPU, production CSS delivery and Android Production behaviour were approved through AB-EV-018. AB-EV-021 adds the current map/profile sizing, zoom, surface and cross-tab layout parity to the Production regression baseline.
```

## 8. Important intentional rules

Immediately after:

```text
- the four constituent nations of the United Kingdom are separate selectable records;
```

add:

```text
- each UK constituent independently contributes to the territory/entity counter, while all four together additionally credit the United Kingdom country and achievement;
```

## 9. Resolved V1.0 decisions

Immediately after the AB-EV-018 responsive-baseline bullet in section 9, add:

```text
- cache-only Firestore startup snapshots are not authoritative for persistent achievement reconciliation; the Clear Map reconciliation race is closed through AB-EV-019;
- the geographic progress baseline is 251 selectable places, 195 conceptual countries and 56 territories/entities, with the approved UK overlap rule recorded in AB-EV-020;
- the public Profile reuses the canonical map in read-only mode, and map/profile zoom, width and surface parity are approved through AB-EV-021.
```
