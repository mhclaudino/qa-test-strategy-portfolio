# AB-EV-055 — C45J Public Profile localization and post-release visual corrections

## Evidence summary

**Evidence ID:** AB-EV-055  
**Checkpoint:** C45J + C45J-FIX1 + C45J-FIX2  
**Subject:** Six-locale public Profile presentation, authenticated Header locale regression and loading layout stability  
**Product commits:** `2f3a640e7a1ba44c065d8ad876d23f0213878175`, `84b26512a9e3bc44b2bc68a718b54caf2bbd1eec`, `309096bcf8bd85c7528592d03c00e5de41729889`  
**Vercel Production deployments:** `dpl_59LUTwNK1AzikFk9yQyVNhqBZgKw`, `dpl_56Fzo29QSxT51j3tTAjkDxxMjjHn`, `dpl_Ht2fTLy3xmcvhfz4291RVKvY9j4X` — all `READY` and mapped to the respective exact commit  
**Final Test Lead decision:** **C45J + FIX1 + FIX2 CLOSED / PRODUCTION TECHNICAL + VISUAL PASS**  
**Production visual decisions:** Header/profile review and authenticated Header accepted on 19 September and FIX2 accepted on 20 September 2026.

## 1. Requirement and scope

Localize the canonical, unprefixed `/@username` public Profile for `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr` and `en-GB`. The Profile must retain its owner/anonymous/viewer privacy model, public-source projection, read-only presentation and C32/C33/C39/C40/C41/C42/C44/C45H/C45I invariants.

The checkpoint covers Profile loading/not-found/private/empty states; contextual Header/Footer; statistics, map labels, continent-section *chrome*, earned flags/achievements, public memories, public Wishlist, ShareCard, owner Edit Profile and accessible/responsive presentation. User-authored display name, biography, custom `visitLabel`, memory notes and persisted visit dates are not translated. Geographic country and continent canonical names remain deliberately deferred.

## 2. Implementation and route/locale boundary

