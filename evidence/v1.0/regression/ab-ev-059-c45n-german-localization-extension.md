# AB-EV-059 — C45N German V1.0 localization extension

**Evidence ID:** AB-EV-059  
**Checkpoint:** C45N — German V1.0 Localization Extension  
**Decision owner:** Test Lead / Product Owner  
**Product repository:** `mhclaudino/atlasbadge`  
**Portfolio repository:** `mhclaudino/qa-test-strategy-portfolio`  
**Baseline:** C45M hotfix `447b568bd70c445c0a98220fcd134fd5bb843259`  
**Feature publication:** `8321975158a389760c0d8d13ae7fb1036b4209c2` — `feat(i18n): add German localization` — 42 files  
**Vercel deployment:** `dpl_8jzWEnP8uhZFzZg443Nn6sAiayjE` — **READY / production / exact SHA**  
**Portfolio C45N publication:** `b565ac8b85bbf07b3ef21a34087dbf1354e1c9bc` — `docs(qa): add C45N German localization evidence`  
**Technical release decision:** **C45N PRODUCTION TECHNICAL PASS — 4 October 2026**  
**Final Test Lead visual decision:** **PENDING — deep authenticated German visual acceptance is not yet explicitly recorded.**

## 1. Requirement and scope change

C45A–C45M correctly established and closed the original six-locale V1.0 localization programme. C45N is a later **additive mandatory V1.0 requirement** that extends that architecture with German (`de-DE`).

C45N is therefore not a retroactive defect in C45A–C45M. Historical evidence that refers to six locales remains correct for the product state at the time it was produced.

The current supported locale set is:

1. `pt-BR` — Português (Brasil) — Brazil
2. `pt-PT` — Português (Portugal) — Portugal
3. `es-419` — Español (Latinoamérica) — Argentina
4. `es-ES` — Español (España) — Spain
5. `fr` — Français — France
6. `de-DE` — Deutsch — Germany
7. `en-GB` — English (UK) — United Kingdom

German uses `/de-de`, the Germany flag (`/flags/locales/de.svg`) and the selector label `Deutsch`. Stable IDs, Firebase data, privacy/auth contracts and user-authored content remain locale-neutral.

## 2. Implementation boundary

C45N extends the existing localization architecture rather than creating a German-specific parallel path. The affected presentation boundary includes:

- locale configuration and resolver;
- message registry and German catalogue;
- localized public Home route `/de-de`;
- locale flag mapping;
- public and authenticated language selectors;
- Home/Header/Footer;
- Login, Onboarding, Verify Email and password-reset action presentation;
- authenticated dashboard;
- country/visit editor;
- Badges and `BadgeUnlockToast`;
- Profile Edit, access-method and deletion presentation;
- public Profile;
- localized geography;
- metadata/document language;
- accessible naming/current-state presentation.

No Firebase schema, Firestore Rules, Storage Rules or persisted locale field was introduced by C45N.

## 3. Catalogue and resolver qualification

### A1 — catalogue integrity

- English keys: **1016**
- German keys: **1016**
- missing keys: **0**
- extra keys: **0**
- empty values: **0**
- placeholder mismatches: **0**
- exact German=English values reviewed as intentional: **123**

### A2 — localization foundation

Focused foundation tests: **50 PASS**

Validated:

- seven-locale canonical order;
- `de-DE → de-DE`;
- generic `de → de-DE`;
- `de-AT` / `de-CH` family → `de-DE` where the existing family resolver applies;
- German message registration;
- `/de-de` route;
- Germany flag mapping;
- Header/public selector mapping;
- integration with the six pre-existing catalogues.

### A3 — direct unit oracles

- targeted test files: **9**
- result: **117 PASS**

## 4. Browser and authenticated regression

### B2R — routing/browser boundary

- **14 / 14 PASS**
- browser: Edge
- Firebase Auth/Firestore/Storage Emulators as required by the wrapper
- protected browser fixture: **`realFirebaseRequests=0`**

Covered German route ownership and `de-CH → /de-de`, while preserving the true unsupported fallback `zh-CN → /pt-br` and existing route isolation.

### C1 / C1A — authenticated German convergence

- **8 / 8 PASS**
- protected browser fixture: **`realFirebaseRequests=0`**

Covered forward/reverse locale switching, current-locale no-op, route/cookie/document convergence, authenticated-session continuity and dirty/saving protection.

### C3A — stale/unit oracle refresh

- targeted test files: **6**
- result: **73 PASS**

German coverage was added to Login, Onboarding, Verify Email, auth/action, Badges metadata and localized geography.

