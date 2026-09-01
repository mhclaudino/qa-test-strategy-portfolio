# AB-EV-048 — C45D Onboarding Localization

## Status

**Evidence ID:** AB-EV-048  
**Increment:** C45D — Onboarding Localization  
**Decision:** CLOSED / PRODUCTION TECHNICAL PASS + LOCAL VISUAL PASS  
**Test Lead approval:** 1 September 2026

## 1. Objective

C45D localizes the existing unprefixed `/onboarding` profile-creation surface for the six approved V1.0 locales while preserving authentication guards, profile schema, username/social-link business rules, privacy defaults and atomic Firestore creation semantics.

The checkpoint deliberately does not localize Verify Email, authenticated application pages, Badges or public Profile.

## 2. Locale contract

Supported locales remain:

1. `pt-BR`
2. `pt-PT`
3. `es-419`
4. `es-ES`
5. `fr`
6. `en-GB`

Onboarding remains unprefixed:

`/onboarding`

Locale resolution:

1. valid `atlasbadge_locale` cookie;
2. browser/device `Accept-Language`;
3. `pt-BR` fallback.

No query parameter is required and no locale/language field is written to Firestore/UserProfile.

## 3. Localized surface

The `onboarding` catalog namespace covers:

- title/subtitle;
- Username;
- Public Name;
- Biography;
- Location;
- Social Networks;
- Public Profile;
- placeholders/helper text;
- availability/checking states;
- username validation;
- social-link validation;
- character-limit errors;
- profile-creation errors;
- submit/loading/accessibility copy;
- localized Onboarding profile-load error presentation;
- metadata title/description.

Representative regional differences validated:

- pt-BR: `Nome de Usuário`, `Finalizar e abrir meu mapa`;
- pt-PT: `Nome de utilizador`, `Concluir e abrir o meu mapa`;
- es-419: `Cualquiera puede ver tu mapa`;
- es-ES: `Todo el mundo puede ver tu mapa`;
- en-GB: `Create your AtlasBadge`, `Public name`, `Finish and open my map`, with British-English metadata wording.

## 4. Locale-neutral business validation

Shared `profile.ts` and `socialLinks.ts` behaviour was intentionally preserved.

C45D adds stable machine-readable codes only where localized Onboarding presentation needs them:

- four username validation codes;
- eight social-link validation codes.

Existing Portuguese `message` values and caller behaviour remain intact.

Onboarding maps stable codes to translated UI. No translated string is used as a business-logic identifier.

Preserved rules include:

- username normalization;
- allowed characters;
- reserved username handling;
- length limits;
- 500 ms availability debounce;
- social-link HTTPS requirement;
- social-network hostname allowlists;
- generated social-link URLs.

## 5. Persistence contract

The existing profile-creation transaction remains authoritative.

Preserved collections/contracts:

- `users/{uid}`
- `publicProfiles/{uid}`
- `usernames/{normalizedUsername}`

Preserved profile semantics:

- `isPublic = true` default;
- stored fallback `Viajante`;
- user-entered displayName/bio/location/social links are never translated before persistence;
- no `locale` or `language` field exists in the stored profile.

French and English Emulator profile-creation scenarios produced equivalent schema/semantics and redirected successfully to `/app`.

## 6. Authentication guards

Existing guard behaviour remains:

- anonymous → `/login`;
- authenticated but unverified → `/verify-email`;
- verified with existing profile → `/app`;
- verified without profile → Onboarding;
- successful profile creation → `/app`.

No auth-routing refactor was introduced.

## 7. Document language and metadata

Production technical validation confirmed Onboarding locale/document values for:

| Preference | Resolved locale / `html lang` |
|---|---|
| pt-BR | pt-BR |
| pt-PT | pt-PT |
| es-419 | es-419 |
| es-ES | es-ES |
| fr | fr |
| en-GB | en-GB |

Locale precedence also verified:

- cookie `pt-PT` → pt-PT;
- cookie `fr` → fr;
- browser `es-AR` → es-419;
- browser `en-US` → en-GB;
- unsupported `de-DE` → pt-BR.

Metadata title/description are localized for all six locales.

Locale context remains scoped: `/app`, `/verify-email` and public Profile still emit `lang="pt-BR"` in C45D.

## 8. Implementation traceability

AtlasBadge release commit:

`5acf5ee4cc264509055163a6fb1470e162683c9a`  
`feat(i18n): localize onboarding`

Release scope:

- 23 files;
- 1,592 insertions;
- 77 deletions;
- 13 modified files;
- 10 added files.

No Firebase configuration/rules, C44 photo logic, Verify Email localization, authenticated-app localization, public-Profile localization or QA-documentation source was changed by C45D.

## 9. Local validation

Final release gates ran under Node `v22.23.2`.

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings outside C45D |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 83 / 83 PASS |
| Playwright Edge | 13 / 13 PASS |
| E2E backend | Firebase Auth/Firestore/Storage Emulators |
| Requests to real Firebase | 0 |
| git diff --check | PASS |

Automated coverage included locale resolution, metadata, translated content, validation mapping, auth guards, profile creation/persistence, responsive behaviour and C45C proxy/Login regression.

## 10. Test Lead local visual QA

A dedicated disposable Emulator account was prepared:

- authenticated;
- email verified;
- no private profile;
- no public profile;
- no username reservation.

This allowed the Test Lead to inspect the real guarded Onboarding form in all six locales without creating Production state.

The Test Lead approved the localized form visually on 1 September 2026.

No final form submission was required for the manual visual pass; automated Emulator tests separately validated real profile creation.

## 11. Production release

**Deployment:** `dpl_DPkW9nB2uDNRysKXCMdvTLDZtxyU`  
**Commit:** `5acf5ee4cc264509055163a6fb1470e162683c9a`  
**Environment:** Vercel Production  
**Status:** READY  
**Runtime:** Node 22.x / Next.js 16.2.11  
**Domain:** `https://atlas-badge.vercel.app`

Production technical smoke confirmed:

- six locale-resolution contexts return HTTP 200;
- correct server `html lang`;
- correct localized metadata;
- locale precedence;
- anonymous Onboarding guard → Login;
- no locale leakage into `/app`, Verify Email or public Profile;
- no missing-message/hydration/runtime/asset errors;
- no unexpected Vercel error/fatal cluster.

## 12. Production visual limitation and sign-off

A full interactive Onboarding visual replay in Production requires a verified authenticated account without an existing profile.

No such safe disposable Production user was available at sign-off, and creating one solely to duplicate already-passed local/Emulator visual testing would have introduced unnecessary Production state.

Therefore the closure classification is intentionally:

**Production technical PASS + local/Emulator visual PASS**

—not “Production visual PASS”.

The Test Lead reviewed the release evidence and approved closure on 1 September 2026.

## 13. Post-release visual follow-up

During multilingual Production Home review after C45D deployment, the Test Lead identified a separate public-Home header consistency issue: the language-selector group is not visually centered at exactly the same horizontal position across locales because right-side CTA widths vary by translated text.

This issue is outside the C45D Onboarding functional scope and does not invalidate C45D. It should be handled as a bounded visual-consistency follow-up before the next localization checkpoint.

## 14. Test Lead decision

**C45D CLOSED / PRODUCTION TECHNICAL PASS + LOCAL VISUAL PASS — 1 September 2026.**

Remaining localization continues in separate checkpoints.
