# AB-EV-046 — C45B Public Home Localization and Language Selector

## Status

**Evidence ID:** AB-EV-046  
**Increment:** C45B — Public Home Localization + Language Selector  
**Decision:** CLOSED / PRODUCTION + MOBILE VISUAL PASS  
**Test Lead approval:** 31 August 2026

## 1. Objective

C45B converted the C45A routing/i18n foundation into the first fully visible AtlasBadge localisation surface. The public Home, Hero, public Header and Footer were translated for all six approved V1.0 locales, with responsive language selection, explicit preference persistence, localized metadata and correct document-language semantics.

The checkpoint remained intentionally limited to the public Home surface. Login, Onboarding, authenticated application pages, Badges, public Profile and other domain/system-controlled UI remain later localisation scope.

## 2. Supported locale contract

Official order:

1. `pt-BR` — Português Brasil — 🇧🇷
2. `pt-PT` — Português Portugal — 🇵🇹
3. `es-419` — Español Latinoamérica — 🇦🇷
4. `es-ES` — Español España — 🇪🇸
5. `fr` — Français — 🇫🇷
6. `en-GB` — English — 🇬🇧

The Argentina flag remains the deliberate visual representation for `es-419`. Locale IDs, not flags, drive application logic.

Public Home URLs remain:

- `/pt-br`
- `/pt-pt`
- `/es-419`
- `/es-es`
- `/fr`
- `/en-gb`

C45A root precedence remains unchanged:

explicit URL → valid `atlasbadge_locale` cookie → browser/device language → `pt-BR` fallback.

## 3. Implemented C45B surface

C45B localizes:

- public Home content;
- Hero copy and CTA;
- anonymous public Header actions;
- public Footer;
- Home/map-preview system-controlled labels;
- public Home metadata `title` and `description`;
- server-rendered document `lang` for each locale route.

Message catalogs use `next-intl` and provide complete keys for the six supported locales.

Representative intentional variant differences include:

- pt-BR: “já pisou”, “Busque”, “Compartilhe”
- pt-PT: “já esteve”, “Procure”, “Partilhe”
- es-419: “Agrega los lugares que visitaste”, “Sin anuncios”
- es-ES: “Añade los lugares que has visitado”, “Sin publicidad”
- en-GB: British-English forms such as `personalised`, `colour` and `traveller`.

The AtlasBadge brand name and approved product terminology such as “Badges” are preserved where intentional.

## 4. Language selector

### Desktop

The localized Home displays six local SVG flags in the official order. The active locale has a visible selected state and every option has a meaningful accessible name.

For anonymous users, the layout is:

logo | language selector | localized Sign in / Create Map actions

After a Test Lead requirement correction, the selector is also visible on the localized Home for authenticated users:

logo | language selector | authenticated avatar/menu

The selector does not appear merely because a user is authenticated; visibility follows the explicit localized-Home surface contract.

### Mobile

At narrow widths, the six inline flags are replaced by one compact current-language trigger. Its menu:

- contains all six locales in official order;
- shows flag + endonym;
- identifies the active locale;
- supports keyboard/touch interaction, arrow navigation, Home/End, Escape and focus restoration;
- stays inside a 390 px viewport without horizontal overflow.

## 5. Locale preference persistence

Explicit selection writes the non-sensitive preference cookie:

`atlasbadge_locale=<internal locale ID>`

Attributes:

- `Path=/`
- one-year `Max-Age`
- `SameSite=Lax`

No Firestore or `UserProfile` locale field was introduced.

Explicit locale URLs remain authoritative over the cookie.

## 6. Document language and metadata

C45B produces the following server-rendered document language values:

| Route | `html lang` |
|---|---|
| `/pt-br` | `pt-BR` |
| `/pt-pt` | `pt-PT` |
| `/es-419` | `es-419` |
| `/es-es` | `es-ES` |
| `/fr` | `fr` |
| `/en-gb` | `en-GB` |

Non-localized routes remain `pt-BR`; for example, `/login` with a French preference cookie still renders Portuguese content with `lang="pt-BR"`.

Localized `title` and `description` metadata are provided for the six Home routes. Full international SEO (`hreflang`, localized canonicals and the future SEO mapping for app locale `es-419`) remains separate scope.

## 7. Rendering architecture review

To produce server-correct `<html lang>`, C45B reads a proxy-injected internal locale header from the shared root layout using Next.js `headers()`.

