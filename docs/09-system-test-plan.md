# AtlasBadge V1.0 System Test Plan

**Document status:** Active / change-controlled  
**Execution status:** Incremental system testing in progress; latest baseline reviewed through AB-EV-050; C45E Production technical PASS + local/Emulator visual PASS / Test Lead approved — 1 September 2026
**Product:** AtlasBadge  
**Target release:** V1.0  
**Document owner:** Test Lead/Product Owner  
**Last updated:** 1 September 2026

---

## 1. Document purpose

This plan defines how the AtlasBadge V1.0 system-test cycle is prepared, executed, controlled, evidenced and concluded.

It converts the product, risk, strategy, scope, environment, defect and reporting decisions into a concrete release-validation model. It is change-controlled as the product progresses toward the official V1.0 release.

Incremental feature/release approvals do not automatically mean the entire V1.0 product has completed final release assessment. They do, however, create valid checkpoints that may be carried forward when later changes do not invalidate them.

---

## 2. Test-plan control

A material update is required when:

- mandatory V1.0 scope changes;
- a quality risk changes state/priority;
- a material defect changes required coverage;
- an environment/browser/device decision changes;
- Firebase/Rules/public projection architecture changes;
- a new implementation invalidates an approved checkpoint;
- the Release Candidate changes;
- an exception affects deployment or release.

Each update should identify what changed, affected risks/tests, which previous results remain valid, which require re-execution and the Test Lead decision.

AB-EV-033 established the broad checkpointed regression baseline; AB-EV-034 and AB-EV-035 demonstrate proportional follow-up validation; AB-EV-036 applies the same principle to Wishlist atomic persistence; AB-EV-037 extends it to a destructive Clear Map redesign; AB-EV-038 hardens the manual-QA environment contract. AB-EV-039/040/041 extend the same checkpoint model to per-memory privacy, manual memory ordering and public-memory Profile presentation. AB-EV-042 applies the same proportional model to editable visit names. AB-EV-043 applies it to a broad but presentation-only brand alignment. AB-EV-044 applies the model to a high-risk Storage/server integration: bounded quota/Rules and Emulator gates were combined with real Firebase Admin/runtime proof, then later exact defects were closed through focused tests and small release gates rather than repeated full-suite execution. AB-EV-045 applies the model to localisation infrastructure by separating routing foundation from translated UI, validating the official Node 22 runtime, patching a framework advisory before publication and proving Production route ownership/resolution with focused smoke.

---

## 3. Relationship with the QA portfolio

This plan applies:

- [Product Overview](01-product-overview.md)
- [Quality Risk Analysis](02-quality-risk-analysis.md)
- [Test Strategy](03-test-strategy.md)
- [Test Scope](04-test-scope.md)
- [Entry and Exit Criteria](05-entry-exit-criteria.md)
- [Test Environments](06-test-environments.md)
- [Defect Management](07-defect-management.md)
- [Metrics and Reporting](08-metrics-and-reporting.md)
- [Lessons Learned](10-lessons-learned.md)
- [V1.0 Evidence Register](../evidence/v1.0/evidence-register.md)

Where documents disagree, the inconsistency must be resolved before the affected release decision.

---

## 4. Product and release context

AtlasBadge includes authentication/account lifecycle, an interactive travel map, six travel statuses, detailed visits/memories, Wishlist management, Manual Visit Order, geographic counters, achievements, private/public profiles, public sanitised projections, account deletion, destructive Clear Map, responsiveness, localisation, privacy and security controls.

The integrated test item includes the browser application plus Firebase Authentication, Cloud Firestore, Firestore Rules, Firebase Storage where relevant, local cache/state, Firebase Emulators, Vercel deployment and geographic data.

C31 establishes the canonical geographic model of 251 selectable records and 252 conceptual Places = 195 Countries + 57 Territories/Entities, including the derived United Kingdom rule. C32 adds read-only public Profile map-to-earned-flag navigation. C33 adds local selected-place sorting on the Map. C34 adds owner-only Manual Visit Order through `visitOrderRank`, replayable rapid visit intents and preserved birthplace transaction integrity.

The C35 product rule defines **Visited + Passed through** as compatible cumulative statuses. Individual travel occurrences remain represented by `RegisteredVisit`; adding the second status does not by itself create another visit.

C36 establishes that Wishlist membership remains `statuses.wishlist`, while canonical presentation order is root `wishlistOrder`; changed order/privacy/public projection work behind one user-visible Save must use one atomic Firestore batch boundary.

C37 establishes that Clear Map is one logical destructive operation. The private place reset, private root lifecycle reset and public root invalidation are committed through one atomic batch of at most 253 writes. Public child projections use `placesGeneration`; stale generations are no longer current/readable public travel state and their later physical deletion is housekeeping rather than part of the Clear Map success criterion.

