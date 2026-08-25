# AtlasBadge Quality Risk Analysis

## 1. Document purpose

This document identifies, evaluates and prioritises the main quality risks associated with AtlasBadge. It supports risk-based test planning by showing what could fail, why it matters, the realistic likelihood, the required coverage and the residual risk that must remain visible after a correction is approved.

This is a living analysis. Risk scores and priorities must be reviewed whenever the product, architecture, geographic catalogue, privacy model or release scope changes.

> **Document status:** Reviewed through AB-EV-037. AB-EV-034 closed the remaining QR-01 failed-write/recovery coverage gap; C35/AB-EV-035 corrected the status model so Visited and Passed through may coexist; C36/AB-EV-036 closes AB-DEF-017 by replacing independent Wishlist settings commits/per-place order amplification with one atomic root-order settings Save; C37/AB-EV-037 closes AB-DEF-018 by replacing split Clear Map commits with one <=253-write logical reset plus generation-based public invalidation, aligned Rules and Production validation.

---

## 2. Assessment basis

This analysis is based on direct review of the deployed application, confirmed Product Owner/Test Lead rules, static implementation review, Firestore/Rules validation, Firebase Emulator regression, Playwright browser regression, local/manual QA, controlled release operations and Vercel/Firebase Production verification.

An intended behaviour is not assumed to be implemented unless evidence supports it. Incomplete areas are classified explicitly as gaps, assumptions, future risks or accepted behaviour.

---

## 3. Confirmed product context

### 3.1 Authentication and account management

AtlasBadge supports Google authentication, e-mail/password authentication, mandatory e-mail verification, password recovery, a 15-character minimum password policy with valid passphrases, secure access-method linking while preserving identity/data, persistent sessions, unique case-insensitive usernames, username changes and account deletion.

A username is mandatory, has a minimum length of three characters and uses a shared trimmed-lowercase identity. Previous usernames become immediately reusable and no historical alias/redirect is created; this is accepted behaviour.

### 3.2 Travel status and Wishlist model

AtlasBadge supports six travel statuses: **Visited**, **Lived / Live there**, **Born there**, **Nationality**, **Passed through** and **Want to visit**.

The Wishlist uses `statuses.wishlist === true` / Want to visit as its membership source of truth rather than maintaining a duplicate status model.

Multiple compatible statuses may coexist. **Born there** and **Lived / Live there** imply **Visited**. C35 establishes that **Passed through is cumulative historical information and may coexist with Visited, Lived or Born**. A person may have passed through a place during one occurrence and visited/lived there during another.

Selecting a proper physical-presence status continues to remove **Want to visit** according to the approved compatibility rules. Passed through + Wishlist may remain valid when no incompatible visited/lived/born state is active.

Wishlist-only records do not represent physical presence, do not increase physical metrics and do not enter Manual Visit Order.

C36 establishes the canonical Wishlist ordering model:

- membership remains `statuses.wishlist` on place records;
- presentation order is root `wishlistOrder: string[]` on `users/{uid}`;
- while the Wishlist is public, sanitised root order is projected to `publicProfiles/{uid}.wishlistOrder`;
- legacy per-place `wishlistOrderRank` remains only a backward-compatible read fallback;
- order is independent from `visitOrderRank`;
- a user-visible Wishlist settings Save persists changed order/privacy/public cleanup or projection through one Firestore batch boundary.

### 3.3 Visits and memories

`RegisteredVisit` represents an individual travel occurrence; status flags represent accumulated place history. Adding a second compatible status does **not** create a new occurrence or increment `visitsCount` by itself.

Where applicable, the first physical-presence state initialises the first visit. Later occurrences are added through the visit workflow. `visitsCount` is derived from the active `registeredVisits` history rather than from the number of status flags.

**Total Visits** is the sum of active individual `visitsCount` values. A place counts once as conquered regardless of repeated visits or multiple compatible statuses.

Memory text uses local draft state and persists only after explicit **Save**. Non-visit memories may use `generalNote` without an artificial registered visit or visit-count increase. Detailed memories for physical occurrences remain associated with `RegisteredVisit`.

