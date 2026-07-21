# AtlasBadge Test Scope

## 1. Document purpose

This document defines the testing scope for AtlasBadge during its incremental development and preparation for the first official V1.0 release.

It establishes:

* which product areas are covered;
* the current coverage status of each area;
* the representative test scenarios expected within each area;
* the priority and required test depth;
* the remaining mandatory coverage before V1.0;
* the features conditionally included or deliberately deferred;
* the current compatibility, accessibility, performance and automation boundaries;
* how the scope changes according to the risk and impact of an implementation.

AtlasBadge has evolved through continuous functional increments rather than formally numbered internal releases. This document therefore defines a stable product-level scope that can be selected and adjusted for each feature, correction or release.

It does not require every scenario to be executed after every change. The actual execution scope is selected according to the affected functionality, dependencies, quality risks and potential consequences of failure.

> **Document status:** Initial completed version covering the testing already performed and the remaining scope currently defined for AtlasBadge V1.0.

---

## 2. Relationship with other portfolio documents

This document should be read together with:

* [AtlasBadge Product Overview](01-product-overview.md);
* [AtlasBadge Quality Risk Analysis](02-quality-risk-analysis.md);
* [AtlasBadge Test Strategy](03-test-strategy.md).

The Product Overview defines the product and its users.

The Quality Risk Analysis identifies what could fail and why those failures matter.

The Test Strategy defines how testing is directed, executed and governed.

This Test Scope defines the product areas and scenario groups that require coverage, their current state, and the expected depth of testing.

It does not recreate the quality risk register or replace detailed test cases, exploratory charters, entry and exit criteria, defect records or test summary reports.

---

## 3. Scope model

AtlasBadge testing covers:

* functional increments already delivered and tested;
* corrections and refinements already completed;
* functionality still being implemented before V1.0;
* final integrated regression before the official release;
* controlled production validation after deployment.

The scope is maintained at product-area and scenario-group level.

Detailed preconditions, data, execution steps and expected results may be documented separately when the risk, complexity or need for repetition justifies structured test cases.

### 3.1 Coverage status

| Status                         | Meaning                                                                                                                                                                                         |
| ------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Executed**                   | Relevant functional, negative, exploratory and regression coverage has been performed for the current implementation. This does not claim exhaustive coverage of every theoretical combination. |
| **Partially executed**         | The current implementation has received meaningful coverage, but additional functionality, refinement or validation remains necessary.                                                          |
| **Planned before V1.0**        | The area or scenario has not yet received the required final coverage and is mandatory before V1.0 approval.                                                                                    |
| **Conditional V1.0 candidate** | The functionality may be included in V1.0 if development is completed in time. If implemented, its required testing becomes mandatory.                                                          |
| **Deferred beyond V1.0**       | The functionality is known but is deliberately not required for the first official release.                                                                                                     |
| **Out of current scope**       | The capability or form of testing is not part of the current AtlasBadge testing responsibility or available validation model.                                                                   |

An `Executed` status confirms that testing has occurred. It does not guarantee that no residual risk, known limitation or future regression risk exists.

---

## 4. Test priority and depth

The required depth is based on potential user impact, data and privacy consequences, implementation reach, regression probability and release importance.

| Priority     | Expected depth                                                                                                                                                                                                                       |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Critical** | Deep and release-blocking coverage. Includes positive, negative, boundary, state-transition, interruption, recovery, destructive, authorisation, persistence and regression scenarios where applicable. Strong evidence is expected. |
| **High**     | Broad coverage of the main flows, negative conditions, limits, state changes, integrations, persistence and affected dependencies.                                                                                                   |
| **Medium**   | Focused coverage sufficient to validate essential behaviour, known risks and the defined V1.0 baseline.                                                                                                                              |
| **Low**      | Targeted validation for an isolated low-risk change. This level is assigned to individual changes rather than permanently to a product area.                                                                                         |

No fixed AtlasBadge product area is classified as Low.

A cosmetic or textual change may initially be treated as Low, but its priority must increase if it can affect:

* data or persistence;
* authentication or privacy;
* accessibility;
* localisation;
* navigation;
* responsive behaviour;
* an essential user journey.

---

## 5. Critical scope

### 5.1 Authentication and account lifecycle

**Priority:** Critical
**Current status:** Executed

Coverage includes:

* successful and unsuccessful Google authentication;
* successful and unsuccessful e-mail and password registration;
* valid and invalid login credentials;
* mandatory e-mail verification;
* attempts to access the application before verification;
* refresh and recovery of authentication state after verification;
* password recovery;
* persistent authenticated sessions;
* logout;
* access to private pages after logout;
* direct access to private URLs without authentication;
* linking Google and password authentication methods;
* preservation of the same user identity and data after linking;
* cancellation, interruption and retry during account-access flows;
* username creation, change and public URL resolution;
* attempts to manipulate account or profile identifiers;
* behaviour after deleted, unavailable or invalid accounts.

Authentication coverage remains subject to regression whenever account identity, profile ownership, Firestore access, session handling or public profile resolution changes.

---

### 5.2 Data persistence and integrity

**Priority:** Critical
**Current status:** Executed

Coverage includes:

* creation, modification and deletion of travel data;
* Firestore as the primary source of truth;
* UID-specific local storage as cache and fallback;
* loading existing data after login;
* saving status changes;
* saving visits, durations, memories and notes;
* reloading the page after a change;
* returning in a later session;
* logout and subsequent login;
* temporary read or connectivity failure;
* recovery from cached state;
* consistency between the interface, local cache and Firestore;
* prevention of one user's data appearing in another account;
* repeated and rapid changes;
* interruption while data is being saved;
* detection of stale, missing or unexpectedly restored data;
* impact on counters, statistics, badges, achievements and profiles.

Any evidence of material data loss, corruption, cross-user contamination or unreliable persistence must block production approval until corrected or reduced to an acceptable level.

---

### 5.3 Privacy and user isolation

**Priority:** Critical
**Current status:** Executed for the current privacy model

Coverage includes:

* access attempts against another user's records;
* Firestore access restrictions;
* manipulation of direct URLs and identifiers;
* private pages without authentication;
* separation between public and private profile information;
* private profile behaviour;
* transition from public to private;
* transition from private to public;
* public profile access by authenticated and unauthenticated visitors;
* confirmation that public profiles remain read-only;
* confirmation that private memories and notes are not exposed;
* attempts to modify data from a public page;
* account isolation after logout or account switching;
* removal of public access after account deletion.

Per-memory privacy remains a **Conditional V1.0 candidate**.

If implemented before V1.0, coverage must include:

* setting a memory as public or private;
* default privacy behaviour;
* changing visibility;
* persistence of the selected visibility;
* behaviour of previously created memories;
* owner visibility;
* unauthenticated visitor visibility;
* direct URL and identifier manipulation;
* absence of private content in unauthorised responses;
* public profile regression;
* account deletion and data cleanup.

A partially implemented privacy control must not be released if it could mislead users or expose information contrary to their selected preference.

---

### 5.4 Travel status rules and combinations

**Priority:** Critical
**Current status:** Executed

Coverage includes all six statuses:

* Visited;
* Lived / Live there;
* Born there;
* Nationality;
* Passed through;
* Want to visit.

Scenario coverage includes:

* selecting and deselecting each status;
* valid combinations;
* prohibited combinations;
* automatic selection of dependent statuses;
* automatic removal of incompatible statuses;
* repeated selection and deselection;
* rapid interaction;
* transition through multiple valid and invalid states;
* restoration after refresh or a new session;
* normalisation of invalid or stale combinations;
* effect on visit counts;
* effect on memories and notes;
* map representation;
* country and territory totals;
* continent statistics;
* badges and achievements;
* public profile representation.

The full approved status-rule definition remains the source of truth for expected results.

---

### 5.5 Account and data deletion

**Priority:** Critical
**Current status:** Executed

Coverage includes:

* deletion of Google-authenticated accounts;
* deletion of e-mail and password accounts;
* deletion of accounts with linked authentication methods;
* recent-authentication requirements;
* cancellation and retry;
* partial failure handling;
* removal of the Firebase Authentication identity;
* removal of Firestore user records and related data;
* removal of travel-place records;
* removal of public-profile availability;
* username release;
* repeated deletion attempts;
* direct access after deletion;
* absence of orphaned or publicly accessible records.

Account deletion may be tested in production using controlled disposable accounts.

---

## 6. High-priority scope

### 6.1 Map and geographical catalogue

**Priority:** High
**Current status:** Executed

Coverage includes:

* all 251 selectable places;
* sovereign countries;
* territories;
* the four constituent nations of the United Kingdom;
* limited-recognition or de facto entities represented by the product;
* Antarctica;
* valid and unsupported place identifiers;
* search and place selection;
* map clicks and corresponding detail selection;
* aliases and geographical labels;
* flags and fallback behaviour;
* continent classification;
* map status colours and intensity;
* multiple-status visual priority;
* zoom and pan;
* touch and pointer interaction;
* desktop and mobile behaviour;
* persistence of selected places;
* consistency between the map, profile, counters and stored records.

The technical United Kingdom aggregate must not receive its own persisted travel status. England, Scotland, Wales and Northern Ireland are tested as independently selectable records.

---

### 6.2 Visits, duration, memories and notes

**Priority:** High
**Current status:** Partially executed

Executed coverage includes:

* initial visit count;
* minimum value and decrement behaviour;
* adding and reducing visits;
* repeated visits;
* duration values and units;
* creating, editing and deleting memories;
* creating and editing notes;
* empty, invalid and boundary values;
* automatic saving;
* refresh and session recovery;
* relationship between `visitsCount` and detailed memory records;
* impact on total visits;
* impact on the profile;
* destructive and repeated actions.

Per-memory privacy remains conditionally planned and is covered under the privacy section.

Future additions such as a photograph for each visit are **Deferred beyond V1.0** and do not block the first official release.

---

### 6.3 Personal profile

**Priority:** High
**Current status:** Executed

Coverage includes:

* loading the authenticated user's profile;
* editing supported profile fields;
* validation;
* username changes;
* display information;
* biography;
* supported social links;
* persistence after refresh and a later session;
* invalid and oversized values where limits apply;
* public and private profile settings;
* relationship between profile data and public presentation;
* account switching and isolation;
* responsive behaviour;
* empty and populated profile states.

---

### 6.4 Public profile

**Priority:** High
**Current status:** Partially executed
**V1.0 requirement:** Mandatory correction and refinement

Existing coverage includes:

* access through a valid public URL;
* invalid or unavailable usernames;
* authenticated and unauthenticated access;
* private profile behaviour;
* read-only enforcement;
* public and private data separation;
* map, progress, counters, badges and achievements;
* profile visibility transitions.

Remaining V1.0 coverage must include the implemented refinements relating to:

* loading speed;
* perceived responsiveness;
* interaction fluidity;
* mobile and desktop layout;
* public map presentation and behaviour;
* badge and achievement presentation;
* counter and statistic consistency;
* visual organisation and readability;
* empty, small and content-heavy profiles;
* the six supported locales;
* regression against profile privacy;
* regression against personal profile changes.

The exact scenario selection must be refined when the final public-profile implementation changes are available.

---

### 6.5 Counters and statistics

**Priority:** High
**Current status:** Executed

Coverage includes:

* visited or conquered places;
* countries;
* territories;
* continents;
* total visits;
* status summaries;
* physical-presence rules;
* places with multiple compatible statuses;
* repeated visits without duplicate conquered-place counting;
* recalculation after adding, removing or changing a status;
* recalculation after changing a visit count;
* refresh and later-session consistency;
* consistency between personal profile, public profile and map;
* empty-state values;
* geographical catalogue changes.

Counters and statistics must be included in regression whenever status rules, visits, geographical data, persistence, badges or achievements change.

