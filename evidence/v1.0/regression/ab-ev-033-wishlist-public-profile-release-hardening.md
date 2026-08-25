# AB-EV-033 — Wishlist, public-profile projection and release hardening

**Evidence status:** Production approved  
**Related defects:** AB-DEF-014, AB-DEF-015, AB-DEF-016  
**Related quality risks:** QR-01, QR-04, QR-07, QR-17, QR-20, QR-30, QR-31, QR-32, QR-33, QR-34, QR-39  
**Target release:** AtlasBadge V1.0  
**Final status:** Wishlist/public-profile release approved in Production; three product defects closed; incremental regression and release-parity gates passed

## 1. Evidence purpose

This record documents the AtlasBadge V1.0 Wishlist/public-profile increment and the release-hardening cycle that followed it.

The work added a user-managed Wishlist based on the existing **Want to visit** status, independent Wishlist ordering, explicit Wishlist privacy, a read-only public Wishlist presentation and a hardened separation between private Firestore records and sanitised public-profile projections.

During affected-area regression, QA also found three material **product defects**. They are recorded separately from stale or obsolete automation assumptions so that product quality findings are not confused with test-maintenance debt.

## 2. Approved Wishlist behaviour

The accepted V1.0 behaviour is:

- Wishlist membership uses `statuses.wishlist === true` as the source of truth;
- status compatibility remains governed by the existing travel-status rules;
- Wishlist is managed from the Map area rather than through a new main navigation tab;
- the Map exposes **Ver todos os 251 Locais** and **Lista de Desejos** actions;
- the owner may set Wishlist **Public** or **Private**;
- Wishlist privacy defaults to private;
- the owner may reorder Wishlist items manually;
- `wishlistOrderRank` is independent from `visitOrderRank`;
- changing Wishlist order must not fabricate visits, physical-presence data, memories or visit chronology;
- changing profile visibility does not overwrite the saved Wishlist-public preference;
- Clear Map removes Wishlist membership/ranks and resets the Wishlist-public preference;
- account deletion includes private/public Wishlist data in the existing deletion lifecycle.

## 3. Public-profile contract

The public Profile remains read-only.

A Wishlist tile is rendered only when the Wishlist is both public and non-empty. The same rule applies when the owner views their own public Profile.

The tile is part of the existing flag grid and opens a read-only modal that contains only public presentation data such as flag and place name. It does not expose status controls, privacy controls, Save actions, drag/reorder controls, visits, memories or private notes.

The Profile may provide local presentation sorting to a viewer, but such sorting is presentation-only and must not persist a preference into the profile owner's record.

## 4. Private/public Firestore boundary

The release established an explicit projection model:

```text
Private source
users/{uid}
users/{uid}/places/{placeId}

Public projection
publicProfiles/{uid}
publicProfiles/{uid}/places/{placeId}
```

Private user/place records are owner-only.

Public-profile routes for non-owner or anonymous viewers read only the public projection. Public place documents are sanitised rather than created by copying the private document wholesale.

Public place data must not expose:

- `generalNote`;
- `registeredVisits`;
- memories or private visit details;
- `firstPhysicalPresenceAt`;
- `statusActivatedAt`;
- `visitsCount`.

The approved public root whitelist is limited to:

```text
achievementMetadata
avatarUrl
bio
displayName
flagSortOrder
isPublic
isWishlistPublic
socialLinks
uid
username
```

Each public `achievementMetadata` entry contains only:

```text
unlockedAt
sequence
```

## 5. AB-DEF-014 — Public place projection missing for normal public statuses

**Classification:** Product defect  
**Severity:** High  
**Priority:** P0 during release hardening  
**Final status:** Closed

After the public Profile was moved to the sanitised `publicProfiles` source, affected-area C34 validation showed that a normal public status could be correctly persisted privately while the equivalent public place projection was missing.

The failure was visible as a public-profile correctness defect: an anonymous/viewer Profile could omit earned place information even though the owner had valid private state.

The correction generalised projection synchronisation for the supported public statuses and kept private/public changes in the same persistence operation where applicable. Public-only records are removed when no public state remains, while mixed public documents preserve only legitimate public fields.

Focused projection and concurrency regression passed after the correction.

## 6. AB-DEF-015 — Public achievement metadata was not synchronised

**Classification:** Product defect  
**Severity:** High  
**Priority:** P0 during release hardening  
**Final status:** Closed

