# AtlasBadge V1.0 System Test Plan

**Document status:** Active / change-controlled  
**Execution status:** Premium Hardening in progress; reviewed through AB-EV-068. AB-EV-065 remains the technical Release Candidate anchor; PA-001/AB-EV-066, PA-002/AB-EV-067 and PA-003/AB-EV-068 are published hardening closures. PA-003 is Production verified. Final Premium Hardening closure and final V1.0 Go Live are not yet declared.  
**Product:** AtlasBadge  
**Target release:** V1.0  
**Document owner:** Test Lead/Product Owner  
**Last updated:** 15 October 2026

---

## 1. Document purpose

This plan defines how the AtlasBadge V1.0 system-test cycle is prepared, executed, controlled, evidenced and concluded.

It converts the Product Overview, Quality Risk Analysis, Test Strategy, Test Scope, environment controls, defect process and Lessons Learned into a concrete release-validation model.

This is a **living control document**, not a chronological execution diary. Detailed historical increment results remain in `evidence/v1.0/` and the Evidence Register. Earlier execution details are preserved there rather than duplicated indefinitely in this plan.

Incremental approvals create valid checkpoints that may be carried forward when later changes do not invalidate them. An incremental PASS does not automatically mean the whole V1.0 product has completed final release assessment.

---

## 2. Test-plan control

A material update is required when:

- mandatory V1.0 scope changes;
- a quality risk changes state or priority;
- a material defect changes required coverage;
- an environment/browser/device decision changes;
- Firebase/Rules/Storage/public-projection architecture changes;
- a server/client ownership boundary changes;
- a new implementation invalidates an approved checkpoint;
- the Release Candidate or Premium Hardening publication anchor changes;
- an exception affects review, publication or release ordering.

Each update identifies, where applicable:

1. what changed;
2. affected risks and acceptance criteria;
3. which previous evidence remains valid;
4. which evidence is invalidated and must be re-executed;
5. the environment/safety boundary;
6. the Test Lead decision.

### 2.1 Current control hierarchy

Read this plan together with:

- `docs/01-product-overview.md`
- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/05-entry-exit-criteria.md`
- `docs/06-test-environments.md`
- `docs/07-defect-management.md`
- `docs/08-metrics-and-reporting.md`
- `docs/10-lessons-learned.md`
- `GOVERNANCE-ERRATA.md`
- `evidence/v1.0/evidence-register.md`

Where current control documents disagree, the conflict is resolved before the affected publication/release decision.

---

## 3. Product and release context

AtlasBadge V1.0 includes authentication/account lifecycle, an interactive travel map, six travel statuses, detailed visits/memories, Wishlist management, Manual Visit Order, geographic counters, achievements, private/public profiles, sanitised public projections, account deletion, destructive Clear Map, visit-photo storage/quota, seven-locale presentation, responsive behaviour, accessibility controls and Firebase/Vercel release integration.

The integrated system-test item includes:

- Next.js/React/TypeScript application;
- Firebase Authentication;
- Cloud Firestore and Firestore Rules;
- Firebase Storage and Storage Rules where applicable;
- Firebase Admin/server routes where applicable;
- client cache/state and mutation orchestration;
- Firebase Emulator Suite;
- browser/E2E harness;
- Vercel Git deployment;
- geographic/catalogue data.

### 3.1 Current Product anchors

The Release Candidate anchor that preceded Premium Hardening is:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
chore(release): qualify Node 24 LTS candidate
Vercel: dpl_Gncwr79sPfZuPLXx5zSiwD1RpqTr
READY / production / exact SHA
Node 24.21.0 / Next.js 16.3.6
```

PA-001 implementation and its later test-only UTF-8 oracle repair are:

```text
b0fef1d825649a7c961a3413f11faa4c104b8aca
fix(clear-map): move reset to transactional server boundary
Vercel: dpl_57PhNgUXQdmDsTByu9xJp4jCkKcK — READY


d0116967fc389353191c11e8865f20e38b9d5835
test(clear-map): fix UTF-8 route expectations
Vercel: dpl_5M8VAxJ6VcWdcgSM78rsVDWYiP4J — READY
```

PA-002 publication is:

```text
49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
fix(visit-photo): enforce active quota lifecycle
Parent: d0116967fc389353191c11e8865f20e38b9d5835
Vercel: dpl_FTp3iCiARWMSejRv12FcHtaMcWeR
READY / production / exact SHA
```

The current published Premium Hardening Product anchor is PA-003:

```text
7616e8ca455f1d2e72a67e4dd839c9c302f98656
fix(visit-photo): complete PA-003 premium hardening
Parent: 49abb003a9b495f3be43b4e30ea4b36dcfb8ceca
Vercel: dpl_B842NnXHecrGNhcQAfpkC8CgyVhj
READY / production / exact SHA
Alias: atlas-badge.vercel.app
```

AB-EV-066, AB-EV-067 and AB-EV-068 are the authoritative public evidence records for PA-001, PA-002 and PA-003.

---

## 4. Current Premium Hardening state

### PA-001 — Clear Map server boundary

**Status:** functionally closed / published.

Current contract:

- client Clear Map delegates protected mutation to an authenticated server route;
- Firebase Admin transaction owns server-managed `visitPhotoSlots` reset;
- transaction reads the finite 251-place canonical private domain before writes;
- historical reset/generation semantics remain intact;
- Firestore Rules are not weakened to restore client ownership.

Explicit carry-forwards:

- **AC-017:** full browser UI → client wrapper → API → Admin server → reload proof with all Firebase dependencies Emulator-backed; carried to PA-007 / Final Premium RC;
- **governance:** planned independent architecture review was skipped before original PA-001 publication. A compensating **post-publication** audit of `12a0dd53... → b0fef1d...` remains required before final Premium Hardening closure. It must never be represented as pre-publication evidence.

### PA-002 — active visit-photo quota lifecycle

**Status:** closed / independently approved / published / Production READY.

Current contract:

- free quota = maximum ten **active logical persisted photos**, not historical reservations;
- logical photo/visit removal invalidates obsolete owner/public reads immediately;
- exact server-managed allocation release uses `countryId`, `visitId`, `slotId`, `reservationId`;
- successful flow = logical mutation + durable tombstone → exact release → durable tombstone clear → awaited refresh;
- failed release leaves exact durable recovery metadata and rejects without success refresh;
- reload/reconciliation retries the exact old identity;
- stale R1 cleanup cannot delete a newer R2;
- full-quota recovery remains reachable when a pending release exists;
- tombstone clear applies to current orchestrated country state so concurrent edits/other pending work survive;
- physical Storage deletion is a separate cleanup concern and is intentionally deferred.

All original PA2-01 through PA2-14 criteria, including PA2-09B and separate OWNER/PUBLIC PA2-12 transitions, are independently PROVEN under AB-EV-067.

### PA-003 — photo Admin/server-route lifecycle and input-contract hardening

**Status:** closed / independently approved / published / Production verified.

Current contract:

- mutation ingress rejects malformed JSON, wrong types, invalid document segments and non-canonical slot values before state mutation;
- unexpected failures are sanitised and quota/auth/account outcomes remain typed/deterministic;
- failed initial upload releases only its unpersisted allocation while failed persisted replacement preserves the current photo/allocation;
- required auth is revocation-aware; reserve requires verified e-mail; inactive/missing account state is gated with the approved 404/403 read-versus-mutation semantics;
- current explicit `photoRef` and modern locator state take precedence over weaker legacy compatibility; present-invalid state fails closed;
- each new photo version receives a fresh immutable Storage key `users/{uid}/visit-photo-objects/{slotId}/{photoRef}.webp`;
- slot/reservation identity remains quota/allocation identity, not byte-version identity;
- the former mutable `visit-photo-slots/**` namespace is frozen for client writes while genuine pre-S5 records remain server-readable;
- a delayed request authorised for X may finish X (or fail safely) but can never receive later Y after replacement or slot reuse;
- malformed-present `photoObjectKey` state remains distinguishable from genuine absence through canonicalisation/save/reload so legacy fallback cannot be silently re-enabled;
- account deletion continues to clean the new immutable namespace by existing user-prefix enumeration.

All PA3-01 through PA3-15 criteria are proven. F01–F08/F10/F11 are PASS, F09 is PASS WITH OBSERVATION and F12 remains rejected as unsupported speculation. AB-EV-068 records the final qualification, publication and bounded non-destructive post-publication smoke.

### Remaining Premium work

PA-003 closure does not absorb later findings. PA-006A deterministic tooling, PA-007 Firebase namespace/safety plus PA-001 AC-017, PA-008 account-deletion origin policy, later accessibility/performance work and final Premium/Go-Live assessment remain separately controlled.

---

## 5. System-test objectives

The V1.0 cycle provides evidence that AtlasBadge can be released safely within the declared scope and limitations.

Objectives include:

- validate registration/authentication/session/account lifecycle;
- confirm correct user ownership and isolation;
- validate approved status/Wishlist combinations;
- verify reliable persistence, failed-write recovery, reload and concurrency behaviour;
- verify atomicity where one user-visible action requires all-or-nothing persistence;
- protect visits/memories/order/photo state from loss, duplication or stale cleanup;
- verify explicit per-memory privacy and sanitised public projection;
- verify current owner/public photo-reference identity independent of retained physical bytes;
- verify authorised photo identity remains bound to immutable physical bytes across replacement/slot reuse;
- confirm destructive Clear Map cannot expose a partially reset protected state;
- confirm stale public generations are not returned as current data;
- confirm canonical geographic/counter semantics;
- validate achievement criteria, chronology/reconciliation and public metadata;
- validate seven-locale presentation without turning translated copy into domain identity;
- validate account deletion/cleanup and server-owned lifecycle boundaries;
- assess declared responsive, compatibility, accessibility and performance scope;
- verify publication/deployment parity and exact source SHA;
- support a defensible Test Lead release recommendation.

---

## 6. Mandatory critical path

Release-blocking coverage includes, when affected:

- application availability;
- authentication/verification/recovery/linking/session/logout;
- owner identity and protected routes;
- travel-status/Wishlist persistence and compatibility;
- visits/memories/manual order/photo lifecycle and concurrency;
- atomic combined actions/destructive resets;
- rejected-write/release recovery;
- privacy/user isolation;
- public projection whitelisting/sanitisation;
- current/stale generation boundaries;
- current/stale visit-photo identity boundaries;
- logical-photo → physical-byte identity boundaries;
- public/private transitions;
- account deletion and Clear Map lifecycle;
- core counters/geographic model;
- achievements where affected;
- responsive/accessibility usability of critical controls;
- Vercel/Firestore Rules/Storage Rules parity where relevant;
- required independent pre-publication review;
- final candidate-byte integrity.

---

## 7. Execution layers and evidence

### 7.1 Static/build quality

Depending on affected code:

- `npx tsc --noEmit`;
- ESLint;
- `git diff --check`;
- Next.js production build;
- dependency/runtime/build-configuration validation.

Final approval must apply to the exact file state that ran the required gates. Later formatting/cleanup invalidates the relevant gate until requalified.

### 7.2 Unit/domain/component

Vitest protects stable business/component behaviour, deterministic state transitions, validation and presentation contracts.

A lower-layer helper test is accepted only when the real Product path uses that same helper/contract. PA-002 demonstrated why a helper that has no Product caller cannot substitute for actual lifecycle evidence.

### 7.3 Rules and backend Emulator

Firestore/Storage Rules and backend-focused Emulator tests cover owner-only access, schema/projection boundaries, atomic/failure behaviour, protected server/client ownership and exact lifecycle races.

Historical PA-002 final evidence recorded Firestore Rules 242/242 and its core quota/slot/route groups. The current PA-003 final gate recorded:

```text
Rules: 258 / 258 PASS, 0 skipped
Owner route unit: 23 / 23 PASS
Combined route Emulator: 45 / 45 PASS
Immutable race suite: 7 / 7 PASS
Quota lifecycle Emulator: 19 / 19 PASS
Slot-server Emulator: 18 / 18 PASS
Account-deletion visit-photo Emulator: 1 / 1 PASS
Required five-suite Emulator group: 90 / 90 PASS
```

A focused Firestore+Storage Rules smoke was also executed under the Product Emulator project `demo-atlasbadge-web` because immutable Storage CREATE authorisation consults Firestore account/allocation state. This proved active allocation allow, no-allocation deny, inactive deny, cross-user deny and same-key second-write deny in the Product configuration.

The existing Rules alias still relies on cached undeclared `tsx`; the result itself is valid but deterministic declaration remains PA-006A follow-up.

### 7.4 Browser E2E — Emulator

Normal stateful/destructive browser regression uses Firebase Emulators and must fail fast on unexpected real Firebase traffic.

The PA-002 C44 affected regression was rerun in PA-003 final qualification and recorded:

```text
Edge: 1 / 1 PASS
realFirebaseRequests=0
```

An incomplete or blocked run must not be reported as PASS.

### 7.5 Manual/exploratory QA

Used for usability, rendered-browser behaviour, native-browser/device differences, edge interaction sequences and Test Lead sign-off.

Manual evidence must use the actual intended browser/session/environment. A scripted isolated browser session cannot be misrepresented as the Test Lead's manual session.

### 7.6 Production validation

