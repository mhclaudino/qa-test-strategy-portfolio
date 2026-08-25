# AB-EV-034 — QR-01 Failed-Write Recovery Closure

## Evidence summary

**Evidence ID:** AB-EV-034  
**Quality risk:** QR-01 — silent data loss after failed Firestore writes  
**Classification:** Risk-coverage closure; no new Product Defect  
**Environment:** Vitest/component runtime and static repository audit  
**AtlasBadge commit:** `66cffbc933710f2b9f4ba007c5726ebc2857ac82` — `test(profile): cover flagSortOrder failed-write recovery`  
**Test Lead decision:** QR-01 technical coverage gap closed; retain as `Regression risk`

---

## 1. Risk statement

QR-01 tracks the risk that a Firestore write may fail while the interface or local cache continues to present the change as saved, causing silent data loss in a later session.

Earlier evidence progressively protected status, visit, cache, reconciliation, concurrency, Clear Map, Manual Visit Order, Wishlist and private/public projection paths. The remaining coverage gap was the owner Profile `flagSortOrder` update path.

The final decision used architectural/risk equivalence rather than requiring a mechanically identical failed-write test for every Firestore call.

---

## 2. Write-path audit conclusion

The write-path audit concluded that:

- normal place status/visit/note mutations are protected by the `CountryMutationOrchestrator` optimistic/confirmed-state model;
- public place projection is written within the same persistence boundary as the corresponding private place mutation where applicable;
- profile creation and profile/username updates use transactions rather than independent UI-confirmed writes;
- Wishlist order/privacy and Manual Visit Order use blocking save semantics and do not falsely confirm failed writes through the same optimistic-cache pattern;
- achievement metadata reconciliation is background/reconciliation behaviour rather than a false-confirmation UI path;
- account deletion belongs to its own retry-safe lifecycle risk;
- `flagSortOrder` was the only remaining Partial path because runtime rollback existed but failure/recovery evidence was missing.

Separate observations about multiple independent Firestore commit boundaries in other flows remain cross-risk/data-consistency considerations and were not misclassified as proven QR-01 failures.

---

## 3. Implemented evidence

A focused component test was added at:

```text
src/app/[username]/page.flagSortOrder.test.tsx
```

The test proves the owner Profile sort-order failure path:

1. confirmed starting state is `alphabetical`;
2. the user selects Visit Order;
3. the UI changes optimistically to `visitOrder` before persistence resolves;
4. `updateUserProfile` rejects deterministically with a controlled network failure;
5. the UI rolls back to the last confirmed `alphabetical` state;
6. UID-scoped local travel cache remains untouched;
7. unmount/remount does not resurrect the rejected value.

The test uses a deferred Promise rather than arbitrary delays and observes real React state transitions through the rendered component.

---

## 4. Retry and idempotency rationale

`updateUserProfile` uses a Firestore transaction. Retrying a `flagSortOrder` change rewrites the intended scalar profile metadata and keeps private/public profile fields aligned in the same transactional operation.

The retry does not append visit/history entities and therefore does not create duplicate list records. For this field the retry is effectively idempotent.

---

## 5. Quality gates

Final evidence for the added test:

```text
Focused ESLint: PASS — 0 errors / 0 warnings
TypeScript: PASS — npx.cmd tsc --noEmit
Focused Vitest: PASS — 1 / 1
Pre-commit audit: PASS
```

The intentionally rejected persistence logs the expected application error to stderr; this was not a test failure.

No production source, Firestore Rules or Firebase configuration changed.

Because the commit is test-only, the previously approved runtime/Rules/Playwright/Production checkpoints were not invalidated and no deployment was required for technical closure of QR-01.

---

## 6. Decision

**QR-01 technical evidence gap: CLOSED.**

Final state:

```text
Current gap → Regression risk
```

The risk is retained because failed persistence remains a material regression concern even though the current architecture and automated evidence now provide proportionate prevention/recovery coverage.

No Product Defect ID was created because the final gap was missing failure-path evidence, not a newly demonstrated product failure.

---

## 7. Traceability

```text
QR-01 risk
→ write-path architectural audit
→ flagSortOrder identified as only remaining Partial path
→ focused React failed-write recovery test
→ ESLint / TypeScript / Vitest PASS
→ pre-commit audit PASS
→ commit 66cffbc933710f2b9f4ba007c5726ebc2857ac82
→ no runtime deployment required
→ QR-01 moved to Regression risk
```

Related evidence:

- AB-EV-008 — map persistence and dashboard regression;
- AB-EV-009 — controlled persistence Production smoke;
- AB-EV-019 — status persistence/OCC closure;
- AB-EV-026 — Clear Map reconciliation race protection;
- AB-EV-032 — Manual Visit Order and rapid-visit persistence;
- AB-EV-033 — Wishlist/private-public persistence and release hardening.