The architecture review confirmed that this makes the page tree request-time dynamic rather than statically prerendered. Nested layouts, client-only document mutation, Metadata API and static params could not provide equivalent server-side `html lang` semantics under the existing route contract. Multiple root layouts could restore isolation, but would require a disproportionate route-group migration and cross-root navigation trade-off.

Decision:

- retain the semantically correct current implementation for V1.0;
- record global request-time dynamic rendering as explicit technical debt;
- treat any future multi-root migration as a dedicated architecture/performance checkpoint.

## 8. Implementation traceability

AtlasBadge release commit:

`c5b025199745425f37caa7a0f68ebdb44ddbb6f8`  
`feat(i18n): localize public home and language selector`

Release scope:

- 47 files;
- 1,812 insertions;
- 116 deletions;
- six message catalogs;
- six local locale-flag SVG assets;
- language selector, cookie writer, document-language helper and focused test coverage.

No Firebase, Firestore Rules, Storage Rules, C44 photo/quota, public-profile URL, authenticated-app localization or QA-documentation source was changed by C45B.

## 9. Local validation

Final release gates ran under Node `v22.23.2`.

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings outside C45B |
| Build | PASS — Next.js 16.2.11 |
| Focused Vitest release gate | 32 / 32 PASS |
| Focused Playwright release gate | 26 / 26 PASS |
| Browser | Edge |
| E2E backend | Firebase Auth/Firestore/Storage Emulators |
| Requests to real Firebase during E2E | 0 |

Earlier full C45B validation also covered message-catalog completeness, locale resolution, Header/component behaviour and responsive selection before the final requirement correction.

## 10. Local environment incident classification

During manual QA, a prior browser console entry showed:

`GET /api/visit-photo/owner?slotId=0&variant=a → 500`

Investigation proved the local Next process was actually running Node 24 despite a Node-22-intended startup command. The Google OAuth transport failed with `ERR_STREAM_PREMATURE_CLOSE`. Restarting the same application directly under Node `v22.23.2` returned HTTP 200 for the same owner-photo endpoint.

Production owner-photo requests also returned 200 and no C45B file touched C44 photo logic.

Classification:

- local runtime/environment incident;
- not a Product regression;
- outside C45B functional scope.

## 11. Production release

**Deployment:** `dpl_5WtEJGeCBtm6QSJpwZhgQzk1CJ66`  
**Commit:** `c5b025199745425f37caa7a0f68ebdb44ddbb6f8`  
**Environment:** Vercel Production  
**Domain:** `https://atlas-badge.vercel.app`  
**Status:** READY  
**Build:** 37 seconds under Node 22

Production smoke confirmed:

- all six localized Home routes return HTTP 200 with translated content;
- all six routes emit the expected `html lang`;
- `/fr` remains the Home route, not `[username]`;
- `/@mhclaudino` remains the public Profile and has no Home language selector;
- `/app` remains unprefixed and has no Home language selector;
- `/pt-br/app` returns 404 as designed;
- anonymous desktop selector shows the six locales in official order;
- selection persists the internal locale ID in `atlasbadge_locale`;
- authenticated Production Home shows language selector + avatar while `/app` remains unchanged;
- no unexpected console/page error or Vercel error/fatal log was observed.

## 12. Mobile Production validation

A 390×844 Production technical smoke confirmed:

- desktop flags hidden;
- compact trigger visible;
- six options in official order;
- menu fully inside viewport;
- no horizontal overflow;
- locale navigation/cookie persistence functional.

The Test Lead then validated the released UI on a real mobile device and approved the mobile behaviour on 31 August 2026.

## 13. Scope intentionally remaining

C45B does not localize:

- Login;
- Onboarding;
- authenticated `/app`;
- Badges;
- public Profile;
- country/geographic presentation data;
- travel-status presentation labels;
- achievements;
- stats;
- memories/Wishlist;
- Firebase/auth error messaging;
- full international SEO.

German and US-English were considered and explicitly not added to the V1.0 locale baseline at this checkpoint. The supported V1.0 set remains the six locales above.

## 14. Test Lead decision

**C45B CLOSED / PRODUCTION + MOBILE VISUAL PASS — 31 August 2026.**

C45A routing and C45B public-Home localisation now form the approved baseline for subsequent V1.0 localisation checkpoints.