---

### 6.6 Badges and achievements

**Priority:** High
**Current status:** Partially executed
**V1.0 requirement:** Mandatory expansion and visual refinement

Existing coverage includes:

* current unlock conditions;
* locked and unlocked states;
* recalculation when criteria are no longer met;
* progress changes;
* persistence and session recovery;
* United Kingdom achievement logic;
* relationship with counters and physical-presence rules.

Remaining coverage includes:

* the final set of new badges and achievements;
* their exact unlock and relock rules;
* threshold and near-threshold values;
* duplicate prevention;
* update after status or visit changes;
* visual refinement;
* readability and identification;
* locked, unlocked and progress states;
* desktop and mobile layout;
* localisation;
* consistency between personal and public profiles;
* regression of existing badges after the new set is introduced.

**Open question:** The final number, design and complete rule set of the additional badges will be confirmed during implementation.

---

### 6.7 Localisation and languages

**Priority:** High
**Current status:** Planned before V1.0
**V1.0 requirement:** Mandatory

The supported V1.0 locales are:

* Brazilian Portuguese (`pt-BR`);
* European Portuguese (`pt-PT`);
* British English (`en-GB`);
* European Spanish (`es-ES`);
* Latin American and Caribbean Spanish (`es-419`);
* French (`fr`).

`pt-BR` is the product fallback language.

The initial locale is automatically selected from the browser or device language preferences. The user must also be able to select another supported language manually, and the manual preference must be retained.

All system-controlled content is within localisation scope, including:

* public and private pages;
* navigation;
* buttons;
* menus;
* forms;
* validation;
* errors;
* confirmations;
* authentication;
* account lifecycle;
* status names;
* geographical names and categories;
* visits and durations;
* counters and statistics;
* badges and achievements;
* personal and public profiles;
* empty, loading and failure states;
* tooltips;
* accessibility labels;
* page titles and system-generated descriptions.

User-created memories, notes and biographies remain in the language entered by the user. Automatic translation of user-authored content is not part of V1.0.

Scenario coverage includes:

* automatic detection for every supported locale;
* multiple browser-language preferences and priority order;
* unsupported language or regional variant;
* fallback to `pt-BR`;
* manual locale selection;
* persistence after refresh, logout and later login;
* authenticated and unauthenticated use;
* missing translation fallback;
* absence of mixed-language interfaces;
* accents and special characters;
* pluralisation;
* terminology consistency;
* long-text expansion;
* truncation and overlapping;
* desktop and mobile layout;
* localisation of validation, error and confirmation messages.

A complete functional regression is performed in `pt-BR`.

Essential user journeys and localisation-specific checks are performed in `pt-PT`, `en-GB`, `es-ES`, `es-419` and `fr`.

---

### 6.8 Performance and responsiveness

**Priority:** High
**Current status:** Partially executed
**V1.0 requirement:** Mandatory status-selection improvement

Executed coverage includes:

* initial loading;
* map loading;
* zoom;
* pan;
* map interaction fluidity;
* repeated interaction;
* general stability;
* desktop behaviour;
* mobile behaviour;
* previous map-fluidity defect correction and retesting.

A perceptible delay currently exists when selecting a travel status.

The V1.0 acceptance approach is qualitative rather than based on an invented numerical service-level target.

Validation must confirm:

* immediate or acceptably fast visual feedback;
* no evident freeze or blocked interface;
* coherent update of the selected button;
* coherent map update;
* coherent counter and statistic update;
* reliable persistence;
* repeated rapid selection without progressive degradation;
* acceptable behaviour in Edge and Chrome on Windows;
* acceptable behaviour in Chrome on the tested Android device;
* perceptible improvement compared with the previous behaviour.

Formal load testing and quantitative response-time targets are not part of the current V1.0 scope.

---

## 7. Medium-priority scope

### 7.1 Browser and device compatibility

**Priority:** Medium
**Current status:** Partially executed

Executed environments:

| Platform        | Browser        | Device or viewport                            |
| --------------- | -------------- | --------------------------------------------- |
| Windows desktop | Microsoft Edge | 1600 × 900                                    |
| Android mobile  | Google Chrome  | Samsung Galaxy S20 FE, standard configuration |

Mandatory additional V1.0 coverage:

| Platform        | Browser       | Required coverage                                           |
| --------------- | ------------- | ----------------------------------------------------------- |
| Windows desktop | Google Chrome | Essential functional regression and localisation validation |

Essential compatibility coverage includes:

* authentication and account access;
* map and place selection;
* status management;
* visits and memories;
* persistence;
* personal profile;
* public profile;
* localisation;
* badges;
* counters and statistics;
* responsive layout.

No validated compatibility claim will be made for:

* Firefox;
* Safari;
* iPhone;
* iPad;
* tablets;
* macOS;
* multiple Android devices;
* a comprehensive range of resolutions and viewports.

The absence of iPhone and Safari coverage is a known test-environment limitation.

---

### 7.2 Accessibility baseline

**Priority:** Medium
**Current status:** Partially executed
**V1.0 requirement:** Mandatory baseline assessment

The pre-V1.0 baseline must cover the essential user journeys and identify obvious barriers relating to:

* keyboard navigation;
* logical focus order;
* visible focus;
* buttons, links, menus, modals and forms;
* accessible names;
* field labels and instructions;
* understandable validation and error messages;
* text and control contrast;
* browser zoom and content resizing;
* relevant image alternatives;
* status identification without relying only on colour;
* touch-target usability;
* basic screen-reader behaviour;
* page language declaration for each supported locale;
* reduced-motion behaviour where relevant.

A complete formal WCAG 2.2 Level AA assessment is **Deferred beyond V1.0**.

No WCAG 2.2 AA compliance claim will be made until the applicable criteria have been formally tested, documented and supported by evidence.

---

### 7.3 Playwright automation suite

**Priority:** Medium
**Current status:** Planned before V1.0
**V1.0 requirement:** Mandatory initial suite

The first permanent automation suite will use:

* Playwright;
* TypeScript.

The initial candidates are:

* public-page smoke tests;
* e-mail and password authentication;
* authenticated access;
* logout;
* map availability and place selection;
* status persistence;
* refresh and state recovery;
* personal profile;
* read-only public profile.

The suite must be:

* versioned;
* repeatable;
* maintainable;
* executable locally on demand;
* used to support relevant regression and the final V1.0 validation.

Continuous integration execution on commits, pull requests or deployments is not required for V1.0.

Automation supports but does not replace:

* exploratory testing;
* visual assessment;
* usability assessment;
* perceived-performance evaluation;
* language-quality review;
* accessibility evaluation;
* Test Lead release approval.

---

### 7.4 Deployment and production smoke testing

**Priority:** Medium
**Current status:** Executed as an ongoing practice

After deployment, production smoke testing covers essential availability and user journeys.

For the V1.0 release, production smoke testing is required in:

* `pt-BR`;
* `en-GB`.

The smoke scope includes:

* public application access;
* account creation or login;
* authenticated navigation;
* map loading;
* place and status interaction;
* persistence after refresh;
* personal profile;
* read-only public profile;
* logout;
* confirmation that no critical deployment failure has been introduced.

Production-only scenarios are executed when they cannot be reproduced adequately in the local environment.

Controlled disposable accounts and data may be used, including for destructive account-deletion scenarios.

---

### 7.5 Pre-launch data reset and clean-start validation

**Priority:** Medium
**Current status:** Planned before V1.0
**V1.0 requirement:** Mandatory

All accounts and data currently present in the production Firebase environment are test data and may be deleted.

Before the official launch:

1. the final regression is completed;
2. controlled production smoke testing is completed;
3. disposable test accounts are deleted;
4. Firestore test data is removed;
5. related authentication records are removed;
6. usernames and public profiles are cleared;
7. no test data remains available through public URLs;
8. local cache or fallback data is prevented from repopulating the clean database;
9. the empty-state experience is validated;
10. production is left completely empty.