**C45J implementation commit:** [`2f3a640`](https://github.com/mhclaudino/atlasbadge/commit/2f3a640e7a1ba44c065d8ad876d23f0213878175) — `feat(i18n): localize public profiles` (26 files).

- The proxy recognizes the canonical `/@username` root route without claiming prefixed Profile paths; locale resolution uses approved saved-cookie/browser fallback, forwarding `x-atlasbadge-document-locale`.
- A dedicated `src/app/[username]/layout.tsx` supplies server locale, `DocumentLocale`, `AppIntlProvider` and generic localized metadata without a second Profile fetch. Root layout supplies server-correct `<html lang>`.
- Anonymous Profile language selection persists `atlasbadge_locale` and makes a full request at the **same** `/@username` path. It does not redirect to Home. Authenticated Profile exposes no language selector.
- Localized copy comes from six `publicProfile` catalogs; achievement title/description presentation reuses existing C45H stable IDs and `badges.achievements` messages, without changing evaluators or earned chronology.
- Shared map presentation receives additive translated labels without translating/storing geographical IDs. `ProfileEditModal` receives the active Profile locale; the C45I scoped provider remains intact.

Preserved boundaries: `/@username` unprefixed, `/fr/@username` and `/pt-br/@username` not claimed; authenticated `/app` and `/badges` unprefixed; no language field in UserProfile/Firestore/public projection, no changed Rules, no viewer private-source fallback, no change to Wishlist order or photo/public-memory publication rules.

## 3. Local QA and release evidence

The C45J agent's final reported focused regression included **264/264 Vitest PASS across 39 files**, followed by a proportionate **20/20 PASS** run over five affected files including the new ShareCard component test. The C45J Emulator-backed Playwright suite ultimately reported **3/3 PASS**, including desktop/anonymous, authenticated/privacy and explicit `390×844` mobile coverage for `fr`, `pt-PT`, `es-ES` and `en-GB`. The six Profile locales and selected cookie/Accept-Language and route-ownership cases were covered across browser/proxy tests. Playwright's real-Firebase interception reported **`realFirebaseRequests=0`**. TypeScript, lint (21 known non-blocking warnings, zero errors), build and diff check passed in the final release gates.

The release process did **not** accept earlier agent claims of PASS when an E2E logout timed out, when an Auth Emulator was unavailable (`ECONNREFUSED 127.0.0.1:9099`), or when mobile and ShareCard coverage were missing. The desktop E2E selector and authenticated logout harness were corrected; existing tests retained their assertions, and missing mobile and ShareCard evidence was added. Broad JSON reformatting was removed so the pre-existing C45H achievement catalog was preserved. These were validation/harness and review findings, not additional demonstrated Product defects.

**C45J deployment:** [`dpl_59LUTwNK1AzikFk9yQyVNhqBZgKw`](https://vercel.com/m-hc/atlas-badge/59LUTwNK1AzikFk9yQyVNhqBZgKw), Production `READY`, exact C45J commit. Independent Vercel verification identified the deployment and the canonical Profile responded HTTP 200 with pt-BR server HTML/document metadata in a non-destructive request. Production account/memory/Firestore state was not manufactured to reproduce local fixtures.

## 4. AB-DEF-019 — Authenticated public Profile Header locale regression (C45J-FIX1)

**Discovery:** Test Lead Production screenshots on 19 September 2026. With `en-GB` selected, authenticated `/app` and `/badges` showed **Map / Badges / Profile**, but the authenticated `/@username` Header reverted to **Mapa / Badges / Perfil** while the Profile body stayed English.

**Root cause:** The `publicProfileLocale` branch of `Header.tsx` rendered `HeaderContent` without the `header.authenticated` translated labels; the component used its Portuguese fallback for navigation/avatar/mobile/accessible names. This is a **Product Defect**, not a cookie or page-translation failure.

**Correction:** [`84b2651`](https://github.com/mhclaudino/atlasbadge/commit/84b26512a9e3bc44b2bc68a718b54caf2bbd1eec) — `fix(i18n): localize authenticated public profile header`; only `Header.tsx` and C45J Playwright E2E changed. The authenticated Profile path reuses `header.authenticated` labels, keeps `modalLocale=publicProfileLocale`, hides the authenticated selector and preserves anonymous selection.

**Retest:** **4/4 Emulator Playwright PASS**, `realFirebaseRequests=0`; authenticated en-GB navigation in `/app`, `/badges`, `/@username`; localized avatar menu, anonymous selector and routing retained. A prior `ECONNREFUSED` run was correctly classified as **environment-blocked**, not PASS; final run used the complete Auth/Firestore/Storage Emulator command. TypeScript/lint/build/diff checks passed as reported.

**Deployment:** [`dpl_56Fzo29QSxT51j3tTAjkDxxMjjHn`](https://vercel.com/m-hc/atlas-badge/56Fzo29QSxT51j3tTAjkDxxMjjHn) — Production `READY`, exact FIX1 SHA. Test Lead's subsequent screenshots confirmed **Map / Badges / Profile** on all three authenticated routes and **Edit profile** in English on the Profile. **AB-DEF-019 closed by Production visual retest.**

## 5. AB-DEF-020 — Header horizontal shift during public Profile loading (C45J-FIX2)

**Discovery:** During Test Lead Production navigation to `/@username`, the Header moved slightly while the short **Searching map...** loading state transitioned to the long completed Profile. This is a **Product visual defect**, separate from Header language.

**Root-cause assessment:** The loading view may not require a classic vertical scrollbar, while the populated Profile does. In browsers where the scrollbar consumes layout width, this can change `document.documentElement.clientWidth` and shift centred Header elements. **Limitation:** automated headless/overlay-scrollbar runs did not directly reproduce the native Windows pixel displacement; do not misrepresent the hypothesis as a headless measured before/after proof.

**Correction:** [`309096b`](https://github.com/mhclaudino/atlasbadge/commit/309096bcf8bd85c7528592d03c00e5de41729889) — `fix(ui): prevent public profile loading layout shift`; only `src/app/globals.css` changed. `scrollbar-gutter: stable` is applied to root `html` only. No fake padding, hidden overflow, altered Header geometry or auth/data changes.

**Retest:** Existing **4/4 C45J Playwright PASS** on the Auth/Firestore/Storage Emulators, `realFirebaseRequests=0`; TypeScript/lint/build/diff PASS. Playwright demonstrates no functional regression, **not** native-scrollbar pixel stability. The Test Lead approved FIX2 visually in the real Production browser on 20 September 2026, providing the missing acceptance evidence.

**Deployment:** [`dpl_Ht2fTLy3xmcvhfz4291RVKvY9j4X`](https://vercel.com/m-hc/atlas-badge/Ht2fTLy3xmcvhfz4291RVKvY9j4X) — Production `READY`, exact FIX2 SHA. **AB-DEF-020 closed by Test Lead Production visual acceptance.**

## 6. Traceability and release decision

| Requirement / finding | Implementation | Verification | Production | Decision |
|---|---|---|---|---|
| C45J canonical public Profile six-locale presentation | `2f3a640` | 264/264 broad focused Vitest; 20/20 final affected; 3/3 Playwright; no real Firebase requests | `dpl_59LUTwNK1AzikFk9yQyVNhqBZgKw` READY | Accepted; follow-up defects tracked |
| AB-DEF-019 authenticated Profile labels | `84b2651` | 4/4 Playwright + targeted gates | `dpl_56Fzo29QSxT51j3tTAjkDxxMjjHn` READY | CLOSED; Test Lead visual PASS |
| AB-DEF-020 loading layout shift | `309096b` | 4/4 regression + targeted gates; native pixel verification remains manual | `dpl_Ht2fTLy3xmcvhfz4291RVKvY9j4X` READY | CLOSED; Test Lead visual PASS |

**Overall:** **C45J + FIX1 + FIX2 CLOSED — Production technical and Test Lead visual PASS.** No Firestore Rules, private/public projection schemas, canonical geographical data or persistent locale fields changed. The final deployed code is the FIX2 commit. No independent accessibility certification or comprehensive browser/device coverage is implied by the focused checks.

## 7. Explicitly deferred — not an open C45J defect

- Country/continent *display-name* mapping across six locales: presentation only; preserve canonical stored values, IDs, statistics, achievements and user-authored text; a **new, separately gated checkpoint** is required before implementation.
- `resetPassword` action presentation and the future authenticated language selector.
- Legacy unreachable UK-selector cleanup, broader compatibility and quantified performance work.

Do not describe geographical translation as part of C45J closure or silently start it under this evidence ID.
