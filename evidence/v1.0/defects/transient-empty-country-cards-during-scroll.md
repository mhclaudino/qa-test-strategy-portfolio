# AB-DEF-006 — Country cards became visually empty during rapid scrolling

## Defect summary

| Field | Value |
|---|---|
| Defect ID | AB-DEF-006 |
| Evidence ID | AB-EV-020 |
| Related risks | QR-39; QR-40 |
| Environment | Chromium-based desktop and mobile contexts; local Next.js runtime; Vercel Production |
| Severity | Medium |
| Status | Closed after controlled A/B diagnosis, correction and Test Lead Production retest |
| Correction commit | `67dae2b306ff045f38fab68a0b57a5019948935d` |
| Final approved Production baseline | `982091c` |

## Expected result

Country cards must remain fully painted and readable during normal and rapid scrolling. The flag, country name, statuses, visit controls and memory section must not disappear while the card remains visible.

## Observed result

During fast upward or downward scrolling, one or more large card-shaped panels could remain visible while their internal content was temporarily not painted.

The React structure, text and child elements remained present in the DOM, but the Chromium rendering pipeline could display only the dark card surface.

## Selected sanitised evidence

<p>
  <img src="./assets/ab-def-006-transient-empty-country-card-sanitised.png" alt="Large empty country-card surface appearing between populated country cards during rapid scrolling" width="900">
</p>

The screenshot contains no credentials, account identifiers, private notes or sensitive browser output.

## Investigation

The investigation ruled out:

- unstable React keys;
- card unmounting caused by scroll;
- a disappearing text subtree;
- status or Firestore reconciliation;
- the SVG map;
- a page-level loading boundary.

Repeated headless DOM checks confirmed that the card identity, text and children remained present.

A controlled visual A/B test then removed the repeated `backdrop-filter: blur(24px)` only from `CountryActionCard`. The rendering failure stopped while the remaining card structure and interactions were unchanged.

## Root cause

The repeated glass-card surface combined a large `backdrop-filter` with clipped card layers across a long list. Under rapid Chromium scrolling, the compositor could temporarily fail to rasterise the children of a card even though the DOM remained intact.

## Correction

A dedicated `.country-action-card-surface` replaced the shared glass effect for repeated country cards.

The corrected surface:

- uses a near-opaque premium dark background;
- preserves border radius, border and shadow;
- removes `backdrop-filter` only from `CountryActionCard`;
- retains glass treatment for small, non-repeated floating surfaces;
- does not change status, visit, memory or persistence logic.

## Permanent validation

The scroll and paint regression suite verifies:

- `CountryActionCard` does not use `backdrop-filter`;
- flags and names remain present;
- card identity and height remain stable;
- repeated rapid scrolling does not create empty panels;
- responsive layouts retain the expected density;
- status-persistence and rapid-mutation suites remain green.

Recorded gates included:

- focused scroll suite repeated five times: **Passed**;
- status-persistence E2E: **Passed**;
- rapid-mutation E2E: **Passed**;
- TypeScript: **Passed**;
- lint: **Passed**;
- Next.js production build: **Passed**;
- `git diff --check`: **Passed**.

## Production retest

After deployment, the Test Lead repeated fast scrolling in Production and confirmed:

- no empty country-card panels;
- stable card painting;
- preserved status interaction;
- preserved counters and memories;
- responsive navigation and layout remained usable.

## Final decision

```text
AB-DEF-006 closed
QR-39 remains Regression risk with permanent paint-stability coverage
```
