# AtlasBadge Quality Risk Analysis

## 1. Document purpose

This document identifies, evaluates and prioritises the main quality risks associated with AtlasBadge. It supports risk-based test planning by showing what could fail, why it matters, the realistic likelihood, the required coverage and the residual risk that must remain visible after a correction is approved.

This is a living analysis. Risk scores and priorities must be reviewed whenever the product, architecture, geographic catalogue, privacy model or release scope changes.

> **Document status:** Reviewed through AB-EV-032 and the AtlasBadge C34 Production baseline. C31 established the canonical `252 Places / 195 Countries / 57 Territories and Entities` model; C32 added read-only Profile map-to-flag navigation; C33 added non-persistent dashboard sorting; and C34 added owner-only manual visit-order correction while closing the P0 rapid-visit concurrency regression and preserving transactional birthplace integrity.

---

## 2. Assessment basis

This analysis is based on direct review of the deployed application, confirmed Product Owner/Test Lead rules, static code audit of authentication/profile/Firestore/cache/travel-map persistence, geographic catalogue decisions, automated regression, local manual QA and controlled Vercel Production validation.

An intended behaviour is not assumed to be implemented unless evidence supports it. Incomplete areas are classified explicitly as gaps, assumptions, future risks or accepted behaviour.

---

## 3. Confirmed product context

### 3.1 Authentication and account management

AtlasBadge supports Google authentication, e-mail/password authentication, mandatory e-mail verification, password recovery, a 15-character minimum password policy with valid passphrases, secure access-method linking while preserving identity/data, persistent sessions, unique case-insensitive usernames, username changes and account deletion.

A username is mandatory, has a minimum length of three characters and uses a shared trimmed-lowercase identity. The full allowed-character policy remains open. Previous usernames become immediately reusable and no historical alias/redirect is created; this is accepted behaviour.

### 3.2 Travel status model

AtlasBadge supports six travel statuses: **Visited**, **Lived / Live there**, **Born there**, **Nationality**, **Passed through** and **Want to visit**.

Multiple compatible statuses may coexist. Confirmed incompatible combinations are normalised by product rules. Selecting **Born there** or **Lived / Live there** automatically selects **Visited**. Selecting a physical-presence status incompatible with **Want to visit** automatically removes **Want to visit**.

### 3.3 Visits and memories

`visitsCount` has a minimum of zero. Selecting **Visited** or **Passed through** changes zero to one where applicable. Passed through supports detailed passage records and multiple occurrences.

**Total Visits** is the sum of active individual `visitsCount` values. A place counts once as conquered regardless of repeated visits. Memory text uses local draft state and persists only after explicit **Save**. Non-visit memories may use `generalNote` without an artificial registered visit or visit-count increase.

Current V1.0 behaviour preserves visit counts and user-created memories when **Visited** is deselected; they are hidden while inactive and restored when Visited returns.

AB-EV-032 extends the concurrency baseline for visit history. Rapid add/remove/save operations use replayable semantic intents; add uses a stable idempotent visit ID, remove is idempotent by visit ID, save updates the target visit on the latest state, and `visitsCount` is derived from `registeredVisits.length`. Rapid `+++`, `+++++` and removal flows are under permanent regression coverage.

### 3.4 Persistence and data model

Cloud Firestore is the primary source of truth for authenticated users. Travel data is stored under `users/{uid}/places/{placeId}` and may contain statuses, visitsCount, registeredVisits, generalNote and the optional C34 `visitOrderRank` used for manual ordering of qualifying physical-presence places.

The UID-scoped browser cache is `atlasbadge_local_data_{uid}`. Registered-place changes render optimistically in React while durable cache state is updated after Firestore confirmation. Rejected writes restore confirmed state.

Authenticated place data uses a real-time Firestore subscription and OCC based on confirmed `updatedAt`. Explicit status/visit intents preserve latest-valid-local-intent semantics. AB-EV-013, AB-EV-018, AB-EV-019, AB-EV-022, AB-EV-026 and AB-EV-032 provide the main concurrency/cache-authority evidence.

