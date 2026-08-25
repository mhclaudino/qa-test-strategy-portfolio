# AtlasBadge Quality Risk Analysis

## 1. Document purpose

This document identifies, evaluates and prioritises the main quality risks associated with AtlasBadge. It supports risk-based test planning by showing what could fail, why it matters, the realistic likelihood, the required coverage and the residual risk that must remain visible after a correction is approved.

This is a living analysis. Risk scores and priorities must be reviewed whenever the product, architecture, geographic catalogue, privacy model or release scope changes.

> **Document status:** Reviewed through AB-EV-033 and the AtlasBadge Wishlist/public-profile Production baseline. C31 established the canonical `252 Places / 195 Countries / 57 Territories and Entities` model; C32 added read-only Profile map-to-flag navigation; C33 added non-persistent dashboard sorting; C34 added owner-only Manual Visit Order and closed the P0 rapid-visit concurrency regression; AB-EV-033 adds Wishlist privacy/order, sanitised public projections, isolated Emulator E2E and closes three release-hardening product defects.

---

## 2. Assessment basis

This analysis is based on direct review of the deployed application, confirmed Product Owner/Test Lead rules, static implementation review, Firestore/Rules validation, Firebase Emulator regression, Playwright browser regression, local/manual QA, controlled release operations and Vercel/Firebase Production validation.

An intended behaviour is not assumed to be implemented unless evidence supports it. Incomplete areas are classified explicitly as gaps, assumptions, future risks or accepted behaviour.

---

## 3. Confirmed product context

### 3.1 Authentication and account management

AtlasBadge supports Google authentication, e-mail/password authentication, mandatory e-mail verification, password recovery, a 15-character minimum password policy with valid passphrases, secure access-method linking while preserving identity/data, persistent sessions, unique case-insensitive usernames, username changes and account deletion.

A username is mandatory, has a minimum length of three characters and uses a shared trimmed-lowercase identity. Previous usernames become immediately reusable and no historical alias/redirect is created; this is accepted behaviour.

### 3.2 Travel status and Wishlist model

AtlasBadge supports six travel statuses: **Visited**, **Lived / Live there**, **Born there**, **Nationality**, **Passed through** and **Want to visit**.

The Wishlist uses `statuses.wishlist === true` / Want to visit as its source of truth rather than maintaining a duplicate status model.

Multiple compatible statuses may coexist. Confirmed incompatible combinations are normalised by product rules. Selecting **Born there** or **Lived / Live there** automatically selects **Visited**. Selecting an incompatible physical-presence status removes **Want to visit**.

Wishlist-only records do not represent physical presence, do not increase physical metrics and do not enter Manual Visit Order. Wishlist ordering is independently persisted through `wishlistOrderRank`.

### 3.3 Visits and memories

`visitsCount` has a minimum of zero. Selecting **Visited** or **Passed through** changes zero to one where applicable. Passed through supports detailed passage records and multiple occurrences.

**Total Visits** is the sum of active individual `visitsCount` values. A place counts once as conquered regardless of repeated visits. Memory text uses local draft state and persists only after explicit **Save**. Non-visit memories may use `generalNote` without an artificial registered visit or visit-count increase.

Current V1.0 behaviour preserves visit counts and user-created memories when **Visited** is deselected; they are hidden while inactive and restored when Visited returns.

AB-EV-032 extends the concurrency baseline for visit history. Rapid add/remove/save operations use replayable semantic intents; add uses a stable idempotent visit ID, remove is idempotent by visit ID, save updates the target visit on the latest state, and `visitsCount` is derived from `registeredVisits.length`.

### 3.4 Persistence and data model

Cloud Firestore is the primary source of truth for authenticated users.

Private state is stored under:

```text
users/{uid}
users/{uid}/places/{placeId}
```

and may contain statuses, visitsCount, registeredVisits, generalNote, optional `visitOrderRank` and optional `wishlistOrderRank`.

Authenticated place data uses a real-time Firestore subscription and confirmed-state reconciliation. Explicit status/visit intents preserve latest-valid-local-intent semantics. AB-EV-013, AB-EV-018, AB-EV-019, AB-EV-022, AB-EV-026, AB-EV-032 and AB-EV-033 provide the main persistence/concurrency evidence.

