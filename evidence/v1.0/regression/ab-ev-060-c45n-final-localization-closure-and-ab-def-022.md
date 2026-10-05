# AB-EV-060 — C45N German localization final closure and AB-DEF-022

**Evidence ID:** AB-EV-060  
**Date:** 5 October 2026  
**Product:** AtlasBadge V1.0  
**Change:** C45N final localization closure / authenticated public-Home menu hotfix  
**Related defect:** AB-DEF-022 / AtlasBadge GitHub Issue #1  
**Test Lead decision:** PASS / CLOSED

## 1. Purpose

This evidence record closes the remaining C45N Test Lead visual gate after the additive German (`de-DE`) localization release and records the Production defect discovered during that final human review.

AB-EV-059 remains the historical technical-release evidence for the initial C45N German extension. This record does not rewrite AB-EV-059. It records the later defect, correction, Production retest and final Test Lead decision.

## 2. Historical C45N technical baseline

Initial C45N Product commit:

`8321975158a389760c0d8d13ae7fb1036b4209c2` — `feat(i18n): add German localization`

Initial C45N Vercel Production deployment:

`dpl_8jzWEnP8uhZFzZg443Nn6sAiayjE` — READY / production / exact SHA

AB-EV-059 established:

- German `/de-de` public Home and auth-entry presentation;
- seven-locale selector order `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr`, `de-DE`, `en-GB`;
- German message-catalog completeness and focused locale resolver/oracle coverage;
- protected Firebase Emulator authenticated convergence and dirty-state protection;
- seven-locale responsive selector qualification, including the pre-release 768 px overlap correction;
- Node 22 TypeScript/production-build PASS;
- non-destructive Production technical smoke;
- no Firebase schema/Rules change.

At that point, representative deep authenticated German visual acceptance was intentionally still pending Test Lead sign-off.

## 3. Final visual review and defect discovery

During the final Production visual review on 5 October 2026, the Test Lead compared authenticated localized public Home presentation across multiple locales.

Observed behavior:

- localized Home Hero/content changed correctly with the selected locale;
- the selected flag changed correctly;
- the authenticated avatar menu remained in Portuguese independently of the active locale.

Representative Portuguese fallback labels were:

- `Idioma`
- `Mapa`
- `Badges`
- `Perfil`
- `Editar perfil`
- `Sair`

The failure reproduced on localized Home presentation including German, English, French and Spanish contexts.

Expected behavior: authenticated menu labels must follow the active locale just as the localized Home and selector state do.

## 4. Classification

The finding is a genuine Product defect because a mandatory V1.0 localization surface remained Portuguese in Production while the surrounding page was localized.

Formal defect:

`AB-DEF-022 — Authenticated localized Home menu remains in Portuguese`

Operational record:

AtlasBadge GitHub Issue #1

Classification:

- Product defect;
- localization / authenticated navigation presentation;
- Production;
- Severity: Medium;
- Priority: P1 because it blocked final C45N/V1.0 localization acceptance;
- no data, privacy, authentication, persistence, Firebase schema or Rules impact.

## 5. Root cause

The localized public Home supplied the active locale and language-selector state to `Header`, but did not supply the localized authenticated-menu label catalogue.

`Header` therefore fell back to its Portuguese authenticated-menu strings even while the localized Home itself rendered the selected locale correctly.

This was a presentation-context omission, not a locale resolver, cookie, Firebase or persisted-data failure.

## 6. Correction

Product correction commit:

`8330b902b0e023891a262b2da837397b86966f59` — `fix(i18n): localize authenticated public Home menu`

Vercel correction deployment:

`dpl_7vfMefKwywz5Xp1nakyUwfU7Y7de` — READY / production / exact SHA

The change passes the active `messages.header.authenticated` catalogue into the authenticated Header rendered from localized public Home and adds focused regression coverage for the affected contract.

The correction was intentionally limited to the presentation/context boundary. It did not change:

- locale IDs or route ownership;
- Firebase Auth behavior;
- Firestore schema or Rules;
- Storage behavior;
- status/visit/memory semantics;
- user data;
- public-profile projection;
- dirty-state protection.

Later Product `main` process-documentation commits changed only agent-governance Markdown. Current Product `main` after those process-only commits is `5e9839c2d5f497710119c30d360dfe85713ab037`; the deployed runtime behavior remains the AB-DEF-022-corrected Product state.

## 7. Technical evidence boundary

The hotfix received:

- direct source/diff review of the isolated Header/localized-Home change;
- a permanent focused regression test committed with the correction;
- Vercel Preview READY at the correction SHA;
- Vercel Production READY at the exact correction SHA;
- successful Production rendering after deployment.

No claim is made here that a separate GitHub Actions/Vitest runner executed the new focused test after the correction; the repository did not expose such a remote runner for this commit. The final user-facing acceptance evidence is therefore the exact-sha deployment plus Test Lead Production retest, supported by the existing C45N Emulator/build baseline from AB-EV-059.

## 8. Test Lead Production retest

Date: 5 October 2026

The Test Lead manually retested the authenticated localized Home after the correction and reported PASS.

Representative review included German, English, French and Spanish localized Home contexts with the authenticated avatar menu opened after locale switching.

For German, the menu now follows the selected locale, including presentation such as:

- `Sprache`
- `Karte`
- `Badges`
- `Profil`
- `Profil bearbeiten`
- `Abmelden`

The Test Lead explicitly confirmed the corrected behavior as PASS.

## 9. Final decision

`AB-DEF-022: CLOSED — Test Lead PASS — 5 October 2026`

`C45N: CLOSED — PRODUCTION TECHNICAL + FINAL TEST LEAD VISUAL PASS`

The seven-locale V1.0 localization implementation is now complete for the currently approved Product scope.

Localization remains permanent regression scope, particularly:

- public Home route/locale convergence;
- authenticated public-Home avatar menu localization;
- flag/avatar bidirectional selector state;
- document language/metadata;
- dirty-state protection;
- public-profile localized presentation;
- seven-locale responsive geometry;
- locale-neutral persisted business/data contracts.

## 10. Release implication

Closing C45N does **not** by itself approve AtlasBadge V1.0 for real-user launch.

The Product now proceeds to final release-readiness activities: release-criteria reconciliation, final V1.0 regression, required Windows Chrome coverage, Release Candidate freeze, final Production smoke, Production data reset/clean-start verification and final Test Lead release decision.

## 11. Process-control note

The AB-DEF-022 code correction was implemented directly by the coordinator instead of using the implementation actor already established for the active localization workflow. The resulting Product state was independently reviewed and received Test Lead PASS, so no artificial rollback/reimplementation is required solely to change actor attribution.

The process deviation is preserved transparently and was addressed separately through repository role-routing governance. It is not hidden or reclassified as a Product defect.