C39 establishes explicit per-memory privacy and sanitised `publicMemories`; C40 stores memory presentation order separately in `memoryOrder` while preserving `registeredVisits` history; C41 displays those authorised public memories from earned flags using the same public projection for owner and anonymous viewers. C42 adds optional `visitName` presentation metadata to `RegisteredVisit` while preserving stable identity, explicit Save, C40 ordering and C41 sanitised public labels. C43 establishes the current AtlasBadge presentation baseline: Atlas Gold/Black/Warm White drive generic interface identity while status, feedback, data-visualisation and external-brand colours remain semantic exceptions.

Previous incremental testing is valid evidence when impact analysis confirms that a later change has not invalidated it.

---

## 5. Test item and version identification

Every material execution record should identify where applicable:

- branch and full Git SHA;
- application target (local Emulator runtime or Production);
- Vercel deployment corresponding to the SHA;
- Firebase project/Emulator identifier;
- relevant Rules version/deployment;
- browser/device/viewport;
- execution date;
- test-data state.

The branch name `main` alone is not sufficient evidence because its content changes.

### Current release anchors

The latest sequence is:

```text
C31 / AB-EV-029 geographic counter integrity:
627f4f948f7b6af0151fec77ffe800380e73e989
fix(stats): align world coverage counters
Vercel automatic Production deployment: READY
Production Guard / validate-production / validate-clear-map: PASS
AB-DEF-012: CLOSED

C32 / AB-EV-030 Profile map-to-earned-flag navigation:
c75891b68836ca495e319c31878c3cb230f4f92e
feat(profile): navigate map clicks to earned flags
Vercel automatic Production deployment: READY
Dedicated navigation regression + Production validators: PASS

C33 / AB-EV-031 dashboard selected-place sorting:
0164b8c6f2cb25dc510248b43d39a7d1ca1dc538
C33 final hardening baseline
Vercel: dpl_4a94LAa8p1D57y3cHpzNHRGATQMq — READY / production
Focused C33/C32/flag-sort E2E + Production validators: PASS

C34 / AB-EV-032 manual visit order + rapid visit concurrency:
8474a78d1f7e6d046b3e918b3be8ac2af01188fc
fix(data): preserve birthplace integrity and clean C34 artifacts
Vercel: dpl_9a5e9rQNraTZpTHrtYJ7h7QtzPfi — READY / production
Firestore Rules-only deployment: SUCCESS
Final gate: 308 Vitest PASS / 7 skipped; 224 Rules PASS; 9 focused Emulator E2E PASS
Production Manual Visit Order / Rapid Visits / Birthplace integrity: PASS
AB-DEF-013: CLOSED

AB-EV-033 runtime/security baseline:
7bbdb9402145523f6a2f36d41cc74e55479cc664
Vercel: dpl_HEKQuz6MAXiW413m6cqnH25zWrRg — READY

AB-EV-034 QR-01 evidence-only closure:
66cffbc933710f2b9f4ba007c5726ebc2857ac82
test(profile): cover flagSortOrder failed-write recovery
No runtime deployment required for the technical closure.

C35 runtime requirement correction:
29c7ac63748fb823899fb77cdb6ee91bb6194b1f
fix(status): allow visited and passed through coexistence
Vercel: dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv — READY / production

C36 / AB-DEF-017 correction:
5d660b016528e75a2a70b49010a84065d884f883
fix(wishlist): make settings save atomic and preserve order
Vercel: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9 — READY / production
Firestore Rules-only deployment: SUCCESS / project atlas-badge
Production smoke: PASS / Test Lead approved

C37 / AB-DEF-018 correction:
add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
fix(clear-map): make logical reset atomic
Vercel: dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz — READY / production
Firestore Rules-only deployment: SUCCESS / project atlas-badge
Production Clear Map retest: PASS / Test Lead approved

C38 / AB-EV-038 documentation hardening:
QA-environment/documentation hardening for the manual-QA contract.
No application runtime behaviour changed. No Firebase Rules changed. No deployment required.
Routine manual QA uses localhost:3000 + real atlas-badge Firebase.
Emulator remains primary for isolated automated/fault-injection coverage.
Manual Emulator QA is scenario-specific rather than default.

C39 / AB-EV-039 individual memory privacy:
daf37e7f24950137473917f6b4e898428b5de20e
feat(memories): add per-memory privacy controls
GitHub/Vercel commit status: success
Test Lead manual QA: PASS

C40 / AB-EV-040 manual memory ordering:
2db201bf89c649655c6e86ccb4c03dc7a5ac621a
feat(memories): add manual memory ordering
Firestore Rules-only deployment: SUCCESS / project atlas-badge
GitHub/Vercel commit status: success
Test Lead manual QA: PASS

C41 / AB-EV-041 public memories from flags:
bb57a004d7b0c64420d093fc4000a67e226eba1e
feat(profile): show public memories from flags
GitHub/Vercel commit status: success
Local Test Lead functional/visual QA: PASS
Final Test Lead Production smoke: PASS — 27 August 2026

C42 / AB-EV-042 editable visit names:
601e34921a7026857bef70c453f7de09c39579ae
feat(visits): add editable visit names
Vercel: dpl_5Jpxq9DsvoCKrbE5gjd7pF67ueyV — READY / production
Full Vitest: 479 PASS / 0 FAIL
Lint / TypeScript / production build / diff-check: PASS
Local Test Lead functional/visual QA: PASS
Final Test Lead Production smoke: PASS — 27 August 2026

C43 / AB-EV-043 AtlasBadge visual identity alignment:
bcc10d63e0f3363d39f3029c0498deb49c30180f
style(brand): align AtlasBadge visual identity
Vercel: dpl_FHmZdquA35oa6FVQjtpHVLZAcfyt — READY / production
Final full Vitest: 457 passed / 22 skipped / 0 failed
Lint / TypeScript / production build / diff-check: PASS
Test Lead desktop/mobile visual QA: PASS
Final Test Lead Production visual smoke: PASS — 27 August 2026

C44 / AB-EV-044 one photo per RegisteredVisit:
fc2cea28c7697c18a9e7a79d5d1451b895066e20 — initial feature
93e31634a9cf747976a01ca6d6e5e3b256fc6f56 — bounded free-quota/storage hardening
1dc6edc04952e03906241cda81beb6649d06c8fa — Node 22 Firebase Admin Storage runtime pin
6b8721140a9e46c6c6260b87b3e6e587e7876ec2 — public photoRef projection correction
f6349977492deea98452df5d6581f6edc07a3a6d — protected-field profile edit correction
c5c39c1407aa25b604ff82e9c4d96522575c143e — private Wishlist public-projection correction
Storage Rules bounded-slot suite: 24 / 24 PASS
Firestore Rules final focused checkpoint: 235 / 235 PASS
C44 focused Emulator E2E: PASS
Vercel final: dpl_CcUWVh7v97oUTQ3W9WAuqkJMxfQh — READY / production
Owner visit-photo API: 200 in Production
Public visit-photo API: 200 in Production
Test Lead Production photo/quota/Profile validation: PASS — 29 August 2026

C45A / AB-EV-045 localisation routing foundation:
92be6f41ee5a71eaf20396383cd11624b84ef842 — localisation routing/i18n foundation
d25ac8510ebd2e5c1fdea34143a7b862d97ae5b7 — Next.js 16.2.11 security hardening
next-intl 4.14.1; final runtime Node v22.23.2
TypeScript / lint / build: PASS; Next.js 16.2.11 generated 19 pages
Focused Vitest: 33 / 33 PASS
Focused Playwright: 20 / 20 PASS on Edge with Firebase Emulators and zero Firebase-real requests
Vercel final: dpl_2mzDcqfXz7hb2fC5ZcTNMZfscjNP — READY / production
Production smoke: all six public Home locale URLs 200; root locale resolution 307 with cookie/Accept-Language precedence; `[username]` preserved; `/app` remains unprefixed; `/pt-br/app` 404 as designed
Test Lead Production smoke: PASS — 30 August 2026

C45B / AB-EV-046 public Home localisation and language selector:
c5b025199745425f37caa7a0f68ebdb44ddbb6f8 — public Home translation/selector release
Final local gates under Node v22.23.2: TypeScript PASS; lint PASS; build PASS
Focused Vitest release gate: 32 / 32 PASS
Focused Playwright release gate: 26 / 26 PASS on Edge with Firebase Emulators and zero Firebase-real requests
Release scope: 47 files; six locale message catalogs; local SVG flags; desktop/mobile selector; cookie persistence; localized metadata/document language
Architecture review: server-correct `<html lang>` retained; root request-time dynamic rendering accepted as V1.0 technical debt because multi-root migration was disproportionate
Vercel final: dpl_5WtEJGeCBtm6QSJpwZhgQzk1CJ66 — READY / production; Node 22; 37 s build
Production smoke: six locale Homes 200 with translated content and correct `html lang`; `/fr` remains Home; `/@mhclaudino` isolated; `/app` unprefixed; `/pt-br/app` 404
Authenticated Production Home: selector + avatar PASS; `/app` selector absent
Mobile 390×844 technical smoke PASS; Test Lead real-mobile visual QA PASS — 31 August 2026

C45C / AB-EV-047 Login localisation and locale continuity:
898cb5bcd088e379afaeed388b7eab0c35b27bb2 — localized unprefixed Login with Home→Login locale continuity
Final local gates under Node v22.23.2: TypeScript PASS; lint PASS; build PASS
Vitest release gate: 76 / 76 PASS across 12 files
Playwright release gate: 21 / 21 focused PASS + Google-linking 1 / 1 PASS; Firebase Emulators; zero real-Firebase requests
Release scope: 27 files; 1,224 insertions; 78 deletions
Vercel final: dpl_tixX939LhgvSCziRg2HnAesn7B6n — READY / Production; build 40 s under Node 22.x
Production smoke: all six Login locales HTTP 200; localized metadata and server-correct `html lang`; query→cookie→browser→pt-BR precedence PASS
Home→Login locale continuity PASS; authenticated Hero/final CTAs preserved `/app`; `/app`, public Profile, Onboarding and Verify Email remain Portuguese/pt-BR
390×844 responsive smoke PASS for fr, pt-PT and en-GB; no runtime/hydration/missing-message/4xx/5xx issues
Test Lead Production visual QA PASS — 31 August 2026

C45D / AB-EV-048 Onboarding localisation:
5acf5ee4cc264509055163a6fb1470e162683c9a — localized unprefixed Onboarding/profile creation
Final local gates under Node v22.23.2: TypeScript PASS; lint PASS; build PASS
Vitest release gate: 83 / 83 PASS
Playwright release gate: 13 / 13 PASS on Edge; Firebase Auth/Firestore/Storage Emulators; zero real-Firebase requests
Release scope: 23 files; 1,592 insertions; 77 deletions
Validation architecture: additive stable username/social-link error codes; shared Portuguese callers preserved; profile schema and stored fallback `Viajante` unchanged; no locale/language persisted
Vercel final: dpl_DPkW9nB2uDNRysKXCMdvTLDZtxyU — READY / Production; Node 22.x; Next.js 16.2.11
Production technical smoke: six Onboarding locale resolutions HTTP 200 with correct server `html lang` and localized metadata; locale precedence PASS; anonymous guard redirects to Login; `/app`, `/verify-email` and public Profile remain pt-BR
Production user-state visual replay was intentionally not created because no qualifying safe Production test user existed; Test Lead visual QA had already passed locally against the Auth/Firestore/Storage Emulators
Test Lead approved release closure — 1 September 2026

AB-EV-049 public Home language-selector centering patch:
33556ecc5c1eda19522ec0c1975d7e755f6dbad4 — true-center localized Home language selector
Final local gates under Node v22.23.2: TypeScript PASS; lint PASS; build PASS
Vitest: 20 / 20 PASS
Playwright Edge: 8 / 8 PASS; Firebase Emulators; zero real-Firebase requests
Root cause: selector was centered only inside remaining flex space, so translated right-side CTA width shifted the visual center by 29.85–66.73 px
Fix: localized Home desktop uses equal flexible side tracks around an auto-width center selector; mobile remains flex/compact
Production deployment: dpl_F8pCXRG6vfY6tyU43i2zboj4C2LG — READY / Production
Production 1280 px geometry: pt-BR, pt-PT, es-419, es-ES, fr and en-GB all selector-center = header-center, 0 px deviation; CTA widths varied without moving selector
390×844 mobile PASS; `/app` and public Profile isolation PASS; no console/network/runtime errors
Test Lead visual PASS before release; Production PASS — 1 September 2026

C45E / AB-EV-050 email-verification localisation:
aa6c2972d4755985de63a2d5c3ba2365c52cebef — localized `/verify-email` and `mode=verifyEmail` action flow
Final local gates under Node v22.23.2: TypeScript PASS; lint PASS; build PASS
Vitest: 63 / 63 PASS
Playwright Edge: 21 / 21 PASS across C45E plus C45C/C45D regressions; Firebase Auth/Firestore/Storage Emulators; zero real-Firebase requests
Release scope: 22 files; 1,515 insertions; 82 deletions
AuthContext strategy: additive machine-readable verification error codes while preserving existing Portuguese `error` strings/statuses for current callers
State-machine regression: resend/cooldown 60 s, concurrent-send protection, auth reload/token refresh, profile refresh and `/app`/`/onboarding` redirects preserved
Shared `/auth/action`: `verifyEmail` resolves contextual locale; `resetPassword`, unsupported and missing modes remain Portuguese with `lang=pt-BR`
Vercel final: dpl_FVD2sb5S8jeTFrt82pzqFm2zgvUT — READY / Production; Node 22.x; Next.js 16.2.11
Production technical smoke: six `/verify-email` document locales/metadata PASS; anonymous redirects preserve locale; localized non-mutating invalid verifyEmail action states PASS; French-cookie resetPassword isolation PASS; `/app`, `/badges` and public Profile remain pt-BR
No Production user or verification email was created for sign-off; Test Lead visual QA and stateful verification/OOB testing were completed locally against Firebase Emulators
Test Lead approved C45E release closure — 1 September 2026
```

