# AB-EV-056 — C45K geographic localization and integrated QA closure

**Evidence ID:** AB-EV-056  
**Checkpoint:** C45K-A1/A2/A3, B1–B8, focused corrections and retests  
**Product repository:** [mhclaudino/atlasbadge](https://github.com/mhclaudino/atlasbadge)  
**Final product/test commit:** [`9374273e4dcc5745628267de61e96c0b12740e00`](https://github.com/mhclaudino/atlasbadge/commit/9374273e4dcc5745628267de61e96c0b12740e00)  
**Environment:** Next.js/Edge; local Firebase Auth/Firestore/Storage Emulators (`demo-atlasbadge-web`); Vercel Production; Test Lead native Edge QA  
**Decision:** C45K-B7 Production Visual PASS; C45K-B8 integrated QA accepted; geographic-localization checkpoint closed. Not a claim of complete V1.0 release certification.

## 1. Requirement and acceptance boundary

C45J/AB-EV-055 localized public Profile interface copy while intentionally deferring canonical geographical **display names**. C45K completes that presentation boundary in `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr` and `en-GB` for selectable places, derived UK map aggregate, continents, search/picker, dashboard cards, private/public Wishlist, manual visit order, public flags/memories, maps, tooltips and landing-page SVG accessible labels.

Preserve the established geographic contract: **252 conceptual Places = 195 Countries + 57 Territories and Entities; 251 selectable Places**. UK aggregate `gb` is map-only; its four selectable constituents retain distinct canonical IDs. Eight canonical continent groups receive localized **presentation**, not new identity or storage. Distinguish conceptual/selectable counts from physical map/source-record counts.

Translation must never alter country/place IDs, classification, map geometry, status/achievement IDs, visit history, persisted notes/names/dates/durations, Wishlist membership/order, Firestore source of truth, Rules or sanitized public projection. User-authored content is not auto-translated.

## 2. Requirement → implementation → validation traceability

| Requirement / risk | Consumer / invariant | Evidence and decision |
|---|---|---|
| QR-25, QR-18 catalogue integrity | Translation catalogues and `getLocalizedCountryName` / `getLocalizedContinentName`; 251/252/195/57, UK aggregate, 8 continents | A1–A3 coverage/domain checks; business counts unchanged |
| QR-01/02/04/17–24 identity and order | CountryActionCard, CountryPicker, dashboard search, WishlistModal, ManualVisitOrderEditor | B1–B4 and focused editor fix; canonical IDs/order remain authoritative |
| QR-26/31/32/34/36 privacy and Profile parity | Public country/flag grid, public memories/Wishlist, public map/continents | B5/B6 and B8 regression; no public whitelist/Rules changes |
| QR-39/40 localized accessibility | Dashboard/profile tooltip, status, legend/zoom instructions; decorative `WorldSvgMap` `aria-label` and rendered marker `title` | B6 failed then retested; B7 accessibility correction and Test Lead Production Visual PASS |
| Metadata/title | `html lang`, `document.title`, native Edge tab and cookie | DOM regression and human retest PASS; original discrepancy not reproduced; cause undetermined |
| E2E oracles | C45 test assertions versus updated C45J/C45K locale contract | B8 test-only maintenance, final 66 PASS; stale automation not Product defects |

## 3. Implementation and checked Product commits

A1/A2 supplied geography dictionaries and pure presentation helpers; individual SHAs are **not established in this record** and are not invented. The following concrete SHAs were independently checked in the Product GitHub history:

| Checkpoint | Published change | Commit |
|---|---|---|
| A3 | UK map-only parent and continents | [`5ca957f1f43b636fae480bb663d24f9428267a96`](https://github.com/mhclaudino/atlasbadge/commit/5ca957f1f43b636fae480bb663d24f9428267a96) |
| B1 | CountryActionCard geography | [`def754fc2561153e5b9bc0ef6049fc57714c288d`](https://github.com/mhclaudino/atlasbadge/commit/def754fc2561153e5b9bc0ef6049fc57714c288d) |
| B2 | CountryPicker/search | [`83a62f593cdc3a67eea6ae5dc6047d1edec7d404`](https://github.com/mhclaudino/atlasbadge/commit/83a62f593cdc3a67eea6ae5dc6047d1edec7d404) |
| B3 | Dashboard search | [`0fd8a98a33d955c4ba2df93f873546311bc7d087`](https://github.com/mhclaudino/atlasbadge/commit/0fd8a98a33d955c4ba2df93f873546311bc7d087) |
| B4 | Wishlist country names | [`28ca8cdc32435b744114ab687b54b309a0860264`](https://github.com/mhclaudino/atlasbadge/commit/28ca8cdc32435b744114ab687b54b309a0860264) |
| Focused fix | ManualVisitOrderEditor names | [`2b4e1052cc211901fc0a7f37ef0cdab347d768dc`](https://github.com/mhclaudino/atlasbadge/commit/2b4e1052cc211901fc0a7f37ef0cdab347d768dc) |
| B5 | Public Profile geography | [`edd4a94c208f6f7eefc0ff9bbe28ed1fb270dcee`](https://github.com/mhclaudino/atlasbadge/commit/edd4a94c208f6f7eefc0ff9bbe28ed1fb270dcee) |
| B6 | Map geography and dashboard UK selector | [`98025482f70016feed8aa50e5e92ed9e4bfd1c24`](https://github.com/mhclaudino/atlasbadge/commit/98025482f70016feed8aa50e5e92ed9e4bfd1c24) |
| B6 correction | Dashboard passes translated map labels | [`a2a0900cfb4042de3003d846391a844ffa1fc7b4`](https://github.com/mhclaudino/atlasbadge/commit/a2a0900cfb4042de3003d846391a844ffa1fc7b4) |
| B7 | Landing-map accessible geography | [`a74358b8769abbd9533f2bf6ca7467419badc69d`](https://github.com/mhclaudino/atlasbadge/commit/a74358b8769abbd9533f2bf6ca7467419badc69d) |
| B8 | Eight updated C45 E2E files + one new title E2E; test-only | [`9374273e4dcc5745628267de61e96c0b12740e00`](https://github.com/mhclaudino/atlasbadge/commit/9374273e4dcc5745628267de61e96c0b12740e00) |

The B8 commit was independently inspected: **eight modified E2E tests and one added E2E test, zero `src/` changes**. Its deployment is not a new Product-feature implementation.

## 4. Defects and investigation disposition

**Manual visit-order names — confirmed Product presentation defect.** Canonical Portuguese country names remained visible under another locale. A consumer-only fix `2b4e105…` preserved stable visit ranks, IDs and persistence; Test Lead Production Visual RETEST PASS.

**Dashboard map — confirmed Product presentation defect.** The public Profile map was in English, while dashboard map status/zoom/legend remained Portuguese (for example, `Brazil — Nasceu lá`). Root cause: dashboard had not supplied the shared map's localized presentation-label props. `a2a0900…` corrected the consumer; B6 six-scenario Production Visual RETEST PASS.

**Decorative landing map — confirmed accessible-label gap.** `WorldSvgMap` uses `interactive=false` but still renders SVG path `aria-label` and conditional micro-marker `title`/`aria-label`. Canonical names reached assistive technology or hover output. `a74358b…` localized both name pipelines, retained path IDs, geometry and non-selectable behavior. Three focused locale DOM tests (fr/en-GB/pt-BR) reported; B7 Test Lead Production Visual PASS. Do not assume all possible markers are visible in a decorative preview.

**Browser tab — observed but not reproduced on retest.** Test Lead originally saw French native Edge tab with English/Spanish UI. Root-layout metadata rewrite was a hypothesis, reverted before publication. No `DocumentTitleSync` exists. Automated DOM checks saw consistent `html lang` and `document.title`; a subsequent human Edge capture showed English UI, `en-GB` language and cookie, English DOM and visual tab title. **Cause indeterminate; no code fix attributed; not reproduced on retest.** Browser translation/cache was not proven as the cause.

**C45 stale E2E — test defects, not Product or Emulator defects.** Pre-C45K scripts awaited `Brasil` in English/French UI or a fixed pt-BR public Profile despite C45J viewer locale. Locator waits were initially miscalled an infrastructure freeze. First broad corrective run: **59 PASS / 7 FAIL**, a diagnostic run, not a pass. Eight tests were updated to the current requirements; machine-ID checks retained, no blanket replace or skips. Added `e2e/document-title-locale.spec.ts` to check the observable DOM contract. The final E2E run and post-edit differential rerun restored evidence. Do not inflate the Product-defect count.

## 5. Integrated QA, safety and release evidence

| Gate | Final observed/reported result | Scope |
|---|---|---|
| Playwright Edge + Firebase Emulators | **66 PASS, 0 FAIL, 0 SKIPPED**, ~2.9 minutes; 65 C45 + 1 title | Final completed C45/title run, after test-oracle updates |
| Dashboard E2E after final test edit | **4 PASS**, ~28.5 seconds | Focused differential rerun |
| Vitest | **832 PASS / 37 SKIPPED**; 123 files (115 passing / 8 skipped) | Unit/component/integration, not 832 E2E |
| TypeScript, lint, build | PASS / PASS (0 errors, 28 warnings reported) / PASS | Local gates |
| Real Firebase isolation | `realFirebaseRequests=0`, base URL `127.0.0.1:3100`, Auth `9099`, Firestore `8080`, Storage in Emulator launch | E2E safety-log evidence; not a universal production-network guarantee |
| B7 Production | `a74358b8769abbd9533f2bf6ca7467419badc69d` / `dpl_9efHWAcGxCrNjci46HsZ6K2PPPMx` **READY** | Exact-SHA Product deployment and Test Lead Production Visual PASS |
| B8 Production | `9374273e4dcc5745628267de61e96c0b12740e00` / `dpl_69uaKtiqJcj2ZvucWhQDMFrwK5JH` **READY** | Test-only HEAD/deployment; no new functional UI change |

**Evidence provenance:** Local counts, duration, warnings and Emulator safety logs above come from agent execution reports. Exact GitHub SHAs and nine-file B8 scope, Portfolio baseline and Vercel B7/B8 READY deployments were checked directly through connected services. Human Production Visual PASS is the Test Lead's decision. A summary conflicted about `git diff --check` versus trailing whitespace; this evidence does **not** claim an independently re-run post-publication clean diff gate.

Coverage is **representative**, not six languages multiplied by every screen/test/browser combination. No blanket WCAG certification, absence-of-risk claim, private raw screenshots, test credentials or unsupported Production Firebase actions are asserted.

## 6. Closure and residual scope

C45K geographic **display-name localization** is accepted for supported consumers and six locales. Canonical IDs, the 251/252/195/57 model, UK aggregate and eight continents, authored text and existing Firestore/public-projection rules remain authoritative. Broader localisation still outside C45K includes password-reset action presentation and a future authenticated language selector. The earlier transient Edge-title discrepancy retains an unknown cause after passing retest. See [AB-EV-055](ab-ev-055-c45j-public-profile-localization-and-fixes.md) for the preceding public Profile localization boundary and the [Evidence Register](../evidence-register.md) for the public index.
