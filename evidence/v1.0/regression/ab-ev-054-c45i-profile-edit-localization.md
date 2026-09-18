# AB-EV-054 — C45I Profile Edit localization

## Evidence summary

**Evidence ID:** AB-EV-054  
**Checkpoint:** C45I  
**Subject:** Profile Edit localization and embedded account/security presentation  
**Product commit:** `ecc2b735e284a0babfa15b9febcda297ad2846ce`  
**Commit message:** `feat(i18n): localize profile editing`  
**Vercel deployment:** `dpl_EaQtK4iv6FjaKdachTj5UKAinhan` — Production `READY`  
**Runtime:** package-enforced Node 22.x; Next.js 16.2.11  
**Final decision:** **C45I CLOSED / PRODUCTION TECHNICAL + VISUAL PASS**  
**Test Lead sign-off:** 18 September 2026

## 1. Change objective

C45I extends the AtlasBadge V1.0 localization programme into owner Profile editing without pulling the external public Profile into scope.

The checkpoint localizes presentation for:

- `ProfileEditModal`;
- profile photo controls and feedback;
- username/display name/bio/location fields;
- privacy controls;
- social-link fields and validation;
- Save/Cancel and success/error feedback;
- embedded `AuthMethods`;
- `DeleteAccountSection`;
- `DeleteAccountModal`.

The supported locale set remains exactly:

1. `pt-BR`;
2. `pt-PT`;
3. `es-419`;
4. `es-ES`;
5. `fr`;
6. `en-GB`.

User-authored profile values are not translated, and no locale/language field is persisted to UserProfile, Firestore or the public projection.

## 2. Localization boundary

Profile Edit is reachable from two different presentation contexts:

- authenticated navigation already inside the localized app shell;
- the owner view of `/@username`, whose external public Profile remains intentionally pt-BR until C45J.

C45I therefore encapsulates locale resolution at the editor boundary instead of globalizing the public Profile.

Resolution follows the approved preference model:

```text
explicit locale
→ saved atlasbadge_locale cookie
→ browser language resolution
→ existing default behaviour
```

This allows, for example, a French Profile Edit modal to render over the still-Portuguese public Profile without changing that page's route/document contract.

## 3. Stable validation and feedback presentation

C45I removes presentation logic that depended on Portuguese message fragments.

Localized UI maps stable machine-readable information where available:

- username validation codes;
- social-link validation codes;
- photo-processing `AppError` codes;
- explicit structured feedback type such as error/success/info.

Translated strings are output, not business-logic identifiers.

The existing profile-service result contract was not redesigned merely for localization. Persistence and conflict behaviour were kept compatible while the UI supplies localized presentation.

## 4. Preserved product/security invariants

The checkpoint was explicitly bounded to presentation/localization for sensitive account/profile areas.

The following contracts remain unchanged:

- UserProfile schema;
- username normalization, uniqueness and availability semantics;
- username-change routing behaviour;
- display/bio/location persistence limits;
- social-link normalization and accepted-network rules;
- profile visibility semantics;
- profile-photo processing/upload/replacement/removal lifecycle;
- deletion of the previous official photo only after successful profile persistence;
- Google/account-method linking identity rules;
- password policy;
- reauthentication behaviour;
- account-deletion API/backend and retry semantics;
- Firebase Rules;
- sanitised public-profile projection.

No destructive account operation was required to prove translated presentation.

## 5. Local automated evidence

Final C45I validation recorded:

- Profile Edit + profile-photo regression: **29 / 29 PASS**;
- expanded focused regression: **113 / 113 PASS across 11 files**;
- Playwright Edge/Firebase Emulator localization suite: **12 / 12 PASS**;
- TypeScript: PASS;
- lint: PASS with zero errors and 21 pre-existing warnings;
- production build: PASS;
- `git diff --check`: PASS;
- Firebase project: `demo-atlasbadge-web`;
- Auth/Firestore/Storage Emulator isolation confirmed;
- `realFirebaseRequests=0`.

Coverage included all six locales, stable-code feedback mapping, profile persistence invariants, public/private projection isolation, embedded account/security presentation, accessibility states and nested dialogs.

## 6. Responsive and accessibility evidence

Representative mobile validation used `390×844` for:

- `fr`;
- `pt-PT`;
- `es-ES`;
- `en-GB`.

The focused browser suite covered long translated copy, horizontal-overflow protection, labels, switches, disabled buttons, focus behaviour and nested account/security dialogs.

C45I does not claim formal accessibility certification; it extends the existing QR-40 technical regression baseline.

