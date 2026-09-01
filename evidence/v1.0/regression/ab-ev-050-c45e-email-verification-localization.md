# AB-EV-050 — C45E Email-Verification Localization

## Status

**Evidence ID:** AB-EV-050  
**Increment:** C45E — Email-Verification Localization  
**Decision:** CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS  
**Test Lead approval:** 1 September 2026

## 1. Objective

C45E localizes the email-verification journey for all six approved V1.0 locales while preserving the existing authentication state machine, resend cooldown, duplicate-send protection, verification code application and post-verification profile routing.

The localized scope is deliberately bounded to:

- unprefixed `/verify-email`;
- the `mode=verifyEmail` branch of unprefixed `/auth/action`.

The shared `mode=resetPassword` branch remains Portuguese/pt-BR and is not functionally modified by C45E.

## 2. Locale contract

Supported locales:

1. `pt-BR`
2. `pt-PT`
3. `es-419`
4. `es-ES`
5. `fr`
6. `en-GB`

For `/verify-email` and `/auth/action?mode=verifyEmail`:

1. valid `atlasbadge_locale` cookie;
2. `Accept-Language`;
3. `pt-BR` fallback.

No locale query is required and no locale/language field is written to Firestore/UserProfile.

For `/auth/action?mode=resetPassword`, missing mode or unsupported mode, document locale intentionally remains `pt-BR` until those surfaces are localized.

## 3. Localized Verify Email surface

The six catalogs now contain a complete `verifyEmail` namespace covering:

- localized metadata title/description;
- page title/subtitle;
- masked-email explanation and localized fallback;
- spam/junk/promotions reminder;
- already-verified action;
- resend action and sending/countdown states;
- resend/check success and error states;
- cooldown accessibility announcements;
- verification/profile-sync/redirect transition states;
- Continue to application;
- logout/use-another-account;
- loading accessibility copy;
- localized ProfileLoadError presentation;
- verifyEmail-mode auth/action processing/success/error/instruction/retry/return states.

Representative language distinctions validated:

- pt-BR: `Verifique seu e-mail`, `Já verifiquei`;
- pt-PT: `Verifique o seu e-mail`, `Já confirmei`;
- es-419: `Verifica tu correo electrónico`, `Ya lo verifiqué`;
- es-ES: `Verifica tu correo`, `Ya lo he verificado`;
- en-GB: `Verify your email`, `Already verified`, `Continue to the application`, `Synchronising profile...`.

## 4. Verification/auth error architecture

C45E does not replace shared Portuguese AuthContext error strings.

Additive machine-readable codes support locale-specific Verify Email presentation while preserving existing return statuses and existing Portuguese `error` fields for legacy callers.

Introduced categories:

- `NO_ACTIVE_SESSION`;
- `ALREADY_VERIFIED`;
- `SESSION_CHANGED`;
- `TOO_MANY_REQUESTS`;
- `NETWORK`;
- `USER_DISABLED`;
- `TOKEN_EXPIRED`;
- `GENERIC`.

Translated strings are not used as business-logic identifiers.

## 5. Preserved verification state machine

The following existing behaviours remain unchanged:

- `refreshAuthUser()`;
- Firebase user reload;
- token refresh after verification;
- `refreshProfile()`;
- profile exists → `/app`;
- profile absent → `/onboarding`;
- focus/visibility re-check;
- persisted 60-second resend cooldown;
- duplicate in-flight resend protection;
- logout behaviour;
- VerificationGate routing.

Auth Emulator validation confirmed concurrent resend events produced exactly one `accounts:sendOobCode` call.

## 6. Email masking

The pre-existing masking algorithm remains unchanged.

Validated examples include:

- `ale***@example.test`;
- `a***@...`;
- `***@...`.

Only the no-valid-email fallback presentation is localized.

## 7. Auth action verification mode

The existing shared action route retains Firebase `applyActionCode()` behaviour for `mode=verifyEmail`.

Auth Emulator coverage used a real generated verification OOB code for success and also validated invalid code and controlled network-failure presentation.

The verification email continuation destination remains `/verify-email`.

No speculative locale query was added to the Firebase action URL.

## 8. Reset-password isolation

C45E intentionally leaves `mode=resetPassword` untouched.

With a French locale preference, validation confirmed:

- document `lang="pt-BR"`;
- Portuguese reset-password/error presentation;
- existing `verifyPasswordResetCode()`, password validation and `confirmPasswordReset()` semantics unchanged.

This avoids a false document-language claim while the reset-password UI is still Portuguese.

## 9. Implementation traceability

AtlasBadge release:

`aa6c2972d4755985de63a2d5c3ba2365c52cebef`  
`feat(i18n): localize email verification flow`

Release scope:

- 22 files;
- 1,515 insertions;
- 82 deletions.

C45E includes Verify Email/auth-action layouts and locale/error helpers, catalog changes, AuthContext additive codes, proxy/document-language handling and focused unit/E2E tests.

No Firebase rules/configuration, C44 photo logic, password-reset business logic, authenticated-app localization, public-Profile localization or QA documentation was changed by the product release.

## 10. Local validation

Runtime:

`Node v22.23.2`

Results:

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings outside C45E |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 63 / 63 PASS |
| Playwright Edge | 21 / 21 PASS |
| Firebase backend | Auth/Firestore/Storage Emulators |
| Requests to real Firebase | 0 |
| git diff --check | PASS |

Playwright total includes the C45E scenarios plus relevant C45C/C45D proxy/regression coverage.

## 11. Test Lead local/Emulator visual QA

Two disposable Emulator users were prepared:

- an authenticated unverified profile-less Verify Email user;
- a separate unverified profile-less user with exactly one real unconsumed VERIFY_EMAIL OOB code.

The Test Lead visually approved the flow locally.

Visual review included the localized `/verify-email` surface, real verifyEmail action processing/success, invalid verifyEmail error presentation and resetPassword isolation.

Stateful resend/cooldown and verification transitions were exercised only against Firebase Emulators.

## 12. Production release

**Deployment:** `dpl_FVD2sb5S8jeTFrt82pzqFm2zgvUT`  
**Commit:** `aa6c2972d4755985de63a2d5c3ba2365c52cebef`  
**Environment:** Vercel Production  
**Status:** READY  
**Runtime:** Node 22.x / Next.js 16.2.11  
**Domain:** `https://atlas-badge.vercel.app`

Production technical smoke confirmed:

- `/verify-email` server-side locale/document metadata for all six locales;
- anonymous browser guard → Login while preserving locale context;
- cookie/browser precedence: pt-PT, fr, es-AR→es-419, en-US→en-GB, unsupported→pt-BR;
- localized non-mutating invalid verifyEmail action states in fr, pt-PT and en-GB;
- French-preference resetPassword remains Portuguese/pt-BR;
- `/app`, `/badges` and public Profile remain pt-BR;
- no missing translation, hydration/runtime, unexpected network or Vercel fatal errors.

## 13. Production visual limitation and sign-off

The complete Verify Email page requires an authenticated unverified account, and successful verification requires stateful OOB handling.

No Production user or verification email was created solely to duplicate the already-passed Emulator visual/stateful tests.

Therefore the closure classification is intentionally:

**Production technical PASS + local/Emulator visual PASS**

—not a claim of full stateful Production visual execution.

The Test Lead accepted this evidence model and approved C45E closure on 1 September 2026.

## 14. Test Lead decision

**C45E CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS — 1 September 2026.**

Remaining localization continues in separate bounded checkpoints.
