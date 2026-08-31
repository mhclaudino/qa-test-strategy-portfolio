# AtlasBadge Lessons Learned

## 1. Purpose

This document records reusable lessons learned from AtlasBadge V1.0 product development and quality validation.

It is deliberately not a diary of every defect, test run or implementation decision. A lesson is included only when it changes how future work should be planned, implemented, tested, evidenced or released.

The emphasis is on reducing avoidable work while preserving risk-based confidence and traceability.

> **Document status:** Active / maintained. Consolidated through AB-EV-047 / C45C.

---

## 2. How lessons are applied

Each lesson is expressed as:

- **Observation** — what repeatedly caused risk, rework or wasted effort;
- **Working rule** — the process decision adopted for future work;
- **Benefit** — why the rule improves quality or efficiency.

Lessons do not replace the Test Strategy, Quality Risk Analysis or System Test Plan. They explain why specific working rules exist and provide a feedback loop for continuous improvement.

---

## 3. Avoiding unnecessary validation effort

### LL-01 — Do not rerun a green checkpoint unless a later change invalidated what it proved

**Observation:** AtlasBadge accumulated strong unit, Rules, browser, build and Production checkpoints. Re-running every suite after every change created unnecessary execution time and occasionally generated unrelated noise.

**Working rule:** Before re-executing a checkpoint, identify the later change that invalidated the previous result. If no relevant dependency changed, carry the result forward and record the reason.

**Benefit:** Less repetitive testing without lowering confidence.

### LL-02 — State the invalidated checkpoint before selecting the next test

**Observation:** “Run everything to be safe” is not a risk-based test strategy.

**Working rule:** Impact analysis precedes execution. State what changed, which evidence became stale and which smallest test can restore confidence.

**Benefit:** Test effort remains proportional to actual risk.

### LL-03 — Use the smallest test layer that can prove the changed behaviour

**Observation:** Unit/domain tests are faster and more deterministic for business rules, while E2E is valuable only when browser/integration behaviour must be demonstrated.

**Working rule:** Prefer unit/domain coverage for rule matrices; add integration/E2E only for boundaries that cannot be proven below that layer. Do not duplicate the entire unit matrix in Playwright.

**Benefit:** Faster feedback, easier diagnosis and lower maintenance cost.

### LL-04 — A small correction must not automatically become a full regression campaign

**Observation:** C35 changed one central status rule and required focused domain coverage plus one browser persistence path, not a new full Playwright campaign.

**Working rule:** Expand regression only when static impact analysis or focused failures show broader invalidation.

**Benefit:** More time is spent investigating meaningful risk rather than exercising unrelated functionality.

### LL-05 — Run formatting and auto-fixes before final quality gates

**Observation:** `eslint --fix`, whitespace cleanup or other file changes performed after a PASS make the final artefact different from the one that was validated, even when semantics did not change.

**Working rule:** Edit → format/fix → test → final static gates → audit. Any later file modification must be explicitly classified for checkpoint impact.

**Benefit:** Avoids unnecessary micro-gates and ambiguity over which file version actually passed.

### LL-06 — Do not treat an empty diff as evidence for an untracked file

**Observation:** `git diff` does not prove the content of a file that is not yet tracked.

**Working rule:** Inspect untracked files directly or compare them read-only against an empty baseline. Never report an empty tracked diff as proof that an untracked test is clean.

**Benefit:** Prevents false pre-commit confidence.

---

## 4. Requirement and product-definition lessons

### LL-07 — Distinguish Product Defect from Requirement Correction

**Observation:** C35 was implemented according to the original product rule, but the rule itself was later recognised as conceptually wrong.

**Working rule:** When implementation matches the approved requirement, classify a later business-rule change as a requirement/scope correction rather than inflating defect counts.

**Benefit:** Defect metrics remain meaningful and engineering accountability remains fair.

### LL-08 — Validate temporal user journeys, not only static status matrices

**Observation:** The original `Passed through` versus `Visited` exclusivity looked plausible as a static matrix but failed real travel-history sequences such as airport connection → later visit → later connection.