C34 made the persistence callback intent-aware. Rapid visit mutations may use the validated fast persistence path, while any `setStatus` intent for **Born there** disables that bypass and uses the normal transaction so `users/{uid}.birthplacePlaceId` and `users/{uid}/places/{placeId}.statuses.born` remain atomic. Firestore Rules retain the birthplace-consistency invariant and validate `visitOrderRank` as an optional integer `>= 1`.

### 3.5 Public profile and privacy

A new profile is private by default and can be changed by the user. Private profiles must not expose map, statistics or private content.

A public profile may display public display name, username, biography, social links, map/status colours, progress counters, total visits, continent progress, conquered flags and achievements. Memories and visit notes remain private.

The public profile remains read-only.

AB-EV-028 confirms canonical `AtlasWorldMapV2` reuse in read-only mode with zoom, pan, wheel zoom, reset, markers and responsive behaviour.

AB-EV-030 extends this baseline: clicking a conquered place in the Profile map scrolls to and temporarily highlights the matching earned-flag card by canonical `countryId`. Normal geographies, micro-markers, alphabetical order, visit order and a mobile viewport were covered. The interaction does not open edit controls, mutate status/visits, change counters or write travel data.

C34 preserves this boundary explicitly: Manual Visit Order editing, Save/Cancel and drag handles exist only on the authenticated Map tab. The public Profile may display Alphabetical or Visit Order presentation but exposes no manual-order editing capability.

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

The United Kingdom aggregate is non-selectable, map-only/derived and receives no persisted travel status.

The approved conceptual progress model is:

```text
252 Places
195 Countries
57 Territories and Entities
```

The relationship is intentionally defined as:

```text
Places = Countries + Territories and Entities
252 = 195 + 57
```

#### Countries

```text
194 directly selectable sovereign countries
+ 1 derived United Kingdom
= 195 Countries
```

The derived UK is credited only when England, Scotland, Wales and Northern Ireland all have qualifying physical presence.

#### Territories and Entities

```text
47 territories
+ 5 limited-recognition entities
+ 4 UK constituent countries
+ 1 Antarctica special region
= 57 Territories and Entities
```

Each UK constituent independently contributes one Place and one Territory/Entity. Completing all four additionally contributes one derived UK Country and one derived Place.

Antarctica contributes one Place and one Territory/Entity, but no Country.

AB-EV-027 preserves the historical post-C26 observation that exposed the previous inconsistency. AB-EV-029 records the C31 catalogue audit, correction, explicit invariants, achievement alignment and Production closure. QR-25 therefore moved from **Current gap** to **Regression risk**.

The public continent model continues to use eight display groups: South America, Central America, North America, Antarctica, Africa, Asia, Europe and Oceania.

### 3.7 Achievements

Achievements use canonical persisted acquisition metadata with sequence and relock/reconquest behaviour protected by AB-EV-023, AB-EV-024 and AB-EV-026.

C31 aligned the affected geographic achievements to the canonical model:

- **A15 — United Kingdom:** requires the four UK constituent countries (`4/4`);
- **A18 — Lenda Atlas:** requires all `252/252 Places`;
- **A31 — Mundo Completo:** requires `195/195 Countries` and does not require all territories/entities;
- **A32 — Além das Fronteiras:** requires `57/57 Territories and Entities`, including Antarctica.

### 3.8 Compatibility, responsive and accessibility baseline

Manual and automated evidence includes Edge/Windows, Chrome/Android, desktop/mobile responsive matrices, touch contexts, constrained-device checks and a scoped WCAG 2.2 AA technical baseline.