Production validation is proportional and separately authorised.

Production deployment identity may be proven by Git/Vercel SHA metadata. Destructive/authenticated Production data mutation is not required merely because a deployment is READY when equivalent Product behaviour has already been qualified under controlled Emulator/local gates and a real-Production mutation would add avoidable risk.

PA-003 additionally used a bounded read-only/non-mutating post-publication smoke: `/`→`/pt-br`, static assets, expected unauthenticated/malformed owner/public photo API failures and a bounded deployment-log error/fatal query. Zero intentional Production Firebase mutation occurred. This proves deployment/runtime availability and non-disclosure at safe rejection paths, not a Production replay of PA3-15 races.

No claim of broader functional Production execution may be inferred solely from Vercel READY or the bounded smoke.

---

## 8. Checkpointed incremental regression

### Principle

A green checkpoint remains valid unless a later change directly invalidates what it proved.

Before a broad rerun, identify:

1. the previous checkpoint;
2. the later change;
3. exactly what evidence became stale;
4. the smallest layer that can restore confidence.

Examples:

- E2E-only stale-oracle cleanup does not invalidate unrelated backend/build checkpoints;
- a persistence change invalidates affected persistence/recovery evidence;
- a Rules change invalidates Rules/release-parity evidence;
- a responsive CSS change invalidates affected rendered-browser evidence;
- a server ownership change invalidates authentication/authorisation/lifecycle evidence around that boundary;
- a physical-locator change invalidates the affected read/upload/race evidence;
- a test file edit after a claimed final gate requires the affected test to be rerun against the final file state.

Carried-forward results and the reason they remain valid must be explicit.

---

## 9. Mandatory AI-agent preflight and stop gate

LL-66 and `GOVERNANCE-ERRATA.md` are standing controls.

Before delegated Product implementation:

| Gate | Required evidence |
|---|---|
| Actor routing | Classify the task as Product implementation, QA documentation, read-only audit, Test Lead decision or mixed. Product source/test/config changes route to Antigravity/Gemini by default; QA Portfolio documentation remains coordinator-owned. |
| Scope/cost | One finishable objective, explicit exclusions, bounded diagnostic budget and STOP condition. |
| Repository | Exact Product HEAD/origin, dirty/untracked paths, protected work and forbidden destructive Git operations. |
| Safety | Effective origin, Firebase project, Emulator endpoints and real-request fail-fast. |
| Test contract | Current approved requirement/oracle; identify stale tests/fixtures separately from Product defects. |
| Checkpoint economy | Name the previous PASS and its invalidating change; select the cheapest adequate layer. |
| Exit | Exact PASS/FAIL/SKIPPED, final-file state, counts/exit codes and missing assertions; unresolved diagnosis/environment/evidence remains BLOCKED. |
| Publication | No commit/push/deploy/Portfolio mutation until the approved review/Test Lead gate explicitly authorises it. |

Default debugging budget is one focused reproduction plus at most one evidence-based retry before returning evidence for a new scope decision.

---

## 10. Defect, oracle, fixture and process classification

A failed scenario is classified before correction as:

- Product Defect;
- stale/test-oracle defect;
- test-data/fixture defect;
- environment/infrastructure issue;
- release-control block;
- governance/process deviation;
- inconclusive.

A later requirement correction is not counted as a Product Defect when implementation correctly matched the previously approved rule.

A green test can still protect the wrong requirement. A failing test can also be wrong when runtime correctly implements the current requirement. The Product/test owner of truth must be established before editing either side.

PA-001 provides the current test-oracle example: three route assertions expected mojibake while runtime correctly returned UTF-8. The isolated repair changed only the test oracle and received independent review; no runtime defect was invented.

PA-002 provides the acceptance-inventory example: original PA2-06/PA2-07 coverage was temporarily lost even though aggregate suites remained green. Final approval required restoration of the original named oracles.

PA-003 provides a fail-closed compatibility example: the first immutable-byte implementation closed the core F08 race, but independent review still rejected S5 because a present malformed private locator could fall through to legacy resolution and canonicalisation could erase malformed-present state into absence after save/reload. S5-R01 corrected the Product boundary; the green race evidence did not substitute for those separate identity/persistence oracles.

---

## 11. Repository and final-byte quality gate

Before final independent approval/publication:

- inventory tracked, modified and **untracked** candidate files;
- remove/segregate scratch/debug artefacts;
- check secrets/credentials;
- check accidental Production/test configuration changes;
- remove stale `.only`, unapproved skips and debug logging;
- run `git diff --check`;
- freeze and hash the exact candidate files;
- execute final required gates without later source/test/rules edits;
- re-hash after qualification;
- compare ESLint warning identity when totals change, rather than accepting only the count.

