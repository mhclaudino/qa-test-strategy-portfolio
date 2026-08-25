# AtlasBadge Test Scope

## 1. Document purpose

This document defines the testing scope for AtlasBadge during incremental V1.0 development and release preparation.

It identifies covered product areas, expected depth, current coverage, mandatory remaining work and intentionally deferred items.

> **Document status:** Completed and maintained through AB-EV-036. AB-EV-034 closes the residual QR-01 failed-write/recovery assessment gap; C35/AB-EV-035 adds the corrected Visited + Passed through compatibility rule; C36/AB-EV-036 closes AB-DEF-017 and establishes atomic Wishlist settings persistence plus canonical root ordering.

---

## 2. Relationship with other portfolio documents

Read together with:

- [Product Overview](01-product-overview.md)
- [Quality Risk Analysis](02-quality-risk-analysis.md)
- [Test Strategy](03-test-strategy.md)
- [Test Environments](06-test-environments.md)
- [System Test Plan](09-system-test-plan.md)
- [Lessons Learned](12-lessons-learned.md)
- [V1.0 Evidence Register](../evidence/v1.0/evidence-register.md)

The actual execution scope is selected according to affected functionality, dependencies, quality risks and consequences of failure. Not every green checkpoint is automatically rerun after every unrelated change.

---

## 3. Coverage status

| Status | Meaning |
|---|---|
| Executed | Meaningful current functional/regression evidence exists for the implemented baseline. |
| Partially executed | Coverage exists but mandatory/final V1.0 work remains. |
| Planned before V1.0 | Required before release approval but not yet complete. |
| Conditional V1.0 candidate | Enters mandatory scope if implementation is accepted for V1.0. |
| Deferred beyond V1.0 | Explicitly not required for first official release. |
| Assessment gap | Available evidence is insufficient for a broad compatibility/quality claim. |

---

## 4. Test priority and depth

### Critical

Deep/release-blocking coverage including positive/negative, persistence, privacy, destructive, recovery, concurrency and regression scenarios where relevant.

### High

Broad coverage of main flows, state changes, integrations, responsive behaviour and important dependencies.

### Medium

Focused validation sufficient for the defined baseline and residual risk.

An apparently visual change may become High/Critical when it makes essential controls unusable, as demonstrated by AB-DEF-016.

---

## 5. Critical scope

### 5.1 Authentication and account lifecycle — Executed

Coverage includes registration/login/logout, verification/recovery, persistent sessions, protected routes, auth-method linking, username lifecycle, account ownership and account deletion.

Regression is required whenever authentication, owner identity, Firestore access or public-profile resolution changes.

### 5.2 Data persistence and integrity — Executed / QR-01 Regression risk

Coverage includes:

- status creation/change/removal;
- visits and private memories;
- refresh/session restoration;
- optimistic/confirmed state reconciliation;
- rejected-write rollback/recovery;
- repeated/rapid mutation;
- Manual Visit Order through `visitOrderRank`;
- Wishlist membership through `statuses.wishlist` and canonical order through root `wishlistOrder`;
- atomic combined Wishlist order/privacy Save;
- private/public place/root projection synchronisation;
- Clear Map cleanup;
- account deletion lifecycle;
- impact on counters/achievements/Profile.

AB-EV-034 closes the previous residual QR-01 assessment gap through write-path architectural equivalence plus focused `flagSortOrder` failure/recovery coverage. QR-01 remains a High `Regression risk` because persistence failure remains consequential, but there is no known Current gap in the assessed V1.0 write paths.

AB-EV-036 adds a real Firestore Emulator rejected-batch regression for the combined Wishlist settings helper. One denied write causes the full Save to fail with private root, public root and public place projections unchanged. C36 also removes per-place order write amplification, keeping the largest supported transition at no more than 253 document writes.

### 5.3 Privacy and user isolation — Executed for current model

Coverage includes:

- private user/place owner-only access;
- public Profile access by owner/non-owner/anonymous sessions;
- sanitised `publicProfiles/{uid}` and public place projections;
- absence of private memory/visit fields in public responses;
- public/private whole-profile transitions;
- Wishlist private/public/public→private transitions;
- public root `wishlistOrder` only while Wishlist visibility is public;
- removal of Wishlist-only public projections when privacy is disabled;
- preservation/sanitisation of mixed public place documents;
- public read-only behaviour;
- viewer-local sorting without persistence;
- account deletion/public projection cleanup.