AB-EV-020 covers responsive navigation/paint stability. AB-EV-025 covers Badge Unlock visual polish. AB-EV-028 covers map/profile surface and micro-marker parity. AB-EV-030 adds mobile Profile map-to-flag navigation. AB-EV-031 adds responsive dashboard sorting, and AB-EV-032 adds mobile/read-only Manual Visit Order coverage plus keyboard-capable drag handles and rapid-mutation regression.

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
| Current gap | A known limitation, inconsistency or unsafe behaviour exists in the current product. |
| Regression risk | The feature exists but failure has material consequences and requires continued coverage. |
| Future risk | Applies to a planned feature and must be reassessed before implementation/release. |
| Assessment gap | Evidence is insufficient to claim adequate coverage. |
| Accepted behaviour | Intentionally permitted behaviour whose consequences still require testing/communication/acceptance. |

---

## 5. Quality risk register

### 5.1 Data integrity, persistence and privacy

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-01 | A Firestore write may fail while the interface and local cache continue to show the change as saved, causing silent data loss in a later session. | Current gap | 5 | 3 | 15 | High |
| QR-02 | Visit-history preservation may regress when **Visited** is deselected and later restored, causing counts or user-created memories to be lost. | Regression risk | 4 | 4 | 16 | High |
| QR-03 | Explicit logout may fail to remove UID-scoped private travel data from browser storage on a shared computer. | Regression risk | 4 | 3 | 12 | High |
| QR-04 | Real-time listener, confirmed-state reconciliation or optimistic concurrency control may regress, allowing stale tabs/devices to lose confirmed travel-data changes. | Regression risk | 4 | 2 | 8 | Medium |
| QR-05 | A memory editor may bypass the explicit-Save contract and reintroduce persistence on every keystroke. | Regression risk | 3 | 4 | 12 | High |
| QR-06 | Approved character-limit enforcement may regress across notes/profile content. | Regression risk | 3 | 4 | 12 | High |
| QR-07 | Account deletion may partially fail and leave authentication records, user data, reserved usernames, public-profile data or orphaned records. | Regression risk | 5 | 3 | 15 | High |

**Applied decisions:** QR-02 is protected by AB-EV-002; QR-03 by AB-EV-003; QR-04 by AB-EV-013/018/019/022/026/032; QR-05 by AB-EV-004; QR-06 by AB-EV-011; QR-07 by AB-EV-010. AB-EV-032 extends QR-01 evidence for manual-order and rapid-visit persistence, but QR-01 remains Current gap outside the specifically validated persistence paths.

### 5.2 Authentication and account identity

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-08 | The approved 15-character minimum or passphrase-compatible password policy may regress or become inconsistent across password flows. | Regression risk | 4 | 3 | 12 | High |
| QR-09 | Linking Google and password methods may create/use a different UID, making existing data appear lost. | Mitigated | 5 | 2 | 10 | Medium |
| QR-10 | An access method may be linked to the wrong identity if matching/reauth/cancellation/rollback fails. | Mitigated | 5 | 2 | 10 | Medium |
| QR-11 | Shared username normalisation, case-insensitive reservation or canonical public-profile resolution may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-12 | Concurrent username reservations may leave `users` and `usernames` inconsistent. | Regression risk | 3 | 2 | 6 | Medium |
| QR-13 | A released username may be registered by another person, causing old links to resolve to a new owner. | Accepted behaviour | 4 | 3 | 12 | High |
| QR-14 | A persistent session may expose an account on a shared device when the user does not explicitly log out. | Regression risk | 4 | 2 | 8 | Medium |
| QR-15 | E-mail may be verified while stale auth state continues blocking application access. | Regression risk | 4 | 3 | 12 | High |

**Applied decisions:** QR-08 AB-EV-012; QR-09 AB-EV-005; QR-10 AB-EV-007; QR-11 AB-EV-014; QR-12 affected-area coverage through AB-EV-014/015; QR-13 accepted through AB-EV-015.

