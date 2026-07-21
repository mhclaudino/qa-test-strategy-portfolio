# Test Environments

**Document status:** Initial completed version
**Product:** AtlasBadge
**Document owner:** Test Lead
**Last updated:** 21 July 2026

---

## 1. Purpose

This document describes the real environments, browsers, devices, configurations, data conditions and environmental limitations used to validate AtlasBadge.

It defines:

* the purpose of each available environment;
* what is normally validated locally;
* when production may be used for testing;
* the supported browser and device sample for V1.0;
* how languages are distributed across environment combinations;
* how test accounts and data are prepared;
* how the Firebase Emulator Suite is used;
* how the planned Playwright suite must be isolated;
* how production will be reset before launch;
* how environmental limitations affect residual risk and release approval.

This document does not describe an artificial enterprise test infrastructure.

AtlasBadge does not currently have:

* a dedicated staging environment;
* a separate QA server;
* a device laboratory;
* comprehensive cross-browser coverage;
* comprehensive operating-system coverage;
* a separate Firebase project for local development and production.

Environmental coverage is based on the tools, browsers and physical devices genuinely available to the project.

---

## 2. Environment principles

The following principles apply to environment selection and use:

1. The local application is the primary environment for development validation and pre-deployment functional testing.

2. Production is not the main test environment.

3. Production may be used for controlled smoke testing and for scenarios that cannot be reproduced adequately in the local environment.

4. Environment selection must be proportional to product and change risk.

5. Critical and High-risk areas require stronger environmental confidence than isolated low-impact changes.

6. The absence or unavailability of an environment does not silently remove an important scenario from scope.

7. Unaffected scenarios may continue when a particular environment or service is unavailable.

8. Approval remains blocked when an environmental gap leaves relevant residual risk.

9. The Test Lead has final authority over environmental exceptions, production testing and release approval.

10. No claim of universal browser, device, resolution or accessibility compatibility may be made from the available sample.

---

## 3. Current environment landscape

AtlasBadge currently uses three distinct execution contexts:

| Context                                  | Purpose                                                                              | Application target                             | Persistence target                              | Current status                      |
| ---------------------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------- | ----------------------------------------------- | ----------------------------------- |
| Local application environment            | Development, functional validation, exploratory testing and regression               | Next.js application running locally on Windows | Real Firebase project `atlas-badge`             | Active                              |
| Firebase Emulator technical test context | Isolated backend and security-rule verification                                      | Local technical scripts                        | Authentication, Firestore and Storage Emulators | Active for specific technical tests |
| Production environment                   | Published product, post-deployment smoke and approved production-dependent scenarios | Vercel deployment                              | Real Firebase project `atlas-badge`             | Active                              |

The local application and production currently use the same Firebase project.

This means that the web application may run in two different execution contexts, local Next.js and Vercel production, while still reading and writing to the same Firebase Authentication, Cloud Firestore and Firebase Storage services.

This shared persistence layer is a confirmed environmental limitation.

---

## 4. Local development and test environment

### 4.1 Configuration

The project is developed on a Windows computer.

Local project path:

```text
D:\AI\AtlasBadge
```

Relevant technology includes:

* Next.js 16;
* React;
* TypeScript;
* Tailwind CSS;
* Firebase Authentication;
* Cloud Firestore;
* Firebase Storage;
* Vercel;
* GitHub.

The application is normally started and validated locally before deployment.

The local Firebase Client SDK selects its project through environment variables, including `NEXT_PUBLIC_FIREBASE_PROJECT_ID`.

The local configuration currently points to:

```text
atlas-badge
```

The Vercel production configuration also points to the same Firebase project.

### 4.2 Purpose

The local environment is used for:

* implementation verification;
* preliminary technical checks;
* functional testing;
* exploratory testing;
* quick regression;
* defect reproduction;
* defect retesting;
* validation of affected areas;
* linguistic validation before deployment;
* preparation for release regression;
* future Playwright execution after safe emulator integration.

