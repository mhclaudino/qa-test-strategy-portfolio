
# [AB-DEF-002] Production frontend and Firestore Rules were deployed out of sync

**Evidence ID:** AB-EV-006  
**Evidence status:** Production approved; public narrative complete  
**Classification:** Production configuration and deployment defect  
**Severity:** High  
**Priority:** P0 — Immediate  
**Related quality risk:** QR-01 and cross-cutting release/deployment integrity  
**Target release:** AtlasBadge V1.0  
**Detection date:** 2026-07-25  
**Production application commit:** `f209c14`  
**Rules content introduced or finalised by:** `72e7672`, `fa47b0b`  
**Final status:** Closed after controlled Firestore Rules deployment and Production retest  

## 1. Evidence purpose

This record documents a Production deployment-parity failure in AtlasBadge V1.0.

The frontend release was successfully published through Vercel, but the corresponding Cloud Firestore Rules were not promoted during the same release process. The result was a deployed product whose user interface and client-side logic were newer than the active Production authorisation policy.

The record demonstrates:

- detection of a Production-only failure;
- comparison between local Emulator and Production behaviour;
- root-cause confirmation;
- controlled deployment of only the missing Firebase surface;
- focused Production retesting;
- release-process improvement following the incident.

This is a selected and sanitised portfolio artefact. It excludes account identifiers, private payloads, credentials, raw logs and unnecessary configuration details.

## 2. Release context

The AtlasBadge release included frontend changes and workflows that depended on the current Firestore security model.

The relevant user journeys included:

- assigning the exclusive **Born / Nasci lá** status;
- maintaining `birthplacePlaceId` consistently with the selected place;
- removing and changing the birthplace;
- clearing the entire travel map through one atomic batch;
- preserving the user profile while removing map-derived data.

The application code was pushed to `main` and the Vercel Production deployment served the new frontend.

The Firebase deployment surface was handled separately. Firestore Rules were not deployed as part of that release.

## 3. Detection

The issue was found during manual Production validation after the frontend deployment.

The same workflows had passed locally with Firebase Emulators.

Production behaviour diverged immediately:

1. selecting **Nasci lá** did not persist;
2. the interface displayed the controlled failure message;
3. selecting **Limpar meu mapa** entered the processing state;
4. the map data remained present after the operation;
5. both operations returned Firebase `permission-denied`.

Because two protected and material user journeys were unavailable in Production, the issue was treated as an immediate P0 release defect.

## 4. Observed behaviour

### 4.1 Birthplace selection

In Production:

- the user selected a place;
- the **Nasci lá** action was triggered;
- the client attempted the expected atomic mutation;
- Firestore rejected the write with `permission-denied`;
- the birthplace was not added;
- the application displayed a controlled error rather than a false success state.

### 4.2 Clear Map

In Production:

- the user selected **Limpar meu mapa**;
- the confirmation modal entered the processing state;
- the atomic batch was rejected by Firestore;
- no map data was removed;
- the interface returned to the existing map state;
- the operation reported `permission-denied`.

No partial deletion was observed.

## 5. Expected behaviour

A completed release must provide compatible versions of all changed deployment surfaces.

For these journeys:

- the frontend must submit the approved atomic mutations;
- Production Firestore Rules must authorise valid owned operations;
- invalid or inconsistent operations must remain blocked;
- birthplace selection must persist and survive refresh;
- a second birthplace must be rejected with controlled feedback;
- removing the original birthplace must permit another selection without refresh;
- Clear Map must delete the user's places and reset map-derived user state atomically;
- profile data must remain preserved;
- the behaviour validated against Emulators must match Production policy.

## 6. User and release impact

The defect affected two material V1.0 capabilities:

- exclusive birthplace management;
- complete map reset.

The frontend itself was available, but the deployed product was functionally inconsistent because its security-policy dependency had not been promoted.

The incident therefore affected:

- Production functional reliability;
- authorisation parity;
- release confidence;
- trust in deployment completion;
- acceptance of protected workflows.

The issue was not classified as a harmless local environment incident. It reached Production and blocked valid user actions.

## 7. Confirmed root cause

The confirmed failure chain was:

1. the AtlasBadge frontend and supporting code were updated and deployed through Vercel;
2. local validation used Firebase Emulators with the current repository version of `firestore.rules`;
3. the local Rules allowed the approved atomic birthplace and Clear Map operations;
4. the Production Firestore project continued to use an older Rules deployment;
5. the newer client submitted operations that the older Production policy did not authorise;
6. Firestore rejected both journeys with `permission-denied`.

The technical root cause was an incomplete multi-surface release.

The release process had treated the Vercel deployment as the completed product deployment even though Firebase Rules were an independent changed or required surface.

## 8. Why local validation passed

The local browser was connected to Firebase Emulators.

The Emulator loaded the current `firestore.rules` file from the working repository. The automated and manual local checks therefore validated the intended frontend and Rules combination.

Production used a different combination:

| Surface | Local validation | Production before correction |
|---|---|---|
| Frontend | Current | Current |
| Firestore Rules | Current repository Rules | Older deployed Rules |
| Result | Passed | `permission-denied` |

The tests were not false positives for the local configuration. The release process failed to preserve the validated configuration parity in Production.

## 9. Relevant implementation and Rules history

The user-identity and birthplace integrity work was introduced and refined in the following AtlasBadge commits:

```text
72e7672 feat(v1): finalize social profiles and birthplace integrity
fa47b0b fix(v1): finalize onboarding and birthplace consistency
```

The Production frontend release later reached:

```text
f209c14 fix(visits): step duration controls by whole numbers
```

The Firestore Rules content required by the protected journeys already existed in the repository, but had not been released to the Production Firebase project during the frontend deployment.

No additional frontend correction was required to resolve the parity failure.

## 10. Technical validation before the corrective deployment

The current repository state was revalidated before changing Production Rules.

The validation included:

| Validation | Result |
|---|---|
| Firestore Rules suite | 154 passed, 0 failed |
| Clear Map tests | Passed |
| Defect regression tests | Passed |
| V1 refinement tests | Passed |
| TypeScript | Passed |
| Production build | Passed |
| Git diff check | Passed |

The focused checks covered:

- first birthplace selection;
- exclusive birthplace enforcement;
- removal and immediate replacement;
- valid atomic user/place consistency;
- Clear Map atomicity;
- rollback when an operation is invalid;
- preservation of profile fields;
- map state remaining correct after refresh.

## 11. Corrective action

A controlled Rules-only deployment was performed.

Command:

```text
npx.cmd firebase deploy --only firestore:rules --project atlas-badge
```

The deployment:

- targeted the approved Production Firebase project;
- published only Cloud Firestore Rules;
- did not publish Hosting;
- did not publish Functions;
- did not publish Storage Rules;
- did not publish Firestore indexes;
- did not trigger a new Vercel deployment;
- did not modify Production data manually.

Recorded deployment time:

```text
2026-07-25T14:33:36Z
```

The Firebase CLI completed successfully and released `firestore.rules` to Cloud Firestore.

## 12. Production retest

After the Rules-only deployment, the Test Lead repeated the affected journeys in Production.

### 12.1 Birthplace

Production validation confirmed:

- the first **Nasci lá** selection succeeded;
- the selection persisted after refresh;
- attempting a second birthplace was blocked with controlled feedback;
- removing the first birthplace succeeded;
- another birthplace could be selected immediately without refresh;
- no duplicate or inconsistent birthplace state was observed.

### 12.2 Clear Map

Production validation confirmed:

- Clear Map completed successfully;
- the confirmation modal closed correctly;
- the non-blocking success message was displayed;
- the map became empty;
- refresh preserved the empty map;
- the user profile remained available;
- a new status could be created after the reset.

