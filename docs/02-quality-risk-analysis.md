# AtlasBadge Quality Risk Analysis

## 1. Document purpose

This document identifies, evaluates and prioritises the main quality risks associated with AtlasBadge. It supports risk-based test planning by showing what could fail, why it matters, the realistic likelihood, the required coverage and any unresolved product or quality decisions.

This is a living analysis. Risk scores and priorities must be reviewed whenever the product, architecture, geographic catalogue, privacy model or release scope changes.

> **Document status:** Reviewed through AB-EV-028 and the AtlasBadge C30 Production baseline. C26–C30 passed their intended Production validation, while a later `9 places / 5 countries / 5 territories/entities` observation reopened QR-25 for a new cross-counter and catalogue-integrity investigation.

---

## 2. Assessment basis

This analysis is based on direct review of the deployed application, confirmed Product Owner rules, static code audit of authentication/profile/Firestore/cache/travel-map persistence, the current backlog and geographic catalogue decisions, and observed behaviour on Microsoft Edge on Windows and Google Chrome on Android.

An intended behaviour is not assumed to be implemented unless evidence supports it. Incomplete areas are classified explicitly as gaps, assumptions, future risks or accepted behaviour.

---

## 3. Confirmed product context

### 3.1 Authentication and account management

AtlasBadge supports Google authentication, e-mail/password authentication, mandatory e-mail verification, password recovery, a 15-character minimum password policy with valid passphrases, secure access-method linking while preserving identity/data, persistent sessions, unique case-insensitive usernames, username changes and account deletion.

A username is mandatory, has a minimum length of three characters and uses a shared trimmed-lowercase identity. The full allowed-character policy remains open. Previous usernames become immediately reusable and no historical alias/redirect is created; this is accepted behaviour.

### 3.2 Travel status model

AtlasBadge supports six travel statuses: **Visited**, **Lived / Live there**, **Born there**, **Nationality**, **Passed through** and **Want to visit**.

Multiple compatible statuses may coexist. Confirmed compatible examples include Visited + Lived, Visited + Born, Visited + Nationality, Lived + Born, Lived + Nationality, Nationality + Passed through, Want to visit + Nationality and Want to visit + Passed through.

Confirmed incompatible examples include Visited + Passed through, Lived + Passed through, Born + Nationality, Born + Passed through, Want to visit + Visited, Want to visit + Lived and Want to visit + Born.

Selecting **Born there** or **Lived / Live there** automatically selects **Visited**. Selecting a physical-presence status incompatible with **Want to visit** automatically removes **Want to visit**.

### 3.3 Visits and memories

`visitsCount` has a minimum of zero. Selecting **Visited** or **Passed through** changes zero to one; Passed through creates the first physical passage record. Detailed visit controls support both statuses and multiple Passed-through occurrences.

**Total Visits** is the sum of individual `visitsCount` values. A place counts once as conquered regardless of repeated visits. Detailed memory count does not need to equal `visitsCount`. Memory text uses local draft state and persists only after explicit **Save**. Non-visit memories may use `generalNote` without an artificial registered visit or visit-count increase.

Current V1.0 behaviour preserves visit counts and user-created memories when **Visited** is deselected; they are hidden while inactive and restored when Visited returns.

### 3.4 Persistence and data model

Cloud Firestore is the primary source of truth for authenticated users. Travel data is stored under `users/{uid}/places/{placeId}` and may contain statuses, visitsCount, registeredVisits and generalNote.

The UID-scoped browser cache is `atlasbadge_local_data_{uid}`. It is a cache/fallback for authenticated travel data and a primary store for unauthenticated demonstration data.

Registered-place changes render optimistically in React while durable cache state is updated only after Firestore confirmation. Rejected writes restore the last confirmed state. AB-EV-008 and AB-EV-009 cover the map-status/visit path; equivalent failure/reload/recovery coverage for every persistence flow remains incomplete under QR-01.

Authenticated place data uses a real-time Firestore subscription and OCC based on confirmed `updatedAt`. Explicit idempotent status intents preserve latest-valid-local-intent semantics. Activation chronology is stored independently of the visible status object. AB-EV-013, AB-EV-018, AB-EV-019 and AB-EV-022 cover listener, OCC, rapid mutation and chronology behaviour.

AB-EV-026 adds a further authority rule: cache-only startup snapshots may hydrate local/UI state but are not authoritative inputs for persistent achievement-metadata reconciliation. Persistent/destructive reconciliation waits for server-confirmed snapshot authority. This prevents a stale/empty cache from queuing phantom delete/recreate work during Clear Map. The exact known Firestore `FAILED_PRECONDITION` base-version contention is qualified by a fail-closed Production harness and remains observable rather than silently ignored.

### 3.5 Public profile and privacy

A new profile is private by default and can be changed by the user. Private profiles must not expose map, statistics or private content.

A public profile may display public display name, username, biography, social links, map/status colours, country/territory progress, total visits, continent progress, conquered flags and achievements. Memories and visit notes remain private.