### 4.3 Normal local validation flow

The normal flow is:

1. Implementation is completed.
2. Preliminary technical checks are performed.
3. The application is confirmed to start and operate locally.
4. A summary of the changes is provided.
5. The Test Lead performs functional validation.
6. Quick regression is performed.
7. Defects are recorded and corrections are requested.
8. Corrected behaviour is retested.
9. Affected areas receive proportional regression.
10. The Test Lead approves or rejects the change.
11. The approved version is deployed.
12. A production smoke test is performed.

Most functional testing should be completed locally before deployment.

Production should not be used merely to replace local tests that could reasonably have been completed before deployment.

### 4.4 Technical checks before functional validation

Technical checks are proportional to the change.

For a small, isolated and low-risk change, it may be sufficient to confirm that:

* the application starts;
* the affected area is accessible;
* no obvious technical error prevents testing;
* the changed behaviour can be exercised locally.

Lint, build and other technical commands are used according to risk and scope.

A complete production build is expected for:

* relevant or higher-risk changes when applicable;
* significant releases;
* release preparation;
* the final V1.0 regression.

A full build is not automatically required before every small local validation.

### 4.5 Shared Firebase limitation before launch

Before the final V1.0 reset, all existing accounts and data are test data and may be discarded.

During this period, local tests may create, modify and delete data through the real `atlas-badge` Firebase project.

This is accepted temporarily because:

* no current account is treated as a real production user;
* no current test data must be preserved;
* all test identities and data will be removed before launch;
* the production database will be reset completely.

This temporary acceptance does not remove the environmental risk.

Changes made locally may be visible in the published application because both contexts use the same backend.

### 4.6 Local restrictions after the final reset

Immediately after the final production reset, local execution must not create, modify or delete data in the real Firebase project.

Two control layers are required:

#### Process control

Manual and automated local tests must not perform persistence operations against the real Firebase project after the clean-start state has been established.

#### Technical protection

A technical control must prevent unsafe local writes to the real Firebase project.

The responsible technical role may propose the implementation, but the solution must:

* distinguish local, emulator and production execution;
* prevent silent fallback to the real Firebase project;
* fail explicitly when the configuration is unsafe;
* allow persistence testing only through the Firebase Emulator Suite or an approved controlled production flow;
* provide sufficient evidence that the protection works;
* be validated and approved by the Test Lead.

Until this protection exists, local persistence testing after the final reset is prohibited.

---

## 5. Firebase Emulator technical test context

### 5.1 Current emulator services

The Firebase Emulator Suite is configured for:

* Authentication;
* Cloud Firestore;
* Firebase Storage;
* Emulator UI.

Authentication, Firestore and Storage Emulators have confirmed use in isolated technical scripts.

The Emulator UI is configured, but regular interactive use has not been confirmed.

Hosting and Functions Emulators are not part of the confirmed current configuration.

### 5.2 Current purpose

The emulator context is currently used for technical backend verification, including:

* Firestore security-rule tests;
* Storage-related rule or operation tests;
* account-deletion logic;
* simulated authenticated and unauthenticated contexts;
* isolated Firebase Admin SDK operations;
* verification using a demonstration project identifier rather than the real Firebase project.

This context is not a complete staging environment.

It does not currently represent the full browser-based AtlasBadge application operating against emulated services.

### 5.3 Current limitation

The Firebase Client SDK used by the Next.js application is not currently configured to connect the browser application to the Firebase Emulators.

The local application therefore continues to use the real Firebase project defined in `.env.local`.

The existence of emulator scripts does not prove that:

* the browser application uses the emulators;
* complete authentication flows use the emulators;
* complete persistence flows use the emulators;
* end-to-end tests are isolated from the real Firebase project.

### 5.4 Future Playwright integration

A permanent Playwright suite using TypeScript is planned before V1.0.

Playwright is not currently installed or configured in the project.

The planned suite will use:

* Chromium as the primary automated browser;
* installed Google Chrome or Microsoft Edge for selected release executions;
* local execution;
* on-demand execution;
* execution before relevant releases;
* execution during the final V1.0 regression.

For any Playwright scenario that creates, modifies or deletes data, normal execution must use:

* Authentication Emulator;
* Firestore Emulator;
* Storage Emulator.

No Playwright persistence test may be executed until:

1. the Firebase Client SDK explicitly supports the three emulators;
2. the test command requires emulator mode;
3. silent fallback to the real Firebase project is prevented;
4. expected hosts and ports are verified;
5. test data preparation and cleanup are controlled;
6. a safe control scenario proves that no data reached the real Firebase project.

If a required emulator does not start correctly, the persistence-test command must fail before opening the automated browser.

The suite must not switch to the real Firebase project as a fallback.

The responsible technical role may propose the data preparation and cleanup strategy, but it must provide:

* isolation;
* repeatability;
* a known starting state;
* reliable cleanup;
* Test Lead approval.

### 5.5 Production Playwright execution

A controlled Playwright smoke subset may be executed against production when approved by the Test Lead.

This is separate from normal emulator-based suite execution.

A production run must:

* use disposable accounts and data;
* avoid real user data;
* be limited to approved scenarios;
* include controlled cleanup;
* distinguish test failure from environmental failure, instability, outdated expectations or inconclusive results.

---

## 6. Production environment

### 6.1 Configuration and purpose

AtlasBadge is published through Vercel.

Production uses the Firebase project:

```text
atlas-badge
```

Production is used for:

* smoke testing after deployment;
* validation of integrations or behaviour that cannot be reproduced adequately locally;
* validation on physical mobile and tablet devices;
* controlled disposable-account scenarios;
* controlled destructive scenarios when necessary and safe;
* final V1.0 clean-start validation.

Production is not the primary environment for functional testing.

### 6.2 Conditions for production testing

A production-dependent scenario may proceed only when:

* all reasonable local testing has been completed;
* the relevant risk has been assessed;
* there is no unacceptable privacy, security or data-integrity risk;
* the reason for production validation is clearly identified;
* test accounts and data are controlled;
* cleanup expectations are defined;
* the Test Lead approves the controlled deployment or test.

The responsible technical role may recommend a production test.

Final approval belongs to the Test Lead.

### 6.3 Production persistence tests after launch

After V1.0 launch, persistence tests may be conducted in production using disposable accounts.

These tests must:

* be approved by the Test Lead;
* use no real user data;
* remain limited to the necessary scenarios;
* avoid interaction with real user accounts;
* remove the account and associated data after execution;
* verify cleanup across all relevant services.

The Test Lead decides whether a production test, emulator-based test, alternative evidence or change blockage is appropriate.

### 6.4 Preview deployments

Vercel Preview Deployments may exist, but they are not part of the formal AtlasBadge validation flow currently documented.

They must not be represented as a confirmed staging or QA environment.

If they become part of the formal process later, their configuration, persistence destination, purpose and limitations must be assessed before this document is updated.

---

## 7. Browsers, devices and resolutions

### 7.1 Confirmed and planned V1.0 coverage

| Platform       | Browser        | Minimum major version | Device or configuration          | V1.0 status                             |
| -------------- | -------------- | --------------------: | -------------------------------- | --------------------------------------- |
| Windows        | Microsoft Edge |                  150+ | Physical desktop at `1600 × 900` | Confirmed coverage                      |
| Windows        | Google Chrome  |                  150+ | Same physical desktop            | Mandatory before V1.0; not yet executed |
| Android        | Google Chrome  |                  150+ | Samsung Galaxy S20 FE            | Confirmed physical device               |
| Android tablet | Google Chrome  |  150+ where available | Redmi Pad 2                      | Additional, non-blocking coverage       |

The exact Edge version confirmed during environment definition was:

```text
150.0.4078.65
```

