# AB-DEF-008 — World Complete achievement was mathematically unattainable

## Defect summary

| Field | Value |
|---|---|
| Defect ID | AB-DEF-008 |
| Evidence ID | AB-EV-023 |
| Related risks | QR-18; QR-25; QR-29; QR-30 |
| Environment | Static dataset audit; Vitest; Firebase Emulators; Playwright E2E |
| Severity | Medium |
| Status | Closed after geographic-contract correction and permanent regression |
| Correction commit | `f6004a9` — `fix(achievements): make world completion attainable` |

## Expected result

A user who satisfies the complete-world requirement must be able to unlock **Mundo Completo** without persisting a separate status on the technical United Kingdom aggregate.

## Observed result

The product had 195 conceptual sovereigns but only 194 directly selectable sovereign records. The non-selectable UK aggregate existed in the denominator, so the original calculation topped out at 194/195.

## Root cause

A selectable-sovereign numerator was compared with a conceptual-sovereign denominator without virtually satisfying the UK aggregate from its four selectable constituents.

## Correction

The evaluator grants the UK conceptual sovereign point only when England, Scotland, Wales and Northern Ireland are all complete. The `gb` aggregate remains non-selectable and receives no travel-status document.

## Permanent validation

Ten focused scenarios, geographic-contract checks, UK interactions, related achievement checks and an Emulator E2E verify that the achievement remains locked before the final required action and persists correctly after unlock.

## Final decision

```text
AB-DEF-008 closed
World Complete is attainable without making the technical UK aggregate selectable
QR-25 / QR-29 / QR-30 remain Regression risks
```
