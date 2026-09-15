# AB-EV-052 — C45G Deep Country and Visit Editor Localization

## Status

**Evidence ID:** AB-EV-052  
**Increment:** C45G — Deep Country / Visit Editor Localization  
**Decision:** CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS  
**Test Lead approval:** 15 September 2026

## 1. Objective

C45G localizes the deep authenticated country-editing surface inside `/app` for the six approved V1.0 locales while preserving the existing status, visit, memory, privacy, concurrency and visit-photo contracts.

Supported locales remain:

- `pt-BR`
- `pt-PT`
- `es-419`
- `es-ES`
- `fr`
- `en-GB`

C45G is presentation localization around established domain behavior. It does not rename or translate persisted machine/domain values.

## 2. Localized C45G surface

C45G covers:

- CountryActionCard presentation;
- all six travel-status labels and related accessibility text;
- total-visit controls and add/remove visit presentation;
- birthplace-conflict presentation;
- general/non-physical memory editing;
- visit-memory privacy presentation;
- RegisteredVisit editing;
- visit-name presentation and validation;
- date/time/duration presentation and validation;
- MemoryOrderEditor;
- visit-photo add/replace/remove presentation;
- visit-photo quota/error presentation;
- dirty/saving/success/error feedback;
- deletion/unsaved-change confirmations;
- relevant deep-editor accessibility labels.

The implementation groups this presentation under `dashboard.countryCard`, `dashboard.visitEditor` and `dashboard.memoryOrder` catalog namespaces.

## 3. Preserved domain contracts

The following remained unchanged:

- canonical `countriesData`, country IDs and continent/domain values;
- `TravelStatus` IDs: `born`, `nationality`, `lived`, `visited`, `passed`, `wishlist`;
- status compatibility including C35 Visited + Passed-through coexistence;
- `birthplacePlaceId` / single-birthplace invariant;
- `RegisteredVisit` schema, stable IDs and historical array order;
- `visitsCount` semantics;
- explicit-Save memory behavior;
- `generalNote` / `isGeneralNotePublic`;
- `isMemoryPublic` and sanitised public projection;
- `memoryOrder` as presentation metadata only;
- optional C42 `visitName`, 40-character limit and same-country duplicate rejection;
- stored date/time values and `DurationUnit` IDs `hours`, `days`, `weeks`, `months`, `years`;
- C44 visit-photo metadata and lifecycle;
- C44 free quota of exactly 10 active visit-photo slots per user;
- Storage paths/slot/variant lifecycle and public opaque `photoRef` boundary.

Canonical country and continent names are still presentation debt and were intentionally not migrated or translated in storage.

## 4. Shared-component and error architecture

`StatusPill` gained an optional localized display label for `/app`; its existing Portuguese label remains the fallback so shared/public-profile callers do not depend on the `/app` Intl provider.

Shared errors gained additive stable machine-readable codes where deterministic localization was required. Existing Portuguese `Error.message` compatibility remains available for current callers.

Representative stable codes include:

- `BIRTHPLACE_CONFLICT`;
- `BIRTHPLACE_POINTER_INCONSISTENT`;
- `CONCURRENCY_CONFLICT`;
- `PLACE_NOT_PHYSICAL`;
- `VISIT_NOT_FOUND`;
- `VISIT_NAME_TOO_LONG`;
- `VISIT_NAME_DUPLICATE`;
- deterministic image-processing categories for invalid type, excessive size, canvas/conversion failure and corrupt input.

Localized UI behavior no longer depends on comparing translated feedback text. Raw backend response bodies are not exposed as user-facing copy.

## 5. Memory and visit regression coverage

C45G requalified the directly affected historical contracts:

- status/OCC persistence and last-intent convergence;
- C35 Visited + Passed-through coexistence;
- C39 individual memory privacy/public sanitisation;
- C40 memory presentation ordering without reordering `registeredVisits`;
- C42 optional editable visit names with stable identity and sanitised `visitLabel`;
- C44 photo quota, replacement, removal, cleanup and privacy projection.

General-memory coverage confirmed typing does not persist before explicit Save, clearing content forces public false and no artificial RegisteredVisit is created.

Memory-order coverage confirmed Save changes `memoryOrder` while historical visit identity/order remains intact and Cancel does not persist.

## 6. Visit-photo regression

C44 remained intact through C45G.

Validated behavior includes:

- one photo per RegisteredVisit;
- add, replace and remove;
- replacement reuses the bounded slot lifecycle;
- removal cleans the intended objects without affecting neighboring data;
- quota remains 10 active slots;
- owner and authorised public reads remain server-mediated;
- public projection exposes only opaque `photoRef` where memory visibility permits it;
- private `photoPath`, slot, variant and stable visit ID remain absent from public projection.

Focused C44 release coverage included API `8/8`, slots/quota/replacement `6/6`, cleanup `1/1` and UI `1/1` PASS.

## 7. Local automated validation

Runtime: `Node v22.23.2`