**Working rule:** For historical/cumulative states, requirements must be challenged with realistic event sequences and reverse transitions before exclusivity rules are accepted.

**Benefit:** Product rules better match real user behaviour.

### LL-09 — Separate long-lived status from individual occurrence/history

**Observation:** A travel status describes accumulated history for a place; `RegisteredVisit` describes individual occurrences. Treating a second compatible status as a new occurrence would create artificial visits and incorrect counters.

**Working rule:** Status changes must not create new `RegisteredVisit` records unless the user actually records another occurrence.

**Benefit:** Counters, memories and chronology remain semantically correct.

### LL-10 — Reuse an adequate model before creating schema or migration work

**Observation:** C35 could have been over-designed with per-visit status types, new collections or migrations. Existing statuses plus `RegisteredVisit` already represented the requirement.

**Working rule:** Before adding fields, collections or migrations, test whether the existing domain model can express the rule without ambiguity.

**Benefit:** Smaller implementation, lower migration risk and less regression surface.

---

## 5. Automation and evidence lessons

### LL-11 — A green automated test can protect the wrong requirement

**Observation:** QR24-UT-11/12 correctly enforced the old mutual-exclusion rule and therefore became stale when the requirement changed.

**Working rule:** When a requirement changes, classify affected failures as stale automation before modifying product behaviour. A green test is evidence only for the specification encoded in it.

**Benefit:** Automation supports product truth instead of becoming an obstacle to legitimate change.

### LL-12 — Never weaken a product criterion merely to make automation pass

**Observation:** AtlasBadge encountered stale locators, protocol assumptions, localhost assumptions and incomplete fixtures during release hardening.

**Working rule:** Correct the test when the automation is wrong; correct the product when the product is wrong. Do not lower the acceptance criterion to satisfy a stale script.

**Benefit:** Test suites retain credibility.

### LL-13 — E2E should prove integration boundaries, not reproduce every unit assertion

**Observation:** C35 required one browser scenario proving UI → persistence → reload and independent deselection. The rule matrix remained better protected by domain tests.

**Working rule:** E2E scenarios must demonstrate high-value cross-layer behaviour and avoid duplicating exhaustive lower-layer permutations.

**Benefit:** Lower E2E execution time and maintenance cost with stronger signal.

### LL-14 — Persistence evidence must distinguish optimistic state from confirmed state

**Observation:** Several AtlasBadge persistence investigations showed that an interface can look correct before Firestore confirms the write.

**Working rule:** High-risk persistence tests must identify what is optimistic, what is confirmed, when cache is updated and what survives reload/relogin.

**Benefit:** Prevents false confidence from transient UI state.

### LL-15 — Failed-write coverage must prove recovery, not merely an exception

**Observation:** QR-01 was not closed by proving that a Promise could reject. Closure required evidence that the UI rolled back/reconciled, confirmed cache remained clean, reload did not resurrect rejected state and retry remained safe.

**Working rule:** Failed-write acceptance criteria include failure visibility/reconciliation, confirmed-state integrity, reload behaviour and retry/idempotency where relevant.

**Benefit:** Reduces silent data-loss risk.

### LL-16 — Emulator isolation must be observable

**Observation:** Browser tests intended for Firebase Emulators previously risked environment ambiguity.

**Working rule:** Emulator E2E must fail fast on unexpected real Firebase traffic and report the effective backend. `realFirebaseRequests=0` is retained as useful evidence when available.

**Benefit:** Destructive/failure scenarios remain controlled and repeatable.

### LL-19 — A helper-level ordering test is not enough when the order originates in interactive UI state

**Observation:** C36 helper tests proved that a supplied `wishlistOrder` could be persisted correctly, but the Test Lead still found that a real reorder + privacy Save lost the displayed order. The missing coverage was the boundary where drag state became the Save payload and later returned through the read path.