C34 made the persistence callback intent-aware so Born there continues to use the transaction that keeps `users/{uid}.birthplacePlaceId` and `users/{uid}/places/{placeId}.statuses.born` consistent.

AB-EV-033 extends the model to private/public Wishlist and place projection changes. Public and private mutations are kept coherent in the same persistence operation where applicable, and empty/stale place records are removed according to the approved lifecycle rules.

### 3.5 Public profile, Wishlist privacy and public projection

A new profile is private by default and can be changed by the user. Private profiles must not expose map, statistics or private content.

The public Profile remains read-only and is served from a sanitised projection:

```text
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

Non-owner and anonymous viewers do not read the private `users` source.

The approved public root whitelist is:

```text
achievementMetadata
avatarUrl
bio
displayName
flagSortOrder
isPublic
isWishlistPublic
socialLinks
uid
username
```

Public place projections must not expose `generalNote`, `registeredVisits`, memories/private visit details, `firstPhysicalPresenceAt`, `statusActivatedAt` or `visitsCount`.

Wishlist privacy defaults to private. A public Wishlist tile is rendered only when `isWishlistPublic` is true and Wishlist membership is non-empty. The tile/modal is read-only. Viewer-local presentation sorting is permitted but must not persist the owner's preference.

AB-EV-033 validates private→public and public→private Wishlist transitions, public-only cleanup, mixed-document sanitisation and zero private viewer reads in Production.

### 3.6 Geographic catalogue and progress model

AtlasBadge contains **251 directly selectable geographic records**.

The audited selectable catalogue is:

```text
194 sovereign_country
47 territory
5 limited_recognition
4 constituent_country
1 special_region (Antarctica)
--------------------------------
251 directly selectable records
```

The United Kingdom aggregate is non-selectable and derived.

The approved conceptual progress model is:

```text
252 Places
195 Countries
57 Territories and Entities
```

with the invariant:

```text
252 = 195 + 57
```

Countries consist of 194 directly selectable sovereign countries plus one derived United Kingdom. Territories and Entities consist of 47 territories, 5 limited-recognition entities, 4 UK constituent countries and Antarctica.

AB-EV-027 preserves the historical observation that exposed the previous inconsistency. AB-EV-029 records the C31 catalogue audit, correction, achievement alignment and Production closure.

### 3.7 Achievements

Achievements use canonical persisted acquisition metadata with sequence and relock/reconquest behaviour protected by AB-EV-023, AB-EV-024 and AB-EV-026.

C31 aligned the affected geographic achievements:

- **A15 — United Kingdom:** four constituent countries (`4/4`);
- **A18 — Lenda Atlas:** `252/252 Places`;
- **A31 — Mundo Completo:** `195/195 Countries`;
- **A32 — Além das Fronteiras:** `57/57 Territories and Entities`.

AB-EV-033 closes a public-profile projection gap: public `achievementMetadata` is synchronised from the private chronology while exposing only `{unlockedAt, sequence}` per entry.

### 3.8 Compatibility, responsive and accessibility baseline

Manual and automated evidence includes Edge/Windows, Chrome/Android, desktop/mobile responsive matrices, touch contexts, constrained-device checks and a scoped WCAG 2.2 AA technical baseline.

AB-EV-020 covers responsive navigation/paint stability. AB-EV-025 covers Badge Unlock polish. AB-EV-028 covers map/profile parity. AB-EV-030 covers mobile Profile map-to-flag navigation. AB-EV-031 covers responsive dashboard sorting. AB-EV-032 covers mobile/manual-order regression. AB-EV-033 closes the mobile zero-width dashboard control-grid defect and validates Wishlist UI, horizontal-overflow protection and modal scroll lock.

Universal browser/device support and formal accessibility certification are not claimed. Untested browser/device combinations remain QR-38.

---

## 4. Risk assessment method

### 4.1 Impact scale

| Score | Meaning |
|---:|---|
| 1 | Negligible effect with no meaningful impact on the user or release decision. |
| 2 | Minor inconvenience or cosmetic issue with a simple workaround. |
| 3 | Important feature is affected, but the user can continue with a workaround. |
| 4 | Major loss of functionality, data integrity, privacy or user trust. |
| 5 | Critical security, privacy, account-access, severe data-loss or core-service failure. |

### 4.2 Likelihood scale

| Score | Meaning |
|---:|---|
| 1 | Rare or dependent on highly unusual conditions. |
| 2 | Unlikely, but credible under specific conditions. |
| 3 | Possible during realistic use. |
| 4 | Likely during normal or repeated use. |
| 5 | Almost certain or easily reproducible. |

### 4.3 Risk score and priority

`Risk score = Impact × Likelihood`

| Score | Test priority |
|---:|---|
| 20–25 | Critical |
| 12–19 | High |
| 6–11 | Medium |
| 1–5 | Low |

### 4.4 Risk state

| State | Meaning |
|---|---|
| Mitigated | Addressed and validated, with acceptable residual risk retained in regression coverage. |
| Current gap | A known limitation, inconsistency or unsafe behaviour exists in the current product or coverage. |
| Regression risk | The feature exists but failure has material consequences and requires continued coverage. |
| Future risk | Applies to a planned feature and must be reassessed before implementation/release. |
| Assessment gap | Evidence is insufficient to claim adequate coverage. |
| Accepted behaviour | Intentionally permitted behaviour whose consequences still require testing/communication/acceptance. |

---

## 5. Quality risk register

### 5.1 Data integrity, persistence and privacy

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-01 | A Firestore write may fail while the interface/cache continue to show the change as saved, causing silent data loss later. | Current gap | 5 | 3 | 15 | High |
| QR-02 | Visit-history preservation may regress when **Visited** is deselected and later restored. | Regression risk | 4 | 4 | 16 | High |
| QR-03 | Explicit logout may fail to remove UID-scoped private travel data from browser storage. | Regression risk | 4 | 3 | 12 | High |
| QR-04 | Listener/reconciliation or optimistic concurrency may regress, allowing stale tabs/actions to lose confirmed changes. | Regression risk | 4 | 2 | 8 | Medium |
| QR-05 | A memory editor may bypass the explicit-Save contract. | Regression risk | 3 | 4 | 12 | High |
| QR-06 | Approved character-limit enforcement may regress. | Regression risk | 3 | 4 | 12 | High |
| QR-07 | Account deletion may partially fail and leave private/public/authentication records. | Regression risk | 5 | 3 | 15 | High |

**Applied decisions:** QR-02 AB-EV-002; QR-03 AB-EV-003; QR-04 AB-EV-013/018/019/022/026/032/033; QR-05 AB-EV-004; QR-06 AB-EV-011; QR-07 AB-EV-010/033. AB-EV-033 strengthens Wishlist/private-public persistence evidence, but QR-01 remains Current gap because equivalent failure/recovery coverage is not complete for every persistence path.

### 5.2 Authentication and account identity

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-08 | The approved 15-character minimum/passphrase-compatible password policy may regress. | Regression risk | 4 | 3 | 12 | High |
| QR-09 | Linking auth methods may create/use a different UID and make existing data appear lost. | Mitigated | 5 | 2 | 10 | Medium |
| QR-10 | An access method may be linked to the wrong identity. | Mitigated | 5 | 2 | 10 | Medium |
| QR-11 | Username normalisation/reservation/public resolution may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-12 | Concurrent username reservations may leave `users` and `usernames` inconsistent. | Regression risk | 3 | 2 | 6 | Medium |
| QR-13 | A released username may be registered by another person and old links resolve to the new owner. | Accepted behaviour | 4 | 3 | 12 | High |
| QR-14 | A persistent session may expose an account on a shared device when the user does not explicitly log out. | Regression risk | 4 | 2 | 8 | Medium |
| QR-15 | E-mail may be verified while stale auth state continues blocking access. | Regression risk | 4 | 3 | 12 | High |

### 5.3 Travel statuses, Wishlist, counters and calculations

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-16 | The application may accept prohibited status combinations or remove valid combinations. | Regression risk | 3 | 4 | 12 | High |
| QR-17 | **Want to visit/Wishlist** may remain selected when an incompatible physical-presence status is applied. | Regression risk | 3 | 3 | 9 | Medium |
| QR-18 | Global country, territory, continent or visit totals may not recalculate correctly. | Regression risk | 4 | 4 | 16 | High |
| QR-19 | Global **Total Visits** may diverge from individual `visitsCount` values. | Regression risk | 4 | 3 | 12 | High |
| QR-20 | **Nationality** or **Wishlist/Want to visit** may incorrectly increase physical-presence metrics. | Regression risk | 3 | 3 | 9 | Medium |
| QR-21 | A place with multiple physical statuses may be counted more than once as conquered. | Regression risk | 4 | 3 | 12 | High |
| QR-22 | Automatic status transitions may incorrectly reset visit counts or delete memories. | Regression risk | 4 | 3 | 12 | High |
| QR-23 | **Born there** or **Lived** may fail to select **Visited** and initialise visit count. | Regression risk | 3 | 3 | 9 | Medium |
| QR-24 | The approved **Passed through** workflow may regress. | Regression risk | 2 | 4 | 8 | Medium |

**Applied decisions:** AB-EV-033 adds explicit Wishlist compatibility, non-physical semantics and independent-order regression to the status/counter baseline.

### 5.4 Geographic catalogue, map and achievements

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-25 | `252 Places`, `195 Countries`, `57 Territories and Entities`, `251 selectable records` and continent groups may diverge. | Regression risk | 4 | 3 | 12 | High |
| QR-26 | A selectable place may be missing, mapped to wrong ID, unclickable, incorrectly coloured or persisted under unsupported data. | Regression risk | 4 | 3 | 12 | High |
| QR-27 | Technical/non-selectable geographic records may create invalid data or duplicate progress. | Regression risk | 4 | 2 | 8 | Medium |
| QR-28 | Map intensity may be wrong when multiple statuses are present. | Regression risk | 3 | 3 | 9 | Medium |
| QR-29 | United Kingdom achievement/progress may use incorrect constituent/derived semantics. | Regression risk | 3 | 3 | 9 | Medium |
| QR-30 | Achievement lock/relock, metadata, chronology, reconquest or notification delivery may become inconsistent across private/public sources. | Regression risk | 3 | 3 | 9 | Medium |

AB-EV-033 extends QR-30 by closing the missing public achievement metadata projection and validating sanitised public chronology.

### 5.5 Public profile and sharing

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-31 | A private profile or private Wishlist may expose identity/travel content to an unauthorised visitor. | Regression risk | 5 | 2 | 10 | Medium |
| QR-32 | Private memories/notes/visit details may be exposed publicly or returned to an unauthorised browser. | Regression risk | 5 | 2 | 10 | Medium |
| QR-33 | A public-profile component may allow editing or unauthorised persistence. | Regression risk | 5 | 2 | 10 | Medium |
| QR-34 | Public→private transitions may leave previously public profile/Wishlist projection accessible. | Regression risk | 4 | 3 | 12 | High |
| QR-35 | Social-link validation may permit an unsafe destination. | Regression risk | 4 | 2 | 8 | Medium |
| QR-36 | Future per-memory visibility/default logic may publish content contrary to preference. | Future risk | 5 | 3 | 15 | High |
| QR-37 | A future generated Story may expose unexpected information or fail across sharing flows. | Future risk | 4 | 3 | 12 | High |

**Applied decision:** AB-EV-033 confirms the public Profile uses `publicProfiles` rather than the private source, validates public-place sanitisation, records zero private viewer reads, validates Wishlist private/public transitions and confirms viewer-local sorting does not persist owner state.

### 5.6 Compatibility, usability, performance and accessibility

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-38 | Behaviour may differ on Firefox, Safari, iPhone, tablets, macOS or other untested combinations. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-39 | Responsive/touch/constrained-device/CSS/navigation/card-paint/map-layout baselines may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-40 | Keyboard access, focus, accessible names, dialogs, contrast or other accessibility behaviour may regress. | Regression risk | 4 | 3 | 12 | High |

**Applied decisions:** QR-39 is protected by AB-EV-018/020/025/028/030/031/032/033. AB-DEF-016 specifically proves that DOM presence is insufficient when responsive CSS collapses a functional control to zero width.

---

## 6. Highest-priority test focus

Priority focus includes silent persistence failure; visit-history preservation; account-deletion integrity; status/Wishlist compatibility; `252/195/57` counter integrity; geographic catalogue integrity; private/public projection and privacy transitions; explicit logout/local-data exposure; explicit-Save integrity; real-time concurrency/cache authority; rapid visit convergence; Manual Visit Order; independent Wishlist ordering; birthplace pointer/status atomicity; achievement chronology/public metadata; responsive/constrained-device behaviour; Profile read-only interaction; and accessibility.

QR-25 is no longer an open arithmetic investigation. Its residual concern is regression.

---

## 7. Important approved rules that must not be reported as defects without new evidence

- a place is counted once as conquered even with multiple compatible statuses;
- repeated visits increase Total Visits but do not create additional selectable records;
- Nationality and Wishlist/Want to visit alone do not represent physical presence;
- Wishlist membership uses the existing Want-to-visit status source rather than a duplicate status system;
- Wishlist order is independent from visit order;
- Wishlist privacy defaults to private and its public preference is independent from whole-profile visibility;
- a public Wishlist tile is shown only when public and non-empty, including when the owner views their Profile;
- public Wishlist/Profile presentation is read-only;
- viewer-local sorting may change presentation but must not persist the owner's preference;
- public viewers read the sanitised `publicProfiles` projection, not private user/place documents;
- public place projections exclude memories, detailed visits and private chronology/count fields;
- 251 records are directly selectable while conceptual Places totals 252 because completing the four UK constituents derives one UK Place/Country;
- the canonical counter invariant is `252 Places = 195 Countries + 57 Territories and Entities`;
- Manual Visit Order exists only on the owner Map tab;
- Wishlist-only and Nationality-only records do not participate in Manual Visit Order;
- Born there remains fixed at the top of Manual Visit Order and the user pointer/status remains transactionally consistent;
- memories are currently private.

---

## 8. Assumptions requiring validation

1. Social-link validation rejects unsafe protocols and unsuitable formats.
2. The geographic fixture and deployed application continue to contain the same 251 selectable records.
3. Responsive behaviour remains acceptable outside the validated browser/device sample.
4. Map-status priority remains identical across map, profile, cards and legends.
5. Future geographic changes trigger explicit re-audit of `252/195/57`.
6. Future changes to public-profile fields extend the whitelist intentionally rather than reintroducing direct private reads.

---

## 9. Open product and quality questions

Resolved V1.0 decisions must not be reopened without new evidence: explicit Save for memories, character-limit policy, retry-safe account deletion, real-time/OCC controls, password minimum/passphrases, canonical usernames, immediate username reuse, Passed-through workflow, accessibility technical baseline, responsive baseline, last-intent chronology, achievement chronology, production runner safety, Badge Unlock polish, Clear Map reconciliation, C27–C30 map/profile parity, C31 counters, C32 map-to-flag navigation, C33 dashboard sorting, C34 Manual Visit Order/rapid visits/birthplace integrity, and AB-EV-033 Wishlist/public-profile projection/release hardening.

Open questions remain around username allowed characters, future memory visibility, broader browser/device support, native assistive-technology coverage, localisation completion, quantitative performance targets and future Story/photo scope.

---

## 10. Risk-based release implications

Release confidence requires evidence that travel data is not silently lost; private content is not exposed; public content is projected intentionally; account deletion/linking preserve integrity; status/Wishlist transitions follow rules; calculations remain consistent; public profiles remain read-only; frontend and Firestore Rules are release-aligned; known gaps are resolved or explicitly accepted; and the supported compatibility/accessibility baseline is defined.

Any unresolved High risk must be reviewed before release and recorded as mitigated, accepted, reduced by control, deferred with limitation/follow-up, or a release blocker.

---

## 11. Review triggers

Review this analysis when travel statuses/combinations, Wishlist privacy/order, geographic catalogue/classification, map implementation, private/public profile projection, save/limit behaviour, auth/linking/password rules, account deletion, real-time/OCC behaviour, `visitOrderRank`, `wishlistOrderRank`, birthplace transactions, public achievement metadata, sharing, supported browsers/devices/accessibility targets or release-parity controls change.

A change to the public projection whitelist, Wishlist privacy model, UK/Antarctica classification, `252/195/57` denominators, achievement criteria, Profile interaction, manual order persistence or mutation orchestration requires explicit affected-area regression and documentation review.

---

## 12. Related portfolio documents

- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `docs/10-c31-c32-production-traceability.md`
- `docs/11-c33-c34-production-traceability.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/defects/ab-def-014-public-place-projection-missing.md`
- `evidence/v1.0/defects/ab-def-015-public-achievement-metadata-not-synchronised.md`
- `evidence/v1.0/defects/ab-def-016-mobile-dashboard-grid-collapse.md`