The official V1.0 production state must contain:

* no user accounts;
* no demonstration profile;
* no preloaded travel data;
* no test visits or memories;
* no test badges or statistics;
* no orphaned public records.

The first real user must receive a clean initial state.

---

## 8. Mandatory remaining V1.0 scope

The following items must be implemented, tested and accepted before V1.0 approval:

* new badges and achievement coverage;
* badge visual refinement;
* improvement of the perceived status-selection delay;
* complete localisation for the six supported locales;
* automatic locale detection;
* manual locale selection and persistence;
* public-profile correction and refinement;
* public-profile map validation;
* Chrome desktop compatibility coverage;
* essential accessibility baseline;
* initial permanent Playwright and TypeScript suite;
* regression after each remaining implementation;
* final broad regression;
* production smoke testing in `pt-BR` and `en-GB`;
* production test-account and data cleanup;
* clean-start validation with an empty production database.

---

## 9. Conditional V1.0 scope

### Per-memory privacy

Per-memory visibility may be implemented before or after V1.0 depending on development capacity.

If it is implemented before V1.0:

* its complete privacy coverage becomes mandatory;
* public and private defaults must be unambiguous;
* unauthorised exposure must block release;
* personal and public profile regression is required;
* persistence and account-deletion behaviour must be validated.

If it is not implemented, memories remain private under the current product behaviour and the functionality is moved to the post-V1.0 scope.

---

## 10. Deferred beyond V1.0

The following product capabilities do not block the first official release:

* photograph for each visit;
* generated Instagram Story;
* per-memory privacy when not completed before V1.0;
* additional future badges beyond the final V1.0 set;
* formal WCAG 2.2 Level AA assessment;
* broad multi-browser and multi-device compatibility expansion;
* formal multi-user load and stress testing;
* quantitative performance service-level targets;
* automated Playwright execution in continuous integration.

When any deferred feature enters development, its risks and testing scope must be reassessed before release.

---

## 11. Out of current scope

The following are outside the current test scope:

* administrative, moderation or back-office capabilities not currently present in AtlasBadge;
* professional external penetration testing;
* independent security certification;
* formal accessibility certification;
* exhaustive validation on every browser, device and screen size;
* production testing with real customer data;
* translation of user-authored memories, notes or biographies;
* proving that every theoretical combination or usage condition has been tested;
* CI/CD automation that has not yet been implemented.

Functional security and privacy testing remains in scope, but it must not be represented as an external security audit or professional penetration test.

---

## 12. Scope adjustment by change impact

### 12.1 Low-impact change

Examples include isolated wording, spacing or cosmetic corrections with no expected functional effect.

Expected coverage:

* direct validation of the change;
* affected-page visual check;
* concise core regression where appropriate.

The impact must be reassessed if localisation, accessibility, navigation or responsive behaviour changes.

### 12.2 Medium-impact change

Examples include a contained component adjustment, profile-field change or isolated calculation correction.

Expected coverage:

* direct functional scenarios;
* relevant positive and negative cases;
* affected integration points;
* refresh and persistence where applicable;
* rapid core regression;
* targeted regression of dependent areas.

### 12.3 High-impact change

Examples include badges, public-profile changes, geographical catalogue changes, localisation or performance-sensitive interaction changes.

Expected coverage:

* broad functional coverage;
* negative and boundary conditions;
* state transitions;
* multiple data states;
* desktop and mobile behaviour;
* persistence;
* affected calculations;
* localisation;
* compatibility;
* broad dependency regression.

### 12.4 Critical-impact change

Examples include authentication, Firestore persistence, privacy, account deletion, identity linking or status-rule architecture.

Expected coverage:

* deep positive and negative testing;
* interruption and recovery;
* destructive actions;
* unauthorised access attempts;
* user isolation;
* direct data and identifier manipulation;
* stale and conflicting state;
* persistence and reload;
* affected calculations and profiles;
* structured evidence;
* direct retesting;
* expanded regression;
* explicit residual-risk review before deployment.

