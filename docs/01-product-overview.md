# AtlasBadge Product Overview

## 1. Document purpose

This document provides a high-level overview of AtlasBadge, including its purpose, target users, core capabilities, data flows, dependencies, constraints, and quality considerations.

It establishes the product context required for the risk analysis and test strategy documented in this repository.

> **Document status:** Completed and maintained through the AB-EV-033 Wishlist/public-profile Production baseline.

## 2. Product summary

AtlasBadge is an interactive travel platform that allows users to record, organise and present their travel history and future travel interests.

Users can interact with a world map, assign travel statuses to places, maintain detailed visits and memories, manage a private or public Wishlist, monitor geographic progress and achievements, and publish a read-only traveller profile.

The product combines travel tracking, personal progress, map interaction, collection-oriented features and selective public sharing in a single experience.

## 3. Product objectives

AtlasBadge aims to:

- Provide an intuitive way to record visited and planned destinations
- Allow users to classify places using meaningful travel statuses
- Maintain detailed visit history without compromising data integrity
- Present geographic progress in a visual and engaging format
- Persist each user's personal travel information securely
- Allow users to keep a Wishlist private or intentionally share it
- Allow users to share selected travel/profile information without exposing private records
- Provide a consistent experience across desktop and mobile devices
- Make travel tracking enjoyable through progress, flags, badges and achievements

## 4. Target users

### 4.1 Registered traveller

A registered traveller can:

- Create and access an account
- Select countries or places on the map
- Assign and change travel statuses
- Save visits and private memories
- Maintain a Wishlist based on the Want to visit status
- Choose whether the Wishlist is public or private
- Reorder visits and Wishlist items independently where supported
- Review saved travel progress and achievements
- Manage personal profile information
- Publish or hide a public profile

### 4.2 Public visitor

A public visitor can:

- Open a traveller's public profile when that profile is public
- View only information intentionally projected for public presentation
- Browse the public map, progress, flags and achievements
- View a public Wishlist only when the owner enabled it and the Wishlist is non-empty
- Use local presentation controls without modifying or persisting changes to the owner's data
- Browse without access to private visits, memories, notes or owner-only records

### 4.3 Product administrator

Administrative capabilities are not currently part of the documented portfolio scope.

This role may be introduced later if AtlasBadge requires content moderation, user support, data management, or product configuration features.

## 5. Core capabilities

### 5.1 User authentication

The application supports account-based access so that travel information can be associated with an individual user.

Relevant behaviours include:

- Account creation
- Login
- Logout
- E-mail verification and recovery
- Authentication-method linking
- Session handling
- Authentication error handling
- Access control for authenticated areas
- Account deletion

### 5.2 Interactive travel map

The map is a central element of the product.

Users can interact with countries or places and review their current travel status.

Relevant behaviours include:

- Selecting a place
- Viewing its current status
- Assigning or changing supported statuses
- Removing a status where supported
- Displaying visual differences between statuses
- Searching/filtering selected places
- Navigating between map and related place/flag presentation
- Maintaining usability across desktop and mobile screen sizes

AtlasBadge contains 251 directly selectable geographic records. The conceptual global progress model is 252 Places / 195 Countries / 57 Territories and Entities because completing all four UK constituent countries derives one additional United Kingdom Place/Country.

### 5.3 Travel status and Wishlist management

AtlasBadge supports six travel statuses:

- Visited
- Lived / Live there
- Born there
- Nationality
- Passed through
- Want to visit

The Wishlist uses `statuses.wishlist === true` / Want to visit as its source of truth; it is not a separate duplicate travel-status system.

Confirmed rules include:

- incompatible physical-presence states remove Want to visit according to the status rules;
- Wishlist-only records do not count as physical presence;
- Wishlist-only records do not participate in Manual Visit Order;
- Wishlist ordering uses `wishlistOrderRank`, independently from `visitOrderRank`;
- Wishlist privacy defaults to private;
- the saved Wishlist-public preference survives unrelated profile-visibility changes;
- Clear Map removes Wishlist membership/ranks and resets its public preference;
- account deletion removes the associated private/public Wishlist data.

### 5.4 Visits, memories and manual order

Qualifying physical-presence places may contain registered visits and private memories.

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

Relevant behaviours include:

- Loading saved data after login
- Saving status/visit/order changes
- Handling delayed, rejected or concurrent writes
- Maintaining consistency between optimistic UI, confirmed state and cloud data
- Preventing one user from accessing another user's private records
- Recovering safely from interruption/reload conditions

### 5.6 Public profile and sanitised projection

The public Profile is a read-only projection rather than a direct view of private Firestore records.

Public data is stored under:

```text
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

A non-owner or anonymous Profile viewer reads the public source only.

The approved public-profile root may expose only selected presentation fields including display information, profile visibility, Wishlist visibility, flag-sort preference, social links and sanitised achievement metadata.

Public place projections must not expose:

- `generalNote`;
- `registeredVisits`;
- memories or private visit details;
- `firstPhysicalPresenceAt`;
- `statusActivatedAt`;
- `visitsCount`.

Public achievement metadata contains only the public chronology fields `unlockedAt` and `sequence`.

A public Wishlist tile appears only when Wishlist visibility is public and the Wishlist is non-empty. Its modal is read-only and does not expose privacy, status, Save or ordering controls.

### 5.7 Responsive experience

AtlasBadge supports desktop and mobile web use.

Testing considers:

- Navigation
- Map interaction
- Dialogs and forms
- Text readability
- Touch interaction
- Scrolling
- Layout stability
- Modal background scroll locking
- Horizontal overflow
- Accessible interaction patterns

The AB-EV-033 release hardening closed a mobile grid regression that could collapse the Map control column to zero width.

## 6. High-level user journeys

### Journey 1: Register and begin tracking

1. A new user opens AtlasBadge.
2. The user creates an account and completes required verification.
3. The user enters the authenticated product area.
4. The user selects a place.
5. The user assigns a travel status.
6. The application persists the selection.
7. Progress is updated.

### Journey 2: Return to saved progress

1. An existing user opens AtlasBadge.
2. The user logs in.
3. Previously saved travel information is loaded.
4. The map displays the correct statuses.
5. The user continues updating travel progress.

### Journey 3: Manage a Wishlist

1. The user applies Want to visit to one or more places.
2. The Wishlist management surface shows those places.
3. The user may reorder them independently from visit chronology.
4. The user chooses public or private Wishlist visibility.
5. The selected membership, order and privacy persist after reload.
6. If public and non-empty, the read-only public Profile displays the Wishlist in the saved order.

### Journey 4: View a public profile

1. A visitor opens a valid public profile address.
2. The application resolves the public profile.
3. Publicly projected travel information is displayed.
4. Private user/place records are not read by the visitor.
5. Private memories/visits remain absent from public responses.
6. Invalid, unavailable or private profiles are handled according to the visibility rules.

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

AtlasBadge depends on services and technologies including:

- Vercel hosting and Git-triggered deployment
- Firebase Authentication
- Cloud Firestore
- Firebase Storage where applicable
- Firebase Emulator Suite for isolated technical/E2E validation
- Playwright for automated browser regression and controlled Production validation
- Browser local storage/cache behaviour
- Map data and visual map components
- Third-party libraries used by the web interface
- Supported browsers and mobile devices

A release that changes both frontend behaviour and Firestore Rules must preserve deployment parity; a Vercel READY state alone is not sufficient evidence that the Firebase security layer is aligned.

## 9. Product constraints and assumptions

The current portfolio is based on the following constraints/assumptions:

- AtlasBadge remains under active V1.0 development
- Features and business rules may change under Test Lead/Product Owner approval
- Testing uses controlled accounts and disposable data
- Desktop and mobile web experiences are both relevant
- Cloud Firestore is the intended source of truth for authenticated progress
- Public Profile data is served from a separate sanitised projection
- Emulator-based browser tests must fail fast rather than silently fall back to real Firebase
- Production testing is controlled and limited to approved scenarios
- Administrative functionality is outside current scope

## 10. Quality characteristics

The most relevant quality characteristics are:

### Functional correctness

Features and business rules must behave according to the intended product design.

### Data integrity

Statuses, visits, orders, progress and profile information must remain accurate and consistent through rapid or repeated changes.

### Security and privacy

Private user/travel data must remain owner-only. Public data must be intentionally projected and sanitised.

### Usability

Core travel-tracking, Wishlist and profile flows must be understandable and efficient.

### Reliability

Saved information must remain available across sessions and expected usage conditions.

### Compatibility

The application must behave consistently across the supported browser/device sample.

### Performance

Maps, profiles and user actions must remain acceptably responsive.

### Accessibility

Navigation, controls, dialogs, feedback and visual status indicators should remain usable with accessible interaction patterns.

### Maintainability

Product and test code should allow changes without creating unnecessary regression risk. Automated expectations must be maintained when product rules legitimately change rather than being masked by arbitrary locators or protocol-dependent assertions.

## 11. Known areas requiring clarification or future work

The following product decisions still require later refinement or broader evidence:

- Final localisation completeness across all V1.0 locales
- Broader browser/device compatibility beyond the current validated sample
- Quantitative performance targets
- Future per-memory visibility rules
- Future photos per RegisteredVisit
- Future Story/share scope
- Administrative and moderation capabilities, if introduced

These items are not automatically defects. They are open product/quality questions or future features that require explicit assessment when they enter scope.

## 12. Related portfolio documents

This product overview provides input for:

- Quality Risk Analysis
- Test Strategy
- Test Scope
- Test Environment Definition
- Entry/Exit Criteria
- Exploratory Test Charters
- Sample Test Cases
- Test Summary Reporting
- V1.0 Evidence Register, including AB-EV-033