If an interruption/workstation failure occurs while untracked candidate files exist, preserve a complete byte-for-byte external snapshot before attempting recovery. `git diff` alone is not a backup for untracked files.

PA-002 proved approved working bytes → staged bytes → committed bytes → GitHub main → Vercel Production SHA. PA-003 repeated and extended that chain:

```text
approved S6 source bytes
=
staged PA-003 bytes
=
7616e8ca455f1d2e72a67e4dd839c9c302f98656
=
origin/main exact commit
=
dpl_B842NnXHecrGNhcQAfpkC8CgyVhj Production source SHA
```

The post-publication review then confirmed all **444 tracked/nonignored files** remained hash-identical throughout the read-only verification.

---

## 12. Independent review and publication gate

When the finding's approved workflow requires independent review:

```text
implementation candidate
→ focused remediation/qualification
→ exact final byte freeze
→ independent Codex review
→ Test Lead publication decision
→ controlled commit
→ controlled push
→ exact remote SHA verification
→ automatic Vercel deployment verification
→ proportional post-publication verification
→ documentation reconciliation
```

Do not reorder this sequence for convenience.

PA-001 contains one known governance deviation: publication occurred before the planned independent architecture review. The Product state remains accepted, but a compensating **post-publication** audit is mandatory before final Premium closure and must not be relabelled as pre-publication review.

PA-002 followed the corrected sequence and received Codex **APPROVE** on exact frozen bytes before Test Lead publication authorisation.

PA-003 followed the corrected sequence throughout: initial audit → approved slice matrix → per-slice independent reviews → dedicated S5 architecture design gate before implementation → S5-R01 correction/re-review → S6 cumulative final qualification → Test Lead approval → clean commit/push → exact Vercel deployment verification → read-only post-publication smoke → documentation reconciliation.

---

## 13. Release/deployment gate

For source-only releases:

1. verify approved base/remote has not moved;
2. verify approved candidate hashes;
3. stage only approved paths;
4. verify staged bytes;
5. create the approved commit;
6. verify committed bytes/parent/subject/scope;
7. fetch remote again;
8. push normally without force/rebase shortcuts;
9. verify `origin/main` / remote SHA;
10. verify automatic Vercel Production deployment is `READY` on the exact commit SHA.

For releases that also require separately deployed Firestore/Storage Rules, source/deployment and Rules parity must be understood/restored before any Production functional smoke that depends on the changed Rules.

No force push, merge/rebase shortcut or unrelated cleanup is used to make publication look clean.

---

## 14. Evidence and traceability

Material system-test evidence must connect:

```text
Requirement / rule
→ Quality risk
→ Implementation
→ Automated/manual tests
→ Defect / stale oracle / fixture issue / governance event / requirement correction, if applicable
→ Retest / regression
→ Independent review when required
→ Commit
→ Deployment
→ Production result or explicit non-execution limitation
→ Test Lead decision
```

The central public index is `evidence/v1.0/evidence-register.md`.

Current Premium evidence:

- AB-EV-065 — Node 24 final technical RC anchor;
- AB-EV-066 — PA-001 Clear Map server-boundary hardening + UTF-8 test-oracle repair + governance carry-forward;
- AB-EV-067 — PA-002 active visit-photo quota lifecycle hardening and Production publication;
- AB-EV-068 — PA-003 photo Admin/server-route hardening, immutable byte identity, final qualification, exact-SHA publication and post-publication Production verification.

Historical C31–C45N execution details remain in AB-EV-029 through AB-EV-060 and are not duplicated here.

---

## 15. Exit and release assessment

An increment/finding may be approved when:

- required acceptance criteria and negative/race/recovery scenarios pass;
- no unacceptable Critical/High Product Defect remains;
- privacy/security/data-integrity boundaries are acceptable;
- required repository/static/build gates pass;
- required independent review is complete;
- exact candidate-byte integrity is established;
- source/deployment/Rules parity is proven where relevant;
- required Production validation is complete or its non-execution is explicitly accepted;
- residual risks/carry-forwards are documented.

The official V1.0 final release assessment additionally requires completion/acceptance of remaining mandatory Premium Hardening scope, final regression, residual-risk consolidation, Production clean-start/reset activities where applicable and explicit Test Lead Go/No-Go.

The final release decision belongs to the Test Lead/Product Owner.

---