**Working rule:** For ordering features, at least one test must connect **reorder interaction/state → persistence payload → confirmed read/reload/render order**. Lower-level helpers may cover permutations, but they cannot replace the state-production boundary.

**Benefit:** Prevents green automation from missing an integration error in the actual user journey.

---

## 6. Architecture, release and environment lessons

### LL-17 — Judge atomicity by real commit boundaries, not by the UI action

**Observation:** One Save button or one `Promise.all()` can still represent multiple independent Firestore commits. A visually single action is not automatically atomic. AB-DEF-017 proved that Wishlist order could commit while privacy failed.

**Working rule:** Data-consistency analysis must identify the actual transaction/batch boundaries and prove failure behaviour at the backend boundary when atomicity matters.

**Benefit:** More accurate risk analysis and better targeted fault-injection tests.

### LL-18 — Publish evidence after the technical result is stable, while preserving historical records

**Observation:** Updating portfolio documents during investigation created avoidable rework, while rewriting old evidence would erase the decision context that existed at the time.

**Working rule:** Investigation → implementation → focused validation → affected regression → Test Lead decision → commit/push → deployment/Production evidence → documentation. Historical evidence remains unchanged; later evidence records the new state.

**Benefit:** Clear chronology, less documentation churn and defensible traceability.

### LL-20 — Quantify the supported maximum before accepting an “atomic batch” redesign

**Observation:** The first obvious AB-DEF-017 remediation was to put the existing per-place order writes into one batch. At the supported maximum, 251 private + 251 public rank writes would require 502 writes and exceed Firestore's 500-write batch limit.

**Working rule:** Before accepting a batch/transaction redesign, calculate the maximum write/read cardinality using the product's supported maximum state. Fix write amplification instead of merely relocating it.

**Benefit:** Prevents a local fix from preserving or creating a deterministic scale limit.

### LL-21 — Verify application/Rules parity before investigating Firestore permission failures

**Observation:** C36 local manual QA initially produced `Missing or insufficient permissions`. The localhost application was using real Firebase with the newly changed application model while Production still enforced the pre-C36 Rules whitelist.

**Working rule:** A Firestore permission failure is not classified as a Product Defect until the effective Firebase backend and Rules version are identified. When application and Rules change together, QA must run in an environment where both revisions are aligned.

**Benefit:** Avoids wasted defect investigation and unnecessary code changes caused by release/environment mismatch.

### LL-22 — “localhost” does not mean “local Firebase”

**Observation:** AtlasBadge intentionally allows local development to use real Firebase. During C36, the browser URL alone created a false assumption about the backend actually evaluating writes.

**Working rule:** Environment evidence must state application origin, Firebase project, Emulator connectivity and effective Rules source separately. Never infer backend isolation from `localhost`.

**Benefit:** Makes environment classification explicit and prevents accidental Production interaction assumptions.

### LL-23 — An Emulator environment is not ready until backend, identity and browser session are ready

**Observation:** Switching to the Auth/Firestore Emulators removed access to the previously authenticated real-Firebase account. Starting services alone did not provide a reusable manual-QA identity or an authenticated Test Lead browser session.

**Working rule:** Manual Emulator readiness has three separate gates:

```text
Emulators running
+
required QA identity/state available
+
Test Lead browser authenticated on the canonical application origin
```

All three must be true before the environment is called “ready”.

**Benefit:** Stops repeated environment-preparation work from consuming functional QA time.

### LL-24 — A scripted/Playwright browser session is not the Test Lead's browser session

**Observation:** A temporary script authenticated an isolated browser context and reported the manual-QA environment as authenticated even though the Test Lead's existing browser was still logged out. `localhost` and `127.0.0.1` also behaved as distinct browser origins for persistence.

**Working rule:** Manual QA uses one canonical application origin and readiness must be verified in the actual Test Lead browser context. Automated browser authentication cannot be reported as manual-session authentication.

**Benefit:** Prevents false environment-ready reports and origin/session churn.

### LL-25 — A source-of-truth change requires write-path, read-path and in-memory-state review

