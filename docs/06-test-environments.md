# Test Environments

**Document status:** Completed and maintained through AB-EV-067  
**Product:** AtlasBadge  
**Document owner:** Test Lead  
**Last updated:** 12 October 2026

---

## 1. Purpose

This document describes the real environments, browsers, devices, configurations, test-data controls and environmental limitations used to validate AtlasBadge.

It defines:

- local development/manual validation;
- isolated Firebase Emulator browser regression;
- manual Emulator QA readiness;
- technical Rules/backend validation;
- controlled Production validation;
- browser/device coverage;
- test-data and release-safety controls;
- residual environmental risk.

AtlasBadge does not claim an artificial enterprise test estate. It does not currently have a dedicated staging environment, device laboratory or comprehensive cross-browser/OS matrix.

---

## 2. Environment principles

1. Environment choice is proportional to product/change risk.
2. Production is not the default functional test environment.
3. Browser E2E persistence regression should use the isolated Firebase Emulator environment.
4. A failed Emulator preflight must fail closed; it must not fall back to real Firebase.
5. Production validation uses controlled QA accounts/data only and requires Test Lead approval.
6. A Vercel frontend deployment and changed Firebase Rules are treated as separate release resources whose parity must be confirmed before any Production functional validation that depends on those Rules.
7. A local browser URL does not identify the Firebase backend; effective project/Emulator routing must be proven separately.
8. Manual Emulator QA is not ready until the services, required QA identity/state and the actual Test Lead browser session are all ready.
9. One canonical manual-QA application origin must be used for a session; `localhost` and `127.0.0.1` are not treated as interchangeable browser origins.
10. Environmental unavailability or parity mismatch is reported as blocked/inconclusive rather than converted into a false product result.
11. No universal compatibility/accessibility claim is made from the available sample.
12. Local secret/configuration state and Vercel Production secret/configuration state are separate facts; one must never be inferred from the other.
13. When a server-side integration depends on a specific runtime/dependency transport, the effective Node/runtime version is part of environment evidence and must be validated on the real integration boundary.
14. Exact deployment metadata/SHA verification may be sufficient publication evidence when stateful/destructive behaviour was already comprehensively proven in the protected Emulator environment and real Production mutation would add avoidable risk.
15. The Test Lead has final authority over Production testing, destructive validation and environmental exceptions.

---

## 3. Current environment landscape

| Context | Purpose | Application target | Firebase target | Status |
|---|---|---|---|---|
| Local manual development | Implementation review, exploratory/manual QA, selected real-backend checks while authorised | Local Next.js runtime on Windows | Real `atlas-badge` when intentionally configured | Active / controlled |
| Manual Emulator QA | Manual validation requiring current local Rules/backend behaviour without Production deploy | Canonical local browser origin for the active session | Auth/Firestore/Storage Emulators with explicit QA identity/session | Active / controlled |
| Browser E2E Emulator | Normal automated persistence/privacy/regression | `127.0.0.1:3100` where the dedicated runner uses that origin | Auth/Firestore/Storage Emulators, project `demo-atlasbadge-web` when Storage is in scope | Active / primary automated E2E |
| Technical Emulator/Rules | Firestore Rules, account-deletion and backend-focused lifecycle/concurrency tests | Scripts/test runners | Firebase Emulators; Rules namespace may use `demo-atlasbadge-rules` | Active |
| Production | Deployment identity, post-deployment smoke and approved real-integration validation | Vercel Production | Real Firebase project `atlas-badge` | Active / controlled |

The local manual application and Production may intentionally use the real Firebase project during controlled pre-launch testing. This is different from the automated Emulator suite, which is explicitly isolated and must not touch real Firebase.

---

## 4. Runtime baseline

AB-EV-065 established the current technical runtime baseline used by Premium Hardening:

```text
Node.js: 24.21.0
Next.js: 16.3.6
npm: 11.19.0
```