The Test Lead declared the corrected Production behaviour satisfactory.

## 13. Deployment decision

No rollback of the frontend was required.

The approved response was to deploy the missing compatible Rules version because:

- the frontend behaviour had already passed local validation;
- the Rules suite passed;
- the local integrated combination represented the intended product;
- Production was missing the corresponding policy deployment;
- a Rules-only deployment provided the smallest controlled correction;
- no source-code change was necessary.

## 14. Release-process gap

The incident exposed a release-governance gap:

> A successful Vercel deployment was treated as evidence that the complete AtlasBadge release was available, even though the release depended on a separately deployed Firebase security surface.

A Vercel `Ready` state proves that the frontend deployment completed. It does not prove that:

- Firestore Rules are current;
- Storage Rules are current;
- Functions are current;
- Firestore indexes are current;
- Firebase configuration matches the tested release.

## 15. Preventive release-parity gate

The following gate was adopted for future AtlasBadge releases.

### 15.1 Change-surface classification

Before deployment, changed or dependent files must be classified into:

- frontend / Vercel;
- Firestore Rules;
- Storage Rules;
- Cloud Functions;
- Firestore indexes;
- Firebase or environment configuration.

### 15.2 Deployment completeness

Each affected surface must have:

- an explicit deployment action;
- a recorded result;
- the target project or environment confirmed;
- the deployed commit or source state identified where possible.

### 15.3 Production verification

A release cannot be declared complete until:

- all required deployment surfaces are complete;
- the frontend is available;
- protected operations complete a focused Production smoke;
- Production behaviour matches the combination validated locally;
- the Test Lead accepts the result.

### 15.4 Failure rule

If any required surface is not deployed or cannot be verified:

- the release remains incomplete;
- Production approval is withheld;
- the missing deployment is treated as a release blocker;
- Vercel readiness alone is not sufficient evidence.

## 16. Test Lead decision

**Defect closed after controlled correction and Production retest.**

The Test Lead approved the result after confirming:

- the cause was deployment parity rather than a new client defect;
- current Rules passed local validation;
- only Firestore Rules were deployed;
- no unnecessary Vercel redeployment occurred;
- **Nasci lá** worked in Production;
- birthplace exclusivity and replacement worked;
- Clear Map worked;
- refresh preserved the correct state;
- profile data remained intact;
- the release-parity gate was added to the future release process.

## 17. Residual regression risk

Residual risk remains when:

- frontend code begins to depend on a newer Rules contract;
- Rules are changed without a matching client release;
- deployments target the wrong Firebase project;
- a release report omits one deployment surface;
- Production smoke covers visual availability but not protected writes;
- automated local tests are interpreted as proof of Production configuration.

Permanent regression and release coverage should retain:

- current project-target verification;
- Firestore Rules validation;
- protected-write smoke;
- birthplace consistency;
- Clear Map atomicity;
- frontend/Firebase deployment parity.

## 18. Public evidence position

This public record intentionally relies on a verified narrative and sanitised command/result summary.

No screenshot is required because the following are recorded clearly:

- affected environment;
- observed Firebase error category;
- validated local state;
- relevant commits;
- exact corrective deployment command;
- deployment scope;
- Production retest;
- Test Lead decision.

The following remain private:

- account identifiers;
- complete browser console output;
- raw Firebase payloads;
- private project configuration;
- tokens and credentials;
- raw AI-assisted investigation logs;
- personal map or profile data.

## 19. Traceability

| Item | Reference |
|---|---|
| Defect | AB-DEF-002 |
| Evidence | AB-EV-006 |
| Related risk | QR-01 and release/deployment integrity |
| Frontend Production commit | `f209c14` |
| Rules history | `72e7672`, `fa47b0b` |
| Corrective action | Firestore Rules-only deployment |
| Environment | Vercel Production and Firebase Firestore Production |
| Result | Passed after correction |
| Decision | Closed and retained in release regression coverage |