The public profile is read-only. AB-EV-028 confirms that the Profile now reuses the canonical `AtlasWorldMapV2` in read-only mode with zoom, pan, wheel zoom, reset, marker rendering and responsive behaviour, but without country mutation or Clear Map.

### 3.6 Geographic catalogue and progress model

AtlasBadge currently exposes **251 selectable places** and public counters using **195 conceptual countries** and **56 territories and entities**.

The internal model distinguishes selectable sovereign countries, four UK constituent nations, territories, limited-recognition/de facto entities, special geographic records and Antarctica. The United Kingdom aggregate is non-selectable and receives no persisted travel status. England, Scotland, Wales and Northern Ireland are individually selectable.

C26/AB-EV-027 confirmed the specific implemented rule that each UK constituent contributes individually to the territory/entity counter and that all four together additionally credit the conceptual United Kingdom country and UK achievement.

A later Production observation showed `9/251 places`, `5/195 countries` and `5/56 territories/entities` for nine conquered selectable places. Therefore the global relationship between the 251-, 195- and 56-item universes is **not considered mathematically closed**. QR-25 is a Current gap until a dataset audit proves classification, overlap/partition semantics and the exact expected counters at full completion.

The public continent model uses eight display groups: South America, Central America, North America, Antarctica, Africa, Asia, Europe and Oceania. The same taxonomy must remain consistent across map/profile/progress cards.

### 3.7 Achievements

Achievements are dynamic with canonical persisted current-acquisition metadata. Earned state stores `unlockedAt` plus a per-user monotonic `sequence`; `nextAchievementUnlockSequence` is not reduced by relocking. Relock removes the current metadata entry and reconquest receives a new timestamp/sequence. Historical achievements are not replayed on login/reload, and locally caused notifications are deduplicated by user, achievement and sequence.

The UK achievement requires England, Scotland, Wales and Northern Ireland. **Mundo Completo** treats the non-selectable UK aggregate as satisfied only when all four constituents are complete. Achievement chronology/notification evidence is retained through AB-EV-023 and AB-EV-024. AB-EV-026 adds the Clear Map reconciliation-race closure.

### 3.8 Compatibility, responsive and visual baseline

Manual evidence covers Edge/Windows and Chrome/Android, including portrait/landscape, touch and cache-sensitive Production retests. Automated coverage includes responsive/reflow matrices from 320×568 upward, touch contexts, constrained network/CPU scenarios, production CSS checks and a WCAG 2.2 AA technical baseline over critical UI states.

AB-EV-020 covers responsive navigation and paint stability. AB-EV-025 covers the final Badge Unlock surface/golden accent. AB-EV-028 adds canonical map reuse, micro-marker zoom scaling, Profile read-only behaviour, shared desktop content width and map-surface parity.

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

**Applied decisions:** QR-02 is protected by AB-EV-002; QR-03 by AB-EV-003; QR-04 by AB-EV-013/018/019/022 plus cache-authority/reconciliation protection in AB-EV-026; QR-05 by AB-EV-004; QR-06 by AB-EV-011; QR-07 by AB-EV-010. QR-01 remains Current gap outside the specifically validated persistence paths.

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

**Applied decisions:** QR-16/17 remain covered by integrated and Production transition validation; QR-18/19 remain regression risks and are additionally affected by the QR-25 cross-counter investigation; QR-24 is protected by AB-EV-016.

### 5.4 Geographic catalogue, map and achievements

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-25 | The `195 countries`, `251 selectable places`, `56 territories and entities`, and `8 continent display groups` may be represented or related inconsistently across screens/statistics. | Current gap | 4 | 3 | 12 | High |
| QR-26 | A selectable place may be missing, mapped to the wrong ID, unclickable, incorrectly coloured or persisted under an unsupported record. | Regression risk | 4 | 3 | 12 | High |
| QR-27 | Technical/non-selectable geographic records may create invalid Firestore data or duplicate progress. | Regression risk | 4 | 2 | 8 | Medium |
| QR-28 | Map intensity may be wrong when multiple statuses are present and priority is inconsistent. | Regression risk | 3 | 3 | 9 | Medium |
| QR-29 | The **United Kingdom** achievement may be calculated without all four constituents or incorrectly rely on technical `gb`. | Regression risk | 3 | 3 | 9 | Medium |
| QR-30 | Achievement lock/relock, persisted metadata, chronology, reconquest sequence or notification delivery may become inconsistent between screens/sessions/reconcilers. | Regression risk | 3 | 3 | 9 | Medium |

**Applied geographic and achievement decisions:**

- **QR-25 — Current gap:** AB-EV-027 confirms the C26-specific UK rule but also records the later `9 places / 5 countries / 5 entities` Production observation. The exact mathematical relationship of the 251/195/56 universes must be re-audited before closure.
- **QR-26 — Regression risk:** AB-EV-028 confirms canonical map reuse, geographic anchoring, micro-marker zoom scaling and interaction.
- **QR-29 — Regression risk:** AB-EV-023 protects World Complete/UK semantics; AB-EV-027 confirms all four constituents are required for the UK country/achievement while each contributes to the entity counter.
- **QR-30 — Regression risk:** AB-EV-023/024 protect chronology and notification behaviour; AB-EV-026 closes the observed Clear Map metadata-reconciliation race.

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