C35 did not alter Firestore Rules. C36 and C37 did, so their Production functional validation was blocked until the separate Rules-only deployment restored application/Rules parity.

---

## 6. System-test objectives

The V1.0 cycle provides evidence that AtlasBadge can be released safely within the declared scope and limitations.

Objectives include:

- validate registration/authentication/session/account lifecycle;
- confirm correct user ownership and isolation;
- validate all approved status/Wishlist combinations;
- verify reliable persistence, failed-write recovery, reload and concurrency behaviour;
- verify one logical multi-resource Save or destructive reset is atomic when the product contract requires all-or-nothing persistence;
- protect visits/memories/ordering from unintended loss or artificial duplication;
- verify explicit per-memory privacy defaults, Save semantics and public projection;
- verify manual memory presentation order without mutating source visit history;
- verify the public Profile flag modal renders only the sanitised public-memory source and preserves exact visit identity/order;
- confirm destructive Clear Map cannot expose a partially cleared private/public state;
- confirm stale public projection generations are not returned as current travel data;
- confirm the canonical geographic/counter model;
- confirm private/public Profile projection and sanitisation;
- confirm public Profile/Wishlist remains read-only;
- validate achievement chronology and public metadata;
- validate complete account deletion/cleanup;
- assess declared localisation, browser/device, responsive and accessibility scope;
- verify release/deployment parity;
- support a defensible Test Lead release recommendation.

