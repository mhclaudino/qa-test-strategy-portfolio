# AtlasBadge V1.0 Evidence Register

**Register status:** In progress  
**Scope:** Selected, sanitised public evidence for AtlasBadge V1.0  
**Owner:** Test Lead/Product Owner

## 1. Purpose

This register provides a lightweight index of selected public evidence produced during AtlasBadge V1.0 quality validation.

It is not an Application Lifecycle Management system, a complete defect backlog or a substitute for the operational source of truth. Its purpose is to connect material quality risks, defects, investigations and Test Lead decisions to public portfolio artefacts without exposing private implementation or user data.

## 2. Evidence records

| Evidence ID | Subject | Related risk or defect | Environment | Build or commit | Evidence status | Test Lead decision | Public artefact |
|---|---|---|---|---|---|---|---|
| AB-EV-001 | Silent country-status persistence failure | AB-DEF-001; QR-01 | Local development, Firebase Rules validation and Vercel Production | Correction commit not supplied in public summary; production-approved deployment | Public narrative complete; selected supporting artefacts pending sanitisation | Corrected behaviour approved after retest, regression, controlled deployment and production smoke | [Status persistence failure](defects/status-persistence-failure.md) |
| AB-EV-002 | QR-02 visit-history preservation | QR-02 | Local development and Vercel Production | `f6cc331`, `322d15b`, `482fd51`, `771bf6c` | Public narrative complete | Current V1.0 behaviour approved; no production functional correction required | [QR-02 visit-history preservation](regression/qr-02-visit-history-preservation.md) |
| AB-EV-003 | QR-03 private cache after explicit logout | QR-03 | Local InPrivate browser validation and Vercel Production | `d8179cb` | Public narrative and sanitised screenshots complete | Correction approved after local QA and production smoke | [QR-03 private cache after logout](defects/qr-03-private-cache-after-logout.md) |
| AB-EV-004 | QR-05 legacy memory editor persisted text on every keystroke | QR-05 | Local development and Vercel Production | `e73fad9` | Production approved | Approved for AtlasBadge V1.0 | [QR-05 legacy memory keystroke persistence](defects/qr-05-legacy-memory-keystroke-persistence.md) |
| AB-EV-005 | Account linking identity preservation | QR-09 | Production | `f209c14` | Public narrative and sanitised screenshots complete | Approved by Test Lead — Mitigated | [QR-09 account linking identity preservation](qr-09/qr-09-account-linking-identity-preservation.md) |
| AB-EV-006 | Production frontend and Firestore Rules deployment parity failure | AB-DEF-002; QR-01; release integrity | Firebase Emulator, Vercel Production and Firestore Production | Frontend `f209c14`; Rules history `72e7672`, `fa47b0b` | Public narrative complete | Defect closed after controlled Rules deployment and focused Production retest | [Production deployment parity failure](defects/production-deployment-parity-failure.md) |
| AB-EV-007 | QR-10 wrong identity during access-method linking and Google photo synchronisation | QR-10 | Firebase Emulators, Edge and Vercel Production | `b3841e4`, `004f8ee`, `a52af4f` | Public narrative/screenshots complete | Approved by Test Lead — Mitigated | [QR-10 wrong identity during access-method linking](defects/qr-10-wrong-identity-account-linking.md) |
| AB-EV-008 | QR-01 map persistence mitigation and affected-area dashboard regression | QR-01; QR-18; QR-19; QR-25; QR-39 | Local, Firebase Emulators, Edge, production build | `0816e0e`, `c363b04`, `234bc0a`, `5140641`, `8951cf1`, `83f5650` | Historical public narrative complete | Scoped path approved; at this historical point QR-01 remained Current gap | [QR-01 map persistence and dashboard regression](regression/qr-01-map-persistence-and-dashboard-regression.md) |
| AB-EV-009 | Final dashboard, filtering and controlled persistence Production smoke | QR-01; QR-18; QR-19; QR-25; QR-39 | Vercel Production, Edge/Windows | Git reference `83f5650` | Public textual record complete | Production path Passed; QR-01 still had broader residual scope at that time | [Final dashboard and persistence Production smoke](smoke/ab-ev-009-final-dashboard-and-persistence-production-smoke.md) |
| AB-EV-010 | QR-07 retry-safe account deletion | QR-07 | Firebase Emulators and Vercel Production | `7150cb2141a86bff659cf417ac1ba5f4e9dd1385` | Public narrative/screenshots complete | Closed and approved; retained as Regression risk | [QR-07 retry-safe account deletion](defects/qr-07-account-deletion-retry-safe.md) |
| AB-EV-011 | QR-06 character-limit closure and regression decision | QR-06 | Local validation and Vercel Production | Product commit retained privately | Public closure narrative complete | QR-06 moved to Regression risk | [QR-06 character-limit closure](regression/qr-06-character-limits-closure.md) |
| AB-EV-012 | QR-08 password-policy closure and permanent automated coverage | QR-08 | Local audit, Vitest, lint, build, Firebase policy confirmation | `d5934ab80301685659c6a870e323288701803959` | Public textual record complete | Approved; QR-08 moved to Regression risk | [QR-08 password-policy closure](regression/qr-08-password-policy-closure.md) |
| AB-EV-013 | QR-04 real-time synchronisation, OCC and two-tab Production validation | QR-04; QR-01; QR-18; QR-19 | Automated regression, build and Production two-tab smoke | Runtime `34b88f3`; tests `c200132` | Public textual record complete | Approved; QR-04 moved to Regression risk | [QR-04 synchronisation and concurrency Production validation](smoke/ab-ev-013-qr-04-real-time-sync-production-smoke.md) |
| AB-EV-014 | QR-11 case-insensitive username normalisation and Production validation | QR-11; QR-12; QR-13 | Automated regression, clean npm reproduction, build and Production smoke | `b7718892a184fbda80df86ab82a1c768347dcba5`; `d648a84` | Public textual record complete | Approved; QR-11 moved to Regression risk | [QR-11 username case-normalisation Production validation](smoke/ab-ev-014-qr-11-username-case-normalisation-production-smoke.md) |
| AB-EV-015 | QR-13 immediate username reuse and containment audit | QR-13; QR-07; QR-11; QR-12 | Audit, focused/full tests, lint and build | `751e3f6` | Public textual record complete | Approved; QR-13 remains Accepted behaviour | [QR-13 immediate username reuse](regression/qr-13-immediate-username-reuse-accepted-behaviour.md) |
| AB-EV-016 | QR-24 Passed through detailed-visit workflow closure | QR-24; QR-01; QR-02; QR-04; QR-05; QR-18; QR-19; QR-20; QR-22; QR-23 | Static audit, 18 focused Vitest, full regression, lint/build | `cf5382d` | Public textual record complete | Approved under the then-current rule; later compatibility rule corrected by C35/AB-EV-035 | [QR-24 Passed through detailed-visit workflow](regression/qr-24-passed-through-detailed-visit-workflow.md) |
| AB-EV-017 | QR-40 accessibility technical baseline | QR-40; QR-24; QR-31; QR-33; QR-34; QR-39 | Emulators, Vitest, Playwright/Axe, Edge, Chrome Android, Production | `8a18615`, `a7eec48`, `918875d`, `c064b11`, `62f0d59`; through `eca539e` | Public textual record complete | Technical baseline approved; QR-40 Regression risk | [QR-40 accessibility technical baseline](regression/qr-40-accessibility-technical-baseline.md) |
| AB-EV-018 | QR-39 responsive, touch and constrained-device baseline | QR-39; QR-04; QR-01; QR-24; QR-40; AB-DEF-003; AB-DEF-004 | Builds, Emulators, Playwright, Edge, Chrome Android, Production | final `eca539ea793a2aadc4be657f0b9dd549f1f04699` | Public narrative/screenshots complete | Android CSS and rapid-status defects closed | [QR-39 responsive and constrained-device baseline](regression/qr-39-responsive-touch-constrained-device-baseline.md) |
| AB-EV-019 | Status persistence and optimistic-concurrency regression closure | AB-DEF-005; QR-01; QR-04; QR-16; QR-18; QR-19; QR-22 | Emulators, Edge/Chromium and Production | Runtime `ff8d13ab923c988cf8f7d459681e9e251f34cf17`; final `982091c` | Public narrative/screenshots complete | AB-DEF-005 closed | [Status persistence and OCC regression closure](regression/ab-ev-019-status-persistence-and-occ-regression.md) |
| AB-EV-020 | Responsive navigation, layout density and country-card paint stability | AB-DEF-006; QR-39; QR-40 | Playwright responsive regression and Production | `67dae2b306ff045f38fab68a0b57a5019948935d`; final `982091c` | Public narrative/screenshot complete | AB-DEF-006 closed | [Responsive navigation and card-paint stability](regression/ab-ev-020-responsive-navigation-and-card-painting-stability.md) |
| AB-EV-021 | V1.0 release hardening and integrated regression | QR-01; QR-04; QR-16; QR-18; QR-19; QR-25; QR-29; QR-30; QR-39; QR-40 | Emulators, Playwright, build, Rules, release checks | baseline `f6004a9`; C1-C8 | Public textual record complete | 258 Vitest, 211/211 Rules and 119/119 E2E Passed | [V1.0 release hardening and integrated regression](regression/ab-ev-021-v1-release-hardening-and-integrated-regression.md) |
| AB-EV-022 | Rapid status last-intent and activation chronology | AB-DEF-007; QR-01; QR-04; QR-16; QR-18; QR-19; QR-22 | Emulators, Playwright concurrency regression, Production parity | `3ee18c8` | Public textual record complete | AB-DEF-007 closed; QR-04 Regression risk | [Rapid status last-intent and activation chronology](regression/ab-ev-022-status-last-intent-and-activation-chronology.md) |
| AB-EV-023 | Achievement chronology, World Completion and notification reliability | AB-DEF-008/009/010; QR-18; QR-25; QR-29; QR-30; QR-39 | Audit, Emulators, Rules, Playwright, Production | `f140c48`, `44ac35a`, `95a53e1`, `f6004a9`, `047974a`, `8887d2b` | Public textual record complete | Achievement defects closed/approved | [Achievement chronology and notification reliability](regression/ab-ev-023-achievement-chronology-world-completion-and-notification-reliability.md) |
| AB-EV-024 | Production release validation and harness hardening | QR-01; QR-04; QR-15; QR-16; QR-18; QR-19; QR-22; QR-29; QR-30; QR-39; QR-40 | Vercel/Firebase Production via dedicated runner | runtime `8887d2b`; harness `90035f72`, `1b040c29` | Public textual record complete | Production validation Passed | [Production release validation and harness hardening](smoke/ab-ev-024-production-release-validation-and-harness-hardening.md) |
| AB-EV-025 | Badge Unlock surface consistency and visual polish | QR-30; QR-39; QR-40 | Emulator regression, Production, desktop/mobile review | C18 `d94c84b`; C19 `6bc091e`; C20 `86bb2a0`; `dpl_66z8qZ6T5YS8JJBRAzMBFkuG1JiV` | Public textual record complete | C20 approved | [Badge Unlock surface consistency](regression/ab-ev-025-badge-unlock-surface-and-visual-polish.md) |
| AB-EV-026 | Clear Map achievement metadata reconciliation race | AB-DEF-011; QR-04; QR-30; scoped QR-01 | Emulators, regression and Production | `84b49376`, `5a45edde`, `56742f35` | Public narrative complete | Race protection/qualification approved | [Clear Map reconciliation race](defects/ab-ev-026-clear-map-achievement-reconciliation-race.md) |
| AB-EV-027 | UK coverage rule and counter-integrity observation | QR-18; QR-25; QR-29 | Local regression and Production | `71fc58df`; `754fd9be`; `a8d1d3ec` | Historical trigger narrative complete | Triggered C31 audit later closed by AB-EV-029 | [UK coverage and counter integrity](regression/ab-ev-027-uk-coverage-and-geographic-counter-integrity.md) |
| AB-EV-028 | Canonical map reuse/read-only Profile parity | QR-26; QR-33; QR-39; QR-25 | Playwright/Vitest/build, Test Lead visual QA, Production | C27-C30 final `a8d1d3ec`; `dpl_J6c7iPBrH9FRy5uAMQJAVHbeguey` | Public narrative complete | Production approved | [Map and profile parity](smoke/ab-ev-028-map-profile-parity-production-validation.md) |
| AB-EV-029 | Geographic counter integrity: 252 / 195 / 57 | AB-DEF-012; QR-18; QR-25; QR-29; QR-30 | Unit/regression, Test Lead local QA, Production | `627f4f948f7b6af0151fec77ffe800380e73e989` | Public narrative complete | AB-DEF-012 closed; QR-25 Regression risk | [Geographic counter integrity](defects/ab-ev-029-geographic-counter-integrity-252-195-57.md) |
| AB-EV-030 | Profile map to earned-flag auto-navigation | QR-26; QR-33; QR-39; QR-40 | Emulators, Playwright, Test Lead QA, Production | `c75891b68836ca495e319c31878c3cb230f4f92e` | Public narrative complete | C32 Production PASS | [Profile map to earned-flag navigation](smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md) |
| AB-EV-031 | Dashboard selected-place sorting | QR-18; QR-19; QR-39; QR-40 | Emulators, Playwright, local gates, Test Lead QA, Production | final `0164b8c`; `dpl_4a94LAa8p1D57y3cHpzNHRGATQMq` | Public narrative complete | C33 Production PASS | [Dashboard selected-place sorting](regression/ab-ev-031-dashboard-selected-place-sorting.md) |
| AB-EV-032 | Manual visit order, rapid-visit concurrency and birthplace integrity | AB-DEF-013; QR-01; QR-04; QR-18; QR-19; QR-22; QR-23; QR-33; QR-39; QR-40 | Emulators, Rules, local gates, Firebase/Vercel Production | final `8474a78`; `dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi` | Public narrative complete | AB-DEF-013 closed; C34 Production PASS | [Manual visit order and concurrency](defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md) |
| AB-EV-033 | Wishlist, public-profile projection and release hardening | AB-DEF-014/015/016; QR-01; QR-04; QR-07; QR-17; QR-20; QR-30; QR-31; QR-32; QR-33; QR-34; QR-39 | Emulators, isolated Playwright, Rules, Vercel/Firebase Production | `276b0c9`; final `7bbdb9402145523f6a2f36d41cc74e55479cc664`; `dpl_HEKQuz6MAXiW413m6cqnH25zWrRg` | Public narrative complete | Wishlist/public projection approved; three product defects closed | [Wishlist/public-profile hardening](regression/ab-ev-033-wishlist-public-profile-release-hardening.md) |
| AB-EV-034 | QR-01 failed-write recovery closure | QR-01 | Static/write-path audit, component Vitest and repository quality gates | `66cffbc933710f2b9f4ba007c5726ebc2857ac82` | Public textual record complete | QR-01 coverage gap closed; `Current gap` → `Regression risk`; no Product Defect | [QR-01 failed-write recovery closure](regression/ab-ev-034-qr-01-failed-write-recovery-closure.md) |
| AB-EV-035 | C35 Visited + Passed-through coexistence | QR-16; QR-18; QR-19; QR-21; QR-22; QR-23; QR-24 | Domain tests, Firebase Emulator E2E, Test Lead manual QA and Vercel deployment verification | `29c7ac63748fb823899fb77cdb6ee91bb6194b1f`; `dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv` READY | Public textual record complete | Requirement correction approved; no Product Defect; Visited + Passed through becomes supported cumulative combination | [C35 Visited + Passed-through coexistence](regression/ab-ev-035-c35-visited-passed-coexistence.md) |

