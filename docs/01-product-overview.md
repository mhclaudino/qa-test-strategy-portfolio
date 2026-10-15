# AtlasBadge Product Overview

## 1. Document purpose

This document provides a high-level overview of AtlasBadge, including its purpose, target users, core capabilities, data flows, dependencies, constraints and quality considerations.

It establishes the product context required for the risk analysis and test strategy documented in this repository.

> **Document status:** Completed and maintained through AB-EV-068. C39–C44 establish the current memory/privacy/order/public-display, editable-name, visual-identity and visit-photo baselines. C45A–C45M established the historical six-locale rollout; C45N/AB-EV-059 added German and AB-EV-060 closed the final seven-locale visual/Production acceptance plus AB-DEF-022. AB-EV-061–065 then reconciled release readiness, recovered deterministic installation, completed the mandatory Node 24 / Next.js 16.3.6 migration and established the final technical RC baseline. Premium Hardening subsequently published PA-001/AB-EV-066, PA-002/AB-EV-067 and PA-003/AB-EV-068. Product `7616e8ca455f1d2e72a67e4dd839c9c302f98656` is the current published Premium Hardening anchor; Vercel deployment `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` is READY / production / exact SHA and received bounded post-publication verification. Final V1.0 Go Live and final Premium Hardening closure are not claimed here.

## 2. Product summary

AtlasBadge is an interactive travel platform that allows users to record, organise and present their travel history and future travel interests.

Users can interact with a world map, assign travel statuses to places, maintain detailed visits and memories, manage a private or public Wishlist, monitor geographic progress and achievements, and publish a read-only traveller profile.

The product combines travel tracking, personal progress, map interaction, collection-oriented features and selective public sharing in a single experience.

## 3. Product objectives

AtlasBadge aims to:

- provide an intuitive way to record visited and planned destinations;
- allow users to classify places using meaningful travel statuses;
- maintain detailed visit history without compromising data integrity;
- present geographic progress in a visual and engaging format;
- persist each user's personal travel information securely;
- allow users to keep a Wishlist private or intentionally share it;
- allow users to share selected travel/profile information without exposing private records;
- provide a consistent experience across desktop and mobile devices;
- make travel tracking enjoyable through progress, flags, badges and achievements.

## 4. Target users

### 4.1 Registered traveller

A registered traveller can:

- create and access an account;
- select countries or places on the map;
- assign and change travel statuses;
- save visits and memories with explicit per-memory privacy;
- attach one representative photo to a `RegisteredVisit`, subject to the V1.0 free quota of 10 active visit photos per user;
- maintain a Wishlist based on the Want to visit status;
- choose whether the Wishlist is public or private;
- reorder visited places, memory presentation and Wishlist items independently where supported;
- review saved travel progress and achievements;
- manage personal profile information;
- publish or hide a public profile.

### 4.2 Public visitor

A public visitor can:

- open a traveller's public profile when that profile is public;
- view only information intentionally projected for public presentation;
- browse the public map, progress, flags and achievements;
- view a public Wishlist only when the owner enabled it and the Wishlist is non-empty;
- click an earned country/territory flag to view only that place's intentionally public memories;
- use local presentation controls without modifying or persisting changes to the owner's data;
- browse without access to private visits, private memories/notes or owner-only records.

### 4.3 Product administrator

Administrative capabilities are not currently part of the documented user-facing portfolio scope. Narrow privileged Firebase Admin/server boundaries exist for protected lifecycle operations such as Clear Map and visit-photo services; those boundaries are treated as security/quality-critical implementation detail rather than as an administrator UI.

## 5. Core capabilities

### 5.1 User authentication

The application supports account-based access so that travel information can be associated with an individual user.

Relevant behaviours include account creation, login/logout, e-mail verification/recovery, authentication-method linking while preserving identity/data, session handling, protected-route access and account deletion.

### 5.2 Interactive travel map

The map is a central element of the product. Users can select places, view status, assign/remove supported statuses, search/filter selected places and navigate between map and related flag presentation.