**Observation:** C36 correctly moved Wishlist ordering from per-place `wishlistOrderRank` to root `wishlistOrder`, but the owner modal initially continued to render legacy/alphabetical order. After that was corrected, an order-only Save still required an explicit Profile refresh because `AuthContext.profile` was not a live root-document subscription.

**Working rule:** Whenever a source of truth moves, inspect all three paths:

1. **write** — what persists the new value;
2. **read/render** — which consumers select it over legacy fallback;
3. **confirmed in-memory state** — how the current session receives the committed value.

**Benefit:** Prevents a backend-correct migration from remaining functionally wrong in the UI.

### LL-26 — For destructive workflows, atomic logical invalidation can be more important than immediate physical deletion

**Observation:** AB-DEF-018 could not be solved safely by placing 251 private deletes, 251 public deletes and two root updates into one Firestore batch because the full operation would require 504 writes. Sequential chunks would still permit partial persistence.

**Working rule:** Separate the state that must change atomically for user-visible/security correctness from physical garbage collection. In C37, one <=253-write atomic batch clears private state and advances a public projection generation; stale public documents become unreadable/inactive immediately and may be physically removed later without deciding whether Clear Map succeeded.

**Benefit:** Preserves all-or-nothing destructive semantics without exceeding backend limits or weakening the product contract.

### LL-27 — A destructive-operation failure must be visible, not merely leave the confirmation UI open

**Observation:** When Clear Map persistence was rejected, the confirmation modal remained open and the page retained an internal error that was not passed to the modal. The behaviour looked frozen even though the backend had correctly refused the operation.

**Working rule:** Destructive flows must distinguish success and failure explicitly: success closes/reconciles the confirmed state; failure must not pretend success and must expose actionable error feedback. A permanently open confirmation surface without visible failure information is not sufficient recovery UX.

**Benefit:** Users can distinguish a rejected destructive action from an unresponsive interface, and QA can diagnose backend failure without ambiguous UI behaviour.

### LL-28 — Do not turn an isolated automated test environment into a mandatory manual QA environment

**Observation:** While automated Emulator E2E is vital for isolation, earlier troubleshooting assumed routine manual QA should also depend on restoring an Emulator identity and browser state. This created unnecessary operational friction because the local real-backend workflow was already safe.

**Working rule:** For manual QA, use the environment proportional to the task.
- identify application origin and backend independently;
- prove the actual backend before reporting environment readiness;
- distinguish service readiness from browser-session readiness;
- reuse the existing real QA account for routine manual QA;
- do not create Emulator identity/bootstrap work without a scenario that actually requires Emulator;
- keep deterministic Emulator coverage for Rules, fault injection and isolation.

**Benefit:** Removes friction from routine Test Lead work while preserving safe isolation where actually needed.

### LL-29 — A public surface must use the public projection even when the owner is the viewer

**Observation:** C41 initially worked for anonymous viewers but not for the owner viewing their own public Profile because owner-specific page state used private place objects that did not contain `publicMemories`. An intermediate fallback rebuilt the public projection in the browser from private data.

**Working rule:** Public-facing content uses the canonical sanitised public projection for every viewer role. Owner-only data may support owner-specific controls, but it must not become an alternate source of truth for content presented as public.

**Benefit:** Prevents privacy drift and different public behaviour for owner versus visitor.

### LL-30 — Diagnose stale public projections before adding migrations or compensating code

**Observation:** After C41 added exact `VISITA n` labels, the existing Brazil public document still showed the legacy generic label because it had not yet been rewritten with the new projection shape.

**Working rule:** When code and current projection logic are correct but a persisted public document lacks a backward-compatible new field, inspect the stored projection first. Prefer the normal product persistence path to rebuild test data before introducing migrations, admin edits or client-side reconstruction.

**Benefit:** Avoids unnecessary code and distinguishes legacy test-data state from product defects.

### LL-31 — When a requirement names an existing UI pattern, inspect and reuse that pattern before designing

