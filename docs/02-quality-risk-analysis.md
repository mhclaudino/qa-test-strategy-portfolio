# AtlasBadge Quality Risk Analysis

## 1. Document purpose

This document identifies, evaluates, and prioritises the main quality risks associated with AtlasBadge.

Its purpose is to support risk-based test planning by showing:

- what could fail;
- why the failure matters;
- how likely the failure is under realistic usage conditions;
- which areas require the strongest test coverage;
- which product decisions are still assumptions or open questions.

This is a living analysis. Risk scores and priorities must be reviewed whenever the product, architecture, geographic catalogue, privacy model, or release scope changes.

> **Document status:** Reviewed through AB-EV-015, including the QR-11 username case-normalisation approval and the QR-13 immediate-reuse acceptance.

---

## 2. Assessment basis

This analysis is based on:

- direct review of the deployed AtlasBadge application;
- confirmed product rules supplied by the product owner;
- static code audit of authentication, profile, Firestore, local cache, and travel-map persistence;
- review of the current AtlasBadge backlog and geographic catalogue decisions;
- observed behaviour on Microsoft Edge on Windows and Google Chrome on Android.

The analysis does not assume that an intended behaviour is already implemented. Where implementation has not been confirmed, the item is explicitly classified as an assumption, open question, known gap, or future risk.

---

## 3. Confirmed product context

### 3.1 Authentication and account management

AtlasBadge currently supports:

- Google authentication;
- e-mail and password authentication;
- mandatory e-mail verification before an e-mail/password user can access the application;
- password recovery;
- an approved minimum password length of **15 characters**;
- valid passphrases, including phrases containing spaces;
- secure linking of Google and password access methods while preserving the same user identity and data;
- persistent authenticated sessions;
- unique usernames used in public URLs such as `/@username`;
- username changes;
- account deletion.

A username is mandatory, must contain at least three characters, and is treated as case-insensitive through a shared trimmed lowercase identity. The complete allowed-character policy has not yet been defined.

When a username is changed, the previous username becomes immediately available for another user. No historical alias or redirect is created; the possibility that an old link later resolves to a new owner is an explicitly accepted product behaviour.

### 3.2 Travel status model

AtlasBadge supports six travel statuses:

| Status | Product meaning |
|---|---|
| Visited | The user has travelled to and experienced the place. |
| Lived / Live there | The user lives or has lived in the place. |
| Born there | The user was born in the place. |
| Nationality | The user holds nationality or citizenship associated with the place. |
| Passed through | The user was physically present for a short period, such as an airport connection. |
| Want to visit | The place is on the user's future travel wish list. |

Multiple statuses may coexist when the combination is meaningful. The application must enforce the approved compatibility rules and automatically remove incompatible statuses.

Confirmed compatible combinations include:

- Visited + Lived / Live there;
- Visited + Born there;
- Visited + Nationality;
- Lived / Live there + Born there;
- Lived / Live there + Nationality;
- Nationality + Passed through;
- Want to visit + Nationality;
- Want to visit + Passed through.

Confirmed incompatible combinations include:

- Visited + Passed through;
- Lived / Live there + Passed through;
- Born there + Nationality;
- Born there + Passed through;
- Want to visit + Visited;
- Want to visit + Lived / Live there;
- Want to visit + Born there.

Selecting **Born there** or **Lived / Live there** automatically selects **Visited**.

Selecting a status incompatible with **Want to visit** automatically removes **Want to visit**.

### 3.3 Visits and memories

Each selected place may contain a `visitsCount` value.

Confirmed rules include:

- the minimum value is zero;
- the decrement control is disabled at zero;
- selecting **Visited** changes the count from zero to one;
- selecting **Passed through** also contributes one visit;
- visit controls are currently enabled through **Visited**;
- the global **Total Visits** value is the sum of the individual `visitsCount` values;
- a place counts only once as a conquered place, regardless of how many times it was visited;
- the number of detailed memory records does not need to equal `visitsCount`;
- users may choose to document only some of their trips;
- memories can contain duration and notes;
- typing changes only local draft state and persistence occurs after an explicit **Save** action;
- memories are available for any travel status;
- a non-visit memory may use the internal `generalNote` model without creating an artificial `registeredVisit` or increasing `visitsCount`;
- memories can be edited and deleted.

Current V1.0 behaviour preserves the visit count and user-created memories when **Visited** is deselected. The information is hidden while the status is inactive and restored if **Visited** is selected again.

