# AB-EV-051 — C45F Authenticated Dashboard Localization

## Status

**Evidence ID:** AB-EV-051  
**Increment:** C45F — Authenticated App Shell + Dashboard Localization  
**Decision:** CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS  
**Test Lead approval:** 2 September 2026

## 1. Objective

C45F localizes the authenticated `/app` shell and dashboard-level presentation for all six approved V1.0 locales while preserving the map/domain state machine.

Supported locales: `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr`, `en-GB`.

The route remains unprefixed: `/app`.

Locale resolution is valid `atlasbadge_locale` cookie → `Accept-Language` → `pt-BR` fallback. No locale/language field is stored in Firestore or UserProfile.

## 2. Localized C45F surface

C45F covers:

- `/app` server document locale and localized metadata;
- authenticated Header navigation/menu presentation;
- authenticated Footer presentation;
- greeting/loading and four statistics labels;
- map-level clear action and confirmation/error feedback;
- Update Map, search and sort controls;
- dashboard empty/result messages;
- CountryFilterButton/CountryFilterPanel;
- CountryPickerModal shell;
- WishlistModal shell;
- ManualVisitOrderEditor presentation;
- app-level ProfileLoadError presentation;
- UK-selector shell text, although that legacy selector is unreachable in the supported baseline flow.

## 3. Explicitly deferred scope

C45F intentionally does not localize CountryActionCard deep content, VisitEditor, MemoryOrderEditor inside country cards, visit-memory/photo/status editing, ProfileEditModal, Badges, public Profile, resetPassword action presentation or the authenticated language selector.

## 4. Domain and persistence invariants

Localization did not change the 251 directly selectable catalogue, conceptual 252/195/57 geographic rules, country/place IDs, UK constituent IDs, map geography/colors/click semantics, travel statuses, coverage/statistics calculations, filter machine values/predicates, Antarctica rule, Wishlist ordering/privacy, manual visit ranks/identity, fixed birthplace ordering, clear-map atomic semantics, dirty/saving protection, public-profile projection or C44 visit-photo contracts.

Canonical country/domain names remain domain values rather than translated storage.

## 5. Regional-language checks

Representative distinctions validated:

- pt-BR: `Carregando seu Atlas...`, `Salvar Alterações`;
- pt-PT: `A carregar o seu Atlas...`, `Guardar Alterações`;
- es-419: `Borrar mi mapa`, `Editar orden`;
- es-ES: `Limpiar mi mapa`, `Modificar el orden`;
- en-GB: `Visited Places`, `Territories and Entities`, `Visit Order`, `Wishlist`, `Clear my map`, including British `organise` wording.

User-authored values such as display name are not translated.

## 6. Implementation traceability

AtlasBadge release:

`fd1a1ebcfc8a902e6d7ae558c917d5909020ab69`  
`feat(i18n): localize authenticated dashboard`

Release scope: 38 audited files; 1,393 insertions; 181 deletions.

No Firebase Rules/configuration, C44 photo logic, deep CountryActionCard/VisitEditor, ProfileEditModal, public Profile, resetPassword presentation or QA documentation was changed by the product release.

## 7. Local automated validation

Runtime: `Node v22.23.2`

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 22 pre-existing warnings |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 76 / 76 PASS |
| Playwright Edge | 38 / 38 PASS |
| Firebase backend | Auth/Firestore/Storage Emulators |
| Requests to real Firebase | 0 |
| git diff --check | PASS |

Stateful coverage included dashboard localization, Header/Footer, filters, picker, Wishlist persistence, manual visit-order persistence, clear-map protection/destructive flow and C45B–C45E localization/isolation regression.

## 8. Test Lead visual QA

A disposable verified Emulator user with valid profile/public profile/username reservation and representative map data was prepared.

Seed: Brazil, France, England and Japan; multiple statuses; five visits; three visit-order ranks; non-empty Japan Wishlist; two memories (one public); no visit photos.

All six app locales were verified. Test Lead visual review covered desktop plus 390×844 mobile, including Header, greeting/stats, map actions, filters, picker, Wishlist, manual order, clear-map confirmation and Footer.

**Test Lead visual PASS.**

## 9. UK selector reachability observation

Visual preparation found that the requested legacy UK-selector check cannot be reached through the real map UI.

Evidence established:

- dashboard handler opens it only for aggregate `countryId === 'gb'`;
- aggregate `gb` is non-selectable/map-only;
- map clicks emit supported constituent IDs directly;
- clicking England opens England's country card.

C45F did not introduce this condition.

C45F acceptance: **N/A — unreachable in baseline.**

Follow-up classification: **Technical task / unreachable-code cleanup candidate — Not a Product Defect.** No AB-DEF identifier is assigned and it is excluded from Product Defect metrics.

Follow-up action: audit whether any supported path still requires the modal; if none exists, remove the unreachable state/UI in a separate bounded cleanup checkpoint without changing the approved UK geographic model or constituent IDs.

## 10. Production release

**Deployment:** `dpl_EUKfqGR1YksaVzhtkQTCLR5xCETo`  
**Commit:** `fd1a1ebcfc8a902e6d7ae558c917d5909020ab69`  
**Environment:** Vercel Production  
**Status:** READY  
**Runtime:** Node 22.x / Next.js 16.2.11  
**Domain:** `https://atlas-badge.vercel.app`

Production technical smoke confirmed all six `/app` document locales and metadata.

Precedence passed: pt-PT cookie→pt-PT; fr cookie→fr; es-AR→es-419; en-US→en-GB; unsupported de-DE→pt-BR.

Isolation passed: `/badges`, public Profile and resetPassword remain pt-BR.

Anonymous Production `/app` correctly redirects to localized Login. No Production authentication or mutation was manufactured solely for release validation.

## 11. Sign-off model

Because the full dashboard requires authenticated profile/map state, stateful visual coverage remained in Firebase Emulators and Production validation stayed non-destructive/server-focused.

**C45F CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS — 2 September 2026.**