AtlasBadge contains 251 directly selectable geographic records. C31/AB-EV-029 established the canonical catalogue as 194 sovereign countries, 47 territories, 5 limited-recognition entities, 4 UK constituent countries and Antarctica as a special region. The conceptual global progress model is 252 Places / 195 Countries / 57 Territories and Entities because completing England, Scotland, Wales and Northern Ireland derives one additional United Kingdom Place/Country. Antarctica contributes to Places and Territories/Entities, not Countries, preserving the invariant `252 = 195 + 57`.

C32/AB-EV-030 adds read-only Profile map-to-flag navigation: clicking a conquered place resolves the canonical `countryId`, scrolls to the exact earned flag and applies a temporary highlight without mutating travel data. The behaviour is protected for normal geographies, micro-markers, sort changes and representative mobile layout.

C33/AB-EV-031 adds the Map selected-place presentation toggle between **Ordem de Visita** and **Ordem Alfabética**. Visit Order is the default Map presentation, the choice is local/session-only, non-physical selected records remain visible after physical records, and alphabetical order uses deterministic `pt-BR` comparison without changing persisted data.

### 5.3 Travel status and Wishlist management

AtlasBadge supports six travel statuses:

- Visited;
- Lived / Live there;
- Born there;
- Nationality;
- Passed through;
- Want to visit.

The Wishlist uses `statuses.wishlist === true` / Want to visit as its source of truth; it is not a duplicate travel-status system.

Confirmed rules include:

- Born there and Lived / Live there imply Visited;
- **Visited and Passed through may coexist** as cumulative historical statuses;
- Passed through may coexist with Lived/Born through their Visited dependency;
- activating the second compatible status does not itself create another travel occurrence;
- incompatible proper physical-presence states remove Want to visit according to the status rules;
- Passed through + Want to visit may coexist when no incompatible visited/lived/born state is active;
- Wishlist-only records do not count as physical presence;
- Wishlist-only records do not participate in Manual Visit Order;
- Wishlist membership remains on place documents, while canonical Wishlist presentation order is stored in root `wishlistOrder` independently from `visitOrderRank`;
- legacy `wishlistOrderRank` may be read as backward-compatible fallback but is no longer the canonical ordering source after C36;
- one user-visible Wishlist Save persists changed order/privacy through one Firestore atomic batch boundary;
- Wishlist privacy defaults to private;
- public root `wishlistOrder` is exposed only while the Wishlist is public;
- the saved Wishlist-public preference survives unrelated profile-visibility changes;
- Clear Map removes Wishlist membership/order state and resets its public preference;
- account deletion removes the associated private/public Wishlist data.

C35/AB-EV-035 is the current source for the corrected Visited + Passed through rule. C36/AB-EV-036 is the current source for Wishlist atomic settings persistence and root order semantics.

### 5.4 Visits, memories and manual order

Qualifying physical-presence places may contain registered visits and memories. Memory visibility is controlled per memory.

`RegisteredVisit` represents an individual occurrence; travel status represents accumulated place history. A status transition such as Passed through → Visited does not manufacture a second visit. Additional actual occurrences are recorded through the visit-add workflow.

Relevant behaviours include:

- repeated visit creation/removal/editing;
- `visitsCount` derived from registered visits where applicable;
- explicit Save for memory text/privacy rather than persistence on every keystroke;
- optional `visitName?: string` presentation metadata, with trimmed explicit-Save editing, stable visit ID and `VISITA n` fallback when absent;
- duplicate custom visit names rejected case-insensitively within the same country/territory while Unicode content remains supported;
- `RegisteredVisit.isMemoryPublic?: boolean` for visit-memory visibility;
- C44 visit-photo metadata using a bounded private slot/variant plus an opaque `photoRef` for public projection; photos inherit visit-memory privacy and do not create visits or change `visitsCount`;
- PA-002/AB-EV-067 defines the current photo-removal lifecycle: quota counts active persisted logical photos, logical removal immediately invalidates old owner/public access, exact server-managed allocation release occurs without waiting for TTL, failed release persists exact retry metadata across reload and physical Storage deletion is not the logical-success boundary;
- PA-003/AB-EV-068 hardens the photo service boundary: new photo versions use immutable object identity `users/{uid}/visit-photo-objects/{slotId}/{photoRef}.webp`; mutable legacy slot paths are write-frozen; owner/public reads bind current logical authorisation to the exact immutable object key; malformed-present modern locator state fails closed rather than falling back; revocation/account lifecycle, request-shape and error-contract gates are enforced at the server routes;
- `UserCountry.isGeneralNotePublic?: boolean` for general-memory visibility;
- legacy/missing privacy flags defaulting to private;
- a public visit requiring at least one approved shareable value (duration, date/time or note), with note not mandatory;
- empty general memory remaining non-public;
- `memoryOrder?: string[]` storing presentation order independently from the `registeredVisits` source/history array;
- stale/duplicate order IDs being ignored and newly added visits appended deterministically;
- deleting a visit cleaning its presentation-order ID in the same logical mutation;
- preservation of original `VISITA n` identity even when memory presentation order changes;
- Manual Visit Order for qualifying physical-presence places only;
- owner-only Manual Visit Order editing in the Map surface through optional `visitOrderRank >= 1`;
- Born there fixed at the top of Manual Visit Order, with Wishlist-only/Nationality-only records excluded;
- Cancel performing no persistence and Save updating presentation ranks without fabricating visits, memories, statuses or chronology;
- rapid visit add/remove/save handled as replayable semantic intents so confirmed concurrent changes are preserved;
- independent Wishlist order;
- transactional integrity for the Born there user pointer/status relationship.

C34/AB-EV-032 is the current Manual Visit Order and rapid-visit concurrency baseline. It preserves the atomic `birthplacePlaceId` + `statuses.born` invariant and records AB-DEF-013 as closed. C39/AB-EV-039 is the current privacy contract for memories. C40/AB-EV-040 is the current manual memory-order contract. C42/AB-EV-042 adds editable visit names as presentation metadata without changing visit identity, visit-history order, memory order or privacy. C45G/AB-EV-052 localizes the deep editor presentation around those existing contracts without changing their stored identifiers, rules or persistence semantics. PA-002/AB-EV-067 later strengthens the photo/visit removal path with durable exact retry and concurrent-edit-safe cleanup while preserving those same visit/history semantics. PA-003/AB-EV-068 preserves that lifecycle while replacing reusable physical byte identity for new uploads and strengthening server-route input/auth/read contracts.

### 5.5 Persistent user data

Cloud Firestore is the primary source of truth for authenticated user progress.

The private source is:

```text
users/{uid}
users/{uid}/places/{placeId}
```

Private user/place records are owner-only. Root user state may include Profile/lifecycle settings including `isWishlistPublic`, canonical `wishlistOrder`, `placesGeneration` and server-managed visit-photo slot metadata; private places may include durable pending photo-release recovery metadata while an exact release is being reconciled, and current S5-era visit photos may include a private immutable `photoObjectKey` that is never part of the public projection.

Relevant behaviours include loading/saving status, visit and order changes; handling delayed/rejected/concurrent writes; maintaining consistency between optimistic UI, confirmed state and cache; and recovering safely across reload/session boundaries.

AB-EV-034 closes the previous QR-01 failed-write/recovery coverage gap using architectural equivalence and focused deterministic rollback evidence. AB-EV-036 adds a real Firestore Emulator rejected-batch proof for combined Wishlist settings and confirms no sibling write partially persists when the atomic batch is denied.

AB-EV-066 moves Clear Map protected-state mutation to an authenticated server/Admin transaction after C44 made `visitPhotoSlots` server-managed. AB-EV-067 makes ordinary photo/visit removal durable and exact: the logical change plus pending-release identity persists before the fallible release, successful cleanup clears only that exact tombstone against current orchestrated state, and failed release remains retryable after Product reload. AB-EV-068 then preserves those quota/recovery semantics while making byte identity immutable for new uploads and preserving the security distinction between locator absence, valid presence and malformed presence through canonicalisation/persistence.

