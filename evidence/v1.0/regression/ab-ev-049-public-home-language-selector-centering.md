# AB-EV-049 — Public Home Language-Selector Centering

## Status

**Evidence ID:** AB-EV-049  
**Scope:** Post-C45D multilingual visual-consistency patch  
**Decision:** CLOSED / PRODUCTION PASS  
**Test Lead approval:** 1 September 2026

## 1. Observation

During Production review of the six localized public Home routes, the Test Lead identified that the desktop language-selector group did not occupy the same visual horizontal center across locales.

The effect was caused by translated right-side actions having different widths. Examples include:

- `Entrar / Criar Mapa`
- `Iniciar sesión / Crear Mapa`
- `Se connecter / Créer une Carte`
- `Sign in / Create Map`

The baseline selector was centered only in the remaining flex space between the AtlasBadge brand and those actions.

Measured baseline deviation from the true header center ranged from approximately **29.85 px to 66.73 px**.

## 2. Requirement

For localized public Home desktop headers:

- AtlasBadge brand remains left-aligned;
- the six-language selector must be geometrically centered;
- anonymous actions or authenticated avatar remain right-aligned;
- translated action width must not move the selector;
- mobile compact-selector behaviour must remain unchanged;
- non-Home headers must remain unchanged.

## 3. Implementation

AtlasBadge commit:

`33556ecc5c1eda19522ec0c1975d7e755f6dbad4`  
`fix(i18n): center public home language selector`

Changed files:

- `src/components/Header.tsx`
- `src/app/globals.css`
- `src/components/Header.c45b.test.tsx`
- `e2e/localization-selector.c45b.spec.ts`

The desktop localized-Home layout now uses equal flexible side tracks around the auto-width selector:

`minmax(0, 1fr) auto minmax(0, 1fr)`

The change is scoped to the existing explicit `publicHomeLocale` contract.

No translation, locale order, flag sizing, cookie, routing, Login, Onboarding, Firebase or mobile-selector behaviour changed.

## 4. Local validation

Runtime:

`Node v22.23.2`

Results:

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 20 / 20 PASS |
| Playwright Edge | 8 / 8 PASS |
| Firebase backend for auth scenarios | Emulators |
| Requests to real Firebase | 0 |
| git diff --check | PASS |

Automated coverage verified:

- official six-flag order;
- anonymous selector visibility;
- authenticated selector visibility;
- desktop geometric centering;
- authenticated Home centering with avatar;
- 767/768 px responsive transition;
- 390 px compact mobile selector;
- `/app` isolation;
- public Profile isolation.

Local measured maximum center deviation after the patch:

**0 px**.

The Test Lead visually approved the patch before release.

## 5. Production release

**Deployment:** `dpl_F8pCXRG6vfY6tyU43i2zboj4C2LG`  
**Commit:** `33556ecc5c1eda19522ec0c1975d7e755f6dbad4`  
**Environment:** Vercel Production  
**Status:** READY  
**Runtime:** Node 22.x  
**Domain:** `https://atlas-badge.vercel.app`

At a consistent 1280 px Production viewport:

| Route | Header center X | Selector center X | Deviation |
|---|---:|---:|---:|
| `/pt-br` | 632.5 px | 632.5 px | 0 px |
| `/pt-pt` | 632.5 px | 632.5 px | 0 px |
| `/es-419` | 632.5 px | 632.5 px | 0 px |
| `/es-es` | 632.5 px | 632.5 px | 0 px |
| `/fr` | 632.5 px | 632.5 px | 0 px |
| `/en-gb` | 632.5 px | 632.5 px | 0 px |

Right-side anonymous action widths varied from approximately **190.48 px to 264.25 px** without moving the selector.

Production also confirmed:

- official flag order and active state unchanged;
- mobile 390×844 compact selector PASS;
- no horizontal overflow;
- `/app` has no Home selector;
- public Profile retains its existing header;
- no console/page/request/broken-asset errors;
- no Vercel `error`/`fatal` cluster.

No safe authenticated Production session was created solely for this patch; the authenticated Home geometry contract had already passed against the Firebase Emulator harness with zero deviation and avatar preserved.

## 6. Test Lead decision

**AB-EV-049 CLOSED / PRODUCTION PASS — 1 September 2026.**

The localized public Home header now has a measurable cross-locale true-centering contract.