**Observation:** C41 first rendered an inline panel even though the intended interaction was the established Wishlist modal family. A later Profile sort-toggle correction matched compact styling but initially missed the reference header placement and spacing.

**Working rule:** If the acceptance criterion says “same as” an existing AtlasBadge control/surface, inspect the actual reference component/layout before editing. Validate placement, spacing, responsive behaviour and interaction—not only internal colours/classes.

**Benefit:** Reduces avoidable visual rework and preserves interface consistency.

### LL-32 — Reject integration commits that contain unrelated file churn

**Observation:** During C34, an intermediate implementation commit contained unrelated mass edits to existing E2E files plus an accidental planning artefact. The behavioural correction could not be treated as a clean release baseline until those changes were removed.

**Working rule:** Before approval, audit the exact staged/committed file scope. Unrelated test rewrites, scratch plans and accidental artefacts are removed or isolated before a release baseline is accepted.

**Benefit:** Keeps traceability credible and reduces hidden regression risk.

### LL-33 — A concurrency shortcut must not bypass a stronger domain invariant

**Observation:** C34 rapid-visit work considered a blanket concurrency bypass that would also have skipped the transaction maintaining `users/{uid}.birthplacePlaceId` with `statuses.born`.

**Working rule:** Concurrency optimisation is intent-aware. A safe replay/bypass path may be used only when it does not bypass a transaction or Rules invariant required by the semantic intent.

**Benefit:** Fixes lost-update/concurrency problems without trading them for cross-document corruption.

### LL-34 — Presentation order metadata must not manufacture domain history

**Observation:** C34 needed user-correctable travel presentation order even when chronology was incomplete. The correct solution was `visitOrderRank`, not fabricated visits, timestamps or statuses.

**Working rule:** When the user is correcting presentation order, persist presentation metadata. Do not mutate the domain/history model solely to make sorting easier.

**Benefit:** Preserves historical truth and keeps ordering concerns independent from visit semantics.

### LL-35 — Do not create increment-specific control documents under docs

**Observation:** C31–C34 traceability was previously split into standalone `docs/10...` and `docs/11...` files, and a later C39–C41 increment briefly repeated the same mistake.

**Working rule:** The `docs/` directory is the fixed living control set `01`–`10`. Product, risk, strategy, scope, plan and lessons changes are merged into those existing files. Increment-specific public traceability is added only under `evidence/`, unless the Test Lead explicitly approves a new control document.

**Benefit:** Prevents document sprawl, duplicate sources of truth and repeated maintenance.

### LL-36 — Visual affordance is a rendered-browser acceptance criterion, not a class-name assertion

**Observation:** During C42, JSDOM correctly reported focus on the visit-name input while the real interaction still looked non-functional to the Test Lead. Later, hover utility classes were present in the DOM but did not produce the intended perceptible result in the browser. A technically stronger neon treatment then over-corrected the problem and was rejected as inconsistent with the premium visual language.

**Working rule:** Unit/component tests verify semantics, state transitions and accessibility hooks; they do not prove visual perceptibility. For important micro-interactions, validate the rendered browser result and prefer the smallest visual change that makes the control understandable. Do not equate class presence, document.activeElement or a computed event state with acceptable product UX.

**Benefit:** Prevents false confidence from headless assertions and avoids oscillating from invisible affordance to visually excessive correction.

### LL-37 — Brand cleanup must classify colour semantics before replacement

**Observation:** C43 found the same neon tokens serving very different roles: some were generic UI focus/CTA styling that conflicted with the AtlasBadge brand, while others represented valid travel-status or geographic/data-visualisation semantics. A mechanical goal such as “zero purple” would have damaged product meaning.

**Working rule:** Before a broad visual-token cleanup, classify each occurrence as brand, status, feedback, data visualisation, external brand, neutral or legacy/inconsistent. Replace only the inconsistent structural usage. Zero grep-count is not an acceptance criterion when a remaining colour has a valid semantic role.

**Benefit:** Improves visual consistency without erasing functional meaning, accessibility cues or legitimate third-party/data-visualisation identity.
---