### 3.4 Persistence and data model

Cloud Firestore is the primary source of truth for authenticated users.

Travel data is stored under:

```text
users/{uid}/places/{placeId}
```

A place document may contain:

- `statuses`;
- `visitsCount`;
- `registeredVisits`;
- `generalNote`.

The browser cache uses a UID-specific key:

```text
atlasbadge_local_data_{uid}
```

The local cache is used as:

- a cache of authenticated travel data;
- a fallback when the initial Firestore read fails;
- the primary storage for demonstration data when no authenticated user is present.

For the registered-place map path, changes are rendered optimistically in React, while the UID-scoped browser cache is updated only after the Firestore write succeeds. A rejected write restores the last confirmed state. This confirmed-cache contract is covered locally by AB-EV-008 and was confirmed in the final Production smoke through AB-EV-009 for map status and visit mutations. Equivalent failure, reload and recovery coverage for all other persistence flows remains incomplete.

Authenticated place data now uses a Firestore real-time subscription. Confirmed remote snapshots are reconciled with local optimistic mutations, pending local writes are not treated as independent remote confirmation, and optimistic concurrency control uses the confirmed `updatedAt` version to prevent a stale client from silently overwriting newer place data. Listener cleanup and UID isolation are retained under permanent regression coverage through AB-EV-013.

### 3.5 Public profile and privacy

A new profile is private by default. The user may change the profile visibility at any time.

When a profile is private, visitors are shown that the profile is private and must not receive the traveller's map, statistics, or private content.

A public profile may display:

- public display name;
- username;
- biography;
- social links;
- map colours and travel-status representation;
- territory and country progress;
- total visits;
- continent progress;
- conquered places and flags;
- achievements and badges.

The public profile is read-only.

Memories and visit notes are currently private. Individual memory visibility is planned but has not yet been implemented.

### 3.6 Geographic catalogue and progress model

AtlasBadge currently has **251 selectable places**.

The internal model distinguishes between:

- the conceptual country total used by the product;
- selectable sovereign countries;
- the four constituent nations of the United Kingdom;
- limited-recognition or de facto entities;
- territories;
- Antarctica.

The United Kingdom aggregate is not assigned its own persisted travel status. England, Scotland, Wales, and Northern Ireland are selectable independently.

The current V1.0 public progress model uses eight display groups:

- South America;
- Central America;
- North America;
- Antarctica;
- Africa;
- Asia;
- Europe;
- Oceania.

A group increases the continent numerator only when at least one selectable place in that group has qualifying physical presence. The same eight-group taxonomy must be used consistently across the personal map, public profile and progress cards.

Each continent has a colour family. Each travel status uses a different intensity within that family. When more than one status is present, a defined status priority determines the final map intensity.

### 3.7 Achievements

Numeric achievements may count any conquered country or territory that represents physical presence.

Achievements are dynamic rather than permanent historical records:

- an achievement unlocks when its current criteria are met;
- it becomes locked again if the user later stops meeting the criteria;
- displayed achievement dates are recalculated;
- the **United Kingdom** achievement requires England, Scotland, Wales, and Northern Ireland;
- a special Antarctica achievement is planned but is not yet implemented.

### 3.8 Current compatibility evidence

Confirmed manual usage currently covers:

- Microsoft Edge on Windows;
- Google Chrome on Android.

The responsive experience appears usable in the tested scenarios, but broader compatibility validation is still required.

Accessibility behaviour has not yet been formally assessed.

---

## 4. Risk assessment method

### 4.1 Impact scale

| Score | Meaning |
|---:|---|
| 1 | Negligible effect with no meaningful impact on the user or release decision. |
| 2 | Minor inconvenience or cosmetic issue with a simple workaround. |
| 3 | Important feature is affected, but the user can continue with a workaround. |
| 4 | Major loss of functionality, data integrity, privacy, or user trust. |
| 5 | Critical security, privacy, account-access, severe data-loss, or core-service failure. |

### 4.2 Likelihood scale

| Score | Meaning |
|---:|---|
| 1 | Rare or dependent on highly unusual conditions. |
| 2 | Unlikely, but credible under specific conditions. |
| 3 | Possible during realistic use. |
| 4 | Likely during normal or repeated use. |
| 5 | Almost certain or easily reproducible. |

### 4.3 Risk score and priority

```text
Risk score = Impact × Likelihood
```