---

## 7. Mandatory critical path

Release-blocking coverage includes:

- application availability;
- authentication/verification/recovery/linking/session/logout;
- owner identity and protected routes;
- travel-status/Wishlist persistence and compatibility;
- visits/memories/order/concurrency;
- atomic combined settings/actions/destructive resets where required by user intent;
- rejected-write recovery where risk-relevant;
- privacy/user isolation;
- public projection whitelisting/sanitisation;
- current/stale public generation boundaries after destructive reset;
- public/private transitions;
- account deletion and Clear Map lifecycle;
- core counters/geographic model;
- achievements where affected;
- responsive usability of critical controls;
- Vercel/Firestore Rules parity when Rules change.

---

## 8. Test levels and execution layers

### 8.1 Static / build quality

Depending on affected code:

- TypeScript;
- lint;
- `git diff --check`;
- Next.js production build when production code/build configuration is affected and the relevant checkpoint is invalidated.

### 8.2 Unit/domain/component tests

Vitest protects stable business/component behaviour.

Important recent checkpoints:

```text
AB-EV-033 integrated checkpoint:
359 passed / 15 skipped / 0 failed

C35 full domain/component checkpoint after statusRules change:
373 / 373 PASS

C35 focused QR-24 status/history checkpoint:
22 / 22 PASS

AB-EV-034 focused flagSortOrder recovery:
1 / 1 PASS

C36 focused combined Wishlist persistence:
19 / 19 PASS

C36 final WishlistModal root-order/refresh:
5 / 5 PASS

C36 full Vitest checkpoint:
375 passed / 16 skipped

C37 Profile public projection preservation:
9 / 9 PASS

C37 Public Profile source/generation caller:
5 / 5 PASS

C37 Wishlist combined regression:
6 / 6 PASS

C37 Wishlist/public projection regression:
17 / 17 PASS

C37 Clear Map confirmation modal:
2 / 2 PASS
```

