# AB-EV-053 — C45H Badges and achievements localization

## Evidence summary

**Evidence ID:** AB-EV-053  
**Checkpoint:** C45H  
**Subject:** Authenticated Badges/achievement localization and Badge Unlock notification  
**Product commit:** `a6e7a4d4ffc119b947f37de34a0f44d514b1de7a`  
**Commit message:** `feat(i18n): localize badges and achievements`  
**Vercel deployment:** `dpl_DfnhzHrwgRxiEzmM2xZP7zMAj1tZ` — Production `READY`  
**Runtime:** Node 22.x; Next.js 16.2.11  
**Final decision:** **C45H CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS**  
**Test Lead sign-off:** 17 September 2026

## 1. Change objective

C45H extends the AtlasBadge V1.0 localization programme into the authenticated Badges/achievement experience while keeping achievement identity, business rules, chronology, reconciliation and public-projection semantics locale-neutral.

The checkpoint localizes:

- the unprefixed `/badges` document language and metadata;
- authenticated Header/Footer presentation on `/badges`;
- Badges page loading, headings, section labels and empty/completion states;
- all current achievement titles and descriptions for the six approved V1.0 locales;
- earned-date presentation;
- `BadgeUnlockToast` label, localized achievement title and accessible close label inside the localized authenticated app.

The supported locale set remains exactly:

1. `pt-BR`;
2. `pt-PT`;
3. `es-419`;
4. `es-ES`;
5. `fr`;
6. `en-GB`.

No locale/language field was added to Firestore/UserProfile and no stored domain value was translated.

## 2. Architecture and invariants

C45H uses stable achievement IDs as the localization key. The canonical achievement definitions and evaluators remain the source of business truth, while localized title/description values are resolved at the presentation boundary and supplied additively to shared cards/toast rendering.

The audited current achievement set contains **31 IDs**:

`a1`–`a27`, `a29`, `a30`, `a31`, `a32`.

There is no `a28`.

The following contracts were explicitly preserved:

- achievement IDs and evaluator conditions;
- progress and `maxProgress` calculations;
- A15 United Kingdom four-constituent rule;
- A18 `252 Places` criterion;
- A31 `195 Countries` criterion;
- A32 `57 Territories and Entities` criterion;
- `sortAchievementsChronologically()` semantics;
- `achievementMetadata.unlockedAt` and `achievementMetadata.sequence`;
- `nextAchievementUnlockSequence` semantics;
- strict invalid-earned-metadata assertion;
- relock/reconquest behaviour;
- notification `eventId`/sequence semantics;
- public achievement metadata projection restricted to `{ unlockedAt, sequence }`.

Shared `AchievementCard`/`AchievementGrid` usage remains compatible with unlocalized consumers. The public Profile therefore continues to render its existing Portuguese achievement presentation until the separate public-Profile localization checkpoint.

## 3. `/badges` locale resolution and document semantics

`/badges` remains unprefixed. Locale resolution reuses the authenticated-app model:

```text
valid atlasbadge_locale cookie
→ weighted Accept-Language
→ pt-BR fallback
```

Representative regional resolution was verified, including:

- `es-AR` → `es-419`;
- `en-US` → `en-GB`;
- unsupported `de-DE` → `pt-BR`.

The server document sets the correct initial `<html lang>` and localized metadata for all six supported locales. No locale-prefixed `/badges` routes, `hreflang` expansion or new locale query contract were introduced.

## 4. Achievement presentation

All 31 current achievement IDs have localized title and description entries in each of the six message catalogs. Translation is presentation-only and is not used as a business-logic, persistence or sorting key.

Earned dates use locale-aware `Intl.DateTimeFormat` presentation while retaining the original stored timestamp and acquisition ordering. The same timestamp was reviewed across locale changes without altering the chronological order.

Locked achievement ordering remains source-order based; earned achievement ordering remains metadata based. Translation cannot influence either order.

## 5. Badge Unlock notification

`BadgeUnlockToast` is localized inside the authenticated localized application while preserving its established behaviour:

- five-second timeout;
- queue and dismissal semantics;
- event/sequence identity;
- click activation;
- Enter/Space keyboard activation;
- navigation to `/badges#<stable achievement ID>`;
- reconquest behaviour.

The localized title is not used in the URL/hash.

## 6. Automated evidence

Final C45H validation recorded:

- TypeScript: PASS;
- lint: PASS with 21 pre-existing warnings and zero errors;
- production build: PASS;
- Vitest: **173 / 173 PASS across 23 files**;
- Firebase Emulator integration: **4 / 4 PASS**;
- Playwright: **8 / 8 PASS across three focused specs**;
- Firebase project: `demo-atlasbadge-web`;
- Auth/Firestore/Storage Emulator isolation confirmed;
- `realFirebaseRequests=0`;
- `git diff --check`: PASS.