| Score | Test priority |
|---:|---|
| 20–25 | Critical |
| 12–19 | High |
| 6–11 | Medium |
| 1–5 | Low |

A quality-risk priority is not the same as defect severity. The risk score determines how strongly an area should influence test design, coverage, sequencing, and release evidence.

### 4.4 Risk state

| State | Meaning |
|---|---|
| Mitigated | The risk has been addressed and validated, with acceptable residual risk retained in regression coverage. |
| Current gap | A known limitation or unsafe behaviour exists in the current product. |
| Regression risk | The feature exists, but failure would have material consequences and requires continued coverage. |
| Future risk | The risk applies to a planned feature and must be reassessed before implementation or release. |
| Assessment gap | Insufficient evidence exists to claim that the quality characteristic is adequately covered. |
| Accepted behaviour | The product intentionally permits the behaviour, but its consequences still require testing, communication, or explicit risk acceptance. |

---

## 5. Quality risk register

### 5.1 Data integrity, persistence, and privacy

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-01 | A Firestore write may fail while the interface and local cache continue to show the change as saved, causing silent data loss in a later session. | Current gap | 5 | 3 | 15 | High |
| QR-02 | Visit-history preservation may regress when **Visited** is deselected and later restored, causing counts or user-created memories to be lost. | Regression risk | 4 | 4 | 16 | High |
| QR-03 | Explicit logout may fail to remove UID-scoped private travel data from browser storage on a shared computer. | Regression risk | 4 | 3 | 12 | High |
| QR-04 | Real-time listener, confirmed-state reconciliation or optimistic concurrency control may regress, allowing stale tabs or devices to lose confirmed travel-data changes. | Regression risk | 4 | 2 | 8 | Medium |
| QR-05 | A legacy or replacement memory editor may bypass the explicit-Save contract and reintroduce persistence on every keystroke. | Regression risk | 3 | 4 | 12 | High |
| QR-06 | Approved character-limit enforcement may regress across notes or profile content, allowing oversized values or inconsistent boundary handling. | Regression risk | 3 | 4 | 12 | High |
| QR-07 | Account deletion may partially fail and leave authentication records, user data, reserved usernames, public-profile data, or other orphaned records. | Regression risk | 5 | 3 | 15 | High |

### 5.1.1 Current QR-01 scoped mitigation evidence

AB-EV-008 demonstrates that the registered-place map path now separates optimistic React state from confirmed browser-cache state.

The covered controls include:

- no durable cache update before Firestore confirmation;
- rollback to the last confirmed state after rejection;
- retry and idempotency;
- sequential mutation ordering;
- reload and cache-fallback coverage;
- immediate visit and counter rendering;
- canonical Total Visits parity between My Map and Public Profile.

**Risk-state decision:** QR-01 remains **Current gap**. The map status and visit path is scoped as mitigated and its final Production confirmation is recorded in AB-EV-009. Equivalent failure, reload and recovery coverage for every other product persistence flow remains incomplete.

### 5.1.2 Applied data-integrity decisions

- **QR-02 — Regression risk:** visit counts and memories are preserved when **Visited** is deselected and restored; see AB-EV-002.
- **QR-03 — Regression risk:** explicit logout removes UID-scoped private browser data; see AB-EV-003.
- **QR-04 — Regression risk:** Firestore real-time synchronisation, confirmed-state reconciliation, OCC conflict handling, listener cleanup and two-tab Production behaviour were approved; see AB-EV-013.
- **QR-05 — Regression risk:** memory and non-visit note text follows an explicit-Save contract; see AB-EV-004.
- **QR-06 — Regression risk:** approved character-limit controls are implemented and remain under boundary and layout regression coverage; see AB-EV-011.
- **QR-07 — Regression risk:** account deletion is retry-safe and Production-approved, with residual cross-service convergence risk retained in regression; see AB-EV-010.