**Applied decision:** AB-EV-028 confirms the canonical Profile map is read-only and does not expose country mutation or Clear Map actions, strengthening QR-33 regression coverage.

### 5.6 Compatibility, usability, performance and accessibility

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-38 | Behaviour may differ on Firefox, Safari, iPhone, tablets, macOS or other untested combinations. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-39 | Responsive/touch/constrained-device/CSS/navigation/card-paint or map-layout baselines may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-40 | Keyboard access, focus, accessible names, dialogs, contrast, animation preference or non-colour identification may regress. | Regression risk | 4 | 3 | 12 | High |

**Applied decisions:** QR-38 remains an Assessment gap. QR-39 is protected by AB-EV-018 and AB-EV-020, with Badge surface evidence in AB-EV-025 and current map/profile width/surface/zoom parity in AB-EV-028. QR-40 is protected by AB-EV-017 and related responsive/visual regression evidence.

---

## 6. Highest-priority test focus

Priority focus includes silent persistence failure; visit-history preservation; account-deletion integrity; status-rule integrity; progress/counter integrity; geographic catalogue integrity; privacy transitions; explicit logout/local-data exposure; explicit-Save integrity; real-time concurrency and cache authority; password policy; Passed-through workflow; responsive/constrained-device behaviour; achievement chronology/notification integrity; and accessibility.

The immediate geographic focus is QR-25: audit the dataset and prove how 251 selectable places, 195 conceptual countries and 56 territories/entities relate, including UK constituents and any special/limited-recognition records, before changing arithmetic again.

---

## 7. Important rules that must not be reported as defects without new product evidence

- a place is counted once as conquered even when it has multiple compatible statuses;
- repeated visits increase Total Visits but do not create additional conquered places;
- detailed-memory count may be lower than `visitsCount`;
- Nationality and Want to visit alone do not represent physical presence;
- Passed through counts as physical presence and may contain multiple registered passages;
- achievements relock when criteria are no longer met;
- relock removes current acquisition metadata and reconquest creates a new acquisition sequence;
- the public continent model uses eight display groups including Antarctica;
- the four UK constituent nations are separate selectable records and the technical UK geometry receives no persisted travel status;
- the current C26 implementation counts each UK constituent in the territory/entity counter and all four together additionally credit the UK country/achievement; the *global arithmetic consequence of this rule is under QR-25 investigation and is not yet a closed mathematical assumption*;
- the public profile is read-only;
- memories are currently private.

---

## 8. Assumptions requiring validation

1. Public-to-private profile changes invalidate previously visible/cached content immediately.
2. Social-link validation rejects unsafe protocols and unsuitable formats.
3. The geographic fixture and deployed application contain the same selectable records.
4. Responsive behaviour remains acceptable outside Edge/Windows and Chrome/Android evidence.
5. Map-status priority is applied identically across map, profile, cards, legends and generated assets.
6. The exact relationship between 251 selectable places, 195 conceptual countries and 56 territories/entities — including whether any categories overlap or leave selectable places outside either counter — remains to be proven after the post-C26 Production observation.

---

## 9. Open product and quality questions

Resolved V1.0 decisions must not be reopened without new evidence: explicit Save for memories, character-limit policy, retry-safe account deletion, real-time/OCC controls, 15-character password minimum/passphrases, canonical lowercase usernames, immediate username reuse, Passed-through detailed workflow, accessibility baseline, responsive baseline, single-intent persistence, responsive navigation/card-paint stability, integrated release hardening, last-intent/activation chronology, achievement chronology/World Complete/notification logic, Production runner safety, Badge Unlock visual polish, Clear Map confirmed-snapshot reconciliation protection and C27–C30 map/profile parity.

**Current reopened question:** What is the canonical mathematical relationship between the 251 selectable places, 195 conceptual countries and 56 territories/entities? The next investigation must prove classification and expected full-completion values before QR-25 can return to Regression risk.

Other open questions remain around username allowed characters, future memory visibility, broader browser/device support, native assistive-technology coverage, and future Story-sharing scope/content/review.

---

## 10. Risk-based release implications

Release confidence requires evidence that travel data is not silently lost; private content is not exposed; account deletion/linking preserve identity/integrity; status transitions follow rules; progress values are mathematically consistent; the geographic catalogue uses approved records/identifiers; public profiles are read-only; known gaps are resolved or explicitly accepted; and supported compatibility/accessibility baselines are defined.

Any unresolved High risk must be reviewed before release and recorded as mitigated, accepted, reduced by control, deferred with limitation/follow-up, or a release blocker.

---

## 11. Review triggers

Review this analysis when travel statuses/combinations, geographic catalogue/classification, map implementation/data source, privacy, save/limit behaviour, auth/linking/password rules, account deletion, real-time/OCC/offline behaviour, sharing, supported browsers/devices/accessibility targets change, or when Production evidence reveals a new failure mode.

---

## 12. Related portfolio documents

- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `test-assets/exploratory-test-charters.md`
- `test-assets/sample-test-cases.md`
- `reports/test-summary-report.md`