## 7. Public Profile isolation

C45I deliberately does not localize the external public Profile.

The isolation proof confirmed that under a French preference:

- the Profile Edit modal can render in French;
- the surrounding `/@username` surface remains under the previous pt-BR contract;
- no locale-prefixed public-profile route is introduced;
- no locale field is added to the public/private profile schema.

Production smoke also confirmed direct unprefixed public-profile routing and that `/pt-br/@mhclaudino` remains unsupported/404 rather than becoming a new public-profile route.

Full public Profile localization remains C45J.

## 8. Release and Production technical evidence

The approved release commit is:

```text
ecc2b735e284a0babfa15b9febcda297ad2846ce
feat(i18n): localize profile editing
```

The commit contained exactly 18 approved C45I files. Before publication the working tree was clean; after push, `HEAD` and `origin/main` matched with ahead/behind `0/0`.

Vercel deployment:

```text
dpl_EaQtK4iv6FjaKdachTj5UKAinhan
Production READY
SHA: ecc2b735e284a0babfa15b9febcda297ad2846ce
Next.js: 16.2.11
Runtime: Node 22.x from package engines
```

Vercel deployment metadata independently confirmed the exact Git SHA, branch `main`, Production target and `READY` state.

Safe Production smoke verified expected base-route behaviour, public-Profile route isolation and normal deployed assets/runtime without mutating real account/profile data.

For the reviewed deployment window:

- Vercel runtime error clusters: none found;
- runtime `error`/`warning`/`fatal` logs: none found;
- no unexpected 5xx was observed in the release smoke.

## 9. Node configuration observation

The Vercel project setting reports Node 24.x, while the repository `package.json` intentionally declares `engines.node = 22.x`.

Build logs explicitly state that the project setting is overridden and Node 22.x is used; the build completed successfully.

This is recorded as **configuration drift / technical debt**, not a Product Defect and not a C45I release blocker. The runtime used for this checkpoint remains the approved Node 22 line.

The Vercel deployment metadata, rather than an unverified custom-domain assumption, is the authoritative deployment identity for this evidence record.

## 10. Production safety boundary

No destructive Production action was executed during technical release validation.

Specifically, the release smoke did not:

- delete an account;
- confirm account deletion;
- change password;
- link/unlink a provider;
- change email;
- change a real username;
- remove/replace a real profile photo;
- manufacture profile state in Production;
- manually write Firestore data for evidence.

Stateful/destructive behaviour remains supported by the local/Emulator regression evidence.

## 11. Test Lead visual decision

After the technical release handoff, the Test Lead granted final Production visual approval on 18 September 2026.

The Production visual decision complements, rather than replaces, the six-locale and responsive local/Emulator evidence.

## 12. Defect classification

No C45I Product Defect was identified.

The Vercel Node project-setting/package-engine mismatch is configuration drift because the approved package runtime deterministically overrides the remote setting and the deployed build used Node 22 successfully. It does not receive an `AB-DEF` ID.

No auth/security behaviour was changed merely to make localization tests pass.

## 13. Traceability

```text
Requirement / scope
C45I Profile Edit + embedded account/security presentation localization
        ↓
Implementation
ecc2b735e284a0babfa15b9febcda297ad2846ce
        ↓
Automated validation
29 Profile Edit/photo + 113 focused regression + 12 Playwright
TypeScript/lint/build/diff PASS
realFirebaseRequests=0
        ↓
Architecture/invariant proof
scoped editor provider
stable validation-code presentation
no locale persistence
public Profile remains isolated
auth/deletion/persistence contracts unchanged
        ↓
Release
dpl_EaQtK4iv6FjaKdachTj5UKAinhan — READY / Production
        ↓
Safe Production technical smoke
route/isolation/runtime/log checks PASS
no destructive Production operations
        ↓
Test Lead decision
C45I CLOSED / PRODUCTION TECHNICAL + VISUAL PASS
18 September 2026
```

## 14. Remaining localization scope

C45I does not close the full V1.0 localization programme. Remaining scope includes:

- C45J public Profile localization;
- `resetPassword` action presentation;
- authenticated language selector;
- country/continent presentation mapping without mutating canonical stored values;
- legacy unreachable UK-selector technical cleanup.

User-authored content remains outside automatic translation scope.

## 15. Final Test Lead decision

**PASSED C45I.**

The implementation, Emulator-isolated regression, safe Production technical evidence, exact deployment identity and Test Lead Production visual approval provide sufficient confidence to close the Profile Edit localization checkpoint while carrying profile/auth/security contracts forward as regression risks.