### 5.2 Authentication and account identity

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-08 | The approved 15-character minimum or passphrase-compatible password policy may regress or become inconsistent across password-based account flows. | Regression risk | 4 | 3 | 12 | High |
| QR-09 | Linking Google and password access methods may create or use a different UID, making existing profile and travel data appear lost. | Mitigated | 5 | 2 | 10 | Medium |
| QR-10 | An access method may be linked to the wrong identity if e-mail matching, reauthentication, cancellation, or rollback behaviour fails. | Mitigated | 5 | 2 | 10 | Medium |
| QR-11 | Shared username normalisation, case-insensitive reservation or canonical public-profile resolution may regress. | Regression risk | 3 | 3 | 9 | Medium |
| QR-12 | Concurrent username reservations may leave the `users` and `usernames` records inconsistent. | Regression risk | 3 | 2 | 6 | Medium |
| QR-13 | A released username may be registered by another person, causing previously shared links to point to the wrong profile and enabling impersonation-like confusion. | Accepted behaviour | 4 | 3 | 12 | High |
| QR-14 | A persistent session may expose an account on a shared device when the user does not explicitly log out. | Regression risk | 4 | 2 | 8 | Medium |
| QR-15 | An e-mail may be successfully verified, but stale authentication state may continue blocking the user from the application. | Regression risk | 4 | 3 | 12 | High |

### 5.2.1 Applied authentication decisions

- **QR-08 — Regression risk:** the approved policy requires at least 15 characters, permits passphrases and is protected by permanent automated coverage; see AB-EV-012.
- **QR-09 — Mitigated:** account linking preserves the existing UID and travel data; see AB-EV-005.
- **QR-10 — Mitigated:** wrong-identity linking and Google-photo synchronisation controls were approved; see AB-EV-007.
- **QR-11 — Regression risk:** creation, change, reservation, public lookup and generated links use the same case-insensitive canonical identity; see AB-EV-014.
- **QR-12 — Regression risk:** transactional reservation must continue to prevent different UIDs from owning the same canonical username; affected-area coverage is retained by AB-EV-014 and AB-EV-015.
- **QR-13 — Accepted behaviour:** a previous username is released immediately, no alias or redirect is created, and another UID may reserve it; see AB-EV-015.

### 5.3 Travel statuses, counters, and calculations

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-16 | The application may accept prohibited status combinations or remove combinations that are valid. | Regression risk | 3 | 4 | 12 | High |
| QR-17 | **Want to visit** may remain selected when an incompatible physical-presence status is applied. | Regression risk | 3 | 3 | 9 | Medium |
| QR-18 | Global country, territory, continent, or visit totals may not recalculate correctly after status changes. | Regression risk | 4 | 4 | 16 | High |
| QR-19 | The global **Total Visits** value may diverge from the sum of the individual `visitsCount` values. | Regression risk | 4 | 3 | 12 | High |
| QR-20 | **Nationality** or **Want to visit** may incorrectly increase physical-presence metrics when used without a qualifying status. | Regression risk | 3 | 3 | 9 | Medium |
| QR-21 | A place with multiple physical statuses may be counted more than once as a conquered country or territory. | Regression risk | 4 | 3 | 12 | High |
| QR-22 | Automatic status transitions may incorrectly reset visit counts or delete related memories. | Regression risk | 4 | 3 | 12 | High |
| QR-23 | **Born there** or **Lived / Live there** may fail to select **Visited** and initialise the visit count. | Regression risk | 3 | 3 | 9 | Medium |
| QR-24 | **Passed through** contributes to physical presence and total visits but does not currently provide the same detail and memory workflow as **Visited**. | Current gap | 2 | 4 | 8 | Medium |

### 5.4 Geographic catalogue, map, and achievements

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-25 | The `195 countries`, `251 selectable places`, `52 territories and entities`, and `8 continent display groups` may be represented inconsistently across screens and statistics. | Regression risk | 4 | 3 | 12 | High |
| QR-26 | A selectable place may be missing, mapped to the wrong ID, unclickable, incorrectly coloured, or persisted under an unsupported record. | Regression risk | 4 | 3 | 12 | High |
| QR-27 | Technical or non-selectable geographic records may create invalid Firestore data or duplicate user progress. | Regression risk | 4 | 2 | 8 | Medium |
| QR-28 | The map may display the wrong intensity when a place has multiple statuses and the status-priority rule is not applied consistently. | Regression risk | 3 | 3 | 9 | Medium |
| QR-29 | The **United Kingdom** achievement may be calculated without all four constituent nations or may incorrectly rely on the technical `gb` geometry. | Regression risk | 3 | 3 | 9 | Medium |
| QR-30 | Achievements may remain unlocked after the criteria are no longer met, or recalculated dates may become inconsistent between screens or sessions. | Regression risk | 3 | 3 | 9 | Medium |