The corresponding pre-hardening technical RC Product SHA is:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
```

PA-001/PA-002 keep this runtime family. The current published Product anchor is:

```text
49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
```

No later environment document may silently revert the runtime baseline to the historical Node 22 qualification state.

---

## 5. Local manual development environment

The product is developed and manually validated on Windows using Next.js, React, TypeScript, Tailwind CSS, Firebase and Vercel/GitHub integration.

Local manual validation may include:

- implementation verification;
- exploratory testing;
- defect reproduction/retest;
- visual/responsive review;
- targeted real-Firebase scenarios when explicitly part of the approved pre-launch validation model.

Because pre-V1.0 Firebase data is controlled/disposable test data, local real-Firebase access can be intentional. Evidence must identify whether the real backend or Emulator was actually used.

C44 added server-side visit-photo routes that require Firebase Admin configuration in the local Next.js process when validating against real Firebase. The required `FIREBASE_ADMIN_*` values are kept only in ignored local environment configuration and are reported as SET/MISSING rather than printed. Their local absence does not imply that Vercel Production variables are absent, and a Production variable set is not recreated merely because the local machine needs its own credential.

No automated or manual conclusion may infer that a local application is isolated merely because the browser address contains `localhost`.

The effective environment record should identify, where relevant:

```text
Application origin
Firebase project ID
Auth backend
Firestore backend
Storage backend
connect*Emulator state
Rules source enforcing the request
Node/runtime version when server integration is relevant
```

---

## 6. Browser E2E Firebase Emulator environment

### 6.1 Current isolated configuration

The hardened Playwright environment uses a dedicated local runtime and explicit Emulator routing.

For the PA-002 affected C44 regression, the protected configuration was:

```text
Application:      http://127.0.0.1:3100
Auth Emulator:    127.0.0.1:9099
Firestore:        127.0.0.1:8080
Storage:          127.0.0.1:9199
Firebase project: demo-atlasbadge-web
```

The E2E Next.js runtime uses a dedicated build/output location and does not reuse an arbitrary already-running application server.

The Playwright configuration forces Emulator-safe environment variables, removes/overrides Production/Admin credentials from the test runtime where applicable and blocks unexpected real-Firebase network access.

Firebase-real Production specs are excluded from the Emulator configuration.

### 6.2 Fail-fast controls

The Emulator environment includes global/fixture protections that verify the expected test context before executing persistence flows.

The suite must fail rather than continue if:

- required Emulator services are absent;
- the application is not using the intended E2E runtime;
- configuration points to the real Firebase project unexpectedly;
- a request attempts to reach real Firebase hosts during an Emulator test.

Current PA-002 C44 evidence includes:

```text
realFirebaseRequests = 0
```

A blocked/failed run that never reached the relevant assertions is not evidence of `realFirebaseRequests=0`.

### 6.3 Purpose

This environment is used for browser-level validation of:

- authentication setup and protected navigation;
- travel-status persistence;
- rapid/concurrent mutations;
- Wishlist membership, privacy and order;
- public/private Profile source boundaries;
- geographic counters and map behaviour;
- Manual Visit Order;
- visit-photo UI/Storage/owner-public access behaviour when in scope;
- responsive/mobile flows;
- selected account lifecycle and Clear Map behaviour.

It is not a public staging environment and does not attempt to reproduce Vercel Production infrastructure completely.

---

## 7. Manual Emulator QA readiness

### 7.1 Readiness contract

The environment is not described as **ready for Test Lead manual QA** until all three conditions are true:

```text
1. Required Emulator services are running with the intended local Rules
2. Required disposable QA identity/baseline state is available
3. The actual Test Lead browser is authenticated on the canonical application origin
```

Starting the Emulator processes alone satisfies only the first condition.

### 7.2 Real Firebase identity versus Auth Emulator identity

A user authenticated in real Firebase Authentication is not automatically present in the isolated Auth Emulator. Switching an application from real Firebase to the Emulator therefore invalidates the prior authentication context unless an Emulator identity/state already exists.

The environment must not silently create replacement identities during a manual-QA handoff. Any seed/restore operation is part of environment setup and must follow the Test Lead's approved test-data policy.

### 7.3 Browser-context boundary

An authenticated Playwright/scripted browser is a separate browser context. It must not be reported as the Test Lead's existing manual browser session.

Likewise:

```text
http://localhost:3000
```

and

```text
http://127.0.0.1:3000
```

are treated as different application origins for browser persistence/session purposes.

A manual QA session chooses one canonical application origin and keeps it for the duration of the test.

### 7.4 Routine manual QA environment

Default routine manual QA uses:

- **Application:** `http://localhost:3000`
- **Backend:** REAL Firebase project `atlas-badge` only when the approved scenario intentionally requires it
- **Identity/Browser:** the existing real Firebase QA account and Test Lead browser session.