### 5.3 Travel statuses, counters and calculations

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-16 | The application may accept prohibited status combinations or remove combinations that are valid. | Regression risk | 3 | 4 | 12 | High |
| QR-17 | **Want to visit** may remain selected when an incompatible physical-presence status is applied. | Regression risk | 3 | 3 | 9 | Medium |
| QR-18 | Global country, territory, continent or visit totals may not recalculate correctly after status changes. | Regression risk | 4 | 4 | 16 | High |
| QR-19 | Global **Total Visits** may diverge from the sum of individual `visitsCount` values. | Regression risk | 4 | 3 | 12 | High |
| QR-20 | **Nationality** or **Want to visit** may incorrectly increase physical-presence metrics when used without a qualifying status. | Regression risk | 3 | 3 | 9 | Medium |
| QR-21 | A place with multiple physical statuses may be counted more than once as a conquered place. | Regression risk | 4 | 3 | 12 | High |
| QR-22 | Automatic status transitions may incorrectly reset visit counts or delete memories. | Regression risk | 4 | 3 | 12 | High |
| QR-23 | **Born there** or **Lived / Live there** may fail to select **Visited** and initialise visit count. | Regression risk | 3 | 3 | 9 | Medium |
| QR-24 | The approved **Passed through** workflow may regress, causing passage records, memories, transitions or metrics to become inconsistent. | Regression risk | 2 | 4 | 8 | Medium |

**Applied decisions:** QR-16/17 remain covered by integrated/Production transition validation; QR-18 receives canonical counter coverage through AB-EV-029 and presentation non-mutation coverage through AB-EV-031; QR-18/19/22/23 receive additional C34 rapid-visit and birthplace integrity regression through AB-EV-032; QR-24 is protected by AB-EV-016.

### 5.4 Geographic catalogue, map and achievements

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-25 | The `252 Places`, `195 Countries`, `57 Territories and Entities`, `251 directly selectable records` and `8 continent display groups` may diverge or be represented inconsistently across screens/statistics. | Regression risk | 4 | 3 | 12 | High |
| QR-26 | A selectable place may be missing, mapped to the wrong ID, unclickable, incorrectly coloured or persisted under an unsupported record. | Regression risk | 4 | 3 | 12 | High |
| QR-27 | Technical/non-selectable geographic records may create invalid Firestore data or duplicate progress. | Regression risk | 4 | 2 | 8 | Medium |
| QR-28 | Map intensity may be wrong when multiple statuses are present and priority is inconsistent. | Regression risk | 3 | 3 | 9 | Medium |
| QR-29 | The **United Kingdom** achievement may be calculated without all four constituents or incorrectly rely on technical `gb`. | Regression risk | 3 | 3 | 9 | Medium |
| QR-30 | Achievement lock/relock, persisted metadata, chronology, reconquest sequence or notification delivery may become inconsistent between screens/sessions/reconcilers. | Regression risk | 3 | 3 | 9 | Medium |

**Applied geographic and achievement decisions:**

- **QR-25 — Regression risk:** AB-EV-027 records the historical observation that reopened the arithmetic question. AB-EV-029 closes the gap with the audited `252/195/57` model, explicit UK/Antarctica rules, full-completion assertions, achievement alignment and Production validation.
- **QR-26 — Regression risk:** AB-EV-028 confirms canonical map reuse and micro-marker behaviour; AB-EV-030 adds deterministic Profile map-to-flag targeting.
- **QR-29 — Regression risk:** AB-EV-023 protects World Complete/UK semantics; AB-EV-027 confirms all four constituents; AB-EV-029 aligns derived UK country/place contribution with the canonical counters.
- **QR-30 — Regression risk:** AB-EV-023/024 protect chronology/notification; AB-EV-026 protects Clear Map reconciliation; AB-EV-029 updates A18/A31/A32 thresholds to the canonical geographic universes.

