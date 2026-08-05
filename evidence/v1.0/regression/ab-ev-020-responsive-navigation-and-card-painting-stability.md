# AB-EV-020 — Responsive Navigation, Layout Density and Country-Card Paint Stability

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-020 |
| Related risks and defects | QR-39; QR-40; AB-DEF-006 |
| Product | AtlasBadge V1.0 |
| Evidence type | Responsive UI regression, rendering-stability correction and Production validation |
| Owner | Test Lead/Product Owner |
| Execution period | 2026-08-01 to 2026-08-05 |
| Final approved Production baseline | `982091c` |
| Final decision | Passed — AB-DEF-006 closed; responsive menu and card-paint standards approved |

## 2. Purpose

This evidence extends the QR-39 baseline after a sequence of Production and local usability findings affecting:

- authenticated navigation;
- floating-menu readability;
- profile and statistics layout;
- map controls;
- summary-card and flag-grid density;
- country-card paint stability during rapid scrolling.

## 3. Approved responsive navigation standard

The final navigation rule is:

| Viewport | Navigation |
|---|---|
| Below 768 px | Hamburger control and vertical mobile menu |
| 768 px and above | Full horizontal navigation: **Mapa**, **Badges**, **Perfil** |

The mobile menu and avatar dropdown share the approved `.floating-surface` visual token:

- dark background with alpha on the background only;
- consistent border and shadow;
- controlled backdrop blur;
- readable text and full-width item highlighting;
- destructive **Sair** treatment in red.

The floating-surface pattern is restricted to menus, dropdowns, popovers and small overlays. It must not be applied to repeated country cards.

## 4. Approved layout-density baseline

The authenticated Home uses:

- four summary cards in one row on desktop;
- two-by-two layout on intermediate widths;
- safe reflow without horizontal overflow on narrow screens.

The conquered-flag grid uses the approved responsive density:

| Width | Columns |
|---|---:|
| 1280 px and above | 8 |
| 1024–1279 px | 6 |
| 768–1023 px | 4 |
| 480–767 px | 3 |
| Below 480 px | 2 |

The section title is **Bandeiras Conquistadas**, with a decorative premium vector icon.

## 5. AB-DEF-006 — transient empty country cards

The public defect record is:

- [AB-DEF-006 — Country cards became visually empty during rapid scrolling](../defects/transient-empty-country-cards-during-scroll.md).

The controlled A/B investigation confirmed that repeated country cards must not use the large glass `backdrop-filter`. A dedicated near-opaque card surface removed the rendering failure while preserving the visual hierarchy.

## 6. Implementation traceability

| Commit | Scope |
|---|---|
| `79ba9ec` | Social-link interaction and map-control alignment |
| `d6c64ab` | Contextual Home routing, hero readability and avatar-menu feedback |
| `9e6b349` | Density, territory-header and menu-highlight baseline |
| `67dae2b306ff045f38fab68a0b57a5019948935d` | Country-card paint stability during rapid scrolling |
| `982091c` | Responsive floating-menu standard and breakpoint coverage |

Short references are used where the complete SHA is retained in the private product repository.

## 7. Permanent validation

Permanent Playwright coverage includes:

- header and navigation behaviour;
- authenticated and unauthenticated Home routing;
- mobile and tablet breakpoints;
- equality of mobile-menu and avatar-menu surface styles;
- profile two-column desktop layout;
- summary-card density;
- flag-grid density and long labels;
- map legend and clear-map alignment;
- social-link interaction;
- rapid-scroll card painting;
- status persistence and rapid mutation.

Final gates recorded:

- TypeScript: **Passed**;
- affected Playwright suites: **Passed**;
- status-persistence regression: **Passed**;
- rapid-mutation regression: **Passed**;
- lint: **Passed**;
- Next.js production build: **Passed**;
- `git diff --check`: **Passed**.

## 8. Production validation

The Test Lead approved in Production:

- hamburger navigation below 768 px;
- full navigation on tablet and desktop;
- consistent floating-menu opacity and readability;
- correct active-route treatment;
- stable avatar menu;
- summary-card and flag-grid density;
- no empty card panels during rapid scrolling;
- preserved map, profile, status and visit behaviour.

## 9. Risk decision

QR-39 remains a **Regression risk** because future CSS, rendering, responsive or navigation changes could reintroduce:

- breakpoint errors;
- menu overlap or unreadable transparency;
- horizontal overflow;
- excessive repeated blur;
- transient card-paint failures;
- unsuitable card density;
- touch or constrained-device regressions.

QR-40 remains a **Regression risk** because navigation and floating-surface changes must retain semantic links, focus visibility, keyboard behaviour and accessible menu state.

## 10. Traceability

```text
QR-39 / QR-40
→ responsive and constrained-device baseline AB-EV-018
→ authenticated navigation and layout changes
→ breakpoint and floating-surface standard
→ density regression coverage
→ transient empty CountryActionCard defect
→ AB-DEF-006
→ repeated backdrop-filter removed from country cards
→ commit 67dae2b
→ responsive menu standard 982091c
→ Production smoke approved
→ AB-EV-020
→ QR-39 and QR-40 Regression risk
```

## 11. Final conclusion

The responsive navigation, layout-density and repeated-card paint standards are implemented, protected by permanent tests and approved in Production.