Manual Emulator QA remains available when specifically justified by a scenario requiring isolation. A reusable one-command manual Emulator bootstrap is not a requirement for routine QA.

---

## 8. Technical Emulator and Firebase Rules context

The Firebase Emulator Suite is used for backend-focused verification including:

- Firestore Rules allow/deny behaviour;
- Storage Rules where photo storage is affected;
- owner/non-owner/anonymous access;
- public-profile projection whitelists;
- valid/invalid order/rank/recovery fields;
- account-deletion failure/retry logic;
- Clear Map/private-public cleanup;
- achievement metadata projection;
- real backend batch/transaction atomicity and contention;
- visit-photo reserve/release/idempotency/stale-reservation races;
- durable Product persistence/hydration after release failure;
- test fixtures requiring deterministic isolated state.

C36 recorded an earlier 226/226 Firestore Rules checkpoint. The current PA-002 independent qualification records:

```text
Firestore Rules: 242 / 242 PASS
PA-002 requested run: 39 / 39 PASS
Core real Emulator acceptance declarations:
- quota lifecycle: 13
- slot server: 10
- route: 10
Context ordering tests: 6 (mock persistence boundaries; not misrepresented as six more Firestore tests)
```

The Rules command currently relies on a cached undeclared `tsx` executable. The 242/242 result is valid, but deterministic tooling declaration is tracked separately as PA-006A.

---

## 9. Production environment

### 9.1 Application and backend

Production application:

```text
https://atlas-badge.vercel.app
```

Production Firebase project:

```text
atlas-badge
```

Production is used for:

- deployment/source identity verification;
- post-deployment availability/smoke when required;
- validation of Firebase Rules when a release requires real-backend rule parity;
- controlled real Auth/Firestore/Storage integration where explicitly approved;
- selected desktop/mobile manual checks;
- approved Production-only browser validation.

### 9.2 Current Premium deployment anchor

PA-002 is the current published Product anchor:

```text
Commit:      49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
Parent:      d0116967fc389353191c11e8865f20e38b9d5835
Deployment:  dpl_FTp3iCiARWMSejRv12FcHtaMcWeR
Target:      production
State:       READY
Source:      Git / main / exact commit SHA
Alias:       atlas-badge.vercel.app
```

Publication verification used deployment metadata/SHA/status. No authenticated Production QA, Production Firestore/Storage mutation or real-Firebase test-data creation was required to approve that publication.

### 9.3 Release-parity gate

AB-DEF-002 established that frontend and Firestore Rules can be out of sync even when the Vercel deployment is healthy. C36 reproduced the same risk locally when new application behaviour was accidentally tested against older deployed Production Rules. C44 extended the parity model to Firebase Storage Rules and server runtime.

Therefore, when a release changes Rules and Production functional smoke depends on those changes:

```text
1. push approved product commits;
2. verify Vercel Production READY at the expected Git SHA;
3. deploy only the approved Firebase Rules target(s);
4. confirm the Rules deployment succeeded;
5. only then begin focused Production functional validation.
```

When publication verification is intentionally metadata-only after protected Emulator qualification, no claim of Production functional execution is made.

### 9.4 Production test-data safety

Production tests use controlled QA accounts and disposable data.

They must not:

- depend on real customer information;
- modify non-QA accounts;
- expose credentials/tokens in reports;
- perform destructive operations outside the explicitly approved scope.

Temporary Production-only test files are removed after execution unless intentionally retained as permanent regression assets.

---

## 10. Browser and device baseline

Current evidence includes:

| Platform | Browser / execution | Coverage |
|---|---|---|
| Windows desktop | Microsoft Edge | Primary manual/Production baseline and current protected C44 browser gate |
| Windows desktop | Playwright Edge/Chromium project as selected | Automated Emulator and Production validation |
| Android | Google Chrome on Samsung Galaxy S20 FE | Historical physical mobile validation used during V1.0 hardening |
| Responsive automation | Desktop/mobile viewports including 375×667 and 390×844 | Layout/overflow/modal regression |

Google Chrome on Windows was explicitly **NOT EXECUTED** in AB-EV-065; no false Chrome PASS is carried forward from that checkpoint.

Additional environments may be used according to risk, but the project does not claim comprehensive validation across Firefox, Safari, iPhone/iPad, macOS, all Android models or historical browser versions.

QR-38 remains an Assessment gap for untested combinations.

---

## 11. Responsive and accessibility environment use

Responsive validation combines physical-device review where available and automated viewport coverage.

