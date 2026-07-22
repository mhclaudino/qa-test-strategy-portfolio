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
| AB-EV-003 | QR-03 private cache after explicit logout | QR-03 | Local InPrivate browser validation and Vercel Production | `d8179cb` | Public narrative complete; sanitised screenshots pending | Correction approved after local QA and production smoke | [QR-03 private cache after logout](defects/qr-03-private-cache-after-logout.md) |
| AB-EV-004 | QR-05 text write-volume investigation | QR-05 | To be recorded from the controlled measurement | To be recorded | **In progress** | No Test Lead conclusion yet | [QR-05 text write-volume template](work-in-progress/qr-05-text-write-volume.md) |

## 3. Evidence-status interpretation

- **Public narrative complete** means the verified quality story and decision can be published safely.
- **Supporting artefacts pending sanitisation** means raw screenshots, logs or command output remain private until reviewed, cropped or redacted.
- **In progress** means investigation or validation is incomplete. It must not be interpreted as Passed, Failed or approved.
- A missing public attachment does not invalidate a decision when the operational evidence was reviewed privately, but the public record must state the limitation clearly.

## 4. Pending Quality Risk updates

The following Quality Risk status changes are intentionally recorded as pending and are **not applied by this evidence task**:

| Quality Risk | Current planned update | Current evidence position |
|---|---|---|
| QR-02 | `Current gap` → `Regression risk` | Current V1.0 behaviour was approved and reusable regression protection was added. |
| QR-03 | `Current gap` → `Regression risk` | The explicit-logout private-cache correction was approved and permanent regression protection was added. |
| QR-05 | Pending investigation and Production validation | Measurement and optimisation work remains in progress. |
| QR-06 | Not started | No evidence package or status decision is available yet. |

The Quality Risk Analysis will be updated in one controlled batch after QR-05 and QR-06, or at another checkpoint explicitly approved by the Test Lead.

## 5. Publication control

Only the linked sanitised artefacts are intended for this public portfolio.

Raw AI-assisted investigation logs, unredacted screenshots, complete payloads, private source excerpts, identifiers, credentials, user data and sensitive configuration remain outside this register.