### 8.3 Firestore Rules and backend Emulator

Rules validate owner-only private access, public projection boundaries, approved data schemas and current/stale generation access.

Latest C37 Rules checkpoint:

```text
226 / 226 PASS
```

Relevant backend Emulator results include:

```text
C36 Wishlist rejected-batch atomicity:
1 / 1 PASS

C37 Clear Map lifecycle/atomicity/generation:
7 / 7 PASS

C37 upsert/concurrency generation regression:
4 / 4 PASS
```

C37 Clear Map coverage includes root Wishlist cleanup, one atomic logical-reset rejection, the supported 251-place/253-write maximum, legacy generation-0 compatibility, stale public direct-read denial, current-generation query/new-place behaviour and the missing-public-root case.

### 8.4 Browser E2E — Emulator

Normal persistence/privacy E2E uses:

```text
App: 127.0.0.1:3100
Auth: 9099
Firestore: 8080
Project: demo-atlasbadge-web
```

The suite fails fast against unexpected real Firebase traffic.

Relevant focused results include:

```text
C35 Visited + Passed-through coexistence: 1 / 1 PASS

C36 Wishlist persistence/public order baseline: 3 / 3 PASS
realFirebaseRequests=0
```

C37 did not require a new broad Playwright campaign because the affected destructive persistence, Rules/public-generation and confirmation-modal boundaries were covered deterministically at backend/Rules/component layers and later validated manually in Production.

### 8.5 Manual/exploratory QA

Used for usability, visual behaviour, edge conditions, unexpected interaction sequences and Test Lead sign-off.