### 5.6 Public profile and sanitised projection

The public Profile is a read-only projection rather than a direct view of private Firestore records.

Public data is stored under:

```text
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

A non-owner or anonymous Profile viewer reads the public source only. C41 applies the same source rule to the owner viewing their own public Profile: public-memory rendering consumes the public projection rather than reconstructing it from private `RegisteredVisit` data.

The public root may expose only approved presentation fields. When the Wishlist is public, the approved root projection may include `isWishlistPublic` and sanitised `wishlistOrder`; private Wishlist order is not exposed when visibility is disabled.

Public place projections must not expose raw `generalNote`, `registeredVisits`, privacy flags, `memoryOrder`, private visit content, `firstPhysicalPresenceAt`, `statusActivatedAt`, `visitsCount` or `photoObjectKey`. They may expose the sanitised `publicMemories` list created by C39.

For visit memories, the projection contains only approved shareable values. C41 introduced the sanitised exact presentation label; C42 allows that public `visitLabel` to use the approved custom visit name when present while retaining `VISITA n` fallback and never exposing raw `visitName`. C44 may add only the opaque `photoRef` for a public visit photo; private Storage locators, slot, variant and stable visit ID are not exposed. Owner and public image reads are served through authenticated/sanitised server routes rather than public Firebase Storage download URLs. PA-002/AB-EV-067 confirms physical object retention does not keep an obsolete photo logically accessible: old owner/public requests return 404 after the current private/public reference is removed. PA-003/AB-EV-068 further requires a current modern record with present immutable locator state to resolve only through its complete canonical private identity; present-invalid/incomplete modern locator state returns 404 and cannot downgrade to a legacy path, while genuine pre-S5 records with the modern key absent retain bounded compatibility. C40 ordering is resolved before privacy filtering, so relative public order is preserved without exposing `memoryOrder` itself.

Public achievement metadata contains only `unlockedAt` and `sequence`. C45H/AB-EV-053 localizes achievement presentation by stable achievement ID without changing or expanding that public metadata projection. C45J localizes public Profile achievement titles/descriptions by reusing the existing C45H stable-ID presentation catalogue. Before C45J the public Profile deliberately stayed Portuguese; C45J changed only the presentation boundary, not the approved public achievement metadata.

A public Wishlist tile appears only when Wishlist visibility is public and the Wishlist is non-empty. Its modal is read-only and renders owner order from the sanitised public root.

A normal earned country/territory flag opens a read-only public-memory modal. The Wishlist tile remains a separate interaction.

### 5.7 Responsive experience

AtlasBadge supports desktop and mobile web use. Testing considers navigation, map interaction, dialogs/forms, text readability, touch, scrolling, layout stability, modal background scroll locking, horizontal overflow and accessible interaction patterns.

C43/AB-EV-043 establishes the current visual-identity baseline: Atlas Gold/Black/Warm White and Atlas neutral surfaces are used for structural UI identity, while status, feedback, data-visualisation and external-brand colours remain semantic exceptions.

C45G/AB-EV-052 adds representative deep-editor responsive coverage at `390×844` for French, Portuguese (Portugal) and Spanish (Spain), including status pills, visit controls, memories, duration, photo actions and confirmation modals. C45H/AB-EV-053 extends the same representative `390×844` coverage to `/badges` and `BadgeUnlockToast`, including long localized achievement titles/descriptions, earned dates, progress bars and accessible toast controls.

## 6. High-level user journeys

### Journey 1: Register and begin tracking

1. A new user creates and verifies an account.
2. The user enters the authenticated product area.
3. The user selects a place and assigns a travel status.
4. The application persists the selection and updates progress.

### Journey 2: Build cumulative travel history

1. The user records a Passed through occurrence and optional memory.
2. At a later point, the user marks the same place as Visited.
3. Both historical statuses may remain active.
4. The existing occurrence is not duplicated merely because the new status was added.
5. If the user records another actual trip, the visit-add workflow creates the new occurrence.

### Journey 3: Return to saved progress

1. An existing user logs in.
2. Previously saved travel information is loaded.
3. The map/status controls reflect confirmed state.
4. The user continues updating travel progress.

### Journey 4: Manage a Wishlist

1. The user applies Want to visit to one or more places.
2. The Wishlist surface shows those places.
3. The user may reorder them independently from visit chronology.
4. The user may change order and privacy in the same Save.
5. The logical Save commits the root order/privacy and required public projection changes atomically.
6. Membership, order and privacy persist after reload.
7. If public and non-empty, the read-only public Profile displays the Wishlist in the canonical saved order.

### Journey 5: View a public profile

1. A visitor opens a valid public profile address.
2. Publicly projected travel information is displayed.
3. Private user/place records are not read by the visitor.
4. Clicking an earned country/territory flag opens the public-memory modal for that place.
5. Only memories included in the sanitised `publicMemories` projection are displayed, preserving C40 order and exact projected `VISITA n` identity.
6. Private memories/visits remain absent from the public response.

## 7. Data overview

| Data category | Examples | Main quality concerns |
|---|---|---|
| Account data | User identifier, authentication state | Security, privacy, account isolation |
| Private profile data | User settings and owner-only lifecycle state | Authorisation, deletion, integrity |
| Private travel data | Statuses, visits, memories, ranks, pending exact photo-release recovery metadata, private immutable photo object key | Accuracy, persistence, ownership, concurrency |
| Wishlist data | Place membership, root `wishlistOrder`, `isWishlistPublic` | Atomicity, status compatibility, privacy, independent ordering |
| Visit-photo quota/data | Server-managed active allocations, exact release identity and immutable per-photo object identity for new uploads | Quota correctness, race safety, retry, stale cleanup, authorisation-to-byte integrity |
| Progress data | Counts, percentages, achievements | Calculation consistency, chronology |
| Local cache | UID-scoped browser state | Synchronisation, stale information |
| Public projection | `publicProfiles` root/places, sanitised `publicMemories`, opaque photo references | Whitelisting, per-memory privacy, ordering, read-only access, no private locator exposure |

No real user credentials or personal data are included in this portfolio.

## 8. External services and technical dependencies

AtlasBadge depends on Vercel hosting/Git deployment, Firebase Authentication, Cloud Firestore, Firebase Storage where applicable, Firebase Emulator Suite, Playwright, browser storage/cache, map data/components and third-party web libraries.

A release that changes both frontend behaviour and Firestore/Storage Rules must preserve deployment parity; a Vercel READY state alone is not sufficient evidence that the Firebase security layer is aligned.

The current supported locale set is seven: `pt-BR`, `pt-PT`, `es-419`, `es-ES`, `fr`, `de-DE`, `en-GB`. Locale remains a presentation concern and is not persisted as travel/profile domain data. AB-EV-060 is the final C45N localization closure record.

## 9. Product constraints and assumptions

- AtlasBadge remains under active V1.0 development / Premium Hardening.
- Features and business rules may change under Test Lead/Product Owner approval.
- Testing uses controlled accounts and disposable data.
- Desktop and mobile web experiences are relevant.
- Cloud Firestore is the intended source of truth for authenticated progress.
- Public Profile data is served from a separate sanitised projection.
- Emulator browser tests must fail fast rather than silently fall back to real Firebase.
- Production testing is controlled and limited to approved scenarios.
- Administrative functionality is outside current user-facing scope; narrow privileged server/Admin boundaries exist for protected lifecycle operations and are subject to Premium Hardening.

## 10. Quality characteristics

Key characteristics are functional correctness, data integrity, security/privacy, usability, reliability, compatibility, performance, accessibility and maintainability.

Maintainability includes keeping business rules central, maintaining automated expectations when requirements legitimately change, avoiding unnecessary regression work when earlier checkpoints remain valid, reviewing write/read/session-refresh paths when a canonical data source changes, preserving absent/present-valid/present-invalid security states across persistence boundaries, and freezing exact candidate bytes before independent final approval/publication.

## 11. Known areas requiring clarification or future work

- The seven-locale V1.0 localization requirement is closed under AB-EV-060; locale architecture remains regression scope rather than an open localization feature gap.
- Premium Hardening remains active. PA-001, PA-002 and PA-003 are published/closed checkpoints. Remaining sequenced findings are selected under the approved Premium programme and must not be pulled into a completed finding without evidence of a direct blocker.
- PA-001 AC-017 still requires full browser UI → API → Admin Emulator reload evidence under PA-007 / Final Premium RC.
- The missed PA-001 pre-publication architecture review remains a governance carry-forward requiring an explicitly post-publication compensating audit before final Premium Hardening closure.
- PA-006A retains deterministic-tooling cleanup for the cached undeclared `tsx` dependency.
- PA-008 retains the account-deletion origin-allowlist hardening item.
- Physical garbage collection of obsolete/retained visit-photo Storage objects remains deferred; PA-003 proves current logical authorisation and byte identity do not depend on immediate deletion.
- `FUTURE-PAID-01` — possible post-V1.0 paid-plan model if infrastructure cost requires monetisation; candidates include an expanded visit-photo quota above the free 10-photo limit and other premium conveniences. No paid CTA, entitlement or billing behaviour is part of V1.0.
- broader browser/device compatibility beyond the current validated sample;
- quantitative performance targets;
- future Story/share scope;
- administrative/moderation capabilities if introduced;
- separate technical cleanup of the legacy unreachable UK-selector modal, without changing the approved UK aggregate/constituent model.

These items are not automatically defects. They are open product/quality questions, technical cleanup candidates, governance residuals or future features requiring explicit assessment when they enter scope.

## 12. Related portfolio documents

- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/06-test-environments.md`
- `docs/09-system-test-plan.md`
- `docs/10-lessons-learned.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md`
- `evidence/v1.0/regression/ab-ev-052-c45g-deep-country-visit-editor-localization.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-059-c45n-german-localization-extension.md`
- `evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md`
- `evidence/v1.0/regression/ab-ev-061-v1-release-readiness-audit.md`
- `evidence/v1.0/regression/ab-ev-062-final-v1-release-candidate-regression-charter.md`
- `evidence/v1.0/regression/ab-ev-063-rc-lockfile-reproducibility-recovery.md`
- `evidence/v1.0/regression/ab-ev-064-c46-node24-lts-upgrade-charter.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-068-pa003-photo-admin-server-route-hardening.md`