Final release results:

| Gate | Result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 21 existing non-blocking warnings |
| Build | PASS — Next.js 16.2.11 |
| Vitest | 216 / 216 PASS |
| Required Emulator integration suites | 20 / 20 PASS in clean serial execution |
| Playwright Edge | 26 / 26 PASS |
| Firebase backend | Auth/Firestore/Storage Emulators, `demo-atlasbadge-web` |
| Requests to real Firebase | 0 |
| `git diff --check` | PASS |

The first parallel attempt at a subset of Emulator tests produced a Storage-fixture collision. The same required gate then passed serially. This was treated as test-harness execution contention rather than Product failure.

An additional non-gate diagnostic of `travelMap.clearMap.emulator.test.ts` reproduced three pre-existing anonymous LIST/Rules failures. C45G did not modify that area or Firestore Rules, so the diagnostic was retained as separate pre-existing test/environment debt and did not block C45G.

## 8. Test Lead local/Emulator visual QA

A disposable Emulator-only visual account was prepared with representative country/status/visit/memory/photo state.

Visual review covered:

- deep country card and six status labels;
- visit counters;
- multiple RegisteredVisits;
- visit names;
- date/time/duration controls;
- private/public memories;
- general non-physical memory;
- MemoryOrderEditor;
- birthplace conflict;
- normal and stronger visit-delete confirmations;
- dirty-draft protection;
- existing-photo and no-photo visit states;
- photo remove confirmation;
- responsive `390×844` checks in `fr`, `pt-PT` and `es-ES`;
- relevant keyboard/accessibility labels.

Test Lead visual QA passed before release.

## 9. Visual-QA fixture incident

During visual QA, `/badges` and the disposable public Profile initially triggered the development achievement-order assertion because the direct rich travel-state seed caused achievements to become earned without creating the required persisted acquisition metadata.

Root cause: the test-data seed bypassed the normal achievement-metadata reconciliation lifecycle.

The six earned achievements for that fixture were reconciled through the existing Product helper, producing valid positive `unlockedAt` values, unique sequences and the next sequence counter. The public profile received only the approved sanitised achievement metadata.

After data-only reconciliation:

- `/app` rendered normally;
- `/badges` rendered normally;
- the disposable public Profile rendered normally;
- browser console/runtime remained clean;
- `realFirebaseRequests=0`;
- no Product/test/documentation file changed.

Classification:

**C45G VISUAL-QA TEST DATA / FIXTURE DEFECT — NOT PRODUCT DEFECT — NOT C45G REGRESSION.**

No AB-DEF identifier is assigned and Product Defect metrics are not affected.

## 10. Implementation and release traceability

AtlasBadge release:

`0c2c248b0b2fee302b00edbf567a6f67248b4a6f`  
`feat(i18n): localize country and visit editing`

Release scope:

- 33 audited Product/test files;
- 1,360 insertions;
- 209 deletions.

No QA documentation was changed in the Product release commit.

## 11. Production release

**Deployment:** `dpl_3ybFA5nDJvpKcePtBHFA5vTKXWEy`  
**Commit:** `0c2c248b0b2fee302b00edbf567a6f67248b4a6f`  
**Environment:** Vercel Production  
**Status:** READY  
**Runtime:** Node 22.x / Next.js 16.2.11  
**Domain:** `https://atlas-badge.vercel.app`

Production technical smoke confirmed:

- `/app` locale handling remains valid for `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr` and `en-GB`;
- anonymous `/app` continues to redirect to localized Login;
- `/badges` remains Portuguese/`pt-BR` under French preference;
- the public Profile remains Portuguese/`pt-BR` and renders normally;
- resetPassword remains Portuguese/`pt-BR`;
- shared `StatusPill` Portuguese fallback remains intact outside localized `/app`;
- no runtime/hydration/missing-message/broken-asset or unexpected 4xx/5xx issue was observed;
- Vercel logs contained no new warning/error/fatal runtime entries.

No Production travel data, visit photos or destructive state were created merely to replay the already-approved Emulator deep-editor scenarios.

## 12. Sign-off model

The complete deep editor requires authenticated, stateful travel data and Storage-backed visit-photo state. That functional/visual surface was exercised locally against Firebase Emulators and approved by the Test Lead.

Production validation remained non-destructive and route/integration focused.

Therefore the closure classification is intentionally:

**C45G CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS**

—not a claim of full stateful Production visual execution.

## 13. Remaining localization debt

Remaining bounded localization work includes:

- ProfileEditModal;
- `/badges`, achievement catalogue text and BadgeUnlockToast;
- public Profile;
- resetPassword presentation;
- authenticated language selector in the avatar/user menu;
- canonical country/continent display localization through presentation mapping rather than domain mutation;
- separate UK-selector unreachable-code cleanup follow-up.

## 14. Test Lead decision

**C45G CLOSED / PRODUCTION TECHNICAL PASS + LOCAL/EMULATOR VISUAL PASS — 15 September 2026.**