C31 manual QA confirmed the corrected counters/achievements against the audited catalogue. C32 manual QA confirmed exact flag targeting/highlight without editing. C33 manual QA confirmed the local dashboard sort and responsive header. C34 manual QA rejected intermediate states with live-Rules mismatch, unsafe blanket concurrency bypass and unrelated repository artefacts; the final clean baseline passed Manual Visit Order, rapid-visit and birthplace-integrity validation.

C35 received Test Lead manual QA before commit approval, including coexistence, memory/counter behaviour and independent status handling.

C36 demonstrates why manual QA remains material even with strong automation. The first manual retest found that the backend correctly saved root `wishlistOrder`, but the owner read path still rendered legacy/alphabetical order after a combined reorder/privacy Save. Static/backend evidence then isolated the frontend read-path issue, and the final Test Lead retest approved root-order precedence plus confirmed Profile refresh for order/privacy changes.

C37 similarly retained a focused manual boundary. Before release, a local working-tree C37 application was briefly exercised against the still-deployed C36 Production Rules; the resulting permission rejection was classified as an application/Rules parity mismatch, not a second product failure. The actual C36 Production baseline independently reproduced the real AB-DEF-018 Clear Map failure. After C37 application and Rules parity was restored, the Test Lead performed the destructive Production retest and approved the correction.

C39 manual QA validated per-memory privacy, explicit Save and no-note visit eligibility. C40 manual QA first exposed a real-backend Rules-parity blocker for the new `memoryOrder` field; after the approved Rules-only deploy, the same scenario persisted correctly and manual reorder/reload/delete behaviour passed. C41 manual/visual QA rejected an inline memory panel in favour of the established Wishlist modal pattern, corrected owner public-memory source selection, verified exact `VISITA n` identity and restored the Profile sort-toggle layout before release. C42 manual QA then refined edit affordance, duplicate-error recovery, single-editor coordination and subtle premium hover behaviour before approving persistence, reorder identity and public/private label projection. C43 used real-browser desktop/mobile review as a release gate because the change intentionally affected visual hierarchy across many surfaces; the Test Lead approved the subtle brand alignment before commit and later confirmed Production Visual PASS.

### 8.6 Production validation

Executed only after the expected source revision and applicable Firebase Rules are deployed/aligned.

Production testing uses controlled QA accounts/data and only the authorised scope.

C31 and C32 reached automatic Vercel READY plus focused Production validators. C33 closed at `0164b8c6...` / `dpl_4a94...` with selected-place sorting and Production validators PASS. C34 required both the clean runtime baseline `8474a78...` / `dpl_9a5e...` and a separately deployed Rules-only change before Production Manual Visit Order, Rapid Visits and Birthplace integrity could be approved.

For C35, release verification confirmed the exact runtime SHA reached a `READY` Production Vercel deployment. AB-EV-035 explicitly limits the claim to that recorded evidence boundary.

For C36, both application source and Firestore Rules changed. The Test Lead therefore required:

```text
Vercel READY at 5d660b016528e75a2a70b49010a84065d884f883
+
firestore:rules deployment SUCCESS against atlas-badge
+
focused Production Wishlist smoke PASS
```

For C37, the same parity gate applied:

```text
Vercel READY at add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
+
firestore:rules deployment SUCCESS against atlas-badge
+
focused destructive Clear Map Production retest PASS
```

AB-EV-036 and AB-EV-037 record the completed Production approvals.

C39 and C40 both reached successful automatic Vercel deployment after their approved local/manual gates; C40 additionally required a separately authorised Firestore Rules-only deployment before manual real-backend QA could proceed. C41 commit `bb57a004d7b0c64420d093fc4000a67e226eba1e` reached Production successfully and the Test Lead completed the focused Production smoke on 27 August 2026. C42 commit `601e34921a7026857bef70c453f7de09c39579ae` reached Vercel deployment `dpl_5Jpxq9DsvoCKrbE5gjd7pF67ueyV` in READY/production state and passed focused Production smoke. C43 commit `bcc10d63e0f3363d39f3029c0498deb49c30180f` then reached Git-sourced Vercel deployment `dpl_FHmZdquA35oa6FVQjtpHVLZAcfyt` in READY/production state with no backend/Rules deployment; the Test Lead reported Production Visual PASS on 27 August 2026. C41–C43 are closed and retained as regression baselines.

---

## 9. Checkpointed incremental regression

### Principle

A previously green checkpoint remains valid unless a later change directly invalidates what it proved.

Examples:

- E2E-only stale locator cleanup does not invalidate full unit/Rules checkpoints;
- a persistence change invalidates affected persistence tests and relevant static/build gates;
- a Rules change invalidates Rules/release-parity coverage;
- a responsive CSS change requires affected responsive/manual/E2E validation;
- formatting performed after a gate invalidates only the gate required to verify the final file form unless semantics changed.

This is not reduced coverage by omission: the carried-forward result and reason must remain explicit.