After the public Profile stopped reading the private user document, achievement chronology could exist privately without an equivalent sanitised `achievementMetadata` projection on `publicProfiles/{uid}`.

The result was a public-profile regression in which an earned achievement could be absent even though the private source was correct.

The reconciler was corrected so that the private and public achievement metadata are maintained transactionally when the public root exists. The public projection contains only `{unlockedAt, sequence}` and does not copy private root fields.

Focused metadata, algorithm and public-profile validation passed after the correction.

## 7. AB-DEF-016 — Mobile dashboard control grid collapsed to zero width

**Classification:** Product defect  
**Severity:** High  
**Priority:** P0 during release hardening  
**Final status:** Closed

Responsive regression at a 375 × 667 viewport found that the **Atualizar Mapa** first column could collapse to zero width even though the element remained present in the DOM.

The root cause was CSS precedence under Tailwind v4: the intended mobile single-column rule lost to the arbitrary two-column grid declaration, producing a layout equivalent to a zero-width first column plus the actions column.

The product fix introduced explicit semantic grid/action classes with a one-column mobile default and a two-column layout from the desktop breakpoint. Validation confirmed visible controls and no horizontal overflow at 375 × 667 and 390 × 844 while preserving the desktop layout.

## 8. Automation debt kept separate from product defects

The hardening cycle also identified obsolete test assumptions. These were corrected as **test-maintenance work**, not assigned AtlasBadge product-defect IDs.

Examples included:

- stale `252 Locais` button selectors after the selectable-button total became 251;
- arbitrary `.first()` locators around **Atualizar Mapa**;
- stale `localhost:3000` application navigation in Emulator specs;
- `setOffline(...)` used as a surrogate for pending-write semantics;
- functional assertions coupled to `/documents:commit` wire behaviour;
- private REST reads without owner authentication that could compare two HTTP 403 responses and falsely pass;
- incomplete fixtures that omitted the public-profile root despite the Production invariant;
- a stale expectation that a Profile viewer could not use local presentation sorting.

These corrections improved the reliability of the automated evidence but did not represent product failures.

## 9. Firebase Emulator isolation

The E2E environment was hardened to prevent accidental interaction with Production Firebase while running the Emulator suite.

The final model uses:

```text
Application: 127.0.0.1:3100
Auth Emulator: 9099
Firestore Emulator: 8080
Firebase project: demo-atlasbadge-web
```

The Playwright configuration uses a dedicated E2E Next.js build/runtime and fail-fast guards against real Firebase hosts. Firebase-real Production specs are excluded from the Emulator configuration.

The validated Emulator executions reported:

```text
realFirebaseRequests = 0
```

## 10. Incremental regression result

The release used checkpointed incremental regression rather than repeatedly rerunning already-green suites after every test-only cleanup.

Preserved checkpoints included:

```text
TypeScript: PASS
ESLint: PASS — 0 errors / 0 warnings
Full Vitest checkpoint: 359 passed / 15 skipped / 0 failed
Firestore Rules: 226 / 226 PASS
Wishlist persistence E2E: 3 / 3 PASS
Clear Map Emulator: 2 / 2 PASS
Account Deletion controlled suite: 6 / 6 PASS
C31: PASS
C32: 2 / 2 PASS
C33: PASS
C34: 22 / 22 PASS FINAL
Remaining Playwright batch: 45 / 45 PASS
Final seven touched E2E specs: 26 / 26 PASS
git diff --check: PASS
```

The Next.js build had already passed at the applicable production-code checkpoint and was intentionally not rerun after later E2E-only cleanup.

A final Quality Inventory found zero blockers: no temporary Git-visible artefacts, no real credentials, no unexpected skips, no stale app-port selectors, no stale offline/wire-protocol functional assertions and no accidental real-Firebase traffic.

**INCREMENTAL REGRESSION GATE = PASS FINAL.**

## 11. Pre-commit and repository-quality gate

A 54-modified / 17-untracked candidate was audited before commit.

All tracked and untracked files were classified. Secrets and Firebase-environment safety were clean. Six temporary `[DEBUG]` logs and the four dead narrowing variables left by their removal were identified and removed before approval.

Final pre-commit state:

```text
TypeScript: PASS
ESLint: 0 errors / 0 warnings
git diff --check: PASS
[DEBUG]: 0
Quality Inventory blockers: 0
PRE-COMMIT AUDIT = PASS FINAL
```