The exact Chrome version confirmed on the Samsung Galaxy S20 FE was:

```text
150.0.7871.128
```

The document uses major browser versions to define the baseline.

Exact build versions may be retained in test evidence where relevant.

Chrome desktop coverage must not be considered completed until Chrome has been installed and used during V1.0 validation.

### 7.2 Mobile orientation

The Samsung Galaxy S20 FE must be tested in:

* portrait orientation;
* landscape orientation.

The Redmi Pad 2, when available, will be tested in:

* portrait orientation;
* landscape orientation.

The tablet is additional coverage.

Absence of the tablet alone does not automatically block V1.0.

However, any relevant defect discovered on the tablet must be assessed normally and may affect approval according to risk.

### 7.3 Desktop resolution

The principal physical desktop resolution is:

```text
1600 × 900
```

Google Chrome may also be checked at another physically available resolution when a specific risk, defect or behaviour requires it.

No second desktop resolution is defined as a mandatory baseline.

The project does not currently use browser device emulation or a formal window-resizing matrix as part of its confirmed responsive coverage.

### 7.4 Coverage not validated comprehensively

The following are not part of the declared validated V1.0 sample:

* Mozilla Firefox;
* Apple Safari;
* iPhone;
* iPad;
* macOS;
* multiple Android device models;
* comprehensive tablet coverage;
* multiple mandatory desktop resolutions;
* historical browser-version coverage;
* broad operating-system combinations.

These browsers and devices are outside the declared sample, but a known severe defect affecting them must not be ignored automatically.

The Test Lead may require investigation, correction, additional evidence, risk acceptance or release blockage according to impact and likelihood.

No universal or complete cross-browser compatibility claim may be made.

---

## 8. Language coverage by environment

AtlasBadge V1.0 requires:

* `pt-BR`;
* `pt-PT`;
* `en-GB`;
* `es-ES`;
* `es-419`;
* `fr`.

Before V1.0, validation must cover:

* automatic browser or device detection;
* manual language selection;
* language persistence;
* translated content;
* consistency of actions and functionality;
* layout behaviour with translated text.

The six languages must be validated before deployment, but not every language must be multiplied across every browser, device, resolution and orientation.

Combination selection is risk-based.

Factors include:

* scope of the change;
* translated text length;
* affected component;
* browser-specific behaviour;
* responsive risk;
* previous defects;
* criticality of the flow.

### 8.1 Mandatory production smoke languages

The V1.0 production smoke must include:

* `pt-BR`;
* `en-GB`.

One language will be validated in Microsoft Edge on Windows and the other in Google Chrome on Windows.

The assignment may be reversed according to release risk.

One of the two languages must also be validated on the Samsung Galaxy S20 FE.

It is not mandatory to execute both languages in every browser and device combination.

---

## 9. Accessibility environment baseline

Before V1.0, AtlasBadge will receive an essential accessibility baseline.

The principal desktop checks include:

* keyboard navigation;
* visible focus;
* browser zoom at levels selected according to component risk;
* visual contrast;
* basic labels and accessible names;
* continued access to essential actions and content.

No fixed zoom percentage is mandatory for every component.

Zoom levels are selected according to:

* interface density;
* forms;
* modals;
* maps;
* menus;
* observed behaviour;
* flow criticality.

On the Samsung Galaxy S20 FE, relevant mobile checks include, when applicable:

* accessible labels and names;
* focus behaviour;
* available browser or device zoom;
* portrait and landscape orientation;
* preservation of essential content;
* preservation of essential actions;
* absence of responsive presentation blockers.

This baseline is not a formal WCAG 2.2 AA assessment.

No WCAG conformity claim may be made without a separately documented formal evaluation.

Screen readers, assistive operating systems or technologies not actually used must not be presented as covered.

---

## 10. Performance and fluency conditions

Performance and fluency are assessed under the normal conditions available on the confirmed physical devices.

The validation includes practical observation of:

* response to user actions;
* map interaction;
* status selection;
* navigation;
* page transitions;
* modal behaviour;
* responsive behaviour;
* visible loading or delay;
* perceived fluency.

When latency, freezing or inconsistent behaviour is observed, Browser Developer Tools may support investigation of:

* console errors;
* network requests;
* resource loading;
* browser storage;
* caching;
* relevant technical failures.

This document does not define:

* a formal performance laboratory;
* mandatory simulated slow-network conditions;
* comprehensive load testing;
* unconfirmed numerical performance targets;
* performance guarantees outside the tested sample.

---

## 11. Test tools by purpose

Tools must be described according to their real role.

### 11.1 Development and implementation

May include:

* Gemini Antigravity IDE;
* local Next.js commands;
* TypeScript tooling;
* lint and build commands;
* GitHub;
* Vercel;
* Firebase Console.

These tools are not necessarily used in every test.

### 11.2 Inspection and investigation

May include:

* Browser Developer Tools;
* console inspection;
* network inspection;
* storage and cache inspection;
* Firebase Console;
* Vercel deployment information.

Developer Tools support investigation and evidence collection but do not replace functional validation.

### 11.3 Technical automation

Currently includes:

* Firebase Emulator Suite;
* Firestore security-rule tests;
* Storage-related technical tests;
* account-deletion technical tests;
* Firebase Admin SDK test flows.

Planned before V1.0:

* Playwright;
* TypeScript;
* Chromium primary execution;
* selected installed Chrome or Edge execution;
* emulator-isolated persistence scenarios.

### 11.4 Manual evidence

Manual evidence primarily consists of:

* screenshots;
* textual descriptions;
* expected result;
* observed result;
* relevant environment context.

Video is not a standard mandatory evidence format.

---

## 12. Test accounts and data

### 12.1 Scenario-based preparation

There is no fixed permanent catalogue of test accounts.

Accounts and data are created according to scenario need.

Test states may include:

* an empty account;
* multiple countries;
* multiple status combinations;
* visits and durations;
* memories and notes;
* badges and achievements;
* public profiles;
* usernames;
* multiple users for isolation checks;
* account deletion;
* destructive controlled scenarios.

This is a scenario-oriented approach.

It does not remove the need to identify the account, state and expected cleanup for a particular execution.

### 12.2 Disposable data before launch

Before V1.0 launch:

* all existing accounts are test accounts;
* all existing Firebase Authentication identities are disposable;
* all Firestore records are disposable;
* all Storage files are disposable;
* test usernames are disposable;
* test public profiles are disposable.

Real user data must not be used as test data.

### 12.3 Cleanup of disposable production accounts after launch

A disposable production account is considered fully removed only when evidence confirms:

* the Firebase Authentication identity no longer exists;
* related Firestore data no longer exists;
* related Firebase Storage files no longer exist;
* the username is no longer reserved;
* the public profile is no longer accessible;
* cache or fallback behaviour does not recreate the data.

A successful delete command alone is not sufficient evidence.

---

## 13. Final production reset and clean-start validation

### 13.1 Required final state

Before the official V1.0 launch:

* all test accounts must be deleted;
* all test identities must be removed from Firebase Authentication;
* all test data must be removed from Cloud Firestore;
* all Firebase Storage content must be removed;
* no demonstration profile may remain;
* no preloaded data may remain;
* no test username may remain reserved;
* no test public profile may remain accessible;
* cache or fallback mechanisms must not repopulate the backend;
* the first real user must begin with a completely clean state.

Firebase Storage must be confirmed completely empty, including:

* orphaned files;
* files from deleted accounts;
* uploads without active references;
* content created by deletion tests;
* any other object not intended for launch.

### 13.2 Responsibilities

The responsible technical role will:

* execute account and data removal;
* inspect Firebase Authentication;
* inspect Cloud Firestore;
* inspect Firebase Storage;
* collect technical evidence;
* report residual data or inconclusive results;
* perform technical correction when required.