---

## 13. Regression scope

### 13.1 Regression after each implementation

Every implementation receives:

* direct feature validation;
* a rapid regression of the essential product core;
* broader regression of affected areas based on impact.

The recurring core includes:

* login;
* account behaviour;
* map;
* selectable places;
* memories;
* personal profile;
* public profile.

### 13.2 Regression after defect correction

Every correction receives:

* reproduction of the original failure;
* direct retesting;
* confirmation of the expected behaviour;
* regression of the affected feature;
* regression of relevant dependencies;
* review of any remaining limitation.

### 13.3 Final V1.0 regression

A broad final regression is required before official approval.

It includes:

* authentication;
* account lifecycle;
* account deletion;
* persistence and data integrity;
* privacy and user isolation;
* status rules;
* visits and memories;
* map and geographical catalogue;
* personal profile;
* public profile;
* counters and statistics;
* badges and achievements;
* localisation;
* compatibility baseline;
* accessibility baseline;
* performance and stability;
* Playwright automated checks;
* pre-launch production readiness.

The complete functional regression is executed in `pt-BR`.

Essential flows are executed in the other five supported locales.

---

## 14. Evidence expectations

Evidence is proportional to priority and release relevance.

### Critical

Expected evidence may include:

* structured test cases or detailed checklists;
* test data and account state;
* expected and observed results;
* screenshots;
* Firestore or browser-state inspection;
* defect evidence;
* retest results;
* regression results;
* explicit residual-risk assessment.

### High

Expected evidence may include:

* scenario checklists;
* exploratory notes;
* screenshots;
* comparison across states or devices;
* defect and retest evidence;
* affected-area regression results.

### Medium

Expected evidence may include:

* focused checklists;
* automated execution output;
* browser and device notes;
* smoke results;
* accessibility observations;
* production cleanup confirmation.

### Low

A brief validation note may be sufficient unless unexpected impact is discovered.

---

## 15. Test data boundaries

Testing uses controlled accounts and disposable data.

Data may be freely created, modified and deleted to validate:

* statuses;
* visits;
* memories;
* profiles;
* account isolation;
* destructive operations;
* account deletion;
* persistence;
* automated test repeatability.

Real data belonging to other users must not be used.

Credentials, access tokens and private account details must not be committed to the portfolio repository.

Before V1.0 opens to real users, every test account and all test data will be deleted and production will be returned to a completely empty state.

---

## 16. Assumptions, open questions and proposed evolution

### Assumptions

* The local development environment remains sufficiently representative for most functional testing.
* Controlled production testing remains available after deployment.
* The supported Firebase and Vercel environments remain available during V1.0 validation.
* Preliminary AI-assisted checks do not replace Test Lead validation.
* All current production data remains disposable until the official launch.

### Open questions

* Whether per-memory privacy will be completed before V1.0.
* The final number and detailed rules of the additional V1.0 badges.
* The exact final implementation changes included in the public-profile refinement workstream.

These questions must be resolved or reclassified when the related implementation becomes available.

### Proposed future evolution

* integrate Playwright execution into CI/CD;
* expand browser engines and viewport coverage;
* introduce additional physical-device testing;
* perform a formal WCAG 2.2 Level AA assessment;
* establish quantitative performance baselines where valuable;
* introduce formal load and scalability testing if the user base grows;
* consider professional external security testing when product scale or risk justifies it.

---

## 17. Scope completion and release use

The scope for a feature, correction or release is considered sufficiently covered when:

* the selected scenarios have been executed;
* the required depth matches the risk and impact;
* reported defects have been retested;
* the required regression has passed;
* known limitations are documented;
* residual risk has been assessed;
* no unacceptable data, privacy, access or essential-flow risk remains.

The Test Lead determines the final execution scope and may expand it whenever new evidence reveals additional risk.

The Test Lead also holds the final decision to approve, approve with accepted minor risk, or block deployment.
