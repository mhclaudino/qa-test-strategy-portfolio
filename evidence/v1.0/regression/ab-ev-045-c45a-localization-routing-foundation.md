# AB-EV-045 — C45A Localization Routing Foundation

## Status

**Evidence ID:** AB-EV-045  
**Increment:** C45A — Localization Routing Foundation  
**Decision:** CLOSED / PRODUCTION PASS  
**Test Lead approval:** 30 August 2026

## 1. Objective

C45A established the minimum safe multilingual routing and i18n foundation for AtlasBadge V1.0 before visual translation work. The checkpoint intentionally separated routing/infrastructure from C45B translated UI so that route ownership, locale resolution, runtime compatibility and Production behaviour could be validated independently.

C45A does not claim that AtlasBadge localisation is complete.

## 2. Product contract

Official V1.0 locales, in the approved visual order:

1. `pt-BR` — Português Brasil — 🇧🇷
2. `pt-PT` — Português Portugal — 🇵🇹
3. `es-419` — Español Latinoamérica — 🇦🇷
4. `es-ES` — Español España — 🇪🇸
5. `fr` — Français — 🇫🇷
6. `en-GB` — English — 🇬🇧

The Argentina flag is the deliberate Product Owner/Test Lead visual representation for `es-419`. Flags are presentation only; implementation logic is keyed by locale IDs.

Public Home routes established by C45A:

- `/pt-br`
- `/pt-pt`
- `/es-419`
- `/es-es`
- `/fr`
- `/en-gb`

Root locale-resolution precedence:

1. explicit locale URL;
2. valid saved `atlasbadge_locale` cookie;
3. browser/device `Accept-Language`;
4. `pt-BR` fallback.

Browser mapping:

- `pt-PT` → `pt-PT`
- other Portuguese → `pt-BR`
- `es-ES` → `es-ES`
- other Spanish → `es-419`
- French → `fr`
- English → `en-GB`
- unsupported language → `pt-BR`

Authenticated routes remain unprefixed. The public Profile root `[username]` contract remains unchanged. No locale field was added to Firestore or `UserProfile`.

## 3. Architectural risk and decision

AtlasBadge already used:

`src/app/[username]/page.tsx`

A naive sibling root:

`src/app/[locale]/page.tsx`

would have created competing dynamic-route ownership at the same level and risked breaking public-profile links.

The approved C45A architecture therefore uses:

- six explicit static public Home locale entry points;
- one shared Home implementation;
- central `src/i18n` locale registry/resolution;
- `next-intl`;
- a root-only locale resolver;
- no migration or semantic change to `[username]`.

This kept the existing public-profile URL contract deterministic while allowing locale-specific public Home URLs.

## 4. Implementation traceability

AtlasBadge implementation commit:

`92be6f41ee5a71eaf20396383cd11624b84ef842`  
`feat(i18n): establish localization routing foundation`

Security-hardening commit:

`d25ac8510ebd2e5c1fdea34143a7b862d97ae5b7`  
`chore(security): upgrade Next.js to 16.2.11`

Final framework/runtime baseline:

- Next.js `16.2.11`
- `eslint-config-next 16.2.11`
- `next-intl 4.14.1`
- Node `v22.23.2`

## 5. Local validation

Final C45A gates were repeated under the official Node 22 runtime.

| Gate | Result |
|---|---|
| TypeScript `tsc --noEmit` | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings outside C45A |
| Production build | PASS — Next.js 16.2.11; 19 pages generated |
| Focused Vitest | 33 / 33 PASS |
| Focused Playwright | 20 / 20 PASS |
| Browser | Edge |
| Backend during E2E | Firebase Auth/Firestore/Storage Emulators |
| Requests to real Firebase during E2E | 0 |

Weighted `Accept-Language` cases were explicitly validated:

- `es-ES;q=0.7,fr-FR;q=0.9,en-US;q=0.8` → `fr`
- `de-DE;q=1.0,en-US;q=0.9` → `en-GB`

## 6. Security hardening before publication

The initial C45A validation identified that Next.js `16.2.10` was inside the affected range of `GHSA-6gpp-xcg3-4w24`.

The AtlasBadge configuration did not use the advisory's vulnerable `config.i18n.locales` scenario, so no exploitable C45A Product Defect was confirmed. However, publication was deliberately held until the framework was moved to the fixed `16.2.11` release.

The security patch:

- upgraded Next.js `16.2.10 → 16.2.11`;
- aligned `eslint-config-next` to `16.2.11`;
- introduced no unrelated dependency upgrades;
- did not use `npm audit fix`;
- removed `GHSA-6gpp-xcg3-4w24` from the audit result.

After the patch, the production-dependency audit still reported 11 vulnerable packages: 6 moderate and 5 high, with 0 critical. Those remaining findings were pre-existing outside C45A. `next-intl` and its newly introduced dependency chain did not appear in the audit findings.

## 7. Production deployment

**Vercel deployment:** `dpl_2mzDcqfXz7hb2fC5ZcTNMZfscjNP`  
**Production commit:** `d25ac8510ebd2e5c1fdea34143a7b862d97ae5b7`  
**Environment:** Vercel Production  
**Domain:** `https://atlas-badge.vercel.app`  
**Deployment state:** READY

## 8. Production smoke

| Scenario | Production result |
|---|---|
| `/pt-br` | HTTP 200 — PASS |
| `/pt-pt` | HTTP 200 — PASS |
| `/es-419` | HTTP 200 — PASS |
| `/es-es` | HTTP 200 — PASS |
| `/fr` | HTTP 200 — Home route, not `[username]` |
| `/en-gb` | HTTP 200 — PASS |
| `/` + `Accept-Language: en-US` | 307 → `/en-gb` |
| `/` + `Accept-Language: es-AR` | 307 → `/es-419` |
| `/` + `Accept-Language: es-ES` | 307 → `/es-es` |
| `/` + `Accept-Language: fr-FR` | 307 → `/fr` |
| `/` + unsupported `de-DE` | 307 → `/pt-br` |
| weighted FR highest | 307 → `/fr` |
| unsupported DE then supported EN | 307 → `/en-gb` |
| cookie `fr` + browser EN | `/fr` |
| cookie `pt-PT` + browser EN | `/pt-pt` |
| invalid cookie + browser EN | `/en-gb` |
| cookie `fr` + direct `/en-gb` | remains `/en-gb` |
| public `/@mhclaudino` | HTTP 200; public Profile preserved |
| anonymous `/app` | existing login flow; no locale prefix |
| `/pt-br/app` | HTTP 404 as designed |

No redirect loop, unhandled JavaScript error, hydration error, 5xx response or broken primary asset was observed on the six public Home routes. The Production smoke did not create or modify Firebase data.

## 9. Intentional C45A scope boundary

At C45A closure:

- all six public Home routes intentionally still render Portuguese copy;
- global `<html lang="pt-BR">` intentionally remains unchanged;
- language flags/selectors are not implemented;
- locale-choice cookie writing from UI is not implemented;
- authenticated UI remains Portuguese;
- Login/Onboarding remain Portuguese;
- public Profile content remains Portuguese.

These are remaining localisation requirements, not C45A defects.

## 10. Test Lead decision

**C45A CLOSED / PRODUCTION PASS — 30 August 2026.**

The routing/i18n foundation is approved as the baseline for the next localisation checkpoint.

C45B remains responsible for:

- translated Home/Hero/public Header/Footer;
- desktop language flags;
- compact mobile language selector;
- locale-choice persistence/writer;
- correct document-language semantics for translated public pages.

No Product Defect ID was created for C45A.
