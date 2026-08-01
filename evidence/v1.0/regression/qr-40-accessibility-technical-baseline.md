# AB-EV-017 — QR-40 Accessibility Technical Baseline

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-017 |
| Related quality risk | QR-40; affected-area regression for QR-24, QR-31, QR-33, QR-34 and QR-39 |
| Product | AtlasBadge V1.0 |
| Evidence type | Accessibility baseline, automated regression and Production smoke |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-08-01 |
| Final decision | Passed — QR-40 moved to Regression risk |

## 2. Purpose

This evidence records the first formal accessibility baseline for critical AtlasBadge V1.0 flows.

The approved statement is:

> **WCAG 2.2 AA technical baseline evaluated for critical AtlasBadge V1.0 flows.**

This is a scoped engineering and QA baseline. It is not a declaration of universal conformance, legal certification or complete assistive-technology coverage.

## 3. Covered controls

The implementation and audit covered:

- keyboard-reachable controls;
- visible focus and logical focus order;
- contextual accessible names for icon controls;
- native button and input semantics;
- `aria-pressed` for selectable status controls;
- dialog roles and accessible names;
- initial dialog focus;
- Tab and Shift+Tab focus containment;
- Escape handling;
- restoration of focus to the triggering control;
- decorative SVG handling;
- text alternatives and non-colour status identification;
- contrast checks without globally disabling the Axe contrast rule;
- reduced-motion behaviour;
- zoom and narrow-viewport reflow;
- public, private and authenticated UI states.

## 4. Technical traceability

| Area | Reference |
|---|---|
| Focus management | `src/hooks/useFocusTrap.ts` |
| Focus-trap permanent tests | `src/hooks/useFocusTrap.test.tsx` |
| Dialog semantics | `src/components/ConfirmModal.tsx`, `ForgotPasswordModal.tsx`, `DeleteAccountModal.tsx`, `CountryPickerModal.tsx`, `ProfileEditModal.tsx` |
| Status semantics | `src/components/StatusPill.tsx`, `CountryActionCard.tsx` |
| Map and icon semantics | `src/components/WorldSvgMap.tsx`, `AtlasWorldMapV2.tsx` |
| Reduced motion | `src/app/globals.css` |
| Automated Axe coverage | `e2e/accessibility.spec.ts` |

Relevant product commits:

- `8a18615` — initial QR-40 accessibility implementation;
- `a7eec48` — permanent focus-trap coverage and correction checkpoint;
- `918875d75034a15dec254c1294592fc262047eb2` — audit corrections and type-safe validation;
- `c064b116a7b9fbc394d01dfe15ceaad1a40bc96d` — authenticated-state Axe coverage;
- `62f0d59dfc5cdb16c6567d1ead2e3c07fccc1ab5` — explicit CountryPicker/filter-state scan;
- final regression remained green through Production release `eca539ea793a2aadc4be657f0b9dd549f1f04699`.

## 5. Permanent automated validation

### 5.1 Axe states

The permanent Playwright suite executes **15 explicit Axe scans**:

| ID | Page or state | Authentication | Result |
|---|---|---:|---|
| QR40-AX-01 | Home | No | Passed |
| QR40-AX-02 | Login | No | Passed |
| QR40-AX-03 | Public Profile | No | Passed |
| QR40-AX-04 | Badges | No | Passed |
| QR40-AX-05 | My Map | Yes | Passed |
| QR40-AX-06 | Mobile/User menu | Yes | Passed |
| QR40-AX-07 | CountryPicker and filters | Yes | Passed |
| QR40-AX-08 | CountryActionCard | Yes | Passed |
| QR40-AX-09 | Status pills | Yes | Passed |
| QR40-AX-10 | Selected status with updated `aria-pressed` | Yes | Passed |
| QR40-AX-11 | VisitEditor | Yes | Passed |
| QR40-AX-12 | Memory editor | Yes | Passed |
| QR40-AX-13 | ProfileEditModal | Yes | Passed |
| QR40-AX-14 | Authentication methods | Yes | Passed |
| QR40-AX-15 | Private-profile state | Yes | Passed |

Result across the recorded baseline:

- Critical: **0**;
- Serious: **0**;
- Moderate: **0**;
- Minor: **0**;
- globally disabled Axe rules: **none**.

### 5.2 Focus-trap regression

The focus-management suite contains **11/11 Passed** scenarios covering:

- initial focus;
- Tab wrap;
- Shift+Tab wrap;
- Escape;
- focus restoration;
- closed-modal behaviour;
- listener cleanup;
- no-focusable-content safety;
- dynamic focusable content;
- nested dialogs;
- keyboard-event isolation without blocking legitimate controls.

### 5.3 Full technical gates

The final automated baseline recorded:

- focused QR-11 tests: **14/14 Passed**;
- focused QR-13 tests: **12/12 Passed**;
- focused QR-24 tests: **18/18 Passed**;
- focus-trap tests: **11/11 Passed**;
- complete Vitest suite: **150/150 Passed**;
- Playwright accessibility tests: **5/5 Passed**;
- explicit Axe scans: **15 Passed**;
- TypeScript: **0 errors**;
- ESLint: **0 errors**;
- Next.js production build: **Passed**;
- working tree: **clean**.

## 6. Manual Production validation

The Test Lead completed the critical Production smoke in Microsoft Edge on Windows and Google Chrome on Android.

The approved checks included:

- Home and Login by keyboard;
- CountryPicker and filters by keyboard;
- Tab and Shift+Tab containment;
- Escape and focus restoration;
- status selection using keyboard controls;
- visits and explicit memory Save;
- ProfileEditModal focus behaviour;
- mouse, touch and textual alternatives to the map;
- Badges and Public Profile;
- browser zoom at 200%;
- narrow viewport and mobile reflow;
- light and dark themes;
- reduced-motion behaviour;
- QR-24 Passed through regression;
- absence of Next.js error overlays or HTTP 500 failures.

## 7. Residual limitations

The public evidence does not claim:

- complete screen-reader interoperability across products and operating systems;
- complete forced-colours or native high-contrast coverage;
- Safari, Firefox, iPhone, macOS or every Android implementation;
- formal WCAG certification.

Native screen-reader and forced-colours execution were not retained as completed public evidence and remain residual coverage items. Broader browser and device coverage remains represented by QR-38.

## 8. Risk decision

QR-40 is no longer an `Assessment gap`.

It is retained as **Regression risk** because future changes could reintroduce:

- unnamed icon controls;
- inaccessible status state;
- broken dialog focus;
- missing focus restoration;
- contrast failures;
- colour-only information;
- motion that ignores user preference;
- mobile reflow that hides accessible controls;
- divergence between public and authenticated states.

## 9. Traceability

```text
QR-40
→ WCAG 2.2 AA technical baseline for critical V1.0 flows
→ semantic and focus audit
→ runtime corrections
→ 11 focus-trap tests
→ 15 explicit Axe states
→ 150-test full regression
→ Production keyboard, zoom, reflow, theme and reduced-motion smoke
→ AB-EV-017
→ QR-40 Regression risk
```

## 10. Final conclusion

The scoped AtlasBadge V1.0 accessibility baseline was implemented, independently audited, protected by permanent automation and approved by the Test Lead in Production.

Future releases must preserve this baseline and extend it rather than treating the current result as universal accessibility certification.
