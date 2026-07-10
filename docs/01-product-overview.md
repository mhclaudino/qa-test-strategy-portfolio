# AtlasBadge Product Overview

## 1. Document purpose

This document provides a high-level overview of AtlasBadge, including its purpose, target users, core capabilities, data flows, dependencies, constraints, and quality considerations.

It establishes the product context required for the risk analysis and test strategy documented in this repository.

> **Document status:** Initial working version. The content will evolve as AtlasBadge features and business rules are refined.

## 2. Product summary

AtlasBadge is an interactive travel platform that allows users to record, organise, and present their travel history.

Users can interact with a map, assign travel statuses to places, monitor their progress, and build a public traveller profile.

The product combines travel tracking, personal progress, map interaction, and profile presentation in a single experience.

## 3. Product objectives

AtlasBadge aims to:

- Provide an intuitive way to record visited and planned destinations
- Allow users to classify places using meaningful travel statuses
- Present travel progress in a visual and engaging format
- Persist each user's personal travel information securely
- Allow users to share selected travel information through a public profile
- Provide a consistent experience across desktop and mobile devices
- Make travel tracking enjoyable through progress and collection-oriented elements

## 4. Target users

### 4.1 Registered traveller

A registered traveller can:

- Create and access an account
- Select countries or places on the map
- Assign and change travel statuses
- Save personal travel progress
- Review previously recorded information
- Access their personal profile
- Share a public profile when the feature is available and enabled

### 4.2 Public visitor

A public visitor may be able to:

- Open a traveller's public profile
- View information intentionally shared by that traveller
- Browse the profile without accessing private account data
- Use the public profile across supported devices and browsers

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
- Session handling
- Authentication error handling
- Access control for authenticated areas

### 5.2 Interactive travel map

The map is a central element of the product.

Users can interact with countries or places and review their current travel status.

Relevant behaviours include:

- Selecting a place
- Viewing its current status
- Assigning a new status
- Changing an existing status
- Removing a status where supported
- Displaying visual differences between statuses
- Maintaining usability across different screen sizes

### 5.3 Travel status management

Users can classify destinations according to their travel relationship with each place.

The exact list and business meaning of each status are controlled by the AtlasBadge product definition.

Testing must confirm that:

- Only supported statuses can be assigned
- Status changes are saved correctly
- The interface reflects the saved status
- Progress calculations remain consistent
- Status information is isolated between users

### 5.4 Persistent user data

Authenticated user progress is stored persistently so that it remains available across sessions and devices.

The current implementation is intended to use cloud-based storage for the primary user record, with local browser storage retained as a cache or fallback where applicable.

Relevant behaviours include:

- Loading saved data after login
- Saving status changes
- Handling delayed or failed saves
- Preventing one user from accessing another user's data
- Maintaining consistency between the interface, local cache, and cloud data
- Recovering gracefully from temporary connectivity problems

### 5.5 User profile

Each registered user has a profile representing their travel activity.

The profile may contain:

- Username
- Display information
- Travel progress
- Status summaries
- Selected public travel information
- A public profile address

Public and private information must remain clearly separated.

### 5.6 Responsive experience

AtlasBadge is intended to support common desktop and mobile screen sizes.

Testing must consider:

- Navigation
- Map interaction
- Dialogs and forms
- Text readability
- Touch interaction
- Scrolling
- Layout stability
- Accessible interaction patterns

## 6. High-level user journeys

### Journey 1: Register and begin tracking

1. A new user opens AtlasBadge.
2. The user creates an account.
3. The user enters the authenticated product area.
4. The user selects a place.
5. The user assigns a travel status.
6. The application saves the selection.
7. The user's progress is updated.

### Journey 2: Return to saved progress

1. An existing user opens AtlasBadge.
2. The user logs in.
3. Previously saved travel information is loaded.
4. The map displays the correct statuses.
5. The user continues updating their travel progress.

### Journey 3: Change an existing status

1. The user selects a place with an existing status.
2. The current status is displayed.
3. The user selects a different status.
4. The new status is saved.
5. Progress and visual indicators are recalculated.
6. The updated information remains available after refreshing or returning later.

### Journey 4: View a public profile

1. A visitor opens a valid public profile address.
2. The application locates the requested profile.
3. Publicly available travel information is displayed.
4. Private account information remains hidden.
5. Invalid or unavailable usernames are handled clearly.

## 7. Data overview

AtlasBadge may process the following categories of information:

| Data category | Examples | Main quality concerns |
|---|---|---|
| Account data | User identifier, authentication details | Security, privacy, account isolation |
| Profile data | Username, display information | Validation, uniqueness, public visibility |
| Travel data | Place identifier and assigned status | Accuracy, persistence, ownership |
| Progress data | Counts, percentages, summaries | Calculation consistency |
| Local cache | Temporarily stored browser data | Synchronisation, stale information |
| Public profile data | Information visible without login | Privacy, access control, correctness |

No real user credentials or personal data are included in this portfolio.

## 8. External services and technical dependencies

AtlasBadge depends on services and technologies that may include:

- Web hosting and deployment through Vercel
- User authentication services
- Firestore or equivalent cloud data storage
- Browser local storage for caching or fallback behaviour
- Map data and visual map components
- Third-party libraries used by the web interface
- Supported web browsers and mobile devices

Failures or changes in these dependencies may affect product behaviour and must be considered during testing.

## 9. Product constraints and assumptions

The current portfolio is based on the following assumptions:

- AtlasBadge is under active development
- Features and business rules may change
- The production-sized user base is not yet established
- Testing is currently performed using controlled accounts and test data
- Desktop and mobile web experiences are both relevant
- Cloud persistence is the intended source of truth for authenticated user progress
- Local storage may temporarily support caching or fallback behaviour
- Public profile functionality may evolve as privacy and sharing rules are refined
- Administrative functionality is outside the current testing scope

These assumptions must be reviewed whenever the product changes significantly.

## 10. Quality characteristics

The most relevant quality characteristics for AtlasBadge are:

### Functional correctness

Features and business rules must behave according to the intended product design.

### Data integrity

Travel selections, statuses, progress, and profile information must remain accurate and consistent.

### Security and privacy

Users must only access information they are authorised to see or modify.

### Usability

Core travel-tracking flows must be understandable and efficient.

### Reliability

Saved information must remain available across sessions and expected usage conditions.

### Compatibility

The application must behave consistently across supported browsers, devices, and screen sizes.

### Performance

Maps, profiles, and user progress must load and respond within acceptable times.

### Accessibility

Navigation, controls, text, feedback, and visual status indicators should remain usable by people with different accessibility needs.

### Maintainability

Features and tests should be structured so that changes can be introduced without creating unnecessary regression risk.

## 11. Known areas requiring clarification

The following product decisions should be refined as development progresses:

- Final list and meaning of travel statuses
- Rules for changing or removing a status
- Username creation and uniqueness rules
- Public profile visibility controls
- Handling of deleted or disabled accounts
- Conflict resolution between local and cloud data
- Supported browsers and minimum device requirements
- Expected performance targets
- Accessibility acceptance criteria
- Administrative and moderation requirements
- Data retention and account deletion behaviour

These items are not automatically defects. They are product questions that may create testing gaps or quality risks if left undefined.

## 12. Related portfolio documents

This product overview provides input for:

- Quality Risk Analysis
- Test Strategy
- Test Scope
- Test Environment Definition
- Exploratory Test Charters
- Sample Test Cases
- Test Summary Reporting