### 5.5 Public profile and sharing

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-31 | A private profile may expose identity, map, statistics, travel statuses, or other data to an unauthenticated visitor. | Regression risk | 5 | 2 | 10 | Medium |
| QR-32 | Private memories or notes may be exposed in the public interface or returned to an unauthorised browser even when they are not rendered. | Regression risk | 5 | 2 | 10 | Medium |
| QR-33 | A public-profile component may allow an editing action or unauthorised data modification. | Regression risk | 5 | 2 | 10 | Medium |
| QR-34 | Changing a profile from public to private may not hide previously visible content immediately. | Regression risk | 4 | 3 | 12 | High |
| QR-35 | Social-link validation may permit an unsafe or unexpected destination. | Regression risk | 4 | 2 | 8 | Medium |
| QR-36 | Future per-memory visibility or visibility-default logic may publish content contrary to the user's selected privacy preference. | Future risk | 5 | 3 | 15 | High |
| QR-37 | A future generated Story may expose information the user did not expect to include or may fail differently across mobile and desktop sharing flows. | Future risk | 4 | 3 | 12 | High |

### 5.6 Compatibility, usability, performance, and accessibility

| ID | Risk statement | State | Impact | Likelihood | Score | Priority |
|---|---|---|---:|---:|---:|---|
| QR-38 | Behaviour may differ on Firefox, Safari, iPhone, tablets, macOS, or other untested browser/device combinations. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-39 | The map or profile may become difficult to use on smaller screens, touch devices, low-performance devices, or slower networks. | Assessment gap | 3 | 3 | 9 | Medium |
| QR-40 | Keyboard access, focus visibility, accessible names, screen-reader behaviour, animation preferences, or non-colour status identification may be inadequate. | Assessment gap | 4 | 3 | 12 | High |

---

## 6. Highest-priority test focus

The following risks should receive the strongest coverage and earliest execution because they combine high impact with realistic likelihood.

| Risk area | Primary validation focus |
|---|---|
| Silent persistence failure | Simulate failed writes, offline transitions, reloads, cache fallback, recovery, and user feedback. |
| Visit-history preservation | Retain permanent regression coverage for deselecting and restoring **Visited** with different counts, notes, durations and compatible status combinations. |
| Account deletion integrity | Validate deletion for Google, password, and linked accounts; partial failures; recent-authentication requirements; subcollections; username release; public-profile removal; and repeat attempts. |
| Status-rule integrity | Execute pairwise and state-transition coverage across all six statuses, including automatic selection and removal rules. |
| Progress calculation integrity | Recalculate countries, territories, continents, total visits, badges, and achievements after every relevant add, remove, and restore operation. |
| Geographic catalogue integrity | Validate the exact selectable fixture, unsupported IDs, United Kingdom records, Antarctica, microterritories, aliases, map clicks, tooltips, colours, and Firestore IDs. |
| Privacy transitions | Test public-to-private changes, direct URLs, cached pages, unauthenticated requests, private data in network responses, and read-only enforcement. |
| Local-data exposure | Retain explicit-logout regression coverage that proves UID-scoped private cache and session data are removed. |
| Explicit-save integrity | Confirm that typing changes only draft state, that no per-keystroke persistence occurs, and that the approved Save action is the only persistence trigger. |
| Real-time concurrency integrity | Retain multi-tab listener, pending-write, confirmed-cache, OCC conflict, retry, cleanup and UID-isolation coverage. |
| Password-policy integrity | Retain boundary, passphrase, Firebase-response and credential-sanitisation coverage across every password-based flow. |
| Accessibility | Perform an initial keyboard, focus, semantic-label, contrast, colour-dependence, screen-reader, and reduced-motion assessment. |

---

## 7. Important rules that must not be reported as defects

The following confirmed behaviours are intentional unless the product definition changes:

- a place is counted once as conquered even when it has multiple compatible statuses;
- repeated visits increase **Total Visits**, but do not create additional conquered places;
- the number of detailed memories may be lower than `visitsCount`;
- **Nationality** and **Want to visit** do not represent physical presence when used alone;
- **Passed through** counts as physical presence and contributes one visit;
- achievements become locked again when their current criteria are no longer met;
- achievement dates are recalculated rather than preserved as permanent historical unlock dates;
- the public progress model uses eight display groups, including Antarctica as a distinct group;
- the four constituent nations of the United Kingdom are separate selectable records;
- the technical United Kingdom geometry must not receive its own persisted travel status;
- the public profile is read-only;
- memories are currently private because individual memory-publication controls are not yet available.

---

## 8. Assumptions requiring validation

