# AtlasBadge Product Overview

## 1. Document purpose

This document provides a high-level overview of AtlasBadge, including its purpose, target users, core capabilities, data flows, dependencies, constraints and quality considerations.

It establishes the product context required for the risk analysis and test strategy documented in this repository.

> **Document status:** Completed and maintained through AB-EV-035, including the C35 Visited + Passed through requirement correction and the AB-EV-034 QR-01 coverage closure.

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
- save visits and private memories;
- maintain a Wishlist based on the Want to visit status;
- choose whether the Wishlist is public or private;
- reorder visits and Wishlist items independently where supported;
- review saved travel progress and achievements;
- manage personal profile information;
- publish or hide a public profile.

### 4.2 Public visitor

A public visitor can:

- open a traveller's public profile when that profile is public;
- view only information intentionally projected for public presentation;
- browse the public map, progress, flags and achievements;
- view a public Wishlist only when the owner enabled it and the Wishlist is non-empty;
- use local presentation controls without modifying or persisting changes to the owner's data;
- browse without access to private visits, memories, notes or owner-only records.

### 4.3 Product administrator

Administrative capabilities are not currently part of the documented portfolio scope.

## 5. Core capabilities

### 5.1 User authentication

The application supports account-based access so that travel information can be associated with an individual user.

Relevant behaviours include account creation, login/logout, e-mail verification/recovery, authentication-method linking, session handling, protected-route access and account deletion.

### 5.2 Interactive travel map

The map is a central element of the product. Users can select places, view status, assign/remove supported statuses, search/filter selected places and navigate between map and related flag presentation.

AtlasBadge contains 251 directly selectable geographic records. The conceptual global progress model is 252 Places / 195 Countries / 57 Territories and Entities because completing all four UK constituent countries derives one additional United Kingdom Place/Country.

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
- Wishlist ordering uses `wishlistOrderRank`, independently from `visitOrderRank`;
- Wishlist privacy defaults to private;
- the saved Wishlist-public preference survives unrelated profile-visibility changes;
- Clear Map removes Wishlist membership/ranks and resets its public preference;
- account deletion removes the associated private/public Wishlist data.

C35/AB-EV-035 is the current source for the corrected Visited + Passed through rule.

### 5.4 Visits, memories and manual order

Qualifying physical-presence places may contain registered visits and private memories.

`RegisteredVisit` represents an individual occurrence; travel status represents accumulated place history. Therefore a status transition such as Passed through → Visited does not manufacture a second visit. Additional actual occurrences are recorded through the visit-add workflow.

Relevant behaviours include:

- repeated visit creation/removal/editing;
- `visitsCount` derived from registered visits where applicable;
- explicit Save for memory text rather than persistence on every keystroke;
- preservation of private memories across supported status transitions;
- Manual Visit Order for qualifying physical-presence places only;
- independent Wishlist order;
- transactional integrity for the Born there user pointer/status relationship.

### 5.5 Persistent user data

Cloud Firestore is the primary source of truth for authenticated user progress.

The private source is:

```text
users/{uid}
users/{uid}/places/{placeId}
```

Private user/place records are owner-only.

Relevant behaviours include loading/saving status, visit and order changes; handling delayed/rejected/concurrent writes; maintaining consistency between optimistic UI, confirmed state and cache; and recovering safely across reload/session boundaries.

AB-EV-034 closes the previous QR-01 failed-write/recovery coverage gap using architectural equivalence and focused deterministic rollback evidence.

### 5.6 Public profile and sanitised projection

The public Profile is a read-only projection rather than a direct view of private Firestore records.

Public data is stored under:

```text
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

A non-owner or anonymous Profile viewer reads the public source only.

Public place projections must not expose `generalNote`, `registeredVisits`, memories/private visit details, `firstPhysicalPresenceAt`, `statusActivatedAt` or `visitsCount`.

Public achievement metadata contains only `unlockedAt` and `sequence`.

A public Wishlist tile appears only when Wishlist visibility is public and the Wishlist is non-empty. Its modal is read-only.

### 5.7 Responsive experience

AtlasBadge supports desktop and mobile web use. Testing considers navigation, map interaction, dialogs/forms, text readability, touch, scrolling, layout stability, modal background scroll locking, horizontal overflow and accessible interaction patterns.

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
4. The user chooses public/private Wishlist visibility.
5. Membership, order and privacy persist after reload.
6. If public and non-empty, the read-only public Profile displays the Wishlist.

### Journey 5: View a public profile

1. A visitor opens a valid public profile address.
2. Publicly projected travel information is displayed.
3. Private user/place records are not read by the visitor.
4. Private memories/visits remain absent from public responses.

## 7. Data overview

| Data category | Examples | Main quality concerns |
|---|---|---|
| Account data | User identifier, authentication state | Security, privacy, account isolation |
| Private profile data | User settings and owner-only lifecycle state | Authorisation, deletion, integrity |
| Private travel data | Statuses, visits, memories, ranks | Accuracy, persistence, ownership, concurrency |
| Wishlist data | Membership, `wishlistOrderRank`, `isWishlistPublic` | Status compatibility, privacy, independent ordering |
| Progress data | Counts, percentages, achievements | Calculation consistency, chronology |
| Local cache | UID-scoped browser state | Synchronisation, stale information |
| Public projection | `publicProfiles` root and places | Whitelisting, sanitisation, read-only access |

No real user credentials or personal data are included in this portfolio.

## 8. External services and technical dependencies

AtlasBadge depends on Vercel hosting/Git deployment, Firebase Authentication, Cloud Firestore, Firebase Storage where applicable, Firebase Emulator Suite, Playwright, browser storage/cache, map data/components and third-party web libraries.

A release that changes both frontend behaviour and Firestore Rules must preserve deployment parity; a Vercel READY state alone is not sufficient evidence that the Firebase security layer is aligned.

## 9. Product constraints and assumptions

- AtlasBadge remains under active V1.0 development.
- Features and business rules may change under Test Lead/Product Owner approval.
- Testing uses controlled accounts and disposable data.
- Desktop and mobile web experiences are relevant.
- Cloud Firestore is the intended source of truth for authenticated progress.
- Public Profile data is served from a separate sanitised projection.
- Emulator browser tests must fail fast rather than silently fall back to real Firebase.
- Production testing is controlled and limited to approved scenarios.
- Administrative functionality is outside current scope.

## 10. Quality characteristics

Key characteristics are functional correctness, data integrity, security/privacy, usability, reliability, compatibility, performance, accessibility and maintainability.

Maintainability includes keeping business rules central, maintaining automated expectations when requirements legitimately change, and avoiding unnecessary regression work when earlier checkpoints remain valid.

## 11. Known areas requiring clarification or future work

- Final localisation completeness across all V1.0 locales;
- broader browser/device compatibility beyond the current validated sample;
- quantitative performance targets;
- future per-memory visibility rules;
- future photos per RegisteredVisit;
- future Story/share scope;
- administrative/moderation capabilities if introduced.

These items are not automatically defects. They are open product/quality questions or future features requiring explicit assessment when they enter scope.

## 12. Related portfolio documents

- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/09-system-test-plan.md`
- `docs/12-lessons-learned.md`
- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