### 5.5 Public profile and sharing

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-31 | A private profile may expose identity, map, statistics, travel statuses or other data to an unauthenticated visitor. | Regression risk | 5 | 2 | 10 | Medium |
| QR-32 | Private memories/notes may be exposed publicly or returned to an unauthorised browser. | Regression risk | 5 | 2 | 10 | Medium |
| QR-33 | A public-profile component may allow an editing action or unauthorised data modification. | Regression risk | 5 | 2 | 10 | Medium |
| QR-34 | Changing a profile from public to private may not hide previously visible content immediately. | Regression risk | 4 | 3 | 12 | High |
| QR-35 | Social-link validation may permit an unsafe or unexpected destination. | Regression risk | 4 | 2 | 8 | Medium |
| QR-36 | Future per-memory visibility/default logic may publish content contrary to user preference. | Future risk | 5 | 3 | 15 | High |
| QR-37 | A future generated Story may expose unexpected information or fail differently across sharing flows. | Future risk | 4 | 3 | 12 | High |

**Applied decision:** AB-EV-028 confirms the canonical Profile map is read-only. AB-EV-030 confirms map-to-earned-flag navigation remains read-only, including no-flag fallback and no status/visit mutation. AB-EV-032 confirms Manual Visit Order editing exists only on the owner Map tab; the Profile exposes no Edit, Save, Cancel or drag controls.

### 5.6 Compatibility, usability, performance and accessibility

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-38 | Behaviour may differ on Firefox, Safari, iPhone, tablets, macOS or other untested combinations. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-39 | Responsive/touch/constrained-device/CSS/navigation/card-paint or map-layout baselines may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-40 | Keyboard access, focus, accessible names, dialogs, contrast, animation preference or non-colour identification may regress. | Regression risk | 4 | 3 | 12 | High |

**Applied decisions:** QR-38 remains an Assessment gap. QR-39 is protected by AB-EV-018/020/025/028/030 plus C33 responsive sorting in AB-EV-031 and C34 mobile/manual-order regression in AB-EV-032. QR-40 is protected by AB-EV-017 and affected interaction regression; C33/C34 add deterministic controls and keyboard-capable ordering coverage without claiming formal accessibility certification.

---

## 6. Highest-priority test focus

Priority focus includes silent persistence failure; visit-history preservation; account-deletion integrity; status-rule integrity; **252/195/57 counter integrity**; geographic catalogue integrity; privacy transitions; explicit logout/local-data exposure; explicit-Save integrity; real-time concurrency/cache authority; rapid visit add/remove convergence; manual visit-order persistence; birthplace pointer/status atomicity; password policy; Passed-through workflow; responsive/constrained-device behaviour; achievement chronology/notification integrity; Profile read-only interaction; and accessibility.

QR-25 is no longer an open arithmetic investigation. Its residual concern is regression: future catalogue/classification, counter, percentage or achievement changes must preserve the approved model unless the Product Owner/Test Lead explicitly changes the rule and rebaselines dependent tests/documentation.

---

## 7. Important rules that must not be reported as defects without new product evidence

- a place is counted once as conquered even when it has multiple compatible statuses;
- repeated visits increase Total Visits but do not create additional directly selectable records;
- detailed-memory count may be lower than `visitsCount`;
- Nationality and Want to visit alone do not represent physical presence;
- Passed through counts as physical presence and may contain multiple registered passages;
- achievements relock when criteria are no longer met;
- relock removes current acquisition metadata and reconquest creates a new acquisition sequence;
- the public continent model uses eight display groups including Antarctica;
- 251 records are directly selectable, while the conceptual Places counter totals 252 because completing the four UK constituents derives one additional UK Place;
- the four UK constituent nations are independently selectable and each belongs to Territory/Entity progress;
- completing all four UK constituents derives one United Kingdom Country and one United Kingdom Place without persisting a separate selectable `gb` status;
- Antarctica belongs to Places and Territories/Entities, but not Countries;
- the canonical counter invariant is `252 Places = 195 Countries + 57 Territories and Entities`;
- the public profile is read-only; Profile map clicks may navigate to matching earned flags but must not mutate travel data;
- Manual Visit Order editing exists only on the owner Map tab;
- Wishlist-only and Nationality-only records do not participate in Manual Visit Order because they do not represent physical presence;
- the Born there location remains fixed at the top of Manual Visit Order and its user pointer/status must remain transactionally consistent;
- memories are currently private.