AB-EV-033 established the public/private projection baseline. AB-EV-036 extends it with the atomic root-order/privacy model, aligned Firestore Rules and final Production validation.

Per-memory visibility remains future/conditional scope and is not implied by public Wishlist support.

### 5.4 Travel status and Wishlist rules — Executed

All six statuses are covered:

- Visited
- Lived / Live there
- Born there
- Nationality
- Passed through
- Want to visit / Wishlist

Coverage includes valid/incompatible combinations, automatic dependent transitions, rapid changes, reload persistence, impact on visits/memories/counters and public projection.

C35 corrects the previous mutual-exclusion interpretation: **Visited and Passed through may coexist**. Passed through may also coexist with Lived/Born because those statuses imply Visited. The second compatible status does not itself create a new `RegisteredVisit` or increment `visitsCount`.

AB-EV-035 protects this contract with focused QR-24 domain tests, a full Vitest checkpoint, one proportional Emulator browser/persistence scenario and Test Lead manual QA.

Wishlist uses the existing Want-to-visit source of truth; Wishlist-only records are non-physical and excluded from Manual Visit Order.

C36/AB-EV-036 protects the separate Wishlist settings contract:

- root `wishlistOrder` is presentation metadata, not membership;
- legacy `wishlistOrderRank` is fallback only;
- order-only, privacy-only and combined Saves use the confirmed root model;
- owner and public views must render canonical saved order after reload;
- successful owner settings changes refresh the confirmed Profile-root state used by the modal.

### 5.5 Account and data deletion — Executed / regression risk

Coverage includes identity deletion, private/public Firestore cleanup, username release, repeat/retry behaviour and absence of orphaned public records.

AB-EV-033 extends lifecycle expectations to Wishlist membership/ranks/public preference and public-profile places. C36 changes the canonical order source to the user/public root; deletion/Clear Map regression must continue to treat root Wishlist order as owned lifecycle state.

---

## 6. High-priority scope

### 6.1 Map and geographic catalogue — Executed

Coverage includes all 251 directly selectable records, status colouring, search/filter, normal/micro-marker interaction, desktop/mobile behaviour and the canonical `252 Places / 195 Countries / 57 Territories and Entities` model.

The technical UK aggregate remains non-selectable/derived.

### 6.2 Visits, memories and ordering — Executed for current V1.0 behaviour

Coverage includes repeated/detailed visits, explicit-Save memories, visit-count derivation, rapid add/remove/save, Manual Visit Order, Born there integrity and independent Wishlist ordering.

C35 adds explicit coverage that adding another compatible place status does not manufacture a travel occurrence and does not duplicate existing memories.

C36 separates ordering domains explicitly: Manual Visit Order remains per-place `visitOrderRank`; Wishlist presentation order is root `wishlistOrder` with legacy rank fallback only.

Future photos per RegisteredVisit are deferred beyond current V1.0 scope.

### 6.3 Personal Profile — Executed

Coverage includes profile fields, username/display/bio/social data, profile visibility, Wishlist visibility preference, canonical Wishlist root order, flag-sort persistence/recovery, responsive behaviour and relationship with the public projection.

The C36 owner modal is covered for root-order precedence and confirmed Profile refresh after order-only, privacy-only or combined settings changes.

### 6.4 Public Profile — Executed for implemented V1.0 baseline

Coverage includes:

- valid/invalid/private profile resolution;
- authenticated and anonymous viewers;
- public-source-only data access;
- read-only map and map-to-flag navigation;
- flag ordering/presentation controls;
- public achievements from sanitised metadata;
- public Wishlist tile/modal only when public and non-empty;
- canonical public Wishlist order from sanitised root `wishlistOrder`;
- no status/privacy/save/reorder controls in the public Wishlist modal;
- desktop/mobile layout and modal scroll lock;
- sanitisation of public root/place fields.

Broader localisation/performance/compatibility refinements remain separate V1.0 scope and do not invalidate the executed privacy/projection baseline.

### 6.5 Counters and statistics — Executed / regression risk

Coverage includes countries, territories/entities, conceptual Places, continents, Total Visits, status summaries, repeated visits and cross-screen consistency.

Wishlist-only status/order must not change physical-presence counters. Multiple compatible physical statuses still count a place once, and C35 confirms that adding Visited/Passed-through compatibility does not itself increase Total Visits.