## 12. Release traceability

The approved implementation was split into two commits:

```text
276b0c9
feat(wishlist): add public wishlist and secure profile projections

7bbdb9402145523f6a2f36d41cc74e55479cc664
test(e2e): isolate Firebase emulators and harden regression coverage
```

The split keeps product/security/lifecycle behaviour distinct from E2E infrastructure and regression hardening.

After Test Lead approval both commits were pushed to `main`; local and remote HEAD converged at:

```text
7bbdb9402145523f6a2f36d41cc74e55479cc664
```

## 13. Production deployment parity

The Git-triggered Vercel Production deployment was verified as:

```text
dpl_HEKQuz6MAXiW413m6cqnH25zWrRg
Production / READY
Git SHA: 7bbdb9402145523f6a2f36d41cc74e55479cc664
```

Because the product commit also changed Firestore Rules, frontend readiness alone was not considered sufficient release evidence.

The Test Lead separately authorised a Rules-only Production deployment:

```text
Project: atlas-badge
Target: firestore:rules
Result: SUCCESS
Other Firebase resources deployed: none
```

Only after Vercel and Firestore Rules were aligned was focused Production validation started.

## 14. Focused Production validation

Production target:

```text
https://atlas-badge.vercel.app
Firebase project: atlas-badge
```

Using controlled QA data, the final validation recorded:

```text
Wishlist add: PASS
Wishlist persistence/reload: PASS
Wishlist remove: PASS
Wishlist private: PASS
Wishlist public: PASS
Public → private cleanup: PASS
Wishlist order persistence: PASS
visitOrderRank independence: PASS
Public Wishlist order: PASS
Anonymous/viewer public Profile: PASS
Private reads by viewer: 0
Public source: publicProfiles/{uid}
Forbidden public fields found: NO
Public achievementMetadata: PASS
Desktop UI: PASS
Mobile UI: PASS
Horizontal overflow: PASS
Wishlist modal background scroll lock: PASS
Destructive operations: 0
Non-QA accounts modified: 0
```

The temporary Production-validation spec was removed immediately after execution. The final product repository remained clean with `HEAD == origin/main` and `0/0` ahead/behind.

**PRODUCTION VALIDATION = PASS.**

## 15. Test Lead decision

The Wishlist/public-profile increment is approved in Production.

The release demonstrated:

- durable Wishlist membership and independent order;
- explicit private/public Wishlist control;
- secure public projection rather than CSS-only hiding;
- zero private profile/place reads by anonymous/viewer validation;
- sanitised public place and achievement metadata;
- read-only public Wishlist presentation;
- responsive desktop/mobile behaviour;
- Firebase Emulator isolation with zero real-Firebase requests;
- checkpointed regression and repository-quality gates;
- controlled Git, Vercel and Firestore Rules release parity;
- focused Production validation against the exact deployed release.

**AB-DEF-014: Closed.**  
**AB-DEF-015: Closed.**  
**AB-DEF-016: Closed.**  
**AB-EV-033: PRODUCTION APPROVED.**

QR-01 remains a **Current gap** outside the specifically validated persistence paths. QR-04, QR-07, QR-17, QR-20, QR-30, QR-31, QR-32, QR-33, QR-34 and QR-39 remain regression risks whose current controls are strengthened by this evidence.

## 16. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-033 |
| Product defects | AB-DEF-014, AB-DEF-015, AB-DEF-016 |
| Product/security commit | `276b0c9` — `feat(wishlist): add public wishlist and secure profile projections` |
| E2E/infrastructure commit | `7bbdb94` — `test(e2e): isolate Firebase emulators and harden regression coverage` |
| Final product SHA | `7bbdb9402145523f6a2f36d41cc74e55479cc664` |
| Vercel Production | `dpl_HEKQuz6MAXiW413m6cqnH25zWrRg` — READY |
| Firestore Rules | 226/226 local gate; Rules-only Production deployment SUCCESS |
| Emulator isolation | `127.0.0.1:3100`; Auth 9099; Firestore 8080; `demo-atlasbadge-web`; `realFirebaseRequests=0` |
| Incremental regression | PASS FINAL |
| Pre-commit audit | PASS FINAL |
| Production validation | PASS |
| Final repository state | Product `main` clean; HEAD/origin aligned at `7bbdb94` |