### LL-38 — Size AI-agent work to a finishable checkpoint, not to the entire remaining release

**Observation:** C44 repeatedly bundled investigation, architecture review, regression, release, Vercel verification and Production smoke into one prompt. Strong models exhausted their available usage before reaching a stable stopping point, while weaker/free models were asked to continue work that exceeded their practical capacity.

**Working rule:** One agent task should have one bounded objective and an explicit stop condition. Before assigning work, consider the available model capability/usage window and select a task that can realistically finish within it. Split release work into independent gates such as diagnose → focused fix → commit → deploy → Production smoke → documentation. Do not give a low-capacity model a multi-stage task merely because the stages are logically related.

**Benefit:** Preserves scarce model capacity, produces complete checkpoints instead of half-finished sessions and reduces repeated context reconstruction.

### LL-39 — Local configuration and Production configuration are separate evidence

**Observation:** During C44, missing local `FIREBASE_ADMIN_*` configuration was incorrectly treated as evidence that the same Vercel Production variables also needed configuration, even though Production had already been configured earlier.

**Working rule:** Report each environment independently. A local SET/MISSING result says nothing about Vercel unless Vercel is actually inspected. Never recreate or rotate a remote secret because the equivalent local variable is missing.

**Benefit:** Avoids unnecessary secret changes, configuration drift and wasted release work.

### LL-40 — Emulator PASS does not prove real Admin/Storage/runtime integration

**Observation:** C44 passed Emulator/Rules coverage while the real owner/public photo path still failed because the local Node 24 Google Cloud Storage OAuth transport returned `ERR_STREAM_PREMATURE_CLOSE`. Firebase Admin Auth/Firestore and client uploads could succeed while Admin Storage failed.

**Working rule:** When a release depends on a real external/server boundary not faithfully reproduced by the Emulator, add a small real-backend qualification for that boundary before release. Record runtime/dependency version when it is causally relevant.

**Benefit:** Prevents false confidence from a green isolated environment when the released integration path is different.

### LL-41 — Prove runtime compatibility with an A/B test before pinning or upgrading

**Observation:** Node 24 was a strong suspect for the C44 Admin Storage failure, but the safe decision required proof. A temporary Node 22 run showed Node 24 FAIL and Node 22 PASS on the same Storage/OAuth path.

**Working rule:** Do not change runtime or dependencies on speculation. Reproduce the same request under controlled runtime A/B conditions, then apply the smallest pin/upgrade supported by evidence.

**Benefit:** Keeps runtime changes evidence-based and avoids introducing unrelated dependency churn.

### LL-42 — Materialised public projections need an end-to-end rewrite/read check after source-model changes

**Observation:** C44's bounded-slot model stored the opaque public identifier in `visit.photoRef`, but the public projection still derived it from the old UUID filename. Helper and owner flows passed while the Profile rendered no image and made no `/api/visit-photo` request.

**Working rule:** When a materialised projection schema/source changes, test private source → projection write → stored public document → public render/network request. Do not stop at a projection helper assertion, and use absence of the expected downstream request as a diagnostic signal.

**Benefit:** Catches projection fields that are technically implemented but never reach the consumer.

### LL-43 — Client edit helpers must patch editable fields, not replay stale full documents

**Observation:** After C44 introduced server-managed `visitPhotoSlots`, Edit Profile reused a client-held full profile object. Saving/removing the avatar re-submitted the protected field and Firestore correctly denied the write.

**Working rule:** For mixed client/server-owned documents, construct an explicit editable-field patch. Server-managed fields are preserved by omission, not copied from client snapshots. Deletion of optional fields should use the approved deletion semantic rather than relying on empty placeholders when the schema expects absence.

**Benefit:** Prevents unrelated profile edits from conflicting with new protected backend metadata.

### LL-44 — Conditional public fields must match the Rules contract, not just the whitelist