Coverage included locale resolution, server document/metadata, catalog completeness, Badges-page presentation, shared card fallback, localized dates, achievement order/invariant assertions, notification timeout/dismiss/navigation/keyboard/reconquest behaviour and public-Profile/resetPassword isolation.

## 7. Stateful Emulator and visual evidence

The Test Lead visual environment used a reconciled Emulator-only profile with six earned achievements (`a1`, `a6`, `a7`, `a8`, `a9`, `a29`) and 25 locked achievements. Earned metadata had valid unique sequences `1–6`, valid `unlockedAt` values and `nextAchievementUnlockSequence=7`.

Representative locked progress included:

- `a2 = 3/10`;
- `a10 = 9/10`;
- `a3 = 3/25`.

The Test Lead reviewed all six locales and representative mobile `390×844` layouts for French, Portuguese (Portugal) and Spanish (Spain), including long achievement titles/descriptions, earned dates, locks, progress bars, Header/Footer and the localized toast.

A legitimate Product flow was used to unlock a new achievement and verify the toast/navigation path. No notification or achievement metadata was fabricated to make the visual scenario pass.

## 8. C45G fixture lesson carried forward

The earlier C45G visual fixture incident established that rich direct seeds must satisfy achievement-derived metadata invariants. C45H applied that rule before Test Lead handoff: earned state was reconciled through the existing Product helper/lifecycle and dependent `/badges`/public-Profile surfaces were validated before visual review.

No Product assertion was weakened and no test-data problem was reclassified as a Product defect.

## 9. Production release and smoke

The approved release commit is:

```text
a6e7a4d4ffc119b947f37de34a0f44d514b1de7a
feat(i18n): localize badges and achievements
```

The initial push attempt was stopped by an automated security/release review pending explicit Test Lead authorization. No push or deployment occurred during that blocked state. After explicit authorization, a normal `git push origin main` completed; no force push/rebase was used.

Vercel deployment:

```text
dpl_DfnhzHrwgRxiEzmM2xZP7zMAj1tZ
Production READY
SHA: a6e7a4d4ffc119b947f37de34a0f44d514b1de7a
Node: 22.x
Next.js: 16.2.11
```

Safe Production smoke verified:

- `/badges` and `/app` locale resolution/document metadata across all six locales;
- expected anonymous redirect to localized `/login`;
- weighted `Accept-Language` behaviour and regional fallback;
- `/@mhclaudino` remains `pt-BR` with Portuguese achievement presentation under French preference;
- invalid `resetPassword` remains Portuguese/`pt-BR` with no reset executed;
- 18 documents and 24 assets checked without observed console errors;
- Vercel logs showed no runtime warning/error/fatal or unexpected 4xx/5xx.

No Production user/travel mutation was created to reproduce authenticated achievement state or toast behaviour. Stateful/visual proof remains local/Firebase Emulator evidence, so the closure is not described as full authenticated Production visual PASS.

## 10. Defect classification

No C45H Product Defect was identified.

The pre-push automated security-review block is a release-control event, not a Product defect. It required explicit authorization and caused no source/deployment mutation before approval.

The prior C45G fixture issue remains classified as QA test-data/fixture defect only; C45H specifically prevents its recurrence by reconciling derived achievement metadata before visual handoff.

No new `AB-DEF` record is created for C45H.

## 11. Traceability

```text
Requirement / scope
C45H localized authenticated Badges/achievements/toast
        ↓
Implementation
a6e7a4d4ffc119b947f37de34a0f44d514b1de7a
        ↓
Automated validation
173 Vitest + 4 Emulator integration + 8 Playwright
TypeScript/lint/build/diff PASS
        ↓
Stateful/visual validation
Firebase Emulator + Test Lead local visual PASS
390×844 representative locale coverage
        ↓
Release
dpl_DfnhzHrwgRxiEzmM2xZP7zMAj1tZ — READY
        ↓
Production technical smoke
six-locale route/document/isolation checks PASS
        ↓
Test Lead decision
C45H CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS
```

## 12. Remaining localization debt

C45H does not close the full V1.0 localization programme. Remaining scope includes:

- `ProfileEditModal`;
- public Profile localization;
- `resetPassword` localization;
- authenticated language selector;
- country/continent presentation mapping without mutating canonical stored values;
- legacy unreachable UK-selector technical cleanup.

User-authored content remains outside automatic translation scope.

## 13. Final Test Lead decision

**PASSED C45H.**

The released source, local/Emulator stateful/visual evidence and safe Production technical smoke provide sufficient confidence for this checkpoint. Achievement-domain and public-projection contracts remain regression risks protected by the existing achievement baselines plus C45H localization regression.