## 3. Evidence-status interpretation

- **Public narrative complete** means the verified quality story and decision can be published safely.
- **Production approved** means the applicable technical validation, release evidence and Test Lead decision support that claim for that record.
- **In progress** means investigation or validation is incomplete. It must not be interpreted as Passed, Failed or approved.
- A missing public attachment does not invalidate a decision when operational evidence was reviewed privately, but the public record must state the limitation clearly.
- A missing deployment identifier is reported as unavailable rather than reconstructed or invented.
- Historical evidence retains the risk state/decision that was true at the time; later evidence may supersede the current risk state without rewriting history.

## 4. Quality Risk decisions applied

| Quality Risk | Applied state | Evidence position |
|---|---|---|
| QR-01 | `Regression risk` | AB-EV-008/009/019/026/032/033 progressively protected persistence paths. AB-EV-034 completed the write-path audit and added the remaining `flagSortOrder` failed-write/recovery evidence, closing the Current gap without inventing a Product Defect. |
| QR-02 | `Regression risk` | Visit-history preservation is protected by AB-EV-002 and later visit-history/concurrency evidence. |
| QR-03 | `Regression risk` | Explicit logout/cache correction recorded in AB-EV-003. |
| QR-04 | `Regression risk` | AB-EV-013/018/019/022/026/032/033 protect reconciliation, OCC, rapid mutation and public/private persistence behaviour. |
| QR-05 | `Regression risk` | Explicit-Save memory contract recorded in AB-EV-004. |
| QR-06 | `Regression risk` | Boundary/character-limit closure recorded in AB-EV-011. |
| QR-07 | `Regression risk` | Retry-safe deletion AB-EV-010; public/Wishlist cleanup extended by AB-EV-033. |
| QR-08 | `Regression risk` | Password policy closure AB-EV-012. |
| QR-09 | `Mitigated` | Identity-preserving linking AB-EV-005. |
| QR-10 | `Mitigated` | Wrong-identity linking closure AB-EV-007. |
| QR-11 | `Regression risk` | Username normalisation/resolution AB-EV-014. |
| QR-13 | `Accepted behaviour` | Immediate username reuse AB-EV-015. |
| QR-16 | `Regression risk` | AB-EV-035 rebaselines the compatibility contract: Visited + Passed through is valid; domain + Emulator browser coverage protects the new rule. |
| QR-17 | `Regression risk` | AB-EV-033 protects Wishlist compatibility/non-physical semantics. |
| QR-18 | `Regression risk` | AB-EV-029 protects canonical totals; AB-EV-031/032/033 protect ordering/visit/Wishlist independence; AB-EV-035 confirms status coexistence does not manufacture visit-count changes. |
| QR-19 | `Regression risk` | Total Visits parity protected across AB-EV-009/013/019/031/032/033 and C35 no-artificial-visit coverage in AB-EV-035. |
| QR-20 | `Regression risk` | AB-EV-016/033 protect non-physical status semantics. |
| QR-21 | `Regression risk` | Multiple compatible status flags count a place once; C35 explicitly exercises this model without creating a second occurrence. |
| QR-22 | `Regression risk` | Memory/history preservation is protected by AB-EV-002/032 and C35 bidirectional history-preservation tests in AB-EV-035. |
| QR-23 | `Regression risk` | Born/Lived continue to imply Visited; C35 allows Passed through to coexist without removing that dependency. |
| QR-24 | `Regression risk` | AB-EV-016 protects the detailed Passed-through workflow; AB-EV-035 supersedes only the old mutual-exclusion interpretation and protects the corrected cumulative rule. |
| QR-25 | `Regression risk` | AB-EV-029 closes the geographic 252/195/57 integrity gap. |
| QR-26 | `Regression risk` | AB-EV-028/030 protect map/Profile interaction. |
| QR-29 | `Regression risk` | AB-EV-023/027/029 protect UK and World Completion semantics. |
| QR-30 | `Regression risk` | AB-EV-023/024/026/029/033 protect achievement chronology/reconciliation/public metadata. |
| QR-31 | `Regression risk` | AB-EV-033 protects private/public Profile and Wishlist privacy boundaries. |
| QR-32 | `Regression risk` | AB-EV-033 protects public-place sanitisation and private-memory exclusion. |
| QR-33 | `Regression risk` | AB-EV-028/030/032/033 protect public read-only and owner-only interactions. |
| QR-34 | `Regression risk` | AB-EV-033 protects public→private cleanup. |
| QR-39 | `Regression risk` | AB-EV-018/020/025/028/030/031/032/033 establish responsive baselines. |
| QR-40 | `Regression risk` | AB-EV-017 establishes the scoped accessibility technical baseline; later increments add targeted interaction regression. |

Evidence IDs reflect public publication order, not necessarily chronological order of underlying technical work.

## 5. Publication control

Only linked sanitised artefacts are intended for this public portfolio.

Raw AI-assisted investigation logs, unredacted screenshots, complete payloads, private source excerpts, identifiers, credentials, user data and sensitive configuration remain outside this register.