The Test Lead will:

* define the cleanup scope;
* assess the evidence;
* coordinate functional clean-start validation;
* verify that cache or fallback does not repopulate data;
* determine required retesting;
* accept or reject the reset result;
* provide final launch approval.

Technical completion does not replace Test Lead acceptance.

### 13.3 Minimum cleanup evidence

The minimum evidence includes:

* screenshots showing Firebase Authentication without test identities;
* screenshots showing Firestore without test data;
* screenshots or equivalent evidence showing Firebase Storage empty;
* technical deletion results;
* evidence that usernames and public profiles were removed;
* functional evidence that the product starts empty;
* confirmation that cache or fallback does not restore data;
* evidence that the final smoke account was also removed.

Additional evidence may be required by the Test Lead when:

* a result is inconclusive;
* unexpected behaviour occurs;
* residual data is found;
* deletion requires multiple stages;
* persistence may exist outside the initially inspected services.

### 13.4 Final smoke after reset

After the first complete reset:

1. A disposable account will be created.
2. Essential first-user flows will be validated.
3. Required production smoke coverage will be executed.
4. The account will be deleted.
5. Authentication, Firestore, Storage, username and public-profile removal will be verified.
6. Cache and fallback behaviour will be checked.
7. The production system must return to a completely empty state.

Any remaining account, record, file, username, profile or repopulated data blocks the V1.0 launch.

The significance or visibility of the residue does not remove the blockage.

The clean-start requirement has not been met until the production state is completely empty again.

---

## 14. Smoke coverage

### 14.1 Intermediate deployments

After an intermediate deployment, the Test Lead selects the smoke coverage according to change risk.

The smoke may use:

* desktop only;
* mobile only when the risk is mobile-specific;
* desktop and Samsung Galaxy S20 FE;
* additional combinations when required.

Selection considers:

* affected areas;
* regression risk;
* responsiveness;
* authentication;
* persistence;
* public profile;
* language;
* performance;
* previous defects.

The absence of mobile smoke from a particular intermediate deployment does not imply complete mobile compatibility.

### 14.2 Final V1.0 production smoke

The final V1.0 production smoke must include:

* Microsoft Edge on Windows;
* Google Chrome on Windows;
* Samsung Galaxy S20 FE with Google Chrome;
* `pt-BR`;
* `en-GB`;
* portrait orientation on the Samsung;
* landscape orientation on the Samsung.

The Redmi Pad 2 may provide additional production coverage in both orientations when available.

---

## 15. Evidence storage

Selected portfolio evidence will be stored in the GitHub repository.

The initial planned structure is:

```text
evidence/
└── v1.0/
    ├── environments/
    ├── smoke/
    ├── regression/
    ├── defects/
    └── production-reset/
```

The responsible technical role may adjust this structure during implementation when necessary.

Any revised structure must continue to provide:

* clear separation by purpose;
* understandable traceability;
* safe publication;
* support for Test Lead review.

Only evidence appropriate for publication should be committed.

Before evidence is published:

* personal data must be removed or masked;
* usernames, email addresses and identifiers must be hidden when necessary;
* passwords, tokens, API keys and environment files must not be included;
* sensitive configuration must not be exposed;
* disposable accounts and data should be used wherever possible.

Logs, screenshots and other evidence may be retained when they support:

* result demonstration;
* defect documentation;
* retesting;
* final reset verification;
* professional portfolio presentation.

Evidence must minimise identifiable data and must not justify retaining test data inside Firebase Authentication, Firestore or Storage.

---

## 16. Environmental limitations and residual risk

### 16.1 Confirmed limitations

Current limitations include:

* no dedicated staging environment;
* no separate QA server;
* local and production sharing the same Firebase project;
* no isolated browser application connection to the Firebase Emulators;
* Playwright not yet implemented;
* no comprehensive device laboratory;
* no validated Safari coverage;
* no validated Firefox coverage;
* no validated iPhone or iPad coverage;
* no validated macOS coverage;
* limited Android-device coverage;
* limited physical-resolution coverage;
* no historical browser-version matrix;
* no formal WCAG 2.2 AA audit;
* no comprehensive performance laboratory.