**Observation:** `wishlistOrder` was on the approved public-profile whitelist but Firestore Rules allowed it only when `isWishlistPublic == true`. A private Wishlist therefore caused an unrelated profile save to fail until the projection omitted the conditional field.

**Working rule:** A public projection whitelist is necessary but not sufficient. For every conditional public field, the projection predicate must match the Rules predicate and be covered by both public/private cases.

**Benefit:** Prevents permission failures and accidental disclosure from a field that is globally approved but conditionally private.

### LL-45 — Cost limits should be structurally enforceable at the storage boundary

**Observation:** A simple counter could limit UI behaviour but would not bound physical Storage exposure under concurrency or bypass. C44 instead uses ten stable slot IDs with two A/B replacement variants and Rules that reject arbitrary visit-photo paths.

**Working rule:** When a quota exists to control infrastructure cost, enforce the upper bound in authoritative backend/storage structure as well as UI. Test the maximum, concurrent edge and release-on-remove behaviour.

**Benefit:** Converts a commercial/cost assumption into a technically enforceable limit.

### LL-46 — Audit dynamic route namespaces before adding localisation segments

**Observation:** C45A found that AtlasBadge already used the root-level `[username]` route. Introducing a sibling root `[locale]` segment would have created competing route ownership and risked breaking established public-profile links.

**Working rule:** Before introducing locale, tenant, slug or other dynamic route namespaces, inventory existing sibling dynamic routes and prove deterministic route ownership. Prefer explicit static discriminators with shared implementations when they preserve current public contracts with less migration risk.

**Benefit:** Prevents localisation infrastructure from breaking existing public URLs and reduces unnecessary routing migrations.

### LL-47 — Request-time APIs in a root layout are architecture-wide rendering decisions

**Observation:** C45B needed server-correct `<html lang>` for six explicit localized Home routes. Reading `headers()` in the shared root layout solved that semantic requirement but changed the whole page tree from prerendered/static eligibility to request-time dynamic rendering. A technically cleaner multiple-root alternative existed, but required a disproportionate route-group migration and cross-root navigation cost for the current checkpoint.

**Working rule:** Before adding a request-time API such as `headers()` or `cookies()` to a shared/root layout, inspect the build classification and treat the change as an architecture/performance decision, not a local implementation detail. Prefer scoped/static solutions when proportional; if the correct alternative requires a broad migration, record the dynamic-rendering trade-off explicitly as technical debt and validate the affected performance risk separately.

**Benefit:** Prevents a small semantic requirement from silently changing caching/rendering behaviour across the application and avoids equally risky over-refactoring merely to preserve a static build label.

### LL-48 — Localisation must preserve auth-aware navigation semantics

**Observation:** During C45C, adding locale continuity to Home CTAs initially replaced two authenticated-aware `/app` destinations with fixed `/login?locale=...` links. Translation and locale propagation were correct, but the navigation contract for already-authenticated users regressed. The issue was caught before commit and fixed by reusing the existing `ContextualHomeLink` abstraction.

**Working rule:** When localising links into authentication flows, preserve the pre-existing destination semantics for each auth state before adding locale parameters. Reuse established auth-aware navigation helpers rather than replacing contextual links with static URLs. Test anonymous and authenticated destinations separately.

**Benefit:** Prevents localisation work from introducing behavioural regressions in authentication/navigation while keeping locale continuity explicit and testable.

## 7. Standing efficiency rules

The following compact rules apply to future AtlasBadge work:

1. One problem/risk at a time unless a shared root cause is proven.
2. Explain checkpoint invalidation before re-running a large suite.
3. Prefer focused deterministic tests over broad repetitive execution.
4. Finish formatting/fixes before final gates.
5. Do not reopen approved product decisions without new evidence or an explicit requirement change.
6. Do not classify infrastructure, Rules-parity mismatch or stale automation as Product Defects.
7. Keep commits semantically small enough to audit and roll back.
8. Test-only commits do not require runtime deployment validation merely because repository HEAD changed.
9. Production-source changes require proportional release validation; Rules deploys remain separately authorised.
10. When application and Rules change together, identify and close the temporary parity window before Production smoke.
11. For Emulator manual QA, confirm backend + test identity/state + actual Test Lead browser session before declaring the environment ready.
12. Use one canonical manual-QA application origin; do not mix `localhost` and `127.0.0.1` casually.
13. When changing a source of truth, audit write, read/render and confirmed-session refresh paths.
14. Ordering regression must cover the boundary where the user's reordered state becomes persisted and later rendered.
15. For destructive cross-projection resets, define the atomic logical completion boundary separately from non-critical physical garbage collection.
16. A rejected destructive action must expose failure feedback and must not be presented as successful completion.
17. The Test Lead makes quality decisions and final sign-off; mechanical evidence preparation should be automated wherever possible.
18. Public Profile content uses the sanitised public projection for owner and non-owner viewers alike; private owner data is not a public-rendering fallback.
19. Before compensating for missing public projection fields, inspect whether the persisted test document is legacy/stale and can be rebuilt through the normal product write path.
20. When a requirement references an existing AtlasBadge visual pattern, inspect/reuse the exact reference surface and validate layout as well as styling.
21. Audit exact staged/committed file scope and reject unrelated churn or scratch artefacts from a release baseline.
22. Do not bypass a stronger transaction/Rules invariant merely to solve concurrency on a narrower path.
23. Use presentation-order metadata rather than fabricating domain/history data.
24. Keep `docs/` fixed to the approved `01`–`10` control set; merge increment updates into those files and publish new change-level material only under `evidence/` unless the Test Lead explicitly approves another control document.
25. For visual affordance, do not treat class presence or JSDOM focus as visual acceptance; verify the rendered browser result and prefer subtle, brand-consistent correction.
26. For brand/token cleanup, classify colour semantics before replacement; preserve valid status, feedback, data-visualisation and external-brand colours instead of optimising for zero search matches.
27. Size AI-agent work to one finishable checkpoint and to the capability/usage window actually available; do not bundle diagnosis, release and documentation into one oversized task.
28. Treat local and Production/Vercel configuration as separate evidence; never infer or recreate remote secrets from a local missing-variable result.
29. When Emulator coverage cannot reproduce a real Admin/Storage/runtime boundary, qualify that boundary against the real backend before release.
30. Use runtime A/B proof before pinning Node or upgrading dependencies.
31. After a materialised public-projection model change, validate private source → projection write → stored public document → public render/network request.
32. Patch only client-editable fields on mixed-ownership documents; never replay server-managed fields from a stale client snapshot.
33. Match conditional public-projection predicates to Firestore Rules predicates and cover both public/private states.
34. Enforce cost-control quotas structurally at the authoritative backend/Storage boundary, not only in UI state.
35. Audit sibling dynamic-route namespaces before adding locale/tenant/slug segments; preserve deterministic route ownership and existing public URLs.
36. Treat request-time APIs in shared/root layouts as architecture-wide rendering/cache decisions; inspect build classification and record any accepted dynamic-rendering trade-off.
37. Preserve auth-aware link destinations when adding locale continuity; test anonymous and authenticated navigation contracts independently.

---

## 8. Review triggers

Update this document when a project event produces a reusable change in working practice, especially when it:

- prevents repeated unnecessary testing;
- changes how risk is classified;
- exposes a recurring automation weakness;
- improves persistence/recovery validation;
- reduces release or documentation rework;
- identifies an environment-readiness failure;
- changes a data source-of-truth pattern;
- changes destructive-operation atomicity or cleanup semantics;
- identifies an architecture decision that should become a standing rule.

Do not add a lesson merely because an isolated defect occurred.

---

## 9. Related documents and evidence

- `docs/02-quality-risk-analysis.md`
- `docs/03-test-strategy.md`
- `docs/04-test-scope.md`
- `docs/06-test-environments.md`
- `docs/08-metrics-and-reporting.md`
- `docs/09-system-test-plan.md`
- `evidence/v1.0/evidence-register.md`
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