## 13. Current Premium Hardening checkpoint

The technically green AB-EV-065 Release Candidate became the starting point for the Premium Hardening programme rather than the final end of engineering.

- **PA-001 / AB-EV-066:** Clear Map protected-state mutation moved to an authenticated server/Admin transaction. The implementation and later UTF-8 test-oracle-only repair are published. AC-017 remains a PA-007/Final Premium RC residual, and the skipped pre-publication independent architecture review is retained as an explicit governance carry-forward.
- **PA-002 / AB-EV-067:** the ten-photo quota now represents active logical photos; normal photo/visit removal releases exact server-managed capacity, persists durable retry identity on failure, protects newer allocations/concurrent edits, invalidates obsolete owner/public reads immediately and does not depend on physical Storage cleanup. All original PA2 criteria were independently proven before publication.
- **PA-003 / AB-EV-068:** photo Admin/server routes now enforce strict request/auth/account/current-reference contracts; new photo versions use immutable physical object identity; present-invalid modern locator state fails closed; legacy mutable-object writes are frozen; PA3-01 through PA3-15 passed final qualification and the published commit passed bounded non-destructive Production verification.
- **Current Product publication anchor:** `7616e8ca455f1d2e72a67e4dd839c9c302f98656`, Vercel `dpl_B842NnXHecrGNhcQAfpkC8CgyVhj` READY / production / exact SHA / post-publication PASS.
- **Remaining Premium work:** PA-006A, PA-007/AC-017, PA-008, the PA-001 compensating architecture audit and deferred physical Storage GC remain explicit carry-forwards; the next implementation service is selected by Test Lead priority rather than being implicitly PA-003.

This checkpoint does not claim final Premium Hardening closure or final V1.0 Go Live.