### Recent applications

**AB-EV-034:** the write-path audit identified only `flagSortOrder` as the remaining Partial QR-01 path. One deterministic component failure/recovery test closed the gap; no runtime deployment or unrelated regression rerun was required.

**C35/AB-EV-035:** central `statusRules.ts` changed, justifying focused QR-24 tests and one full Vitest checkpoint. Browser impact was restored through one focused Emulator E2E; a full Playwright campaign was not required because no wider browser contract was invalidated.

**C36/AB-EV-036:** shared Wishlist persistence/read logic and Rules changed, so one full Vitest/Rules/build baseline was justified. Later focused dependency-injection and owner read-path changes were requalified with their directly affected Emulator/component/E2E/manual paths instead of repeatedly rerunning the full suite. The final Production smoke was proportional to the released Wishlist contract rather than an unrelated full-system campaign.

**C37/AB-EV-037:** `travelMap.ts`, public source handling, profile projection and Firestore Rules changed materially, so the full Rules checkpoint and Production build were rerun. Focused Clear Map, Wishlist/upsert, public-source/profile and modal tests restored the invalidated functional boundaries. The previous full Vitest and broad Playwright checkpoints were carried forward because no failing focused evidence or shared-domain change justified another complete campaign.

The efficiency rules are retained in `docs/10-lessons-learned.md`.

---

## 10. Defect and requirement-change handling

A failed scenario is classified before correction as:

- product defect;
- stale/test-automation defect;
- infrastructure/environment issue;
- inconclusive.

A later requirement correction is not counted as a Product Defect when the implementation correctly matched the previously approved product rule.

C35 is the reference requirement-correction example: tests valid for the old rule became stale after Product Owner/Test Lead redefinition and were updated without inventing a Product Defect ID.

AB-DEF-017 is a true Product Defect example: fault injection proved partial persisted state from two independent Wishlist commits. During retest, a Rules-parity permission failure was correctly separated as an environment/release mismatch, while lost order under the correctly aligned environment was correctly treated as a product read-path failure until fixed.

AB-DEF-018 is the destructive-operation counterpart: C36 root `wishlistOrder` made the old Clear Map lifecycle invalid under the aligned Rules, and the existing public/private split commits independently permitted partial persisted state. Both failure modes were proven and closed under one defect because they violated the same single Clear Map intent. A later local C37-vs-C36-Rules failure was classified separately as parity mismatch rather than inflating the defect count.

---

## 11. Pre-release repository quality gate

Before commits are approved, the candidate is audited for:

- unexpected tracked/untracked files;
- scratch/debug artefacts;
- secrets/credentials;
- accidental Production/test configuration changes;
- stale skips/`.only`/debugger/logging;
- diff integrity;
- coherent commit grouping.

Auto-fix/formatting should occur before final validation whenever possible so that the final committed artefact is the artefact that passed the quality gates.

Temporary manual-QA bootstrap/diagnostic scripts are not promoted into product commits merely because they were useful during an investigation.

---

## 12. Release and deployment gate

For releases that alter Firestore Rules:

```text
1. Test Lead approves commits.
2. Create controlled commits.
3. Test Lead approves push.
4. Push to main.
5. Verify Vercel READY at expected SHA.
6. Test Lead explicitly approves Rules deployment.
7. Deploy only firestore:rules.
8. Confirm success and no unrelated Firebase resource deployment.
9. Start focused Production validation.
```

C36 followed this gate:

```text
Commit: 5d660b016528e75a2a70b49010a84065d884f883
Vercel: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9 — READY / production
Rules: firebase deploy --only firestore:rules --project atlas-badge — SUCCESS
Other Firebase services: none
Production smoke: PASS
```

C37 followed the same controlled parity gate:

```text
Commit: add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
Vercel: dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz — READY / production
Rules: firebase deploy --only firestore:rules --project atlas-badge — SUCCESS
Other Firebase services: none
Production destructive retest: PASS
```

No force push/rebase/deploy shortcut is used to bypass this gate.

---

## 13. Latest Production/release evidence

AB-EV-033 remains the latest broad authenticated Production validation for the larger Wishlist/public projection release baseline.

Later release evidence:

```text
AB-EV-034:
test-only QR-01 coverage closure — no runtime deployment required

AB-EV-035 / C35:
Commit: 29c7ac63748fb823899fb77cdb6ee91bb6194b1f
Vercel Production: dpl_HjnEQUdzS7G19So5hxyDRgkUxLvv
State: READY
Firestore Rules/schema/config: unchanged

AB-EV-036 / AB-DEF-017 / C36:
Commit: 5d660b016528e75a2a70b49010a84065d884f883
Vercel Production: dpl_HfDXpCCDisqAHXL85fyqHjnUd5N9
State: READY
Firestore Rules-only deploy: PASS / atlas-badge
Production Wishlist smoke: PASS / Test Lead approved
Decision: AB-DEF-017 CLOSED

AB-EV-037 / AB-DEF-018 / C37:
Commit: add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c
Vercel Production: dpl_2ywN1nFvxnoZD4JHo4YMd65ogkrz
State: READY
Firestore Rules-only deploy: PASS / atlas-badge
Production Clear Map retest: PASS / Test Lead approved
Decision: AB-DEF-018 CLOSED

AB-EV-038 / C38:
Environment documentation hardening.
No runtime deployment, Rules changes, or system regression required.
Decision: PASS — Test Lead approved — 26 August 2026
```

