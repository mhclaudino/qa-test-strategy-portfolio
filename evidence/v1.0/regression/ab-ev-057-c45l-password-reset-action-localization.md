# AB-EV-057 — C45L password-reset action localization and release qualification

**Evidence ID:** AB-EV-057  
**Checkpoint:** C45L — Password Reset Action Localization  
**Decision owner:** Test Lead / Product Owner  
**Product repository:** [mhclaudino/atlasbadge](https://github.com/mhclaudino/atlasbadge)  
**Product baseline:** C45K/B8 [`9374273e4dcc5745628267de61e96c0b12740e00`](https://github.com/mhclaudino/atlasbadge/commit/9374273e4dcc5745628267de61e96c0b12740e00)  
**Product publication:** [`6f308276600be2b14b15076def0c33a16c118770`](https://github.com/mhclaudino/atlasbadge/commit/6f308276600be2b14b15076def0c33a16c118770) — `feat(i18n): localize password reset action`; 18 files; Product `main` fast-forwarded  
**Vercel:** [`dpl_AzasBv7Wo4bKtSfJBTVFAi6xGTE9`](https://vercel.com/m-hc/atlas-badge/AzasBv7Wo4bKtSfJBTVFAi6xGTE9) — **Production / READY / exact Product SHA**, independently verified through the connected Vercel project  
**Test Lead decision:** C45L local functional/visual review PASS; emulated 390×844 mobile PASS; release deployment READY. Physical-phone manual test **NOT EXECUTED**. No live-Firebase password-reset operation or post-deployment Production functional smoke is claimed.

## 1. Requirement and scope

C45E/AB-EV-050 intentionally kept `/auth/action?mode=resetPassword` in pt-BR while localizing `verifyEmail`. C45L supersedes **only that historical isolation requirement**: the reset action now uses the same cookie/`Accept-Language`/pt-BR-fallback locale resolution as verification in `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr` and `en-GB`. Missing or unsupported action modes retain their established default-locale presentation. The canonical unprefixed action route, Firebase Auth action-code flow and existing localized `ForgotPasswordModal` are preserved.

The new `resetPasswordAction` catalog namespace supplies mode-specific metadata, loading/validation/form/error/success/return copy for all six locales, without wording that confuses reset with email verification. The existing `verifyEmail.authAction` remains separate. Locale is presentation only: no account schema, Firestore persistence, user-authored text, Firebase Rules, authentication method or password policy changes.

## 2. Requirement → implementation → test traceability

| Requirement / risk | Implementation | Verification / disposition |
|---|---|---|
| QR-08 password rule preservation | `AuthActionClient.tsx` keeps `validatePasswordLength`, `validatePasswordConfirmation` and `validateNewPassword`, min/max policy and Firebase `verifyPasswordResetCode` / `confirmPasswordReset` | Real Auth Emulator form flow; minimum-length and mismatch checks. **15-character minimum retained** |
| C45E shared-mode isolation | `resolveAuthActionLocale` accepts `verifyEmail` + `resetPassword`; missing/unsupported modes default; `proxy.ts` uses established resolution | `verificationLocale.test.ts`, `proxy.locale-resolution.test.ts` and affected C45E E2E expectations |
| Six-locale UI and accessibility | New `resetPasswordAction` namespace in six existing catalogues; distinct `verifyEmail.authAction` | Six C45L E2E reset-flow locales; Test Lead local manual visual PASS; per-locale server document/metadata checks |
| Correct Next.js metadata architecture | Server `src/app/auth/action/page.tsx` owns `generateMetadata({ searchParams })`; `AuthActionClient.tsx` owns the client Firebase/React flow; existing route layout provides locale context | TypeScript and Next.js build PASS; DOM `html lang`, document title and description included in locale E2E |
| Firebase action security | Genuine disposable Auth Emulator action codes; no logs/files with token added to commit; password changes handled by Firebase Auth | E2E code-generation/verification/reset, **login with new password and redirect to `/app`**; `realFirebaseRequests=0` reported |
| QR-39/40 responsive accessibility | Same UI, additional viewport scenario at 390×844 | One mobile Playwright PASS with fr/en-GB/pt-PT form/success/validation/invalid-link states; physical phone NOT EXECUTED |

## 3. Implementation and change scope

The Product commit contains **18 files**: one new `AuthActionClient.tsx`; one new `e2e/reset-password-action.c45l.spec.ts`; modified Server Page, C45E unit test, six message JSONs, locale resolver and its tests, proxy locale test, and **five existing C45 E2E specs** (Badges, Dashboard, Deep Country, Onboarding, Verify Email). GitHub commit parent is exactly C45K/B8 `9374273e…`, with no separate intermediate product commit.

Those five E2E modifications update the old contractual expectation `<html lang="pt-BR">` to the contextual French expectation when navigating to resetPassword with French preference. The existing C45E historical evidence remains accurate **for C45E's time**; C45L supersedes the isolation expectation prospectively. These are **test-oracle maintenance changes**, not product regressions or new defect counts.

The legacy C45E component test is updated for the new mode contract. The reset UI uses a server/client separation because Next.js does not permit `generateMetadata` exports from a file marked `"use client"`. The server reads the action mode and the locale header already passed from the proxy, selecting `verifyEmail.metadata`, `resetPasswordAction.metadata` or the existing fallback metadata namespace. No `DocumentTitleSync`, RootLayout workaround or custom Auth backend was added.

## 4. Test execution, diagnosis and Test Lead review

| Gate | Result / source | Important qualification |
|---|---|---|
| C45L six-locale and affected C45 E2E | **29 PASS / 0 FAIL / 0 SKIPPED** (~1.6 minutes), agent final clean Emulator run | The earlier Turbopack task-cache restoration error blocked a first attempt; `.next` was cleared and the completed run re-executed |
| Mobile viewport differential | **1 PASS** (~7.8 s), fr + en-GB + pt-PT at **390×844** | Added after the 29-case run; separate narrow follow-up, not counted inside the earlier 29 |
| Vitest | **832 PASS / 37 SKIPPED / 0 FAIL** (agent report) | An unchanged aggregate count is not evidence of no new assertions; focused tests and changed expectations were reported separately |
| TypeScript / lint / build | PASS / PASS (agent reported 0 errors, 0 warnings) / PASS | Final non-mobile Product source state; mobile edit was E2E-only |
| Staged diff check | PASS | Reported `git diff --cached --check` after E2E-only whitespace cleanup |
| Firebase isolation | `realFirebaseRequests=0` on completed Emulator E2E | Not a claim of a real Firebase Production reset |
| Test Lead local visual QA | **PASS** for all manually executable non-physical-phone scenarios | Six locale interfaces, form, errors/success and Login return accepted |
| Physical phone | **NOT EXECUTED** | Local-only access limitation; 390×844 viewport test is not a physical-device substitute |
| Production deployment | **READY**, exact `6f308276…`, target Production | Deployment readiness independently verified; live password reset in Production not executed |

**Intermediate failures are not final regressions.** During test construction, a fabricated `oobCode` could not render a valid reset form because the Auth Emulator correctly rejected it; a locator also matched “New password” and “Confirm new password” ambiguously, and an older test expected an eight-character rule while the real policy requires fifteen. The test was corrected to generate a genuine disposable Auth Emulator code, use unambiguous selectors and assert the actual policy. The later Next.js/Turbopack cache failure was an environment artifact; the final green run followed a clean cache rebuild. No password policy or Firebase behavior was weakened to satisfy an oracle.

**Public-evidence boundary:** A disposable QA link was initially included in a conversational agent report; it was rotated for manual testing and is **not reproduced here**. The temporary local script and link file are not among the 18 committed Product files. A local `.git/info/exclude` is an unversioned checkout safeguard, not a repository-wide `.gitignore` guarantee; staging was inspected before publication. Do not publish action codes, test-email addresses, credentials, password strings, raw Auth logs or local link contents.

## 5. Release decision and residuals

C45L local technical and Test Lead visual acceptance is **PASS within the executed scope**; the exact Product commit is published and the automatic **Production deployment is READY**. No additional Firebase Rules deployment was required or authorized. The two distinct unclaimed areas remain **physical-device manual testing** and **live Production reset-password functional smoke**; neither is to be silently relabeled PASS. The absence of Vercel CLI credentials on the agent workstation affected **local verification only**: the deployment's READY/Production/exact SHA state was independently established via connected Vercel.

C45M authenticated language selection remains a **separate pending product checkpoint**; this evidence makes no claim that it was implemented. Historical AB-EV-050/C45E pt-BR reset isolation has been intentionally superseded by C45L, while the C45E verifyEmail behavior remains in regression scope. Refer to the [Evidence Register](../evidence-register.md) and [AB-EV-056](ab-ev-056-c45k-geographic-localization.md).