### 6.6 Badges and achievements — Executed baseline / continued regression

Coverage includes unlock/relock chronology, World Completion, UK semantics, persisted metadata, notification behaviour and public sanitised `achievementMetadata`.

Final localisation/visual expansion remains subject to product scope.

### 6.7 Localisation — Planned/ongoing before V1.0

Supported locales remain:

- `pt-BR`
- `pt-PT`
- `en-GB`
- `es-ES`
- `es-419`
- `fr`

System-controlled UI requires localisation coverage; user-authored content is not automatically translated.

### 6.8 Performance and responsiveness — Partially executed / ongoing

Executed coverage includes map interaction, responsive layouts, repeated interaction, desktop/mobile viewports and important modal/control behaviour.

Quantitative performance targets and broader device/browser evidence remain incomplete.

### 6.9 Accessibility — Technical baseline executed

AB-EV-017 establishes a scoped WCAG 2.2 AA technical baseline; later features include targeted keyboard/semantic/dialog regression.

Formal accessibility certification and comprehensive native assistive-technology coverage remain out of claim.

---

## 7. Automation scope

Permanent automated coverage includes:

- Vitest component/domain tests;
- Firestore Rules suite;
- backend-focused Firestore Emulator integration/fault-injection tests;
- Playwright browser E2E against Firebase Emulators;
- controlled Production-only Playwright validation when authorised.

The normal E2E persistence environment is isolated at `127.0.0.1:3100` with Auth/Firestore Emulators and project `demo-atlasbadge-web`.

Real Firebase requests are fail-fast blockers in Emulator regression. The C36 focused Wishlist E2E recorded `realFirebaseRequests=0`.

C36 permanent coverage includes real Emulator batch rejection rather than relying only on a mocked `commit()` failure. Ordering coverage also includes the owner read-path/root-order precedence that manual QA initially exposed as missing.

Stale test assumptions are test-maintenance defects, not product defects. They are corrected without weakening functional acceptance criteria.

---

## 8. Regression scope selection

### Core checkpoints

High-value checkpoints include static quality gates, unit/domain tests, Rules, persistence/privacy E2E, backend atomicity, geographic/map regression, lifecycle scenarios and responsive/profile flows.

### Checkpoint rule

A green checkpoint is retained unless a later change directly invalidates it. Before a broad rerun, the invalidated evidence and reason should be identified.

AB-EV-034/035 demonstrate proportional gap/requirement validation. AB-EV-036 extends the principle: the central Wishlist persistence redesign justified one full Vitest/Rules/build baseline, while later read-path corrections were requalified through focused Wishlist tests and Test Lead retest rather than repeatedly rerunning unrelated suites.

---

## 9. Production scope

Production validation is focused on real integration after local/Emulator gates.

When Firestore Rules change, Production validation begins only after both:

- Vercel is READY at the expected Git SHA;
- the approved Firestore Rules-only deployment succeeds.

C35 changed production application source but not Firestore Rules/schema/configuration. Its Vercel deployment required no Rules deploy.

C36 changed both production application source and Firestore Rules. The controlled release recorded:

```text
Product SHA: 5d660b016528e75a2a70b49010a84065d884f883
Vercel: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9 — READY / production
Rules command: firebase deploy --only firestore:rules --project atlas-badge
Rules result: SUCCESS
Production smoke: PASS / Test Lead approved
```

Production uses controlled QA accounts/data only. Destructive operations require separate explicit authorisation and are not implied by a normal release smoke.

---

## 10. Deferred / assessment-gap scope

Current gaps/deferred areas include:

- broad Firefox/Safari/macOS/iPhone compatibility;
- formal load/stress testing;
- independent penetration testing;
- formal accessibility certification/native AT matrix;
- quantitative performance SLAs;
- future per-memory privacy;
- future RegisteredVisit photos;
- future Story/social features beyond current scope.

---

## 11. Release-blocking principles

Release approval is blocked by evidence of:

- material data loss or partial persistence contrary to an atomic user action;
- private-data exposure or unauthorised private reads;
- broken account/authentication essentials;
- persistence that cannot be trusted;
- broken status/Wishlist compatibility;
- inconsistent core counters;
- inaccessible/unusable critical controls;
- frontend/Rules deployment mismatch;
- insufficient evidence for a High/Critical change.

---

## 12. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
- `evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md`
- `docs/12-lessons-learned.md`
