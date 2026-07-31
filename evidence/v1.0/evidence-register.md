
# AtlasBadge V1.0 Evidence Register

**Register status:** In progress  
**Scope:** Selected, sanitised public evidence for AtlasBadge V1.0  
**Owner:** Test Lead/Product Owner  

## 1. Purpose

This register provides a lightweight index of selected public evidence produced during AtlasBadge V1.0 quality validation.

It is not an Application Lifecycle Management system, a complete defect backlog or a substitute for the operational source of truth. Its purpose is to connect material quality risks, defects, investigations and Test Lead decisions to public portfolio artefacts without exposing private implementation or user data.

## 2. Evidence records

| Evidence ID | Subject | Related risk or defect | Environment | Build or commit | Evidence status | Test Lead decision | Public artefact |
|---|---|---|---|---|---|---|---|
| AB-EV-001 | Silent country-status persistence failure | AB-DEF-001; QR-01 | Local development, Firebase Rules validation and Vercel Production | Correction commit not supplied in the public summary; production-approved deployment | Public narrative complete; selected supporting artefacts pending sanitisation | Corrected behaviour approved after retest, regression, controlled deployment and production smoke | [Status persistence failure](defects/status-persistence-failure.md) |
| AB-EV-002 | QR-02 visit-history preservation | QR-02 | Local development and Vercel Production | `f6cc331`, `322d15b`, `482fd51`, `771bf6c` | Public narrative complete; selected command output may be added after sanitisation | Current V1.0 behaviour approved; no production functional correction required | [QR-02 visit-history preservation](regression/qr-02-visit-history-preservation.md) |
| AB-EV-003 | QR-03 private cache after explicit logout | QR-03 | Local InPrivate browser validation and Vercel Production | `d8179cb` | Public narrative and sanitised screenshots complete | Correction approved after local QA and production smoke | [QR-03 private cache after logout](defects/qr-03-private-cache-after-logout.md) |
| AB-EV-004 | QR-05 legacy memory editor persisted text on every keystroke | QR-05 | Local development and Vercel Production | `e73fad9` | Production approved | Approved for AtlasBadge V1.0 | [QR-05 legacy memory keystroke persistence](defects/qr-05-legacy-memory-keystroke-persistence.md) |
| AB-EV-005 | Account linking identity preservation | QR-09 | Production (2026-07-25) | `f209c14` | Public narrative and sanitised screenshots complete | Approved by Test Lead (Passed - Mitigated) | [QR-09 account linking identity preservation](qr-09/qr-09-account-linking-identity-preservation.md) |
| AB-EV-006 | Production frontend and Firestore Rules deployment parity failure | AB-DEF-002; QR-01; release/deployment integrity | Firebase Emulator validation, Vercel Production and Cloud Firestore Production | Frontend `f209c14`; Rules history `72e7672`, `fa47b0b`; Rules-only deployment on 2026-07-25 | Public narrative complete; no public screenshot required | Defect closed after controlled Firestore Rules deployment, focused Production retest and adoption of a mandatory release-parity gate | [Production deployment parity failure](defects/production-deployment-parity-failure.md) |
| AB-EV-007 | QR-10 wrong identity during access-method linking and Google photo synchronisation | QR-10 | Firebase Auth/Firestore Emulators, Microsoft Edge and Vercel Production (2026-07-29) | `b3841e4`, `004f8ee`, `a52af4f` | Public narrative and sanitised Production screenshots complete | Approved by Test Lead (Passed - Mitigated) | [QR-10 wrong identity during access-method linking](defects/qr-10-wrong-identity-account-linking.md) |
| AB-EV-008 | QR-01 map persistence mitigation and affected-area dashboard regression | QR-01; QR-18; QR-19; QR-25; QR-39 | Local development, Firebase Auth/Firestore Emulators, Microsoft Edge, Next.js production build and GitHub `main` | `0816e0e`, `c363b04`, `234bc0a`, `5140641`, `8951cf1`, `83f5650` | Public narrative and sanitised local screenshots complete; final Production confirmation is cross-referenced through AB-EV-009 | Map persistence path and affected-area regression approved; QR-01 remains Current gap only because equivalent failure, reload and recovery coverage is not yet complete for every persistence flow | [QR-01 map persistence and dashboard regression](regression/qr-01-map-persistence-and-dashboard-regression.md) |
| AB-EV-009 | Final dashboard, filtering and controlled persistence Production smoke | QR-01; QR-18; QR-19; QR-25; QR-39 | Vercel Production, Microsoft Edge on Windows (2026-07-29) | Deployed Git reference `83f5650`; full SHA retained in the private release record | Public textual record complete; raw operational screenshots remain private | Passed in Production; deployment was Ready, cross-screen metrics remained aligned, the controlled visit mutation persisted across refresh and relogin, and the test data was restored. QR-01 remains Current gap due to broader residual scope | [Final dashboard and persistence Production smoke](smoke/ab-ev-009-final-dashboard-and-persistence-production-smoke.md) |
| AB-EV-010 | QR-07 retry-safe account deletion | QR-07 | Firebase Emulators and Vercel Production (2026-07-23) | `7150cb2141a86bff659cf417ac1ba5f4e9dd1385` | Public narrative and sanitised Production screenshots complete | Closed and approved in Production; retained as a Regression risk because cross-service deletion remains non-transactional and retry is an intentional control | [QR-07 retry-safe account deletion](defects/qr-07-account-deletion-retry-safe.md) |
| AB-EV-011 | QR-06 character-limit closure and regression decision | QR-06 | Local validation and Vercel Production; completion confirmed by the Test Lead before 2026-07-30 | Product commit and raw operational output retained in the private product record | Public closure narrative complete; no sensitive field content or private screenshots published | Original missing-limit gap closed and approved; QR-06 moved to Regression risk so approved limits and boundary behaviour remain under permanent coverage | [QR-06 character-limit closure](regression/qr-06-character-limits-closure.md) |