Current V1.0 behaviour preserves user-created visit history/memories across supported status transitions. AB-EV-032 protects replayable/idempotent rapid visit mutations; AB-EV-035 adds explicit protection that combining Visited + Passed through does not duplicate visits or memories.

### 3.4 Persistence and data model

Cloud Firestore is the primary source of truth for authenticated users.

Private state is stored under:

```text
users/{uid}
users/{uid}/places/{placeId}
```

The private root may contain Profile/lifecycle settings including `isWishlistPublic`, canonical `wishlistOrder` and `placesGeneration`. Place records contain status membership/history, visits and memories, plus optional owner ordering such as `visitOrderRank`; legacy `wishlistOrderRank` may remain only for compatibility.

Authenticated place data uses a real-time Firestore subscription and confirmed-state reconciliation. Explicit status/visit intents preserve latest-valid-local-intent semantics.

The main persistence/concurrency evidence includes AB-EV-013, AB-EV-018, AB-EV-019, AB-EV-022, AB-EV-026, AB-EV-032, AB-EV-033, AB-EV-034, AB-EV-036 and AB-EV-037.

AB-EV-034 completes the QR-01 write-path audit using architectural/risk equivalence and adds deterministic failed-write/recovery coverage for the remaining Profile `flagSortOrder` path.

AB-EV-036 records a true persistence defect: Wishlist order and privacy were previously committed independently behind one UI Save. Fault injection proved partial persistence. C36 replaces that model with one combined batch, adds a real Firestore Emulator rejected-batch proof and reduces the supported maximum from a possible 502 per-place order writes to no more than 253 writes for the largest privacy transition.

AB-EV-037 records a destructive persistence defect: the old Clear Map both failed to remove C36 root `wishlistOrder` and used separate public/private commit boundaries. C37 resets the logical private state in one WriteBatch, advances private/public `placesGeneration` atomically and keeps the supported maximum logical reset at 253 writes. Obsolete public child documents become non-current through Rules/query generation checks rather than being required inside the atomic boundary.

### 3.5 Public profile, Wishlist privacy and public projection

A new profile is private by default and can be changed by the user. Private profiles must not expose map, statistics or private content.

The public Profile remains read-only and is served from a sanitised projection:

```text
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

Non-owner and anonymous viewers do not read the private `users` source.

The approved public root whitelist includes the current presentation fields required by the implemented Profile, including:

```text
achievementMetadata
avatarUrl
bio
displayName
flagSortOrder
isPublic
isWishlistPublic
placesGeneration
socialLinks
uid
username
wishlistOrder   // only while Wishlist visibility permits public order
```

Public place projections must not expose `generalNote`, `registeredVisits`, memories/private visit details, `firstPhysicalPresenceAt`, `statusActivatedAt` or `visitsCount`.

Current-generation public place projections include `placesGeneration`. Existing legacy public roots/places without the field remain compatible as generation 0. Once a root is versioned, stale generation public-place reads are denied to non-owner/anonymous viewers and current public queries target the active generation.

Wishlist privacy defaults to private. A public Wishlist tile is rendered only when `isWishlistPublic` is true and Wishlist membership is non-empty. The tile/modal is read-only. Viewer-local presentation sorting is permitted but must not persist the owner's preference.

AB-EV-033 validates private→public and public→private Wishlist transitions, public-only cleanup, mixed-document sanitisation and zero private viewer reads in Production. AB-EV-036 extends that baseline by atomically coupling privacy/order changes, exposing root order only in the approved public state and validating the aligned Rules release in Production. AB-EV-037 extends the lifecycle boundary further: Clear Map invalidates all obsolete public travel-place generations atomically without requiring 251 public child deletes inside the logical reset.

### 3.6 Geographic catalogue and progress model

AtlasBadge contains **251 directly selectable geographic records**.

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

The United Kingdom aggregate is non-selectable and derived from its four constituent countries. Countries consist of 194 directly selectable sovereign countries plus the derived United Kingdom. Territories and Entities consist of 47 territories, 5 limited-recognition entities, 4 UK constituent countries and Antarctica.

AB-EV-027 preserves the historical observation that exposed the previous inconsistency. AB-EV-029 records the C31 catalogue audit, correction, achievement alignment and Production closure.

### 3.7 Achievements

Achievements use canonical persisted acquisition metadata with sequence and relock/reconquest behaviour protected by AB-EV-023, AB-EV-024 and AB-EV-026.

C31 aligned the affected geographic achievements:

- **A15 — United Kingdom:** four constituent countries (`4/4`);
- **A18 — Lenda Atlas:** `252/252 Places`;
- **A31 — Mundo Completo:** `195/195 Countries`;
- **A32 — Além das Fronteiras:** `57/57 Territories and Entities`.

AB-EV-033 closes the missing public achievement-metadata projection while exposing only `{unlockedAt, sequence}` per achievement entry.

### 3.8 Compatibility, responsive and accessibility baseline

Manual and automated evidence includes Edge/Windows, Chrome/Android, desktop/mobile responsive matrices, touch contexts, constrained-device checks and a scoped WCAG 2.2 AA technical baseline.

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

`Risk score = Impact × Likelihood`

| Score | Test priority |
|---:|---|
| 20–25 | Critical |
| 12–19 | High |
| 6–11 | Medium |
| 1–5 | Low |

### 4.3 Risk state

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
| QR-01 | A Firestore write may fail while the interface/cache continue to show the change as saved, causing silent data loss later. | Regression risk | 5 | 3 | 15 | High |
| QR-02 | Visit-history preservation may regress when a qualifying status is removed and later restored. | Regression risk | 4 | 4 | 16 | High |
| QR-03 | Explicit logout may fail to remove UID-scoped private travel data from browser storage. | Regression risk | 4 | 3 | 12 | High |
| QR-04 | Listener/reconciliation or optimistic concurrency may regress, allowing stale tabs/actions to lose confirmed changes. | Regression risk | 4 | 2 | 8 | Medium |
| QR-05 | A memory editor may bypass the explicit-Save contract. | Regression risk | 3 | 4 | 12 | High |
| QR-06 | Approved character-limit enforcement may regress. | Regression risk | 3 | 4 | 12 | High |
| QR-07 | Account deletion or destructive data reset may partially fail and leave private/public/authentication or lifecycle records inconsistent. | Regression risk | 5 | 3 | 15 | High |

**Applied decisions:** AB-EV-034 closes the remaining QR-01 assessment/coverage gap through a full write-path audit plus focused failed-write/recovery evidence for `flagSortOrder`; QR-01 remains a High `Regression risk`, not a Current gap. AB-EV-036 adds atomic combined Wishlist settings persistence, a real rejected-batch rollback proof and confirmed Profile-root refresh/read behaviour. AB-EV-037 adds an atomic logical Clear Map reset, rejected-batch containment, root Wishlist cleanup and versioned stale-public invalidation. QR-02 AB-EV-002; QR-03 AB-EV-003; QR-04 AB-EV-013/018/019/022/026/032/033/036/037; QR-05 AB-EV-004; QR-06 AB-EV-011; QR-07 AB-EV-010/033/037.

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
| QR-17 | **Want to visit/Wishlist** membership, privacy or canonical order may diverge from the approved status/settings model. | Regression risk | 3 | 3 | 9 | Medium |
| QR-18 | Global country, territory, continent or visit totals may not recalculate correctly. | Regression risk | 4 | 4 | 16 | High |
| QR-19 | Global **Total Visits** may diverge from individual `visitsCount` values. | Regression risk | 4 | 3 | 12 | High |
| QR-20 | **Nationality** or **Wishlist/Want to visit** may incorrectly increase physical-presence metrics. | Regression risk | 3 | 3 | 9 | Medium |
| QR-21 | A place with multiple physical statuses may be counted more than once as conquered. | Regression risk | 4 | 3 | 12 | High |
| QR-22 | Automatic status transitions may incorrectly reset visit counts or delete memories. | Regression risk | 4 | 3 | 12 | High |
| QR-23 | **Born there** or **Lived** may fail to select **Visited** and initialise visit behaviour correctly. | Regression risk | 3 | 3 | 9 | Medium |
| QR-24 | The approved **Passed through** workflow may regress. | Regression risk | 2 | 4 | 8 | Medium |

**Applied decisions:** AB-EV-035 rebaselines QR-16/QR-24 after the C35 requirement correction. Visited + Passed through is now a valid cumulative combination; adding a second compatible status does not itself create an additional `RegisteredVisit` or increment Total Visits. AB-EV-033 protects Wishlist compatibility/non-physical semantics. AB-EV-036 preserves `statuses.wishlist` as membership source, replaces legacy per-place order persistence with root `wishlistOrder`, makes combined privacy/order Save atomic and validates order after read/reload/public rendering. AB-EV-037 verifies that destructive Clear Map removes root order/public Wishlist state inside the same atomic lifecycle reset.

### 5.4 Geographic catalogue, map and achievements

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-25 | `252 Places`, `195 Countries`, `57 Territories and Entities`, `251 selectable records` and continent groups may diverge. | Regression risk | 4 | 3 | 12 | High |
| QR-26 | A selectable place may be missing, mapped to wrong ID, unclickable, incorrectly coloured or persisted under unsupported data. | Regression risk | 4 | 3 | 12 | High |
| QR-27 | Technical/non-selectable geographic records may create invalid data or duplicate progress. | Regression risk | 4 | 2 | 8 | Medium |
| QR-28 | Map intensity may be wrong when multiple statuses are present. | Regression risk | 3 | 3 | 9 | Medium |
| QR-29 | United Kingdom achievement/progress may use incorrect constituent/derived semantics. | Regression risk | 3 | 3 | 9 | Medium |
| QR-30 | Achievement lock/relock, metadata, chronology, reconquest or notification delivery may become inconsistent across private/public sources. | Regression risk | 3 | 3 | 9 | Medium |

### 5.5 Public profile and sharing

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-31 | A private profile or private Wishlist may expose identity/travel content to an unauthorised visitor. | Regression risk | 5 | 2 | 10 | Medium |
| QR-32 | Private memories/notes/visit details may be exposed publicly or returned to an unauthorised browser. | Regression risk | 5 | 2 | 10 | Medium |
| QR-33 | A public-profile component may allow editing or unauthorised persistence. | Regression risk | 5 | 2 | 10 | Medium |
| QR-34 | Public→private transitions or destructive resets may leave previously public profile/Wishlist/place projections accessible. | Regression risk | 4 | 3 | 12 | High |
| QR-35 | Social-link validation may permit an unsafe destination. | Regression risk | 4 | 2 | 8 | Medium |
| QR-36 | Future per-memory visibility/default logic may publish content contrary to preference. | Future risk | 5 | 3 | 15 | High |
| QR-37 | A future generated Story may expose unexpected information or fail across sharing flows. | Future risk | 4 | 3 | 12 | High |

**Applied decisions:** AB-EV-036 adds `wishlistOrder` to the intentional public-root contract only when Wishlist visibility permits it, preserves private order while public visibility is off, validates public→private atomic cleanup and restores frontend/Rules parity before the Production smoke. AB-EV-037 adds `placesGeneration` to the root/place projection lifecycle, keeps legacy generation-0 compatibility, denies stale-generation public reads after Clear Map and validates the aligned application/Rules release in Production. QR-31/QR-34 remain regression risks because privacy and destructive transitions remain consequential.

### 5.6 Compatibility, usability, performance and accessibility

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-38 | Behaviour may differ on Firefox, Safari, iPhone, tablets, macOS or other untested combinations. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-39 | Responsive/touch/constrained-device/CSS/navigation/card-paint/map-layout baselines may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-40 | Keyboard access, focus, accessible names, dialogs, contrast or other accessibility behaviour may regress. | Regression risk | 4 | 3 | 12 | High |

---

## 6. Highest-priority test focus

Priority focus includes failed-write/recovery regression; atomic multi-resource Save/destructive-reset behaviour; visit-history preservation; account-deletion integrity; status/Wishlist compatibility; `252/195/57` counter integrity; geographic catalogue integrity; private/public projection and privacy transitions; explicit logout/local-data exposure; explicit-Save integrity; real-time concurrency/cache authority; rapid visit convergence; Manual Visit Order; independent Wishlist root ordering; Clear Map generation invalidation; birthplace pointer/status atomicity; achievement chronology/public metadata; responsive/constrained-device behaviour; Profile read-only interaction; and accessibility.

QR-01 and QR-25 are no longer open investigations. Their residual concern is regression. AB-DEF-017 and AB-DEF-018 are closed; atomic Wishlist settings/order and atomic logical Clear Map integrity are permanent regression scope.

---

## 7. Important approved rules that must not be reported as defects without new evidence

- a place is counted once as conquered even with multiple compatible statuses;
- repeated visits increase Total Visits but do not create additional selectable records;
- adding a second compatible status does not itself create another RegisteredVisit;
- **Visited and Passed through may coexist** as cumulative historical statuses;
- Passed through may coexist with Lived/Born because those statuses imply Visited;
- Nationality and Wishlist/Want to visit alone do not represent physical presence;
- Wishlist membership uses the existing Want-to-visit status source rather than a duplicate status system;
- canonical Wishlist order is root `wishlistOrder`; legacy `wishlistOrderRank` is fallback only;
- Wishlist order is independent from visit order;
- one logical Wishlist settings Save must use one atomic persistence boundary;
- Wishlist privacy defaults to private and its public preference is independent from whole-profile visibility;
- public `wishlistOrder` is not exposed while Wishlist visibility is private;
- Clear Map logical completion uses one atomic private/root/public-root boundary and must remain <=500 writes at the supported 251-place maximum;
- Clear Map advances `placesGeneration`; stale pre-clear public place documents are not current public data and must not be readable as such;
- physical deletion of stale-generation public place documents is housekeeping, not a prerequisite for Clear Map correctness;
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
4. Map-status priority remains identical across map, profile, cards and legends when multiple statuses coexist.
5. Future geographic changes trigger explicit re-audit of `252/195/57`.
6. Future changes to public-profile fields extend the whitelist intentionally rather than reintroducing direct private reads.
7. Future changes to Wishlist order/settings preserve the root-order atomic Save contract and supported maximum write ceiling.
8. Future Clear Map/public-projection cleanup changes preserve generation invalidation and keep physical garbage collection correctness-independent.

---

## 9. Open product and quality questions

Resolved V1.0 decisions must not be reopened without new evidence or an explicit requirement correction: explicit Save for memories, character-limit policy, retry-safe account deletion, real-time/OCC controls, password minimum/passphrases, canonical usernames, immediate username reuse, Passed-through detailed-visit workflow, QR-01 failed-write recovery baseline, C35 Visited + Passed-through coexistence, C36 Wishlist atomic settings/root-order model, C37 Clear Map atomic generation-reset model, accessibility technical baseline, responsive baseline, achievement chronology, map/profile parity, geographic counters, dashboard/manual ordering and Wishlist/public-profile projection.

Open questions remain around username allowed characters, future memory visibility, broader browser/device support, native assistive-technology coverage, localisation completion, quantitative performance targets and future Story/photo scope.

---

## 10. Risk-based release implications

Release confidence requires evidence that travel data is not silently lost; multi-document logical Saves/destructive resets are atomic where the user contract requires it; private content is not exposed; public content is projected intentionally; account deletion/linking preserve integrity; status/Wishlist transitions follow approved rules; calculations remain consistent; public profiles remain read-only; frontend and Firestore Rules are release-aligned when both change; known gaps are resolved or explicitly accepted; and the supported compatibility/accessibility baseline is defined.

Any unresolved High risk must be reviewed before release and recorded as mitigated, accepted, reduced by control, deferred with limitation/follow-up, or a release blocker.

---

## 11. Review triggers

Review this analysis when travel statuses/combinations, Wishlist membership/privacy/root order, Clear Map generation/reset semantics, geographic catalogue/classification, map implementation, private/public profile projection, save/limit behaviour, auth/linking/password rules, account deletion, real-time/OCC behaviour, visit ordering, birthplace transactions, public achievement metadata, sharing, supported browsers/devices/accessibility targets or release-parity controls change.

A change to the public projection whitelist, Wishlist source of truth, Wishlist atomic Save boundary, Clear Map logical atomic boundary/generation model, status compatibility matrix, `252/195/57` denominators, achievement criteria, Profile interaction, manual order persistence or mutation orchestration requires explicit affected-area regression and documentation review.

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
- `docs/12-lessons-learned.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
- `evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md`
- `evidence/v1.0/defects/ab-ev-037-clear-map-atomic-generation-reset.md`