The release-hardening baseline includes checks for:

- control visibility/bounding boxes;
- horizontal overflow;
- responsive grid behaviour;
- modal usability;
- modal background scroll lock;
- touch-capable interaction where applicable.

AB-DEF-016 demonstrates why DOM presence alone is insufficient: a control can exist but be functionally invisible when CSS collapses its width.

A scoped WCAG 2.2 AA technical baseline is recorded in AB-EV-017. This is not formal accessibility certification or comprehensive native assistive-technology coverage.

---

## 12. Test data and cleanup

### Emulator data

Emulator tests create deterministic isolated data and may freely create/update/delete test records within the approved demonstration project.

Manual Emulator QA data is disposable but its creation/restoration must be deliberate. Temporary diagnostic/bootstrap scripts are not automatically permanent project assets.

Rich direct fixtures that create derived Product state must satisfy/reproduce the normal reconciliation invariants before manual handoff.

### Production QA data

Production testing uses known QA accounts/data only. Test data may be changed where required by the approved scenario and should be returned to a stable state afterwards when practical.

### Destructive scenarios

Clear Map/account deletion are treated separately because they are destructive. They require an appropriate dedicated account/context and explicit authorisation when executed against Production.

A release smoke does not automatically authorise destructive retesting.

PA-001/PA-002 publication did not perform destructive Production retest solely to verify deployment.

---

## 13. Environment failure classification

A run is not classified as a product failure when execution cannot start or is invalid because of an infrastructure/environment condition such as:

- Emulator service not running;
- residual local port conflict;
- unavailable dependency;
- application pointing to an unintended Firebase backend;
- frontend/Rules parity mismatch;
- missing Emulator identity/state required for the intended manual scenario;
- browser-origin/session mismatch;
- missing authorised credential required for a specific destructive/Admin scenario;
- missing local Firebase Admin configuration for a real-backend server route;
- a confirmed runtime/dependency incompatibility.

The condition is reported as environment/infrastructure blocked or parity mismatch, corrected where safe, and the valid execution result is recorded separately.

This classification does not hide true Product defects. C44 and PA-002 both required separating environment/runtime/test-oracle problems from genuine Product lifecycle/projection/concurrency defects.

---

## 14. Security and secrets controls

Public and test repositories must not contain:

- Production passwords;
- access tokens;
- service-account private keys;
- real Admin credentials;
- `.env` secrets;
- disposable test-account credentials where publication is unnecessary.

Mock/dummy credentials used only to prove Emulator isolation must be clearly fictitious.

Public evidence excludes QA identities, passwords, Auth action links, private payloads, cookies, sensitive cloud configuration and raw AI/scratch logs.

---

## 15. Current environmental limitations

Known limitations include:

- no dedicated staging environment;
- incomplete browser/device matrix;
- no independent security lab/penetration-test environment;
- no formal load/stress environment;
- no comprehensive native assistive-technology/device laboratory;
- some Production destructive/Admin validations may remain blocked if legitimate required credentials are unavailable;
- the Rules alias's cached undeclared `tsx` dependency is a deterministic-tooling concern under PA-006A.

These limitations must remain visible in release/residual-risk decisions rather than being interpreted as passed coverage.

---

## 16. Review triggers

Review this document when:

- Firebase project/environment routing changes;
- Emulator ports/services change;
- Playwright server/build isolation changes;
- server/Admin route ownership changes;
- Production/Preview deployments become formal test environments;
- staging/QA infrastructure is introduced;
- browser/device support changes;
- the final pre-launch Firebase reset changes local-data policy;
- CI starts managing Emulator services;
- Node/runtime support changes;
- a new Production safety, parity or session-origin failure is discovered.

PA-003 must use this environment contract while auditing photo Admin/server routes. PA-007 owns broader server-boundary Firebase project/safety hardening and must not be silently absorbed into PA-003 unless a direct blocker is demonstrated.

---

## 17. Related evidence

- `evidence/v1.0/environments/`
- `evidence/v1.0/regression/ab-ev-021-v1-release-hardening-and-integrated-regression.md`
- `evidence/v1.0/smoke/ab-ev-024-production-release-validation-and-harness-hardening.md`
- `evidence/v1.0/defects/production-deployment-parity-failure.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md`
- `evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md`
- `evidence/v1.0/regression/ab-ev-055-c45j-public-profile-localization-and-fixes.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