## 3. Evidence-status interpretation

- **Public narrative complete** means the verified quality story and decision can be published safely.
- **Supporting artefacts pending sanitisation** means raw screenshots, logs or command output remain private until reviewed, cropped or redacted.
- **Production approved** means the corrected behaviour completed the applicable technical validation, manual QA, deployment and Production smoke, and was approved by the Test Lead.
- **In progress** means investigation or validation is incomplete. It must not be interpreted as Passed, Failed or approved.
- A missing public attachment does not invalidate a decision when the operational evidence was reviewed privately, but the public record must state the limitation clearly.

## 4. Quality Risk decisions applied

The following decisions are reflected in the current Quality Risk Analysis and public evidence index:

| Quality Risk | Applied state | Evidence position |
|---|---|---|
| QR-01 | `Current gap` | AB-EV-008 and AB-EV-009 confirm the map status and visit path locally and in Production. The remaining gap is limited to equivalent failure, reload and recovery coverage for every other product persistence flow. |
| QR-02 | `Regression risk` | Visit-history preservation was approved and reusable regression protection was added through AB-EV-002. |
| QR-03 | `Regression risk` | Explicit logout removes the UID-scoped private cache; correction and Production smoke are recorded in AB-EV-003. |
| QR-05 | `Regression risk` | The legacy non-visit memory editor now uses local draft state and explicit Save; Production approval is recorded in AB-EV-004. |
| QR-06 | `Regression risk` | Approved character-limit controls were completed and approved by the Test Lead; the public closure decision is recorded in AB-EV-011. |
| QR-07 | `Regression risk` | Retry-safe account deletion was validated with emulator fault coverage and a complete disposable-account Production execution in AB-EV-010. |
| QR-09 | `Mitigated` | Account-linking identity preservation is recorded in AB-EV-005. |
| QR-10 | `Mitigated` | Wrong-identity linking and Google-photo synchronisation are recorded in AB-EV-007. |

Evidence IDs reflect public publication order, not the chronological order of the underlying technical work.

## 5. Publication control

Only the linked sanitised artefacts are intended for this public portfolio.

Raw AI-assisted investigation logs, unredacted screenshots, complete payloads, private source excerpts, identifiers, credentials, user data and sensitive configuration remain outside this register.