The following statements are plausible but have not yet been fully demonstrated across all relevant scenarios:

1. Public-to-private profile changes invalidate all previously visible or cached content immediately.
2. Social-link validation rejects unsafe protocols and unsuitable destination formats.
3. The current geographic fixture and the deployed application contain the same selectable records.
4. Responsive behaviour remains acceptable outside the currently tested Windows/Edge and Android/Chrome combinations.
5. The current map-status priority is applied identically in the personal map, public profile, cards, legends, and generated assets.

Account deletion is no longer listed as an assessment assumption. AB-EV-010 records emulator fault coverage, retry-safe behaviour and a complete disposable-account Production validation; the area remains a Regression risk because the underlying services do not form a single distributed transaction.

---

## 9. Open product and quality questions

The following decisions remain open or require formal acceptance before the related risks can be closed.

The following V1.0 decisions are already resolved and must not be reopened as gaps without new evidence:

- memory and non-visit note text uses local draft state and explicit Save;
- the approved character-limit policy is implemented and retained under QR-06 regression coverage;
- account deletion uses a retry-safe, idempotent convergence model validated through AB-EV-010.
- registered-place changes use real-time synchronisation and optimistic concurrency controls validated through AB-EV-013;
- the approved password policy requires at least 15 characters and permits passphrases, as recorded in AB-EV-012;
- usernames use case-insensitive trimmed lowercase normalisation across reservation and public-profile flows, as recorded in AB-EV-014;
- a previous username becomes reusable immediately without an alias or redirect, and that consequence is accepted through AB-EV-015.

### 9.1 Data and persistence

- What user-visible save states are required for persistence flows outside the registered-place map path: saving, saved, failed, offline, or pending synchronisation?

### 9.2 Account and username policy

- What characters, separators, casing, and reserved words are allowed in usernames?

### 9.3 Memories and notes

- When will **Passed through** receive a detail or memory workflow?
- Where will the default memory-visibility preference be configured?
- How will existing memories be normalised when individual privacy controls are introduced?

### 9.4 Compatibility and accessibility

- Which desktop and mobile browsers are officially supported?
- What minimum screen sizes and device capabilities are required?
- What performance targets apply to map loading, interaction, profile loading, and saving?
- Which accessibility standard or acceptance criteria will be used?
- How will travel status be communicated without relying only on colour intensity?

### 9.5 Sharing

- Which platforms and devices must support the future Story-sharing flow?
- What information will be included by default in a generated Story image?
- Must the user review the generated image before opening an external sharing flow?

---

## 10. Risk-based release implications

A release recommendation should not be based only on whether the main happy paths work.

Release confidence should require evidence that:

- user-created travel data is not silently lost;
- private content is not exposed;
- account deletion and authentication linking preserve identity and data integrity;
- status combinations and automatic transitions follow the approved rules;
- all progress values remain mathematically consistent;
- the geographic catalogue and map use the approved records and identifiers;
- public profiles are read-only and become private immediately when requested;
- known gaps are either resolved or explicitly accepted with documented impact;
- the supported compatibility and accessibility baseline is defined.

Any unresolved High risk should be reviewed before release and recorded as one of the following:

- mitigated by completed testing and evidence;
- accepted by the product owner;
- reduced by a technical or product control;
- deferred with a documented limitation and follow-up action;
- considered a release blocker.

---

## 11. Review triggers

This analysis must be reviewed when any of the following occurs:

- a new travel status or status combination is introduced;
- the geographic catalogue or country/territory classification changes;
- the map implementation or data source changes;
- per-memory privacy is implemented;
- character limits or save behaviour are introduced;
- authentication providers, password-policy rules or linking rules change;
- account deletion logic changes;
- real-time synchronisation, optimistic concurrency control or offline behaviour changes;
- Story generation or external sharing is released;
- new browsers, devices, or accessibility targets become officially supported;
- a production defect or incident reveals a new failure mode.

---

## 12. Related portfolio documents

This analysis provides direct input for:

- `docs/03-test-strategy.md`;
- `docs/04-test-scope.md`;
- `docs/05-entry-exit-criteria.md`;
- `docs/06-test-environments.md`;
- `docs/07-defect-management.md`;
- `docs/08-metrics-and-reporting.md`;
- `docs/09-system-test-plan.md`;
- `test-assets/exploratory-test-charters.md`;
- `test-assets/sample-test-cases.md`;
- `reports/test-summary-report.md`.