---

## 8. Assumptions requiring validation

1. Public-to-private profile changes invalidate previously visible/cached content immediately.
2. Social-link validation rejects unsafe protocols and unsuitable formats.
3. The geographic fixture and deployed application continue to contain the same 251 directly selectable records.
4. Responsive behaviour remains acceptable outside the validated Edge/Windows and Chrome/Android evidence.
5. Map-status priority remains identical across map, profile, cards, legends and generated assets.
6. Future geographic catalogue changes will trigger explicit re-audit of the `252/195/57` model rather than silently changing a denominator or derived rule.

---

## 9. Open product and quality questions

Resolved V1.0 decisions must not be reopened without new evidence: explicit Save for memories, character-limit policy, retry-safe account deletion, real-time/OCC controls, 15-character password minimum/passphrases, canonical lowercase usernames, immediate username reuse, Passed-through detailed workflow, accessibility baseline, responsive baseline, single-intent persistence, responsive navigation/card-paint stability, integrated release hardening, last-intent/activation chronology, achievement chronology/World Complete/notification logic, Production runner safety, Badge Unlock visual polish, Clear Map confirmed-snapshot reconciliation protection, C27–C30 map/profile parity, **C31 counter integrity**, **C32 Profile map-to-flag navigation**, **C33 dashboard sorting** and **C34 Manual Visit Order / rapid-visit concurrency / birthplace integrity**.

The previous QR-25 question about `251/195/56` is closed. The approved model is `251 directly selectable records`, `252 conceptual Places`, `195 Countries` and `57 Territories and Entities`.

Other open questions remain around username allowed characters, future memory visibility, broader browser/device support, native assistive-technology coverage, localisation completion and future Story-sharing scope/content/review.

---

## 10. Risk-based release implications

Release confidence requires evidence that travel data is not silently lost; private content is not exposed; account deletion/linking preserve identity/integrity; status transitions follow rules; progress values remain mathematically consistent; the geographic catalogue uses approved records/identifiers; public profiles remain read-only; known gaps are resolved or explicitly accepted; and supported compatibility/accessibility baselines are defined.

Any unresolved High risk must be reviewed before release and recorded as mitigated, accepted, reduced by control, deferred with limitation/follow-up, or a release blocker.

---

## 11. Review triggers

Review this analysis when travel statuses/combinations, geographic catalogue/classification, map implementation/data source, privacy, save/limit behaviour, auth/linking/password rules, account deletion, real-time/OCC/offline behaviour, `visitOrderRank` semantics, manual ordering, birthplace transaction logic, sharing, supported browsers/devices/accessibility targets change, or when Production evidence reveals a new failure mode.

A change to UK derivation, Antarctica classification, any of the `252/195/57` denominators, A15/A18/A31/A32 criteria, Profile map interaction, manual visit-order persistence or rapid mutation orchestration requires explicit affected-area regression and documentation review.

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
- `evidence/v1.0/defects/ab-ev-029-geographic-counter-integrity-252-195-57.md`
- `evidence/v1.0/smoke/ab-ev-030-profile-map-to-earned-flag-navigation.md`
- `evidence/v1.0/regression/ab-ev-031-dashboard-selected-place-sorting.md`
- `evidence/v1.0/defects/ab-ev-032-manual-visit-order-and-rapid-visit-concurrency.md`
- `test-assets/exploratory-test-charters.md`
- `test-assets/sample-test-cases.md`
- `reports/test-summary-report.md`