### 16.2 Effect on approval

Environmental limitations must be visible in the evidence and release assessment.

They do not automatically block every execution.

The Test Lead evaluates:

* affected risk level;
* available alternative evidence;
* probability of environment-specific failure;
* impact of an unexecuted scenario;
* known defects outside the declared sample;
* confidence in Critical and High-risk flows;
* residual risk.

Critical or High-risk scenarios left unexecuted require explicit assessment.

A relevant unresolved environmental gap may block approval.

### 16.3 Service unavailability

If Vercel, Firebase or another required service is unavailable:

* unaffected scenarios may continue;
* dependent scenarios are suspended or rescheduled;
* scenarios remain within scope;
* alternative evidence may be considered;
* residual risk is assessed;
* approval remains blocked when confidence is insufficient.

Environmental unavailability does not produce automatic approval or automatic failure of every scenario.

### 16.4 Version mismatch

If the deployed version differs from the version approved by the Test Lead:

* the previous approval does not automatically apply;
* the difference must be analysed;
* proportional validation must be performed;
* a new approval decision is required when the change affects confidence or risk.

---

## 17. Responsibilities and authority

### Responsible technical role

The responsible technical role may:

* implement changes;
* perform technical inspection;
* execute technical commands;
* maintain environment configuration;
* propose emulator and Playwright integration;
* propose evidence-folder changes;
* execute the production reset;
* recommend production-dependent testing;
* provide technical evidence;
* identify environmental differences and risks.

### Test Lead

The Test Lead:

* defines the required coverage;
* evaluates environmental risk;
* approves environment exceptions;
* approves production testing;
* determines browser, device and language combinations;
* assesses unavailable or unexecuted scenarios;
* reviews evidence;
* determines retest and regression;
* accepts or rejects residual risk;
* approves or blocks release;
* provides final approval of the production reset;
* approves material updates to this document.

Technical recommendations support the decision.

They do not replace Test Lead authority.

---

## 18. Document maintenance

The responsible technical role must communicate changes involving:

* Firebase configuration;
* Vercel configuration;
* emulator services;
* browser support;
* physical devices;
* Playwright execution;
* persistence destinations;
* environment protection;
* production-reset procedures;
* evidence structure.

The Test Lead assesses the effect of the change on:

* coverage;
* risk;
* evidence;
* limitations;
* release criteria.

Material updates to this document require Test Lead review and approval.

---

## 19. Proposed approaches requiring implementation

The following are approved directions but are not yet completed capabilities:

### Proposed approach: Local-write protection

Implement a technical safeguard preventing unsafe local writes to the real Firebase project after the final V1.0 reset.

### Proposed approach: Browser application emulator integration

Configure the Firebase Client SDK to connect explicitly to Authentication, Firestore and Storage Emulators during safe automated execution.

### Proposed approach: Playwright suite

Implement a permanent TypeScript Playwright suite with:

* Chromium as the primary browser;
* selected real Chrome and Edge execution;
* fail-safe emulator startup;
* no silent real-Firebase fallback;
* repeatable data setup;
* reliable cleanup;
* safe isolation evidence.

These proposed approaches must be technically reviewed and validated before they are described as operational.

---

## 20. Approval statement

AtlasBadge V1.0 may be approved only when the Test Lead has sufficient confidence that:

* required local validation has been completed;
* production testing has been controlled;
* mandatory browsers and devices have been covered;
* required languages have been validated proportionally;
* Critical and High-risk environmental gaps have been resolved or explicitly assessed;
* the production reset has been technically and functionally confirmed;
* the final smoke account has been completely removed;
* production has returned to a fully empty state;
* no unacceptable residual environmental risk remains.