---

## 14. Evidence and traceability

Material system-test evidence must connect:

```text
Requirement / rule
→ Quality risk
→ Implementation
→ Automated/manual tests
→ Defect(s) or requirement correction, if applicable
→ Retest / regression
→ Commit
→ Deployment
→ Production result
→ Test Lead decision
```

The central public index is `evidence/v1.0/evidence-register.md`.

Current recent evidence is AB-EV-033 through AB-EV-050.

---

## 15. Exit and release assessment

An increment may be approved when:

- required tests/gates pass;
- no unacceptable Critical/High defect remains;
- privacy/security/data-integrity boundaries are acceptable;
- repository quality is clean;
- source/deployment/Rules parity is proven where relevant;
- required Production validation is complete or its limitation explicitly accepted;
- residual risks are retained in the risk register.

The official V1.0 final release assessment additionally depends on completion/acceptance of the remaining mandatory scope, including localisation beyond the completed public Home baseline, compatibility/performance and final reset/clean-start activities as applicable. C44 one-photo-per-`RegisteredVisit` scope and C45A/C45B/C45C/C45D/C45E public-Home + Login + Onboarding + email-verification localisation baseline are closed and retained as regression coverage.

The final release decision belongs to the Test Lead/Product Owner.

---

## 16. Current limitations carried into later V1.0 work

- QR-01 is now a Regression risk rather than a Current gap.
- AB-DEF-017 is closed; Wishlist atomic settings/order behaviour remains permanent regression scope.
- AB-DEF-018 is closed; Clear Map atomic logical reset/generation invalidation remains permanent regression scope.
- Physical garbage collection of obsolete public-generation child documents is optional housekeeping; correctness does not depend on it.
- Browser/device coverage is not comprehensive.
- Quantitative performance targets are not established.
- Formal accessibility certification/native assistive-technology coverage is not claimed.
- No independent penetration/security audit or formal load test has been completed.
- C44 one-photo-per-`RegisteredVisit` is implemented, Production-approved and retained as permanent regression scope, including the free 10-photo quota and server-mediated read/privacy boundary.
- C45A/C45B public-Home localisation, C45C Login localisation, C45D Onboarding localisation and C45E email-verification localisation are approved. Authenticated application, Badges, public Profile, resetPassword action presentation and other remaining system-controlled localisation still require separate V1.0 assessment. The current root-layout `headers()` approach makes page rendering request-time dynamic and is retained as explicit V1.0 technical debt pending any future multi-root routing redesign.

---

## 17. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-033-wishlist-public-profile-release-hardening.md`
- `evidence/v1.0/regression/ab-ev-034-qr-01-failed-write-recovery-closure.md`
- `evidence/v1.0/regression/ab-ev-035-c35-visited-passed-coexistence.md`
- `evidence/v1.0/defects/ab-ev-036-wishlist-atomic-settings-save-and-order-integrity.md`
- `evidence/v1.0/defects/ab-ev-037-clear-map-atomic-generation-reset.md`
- `evidence/v1.0/environments/ab-ev-038-manual-qa-environment-contract.md`
- `evidence/v1.0/regression/ab-ev-039-c39-individual-memory-privacy.md`
- `evidence/v1.0/regression/ab-ev-040-c40-manual-memory-ordering.md`
- `evidence/v1.0/regression/ab-ev-041-c41-public-memory-flag-modal.md`
- `evidence/v1.0/regression/ab-ev-042-editable-visit-names.md`
- `evidence/v1.0/regression/ab-ev-043-visual-identity-alignment.md`
- `evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md`
- `evidence/v1.0/regression/ab-ev-045-c45a-localization-routing-foundation.md`
- `evidence/v1.0/regression/ab-ev-046-c45b-public-home-localization-and-language-selector.md`
- `evidence/v1.0/regression/ab-ev-047-c45c-login-localization-and-locale-continuity.md`
- `evidence/v1.0/regression/ab-ev-048-c45d-onboarding-localization.md`
- `evidence/v1.0/regression/ab-ev-049-public-home-language-selector-centering.md`
- `evidence/v1.0/regression/ab-ev-050-c45e-email-verification-localization.md`
- `docs/10-lessons-learned.md`