### C3B / C3B-A — auth/dashboard browser evidence

- final source-derived set: **25 / 25 PASS**
- browser isolation evidence recovered from the protected execution: **`realFirebaseRequests=0`**
- correct fallback contract: `de-DE → de-DE`; unsupported `zh-CN → pt-BR`

`realFirebaseRequests=0` is an **Emulator/browser-fixture result only**. It is not used as a Production request-count claim.

## 5. Responsive finding and pre-release correction

Adding the seventh public locale exposed a real pre-release responsive finding at the 768px boundary:

- measured overlap: **14.1875 px**
- affected area: public language selector
- correction: `PublicLanguageSelector` width changed to `h-10 w-8 lg:w-10`

Final focused browser validation:

- **8 / 8 PASS**
- 767px
- 768px
- 390px
- canonical seven-locale order
- German presentation
- no overlap
- protected browser fixture: **`realFirebaseRequests=0`**

Classification: **pre-release implementation finding / correction**. It was corrected before the accepted C45N release and is not recorded as an `AB-DEF` Production Product Defect.

## 6. Final release qualification

Final qualification after the responsive source change used the official runtime:

- Node: **v22.23.2**
- TypeScript: **PASS**
- Next.js production build: **PASS**
- `/de-de` generated: **YES**
- existing locale routes preserved: **YES**
- `git diff --check`: **PASS**

C5A then published one Product commit:

- SHA: `8321975158a389760c0d8d13ae7fb1036b4209c2`
- parent: `447b568bd70c445c0a98220fcd134fd5bb843259`
- message: `feat(i18n): add German localization`
- files: **42**
- push: normal fast-forward to `origin/main`
- force push: **NO**

Four previously known raw-status/index phantom files were later rechecked and their filtered working-tree hashes matched `origin/main`; they were not semantic Product changes.

## 7. Exact-SHA Production deployment

The released Product commit was independently matched to Vercel:

- deployment ID: `dpl_8jzWEnP8uhZFzZg443Nn6sAiayjE`
- state: **READY**
- target: **production**
- branch: `main`
- Git SHA: `8321975158a389760c0d8d13ae7fb1036b4209c2`
- commit message: `feat(i18n): add German localization`
- canonical alias: `https://atlas-badge.vercel.app`

A scoped runtime-log review found **no relevant error** for the checked deployment/window.

## 8. Production technical smoke

Production validation was deliberately non-destructive.

### German public Home

- `/de-de`: **HTTP 200**
- `<html lang>`: **`de-DE`**
- German metadata/title: **PASS**
- German heading/CTA: **PASS**

### Germany flag asset

- `/flags/locales/de.svg`: **HTTP 200**
- `Content-Type`: **`image/svg+xml`**

### Locale routing and route isolation

- root + `Accept-Language: de-DE`: **307 → `/de-de`**
- root + unsupported `Accept-Language: zh-CN`: **307 → `/pt-br`**
- `/de-de/app`: **404**

### German Login

`/login?locale=de-DE`:

- HTTP: **200**
- `<html lang>`: **`de-DE`**
- title: **`AtlasBadge | Anmelden`**
- heading: **`Bei AtlasBadge anmelden`**
- password label: **`Passwort`**
- public-Home language selector absent: **YES**

### Auth guard

- unauthenticated `/app` → `/login`: **PASS**

### Responsive technical Production smoke

Viewport `390×844`:

- horizontal overflow: **false**
- German heading visible: **YES**
- German selector visible/usable: **YES**

### Final rendered seven-locale selector

Observed at `1440×900` on `/de-de`:

| Position | Locale | Route | Flag | Current |
|---:|---|---|---|---|
| 1 | `pt-BR` | `/pt-br` | `br.svg` | No |
| 2 | `pt-PT` | `/pt-pt` | `pt.svg` | No |
| 3 | `es-419` | `/es-419` | `ar.svg` | No |
| 4 | `es-ES` | `/es-es` | `es.svg` | No |
| 5 | `fr` | `/fr` | `fr.svg` | No |
| 6 | `de-DE` | `/de-de` | `de.svg` | **Yes — `aria-current="page"`** |
| 7 | `en-GB` | `/en-gb` | `gb.svg` | No |

Assertions:

- count = 7: **PASS**
- canonical order: **PASS**
- `de-DE` position 6: **PASS**
- `de-DE` current: **PASS**
- `en-GB` last: **PASS**
- flags: **PASS**
- duplicates: **NO**
- missing locales: **NO**

### Production safety

- Production authentication performed: **NO**
- Production write/mutation performed: **NO**
- Production account created: **NO**
- Production Firebase Admin used: **NO**