## 16. Current limitations and carry-forwards

- QR-01 is a Regression risk, not a Current gap.
- AB-DEF-017/018 and historical C45 defects are closed; their corrected behaviour remains regression scope.
- Seven-locale localization is closed under AB-EV-060 and remains regression scope.
- AB-EV-065 is the technical RC anchor but does not by itself equal final Go Live.
- PA-001 is functionally closed/published; AC-017 remains for PA-007 / Final Premium RC.
- PA-001 compensating **post-publication** architecture audit remains open as governance closure work.
- PA-002 is closed/published/Production READY; its active-logical quota and exact retry contract remain regression scope.
- PA-003 is closed/published/Production verified under AB-EV-068.
- Physical immutable visit-photo object GC remains deferred; logical access, quota correctness and immutable-byte correctness do not depend on immediate deletion.
- Old clients intentionally cannot write the frozen `visit-photo-slots/**` namespace; rollback must not re-enable mutable slot writes.
- Authorisation-snapshot semantics remain accepted: an already-authorised immutable X may finish X after later logical invalidation, while fresh stale-X requests are denied.
- Malformed private immutable locators remain nominally string-typed in the TypeScript domain model, but current runtime consumers validate type/canonical equality and fail closed.
- No Production race test is claimed; PA3-15 is qualified deterministically under controlled local/Emulator boundaries.
- PA-006A retains the undeclared/cached `tsx` deterministic-tooling concern.
- PA-007 retains Firebase namespace/safety plus PA-001 AC-017.
- PA-008 retains the account-deletion origin allowlist follow-up.
- Browser/device coverage is not comprehensive.
- Quantitative performance targets are not established.
- Formal accessibility certification/native assistive-technology coverage is not claimed.
- No independent penetration/security audit or formal load test has been completed.
- The root-layout request-time rendering choice remains technical debt pending any later routing redesign.
- The legacy UK-selector modal remains an unreachable-code cleanup candidate, not a Product Defect.
- The unchanged legacy `travelMap.clearMap.emulator.test.ts` fixture debt remains separate from the authoritative Clear Map 11/11 server and 5/5 client regression that passed PA-003 S6.

---

## 17. Post-PA-003 service entry contract

PA-003 is no longer an active implementation service. New Premium work begins from the published PA-003 Product anchor and must preserve:

- PA2 active logical quota, exact release/recovery identity and stale-release safety;
- PA3 strict mutation/input/error contracts;
- revocation/account lifecycle gates;
- explicit current-reference precedence and fail-closed present-invalid state;
- immutable `photoRef`→Storage-object binding for new photo versions;
- client-write freeze of the old mutable slot namespace;
- current owner/public logical authorisation independent of retained physical bytes;
- account deletion coverage for `visit-photo-objects/**`;
- acceptance-oracle inventory and exact candidate-byte publication controls.

Later findings such as PA-006A, PA-007 and PA-008 remain separately scoped. No later service may silently reopen or weaken PA-003 merely to simplify another implementation.

---

## 18. Related evidence

- `evidence/v1.0/evidence-register.md`
- `evidence/v1.0/regression/ab-ev-044-c44-registered-visit-photo-production-closure.md`
- `evidence/v1.0/regression/ab-ev-052-c45g-deep-country-visit-editor-localization.md`
- `evidence/v1.0/regression/ab-ev-058-c45m-authenticated-language-selector-and-production-hotfix.md`
- `evidence/v1.0/regression/ab-ev-059-c45n-german-localization-extension.md`
- `evidence/v1.0/regression/ab-ev-060-c45n-final-localization-closure-and-ab-def-022.md`
- `evidence/v1.0/regression/ab-ev-061-v1-release-readiness-audit.md`
- `evidence/v1.0/regression/ab-ev-062-final-v1-release-candidate-regression-charter.md`
- `evidence/v1.0/regression/ab-ev-063-rc-lockfile-reproducibility-recovery.md`
- `evidence/v1.0/regression/ab-ev-064-c46-node24-lts-upgrade-charter.md`
- `evidence/v1.0/regression/ab-ev-065-node24-final-rc-qualification-and-production-deployment.md`
- `evidence/v1.0/regression/ab-ev-066-pa001-clear-map-server-boundary-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-067-pa002-active-visit-photo-quota-lifecycle-premium-hardening.md`
- `evidence/v1.0/regression/ab-ev-068-pa003-photo-admin-server-route-hardening.md`
- `docs/10-lessons-learned.md`
- `GOVERNANCE-ERRATA.md`