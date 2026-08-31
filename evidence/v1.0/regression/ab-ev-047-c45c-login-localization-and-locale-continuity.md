# AB-EV-047 — C45C Login Localization and Locale Continuity

## Status

**Evidence ID:** AB-EV-047  
**Increment:** C45C — Login Localization + Locale Continuity  
**Decision:** CLOSED / PRODUCTION + VISUAL PASS  
**Test Lead approval:** 31 August 2026

## 1. Objective

C45C localizes the existing unprefixed `/login` authentication-entry surface for the six approved V1.0 locales while preserving the existing Firebase authentication, callback and authenticated-route contracts.

The checkpoint deliberately does not create locale-prefixed Login routes and does not localize Onboarding, Verify Email, authenticated application pages, Badges or public Profile.

## 2. Locale contract

Supported locales remain exactly:

1. `pt-BR`
2. `pt-PT`
3. `es-419`
4. `es-ES`
5. `fr`
6. `en-GB`

No German or `en-US` locale was added.

Login locale resolution:

1. valid explicit `locale` query parameter;
2. saved `atlasbadge_locale` cookie;
3. browser/device `Accept-Language`;
4. `pt-BR` fallback.

Valid explicit query values use the internal locale IDs. Invalid query values are ignored safely.

A valid explicit query refreshes the existing preference cookie using the C45B semantics:

`atlasbadge_locale=<locale ID>; Path=/; Max-Age=31536000; SameSite=Lax`

No Firestore/UserProfile locale field is created.

## 3. Routing and Home→Login continuity

The Login route remains:

`/login`

No `/{locale}/login` routes were introduced.

Localized anonymous Home actions carry the active locale explicitly to Login, for example:

- `/fr` → `/login?locale=fr`
- `/en-gb` → `/login?locale=en-GB`

Header Sign in and Create Map use the localized Login destination.

During pre-release review, an unintended regression was identified: the Hero and final Home CTAs, which previously preserved authenticated navigation to `/app`, had temporarily been converted to fixed Login links. This was corrected before commit by reusing the existing auth-aware `ContextualHomeLink`.

Final contract:

- anonymous Hero/final CTA → localized `/login?locale=<locale>`;
- authenticated Hero/final CTA → `/app`.

No new signup query parameter or Login-mode contract was invented; the existing internal Login/Create Account toggle remains authoritative.

## 4. Localized Login scope

The six catalogs include Login metadata and system-controlled presentation for:

- page heading/subtitle;
- Google authentication button;
- form fields and placeholders;
- password and confirmation guidance;
- Remember Me;
- Login/Create Account toggle;
- Forgot Password modal;
- loading/submission states;
- Login-local validation and Firebase-auth error presentation;
- accessibility labels.

Regional wording remains intentionally distinct. Examples validated include:

- pt-BR: `Senha`, `Lembrar de mim`;
- pt-PT: `Palavra-passe`, `Manter sessão iniciada`;
- en-GB: British-English wording such as `personalised`;
- es-419 and es-ES retain distinct regional text where appropriate.

## 5. Document language and metadata

`/login` is still unprefixed but is now locale-aware.

Production verified server document language:

| Login locale | `html lang` |
|---|---|
| pt-BR | pt-BR |
| pt-PT | pt-PT |
| es-419 | es-419 |
| es-ES | es-ES |
| fr | fr |
| en-GB | en-GB |

Title/description metadata are localized for all six Login locales.

Locale context is scoped to Login. With a French locale cookie, `/app`, public Profile, Onboarding and Verify Email continue rendering the existing Portuguese surface with `lang="pt-BR"`.

## 6. Authentication integrity

C45C changed presentation/context only. Existing behaviour remains:

- Google sign-in;
- email/password sign-in;
- account creation;
- password recovery;
- Remember Me;
- verification routing;
- onboarding/application redirects;
- Firebase configuration and identity contracts.

The Google-linking regression test remained green and preserved UID/profile identity without duplicate-user creation.

## 7. Implementation traceability

AtlasBadge release commit:

`898cb5bcd088e379afaeed388b7eab0c35b27bb2`  
`feat(i18n): localize login and preserve locale continuity`

Release scope:

- 27 files;
- 1,224 insertions;
- 78 deletions;
- 19 modified files;
- 8 added files.

No Firebase rules/configuration, C44 photo logic, authenticated-application localisation, public-Profile localisation or QA-documentation source was changed by C45C.

## 8. Local validation

Final gates ran under Node `v22.23.2`.

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings outside C45C |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 76 / 76 PASS across 12 files |
| Playwright focused | 21 / 21 PASS |
| Google-linking regression | 1 / 1 PASS |
| E2E backend | Firebase Auth/Firestore/Storage Emulators |
| Requests to real Firebase | 0 |

The final review also confirmed the Emulator safety changes were required only to keep Portuguese locators deterministic under browser-driven locale resolution; Emulator protection was not weakened.

## 9. Production release

**Deployment:** `dpl_tixX939LhgvSCziRg2HnAesn7B6n`  
**Commit:** `898cb5bcd088e379afaeed388b7eab0c35b27bb2`  
**Environment:** Vercel Production  
**Domain:** `https://atlas-badge.vercel.app`  
**Status:** READY  
**Build:** 40 seconds under Node 22.x

Production smoke confirmed:

- all six explicit Login locales return HTTP 200;
- localized Login content is present;
- `html lang` is correct for all six;
- localized metadata is correct;
- no missing-message/runtime/hydration errors;
- query beats cookie;
- cookie beats browser language when query is absent;
- browser locale is used when query/cookie are absent;
- unsupported language falls back to pt-BR;
- valid query persists the internal locale ID;
- French Home anonymous Login-bound CTAs preserve `fr`;
- representative English CTA preserves `en-GB`;
- authenticated Hero/final CTAs still resolve to `/app`;
- non-localized routes remain Portuguese/pt-BR.

## 10. Responsive and functional Production smoke

At 390×844, `fr`, `pt-PT` and `en-GB` Login surfaces showed:

- no horizontal overflow;
- no clipped translated text;
- usable Google button;
- usable inputs/buttons;
- usable account-mode toggle;
- usable Forgot Password UI.

Functional UI smoke confirmed form entry, Login/Create Account toggle, Forgot Password open/close, Google control presence and Remember Me behaviour without creating a Production account or triggering an unnecessary password reset.

No unexpected 4xx/5xx or Vercel runtime errors were observed.

## 11. Test Lead decision

The Test Lead completed Production visual review and approved C45C on 31 August 2026.

**C45C CLOSED / PRODUCTION + VISUAL PASS — 31 August 2026.**

The approved localisation baseline is now:

C45A routing foundation → C45B public Home/selector → C45C Login.

Remaining localisation continues in separate bounded checkpoints.