Authenticated German convergence was **not re-executed in Production**. It is carried forward from the protected C1/C1A Emulator evidence.

The original Production smoke did not explicitly register browser `pageerror`/console-error listeners, so this record does **not** claim an automated browser-console-error-free Production run. The available Vercel runtime-log review found no relevant error.

## 9. Requirement → risk → implementation → verification traceability

| Requirement / risk | Implementation boundary | Verification | Result | Release relevance |
|---|---|---|---|---|
| Add German as mandatory V1.0 locale | config, resolver, catalogue, `/de-de`, selectors | A1/A2/A3/B2R | PASS | Establishes seven-locale contract |
| QR-39 responsive/layout regression | seven-locale public selector | B3/B3F + 390×844 Production smoke | PASS after pre-release correction | 768px overlap corrected before release |
| QR-40 accessible naming/current state | public/authenticated selectors and localized presentation | unit/E2E + Production `aria-current` observation | PASS | de-DE current state proven |
| Locale-switch data-loss risk | authenticated selector + dirty/saving editor coordination | C1/C1A | PASS | No silent discard in protected flows |
| Existing localization regressions | auth/dashboard/geography/catalogue oracles | C3A/C3B/C3B-A | PASS | Prior six-locale architecture remains protected |
| Release integrity | final build + one controlled Product commit | C4/C5A | PASS | Exact release SHA established |
| Deployment integrity | Vercel exact-SHA identity | C5B evidence reconciliation | PASS | READY / production / exact SHA |
| Public Production presentation | German Home, Login, routing, selector, responsive smoke | C5B/C5B-A/C5B-B | PASS | Production technical closure |

## 10. Residual risk and decision boundary

The following remain explicit rather than being silently converted into PASS:

- broader browser/device coverage remains non-exhaustive;
- formal native assistive-technology certification is not claimed;
- user-authored content remains intentionally untranslated;
- authenticated German locale convergence is proven in Emulator evidence and was not re-executed against Production;
- the Production smoke proves public/auth-entry/responsive technical behavior, not a complete manual visual tour of every deep authenticated German surface;
- a final Test Lead visual acceptance of representative deep German authenticated surfaces is not yet explicitly recorded in the project history available for this evidence record.

**Current decision:** **C45N PRODUCTION TECHNICAL PASS.**  
**Final C45N Test Lead visual acceptance:** **PENDING explicit sign-off.**

Historical C45A–C45M evidence remains unchanged and retains the six-locale scope that was true when those records were produced.

## 11. Portfolio publication control

The C45N public evidence package was published to the QA Portfolio before the final reconciliation audit recorded here.

Verified publication identity:

- Portfolio commit: `b565ac8b85bbf07b3ef21a34087dbf1354e1c9bc`
- parent: `75b2fa69929d84ea8edeca30bbd8bc947bb1ac17`
- tree: `a48fc393c8729b6d0c35bc0a224e238c79f2b8fe`
- actual commit message: `docs(qa): add C45N German localization evidence`
- topology: one fast-forward commit from the previous Portfolio `main`
- published C45N documentation scope: **12 files**

The 12-file publication contains:

1. `README.md`
2. `docs/01-product-overview.md`
3. `docs/02-quality-risk-analysis.md`
4. `docs/03-test-strategy.md`
5. `docs/04-test-scope.md`
6. `docs/05-entry-exit-criteria.md`
7. `docs/08-metrics-and-reporting.md`
8. `docs/09-system-test-plan.md`
9. `evidence/v1.0/README.md`
10. `evidence/v1.0/evidence-register.md`
11. `evidence/v1.0/regression/README.md`
12. `evidence/v1.0/regression/ab-ev-059-c45n-german-localization-extension.md`

Publication reconciliation confirmed:

- `docs/06-test-environments.md`, `docs/07-defect-management.md` and `docs/10-lessons-learned.md` were not modified;
- historical AB-EV-045 through AB-EV-058 evidence files were not modified;
- no sentinel or scratch artefact was present in the publication diff;
- AB-EV-059 appears in the Evidence Register and QR-39/QR-40 remain `Regression risk`;
- the System Test Plan preserves C45M Test Lead approval on 2 October 2026 and C45N Production Technical PASS on 4 October 2026.

The original ref update had already occurred before this reconciliation audit began. The resulting Git topology proves a direct fast-forward from the previous `main`, but this record does **not** retroactively claim that the original ref move was observed using an `expected_sha` lease.

This publication-control clarification changes no Product result and does not convert the pending representative deep authenticated German visual Test Lead gate into an approval.