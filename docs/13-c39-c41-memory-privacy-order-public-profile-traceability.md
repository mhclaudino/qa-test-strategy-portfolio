# AtlasBadge C39–C41 Memory Privacy, Ordering and Public-Profile Traceability

**Document status:** Active change record — C39/C40 closed; C41 Production smoke pending  
**Target release:** AtlasBadge V1.0  
**Owner:** Test Lead/Product Owner  
**Date:** 27 August 2026

## 1. Purpose

This document connects C39–C41 requirements, implementation, quality risks, tests, release controls and evidence.

Detailed records:

- [AB-EV-039 — C39 Individual memory privacy](../evidence/v1.0/regression/ab-ev-039-c39-individual-memory-privacy.md)
- [AB-EV-040 — C40 Manual memory ordering](../evidence/v1.0/regression/ab-ev-040-c40-manual-memory-ordering.md)
- [AB-EV-041 — C41 Public memories from earned flags](../evidence/v1.0/regression/ab-ev-041-c41-public-memory-flag-modal.md)

## 2. C39 — Individual memory privacy

C39 adds explicit privacy per visit memory and general memory while preserving the explicit-Save contract.

Accepted rules:

- default/legacy privacy is private;
- visit memory may be public with positive duration, valid date/time or non-empty note;
- note is not mandatory for a public visit;
- empty general memory cannot be public;
- profile-level privacy remains authoritative;
- public projection exposes only sanitised `publicMemories`, never raw privacy flags or private visit objects.

Final recorded gates:

```text
Full Vitest: 421 PASS
Firestore Rules: 228 PASS
Privacy Emulator E2E: 1/1 PASS
lint / TypeScript / build / diff-check: PASS
Test Lead manual QA: PASS
```

Commit: `daf37e7f24950137473917f6b4e898428b5de20e` — `feat(memories): add per-memory privacy controls`. GitHub/Vercel commit status: success.

**Decision: C39 APPROVED / RELEASED.**

## 3. C40 — Manual memory ordering

C40 separates `registeredVisits` source/history order from `memoryOrder` presentation metadata. Stale/duplicate order IDs are ignored, missing current visits append naturally, delete cleans the order ID in the same logical mutation and original `VISITA n` identity remains stable.

The public projection resolves C40 order before C39 privacy filtering and never exposes `memoryOrder`.

Real-backend QA initially failed with `permission-denied / Missing or insufficient permissions` because the client serialised `memoryOrder: []` before the new Rules whitelist was deployed. This was classified as a **Rules parity blocker, not a Product Defect**.

After Test Lead approval, `firestore:rules` only was deployed successfully to `atlas-badge` at 2026-08-26T18:13:49Z.

Final recorded gates:

```text
Focused C40: 33 PASS
Full Vitest: 427 PASS / 22 skipped / 0 failed
Rules: 230 PASS
Privacy Emulator E2E: 1/1 PASS
lint / TypeScript / build / diff-check: PASS
Test Lead manual QA: PASS
```

Commit: `2db201bf89c649655c6e86ccb4c03dc7a5ac621a` — `feat(memories): add manual memory ordering`. GitHub/Vercel commit status: success.

**Decision: C40 APPROVED / RELEASED.**

## 4. C41 — Public memories from earned flags

C41 displays only the C39-authorised public memories for a selected earned country/territory flag, preserving C40 order.

Final accepted UI:

```text
click earned flag
→ Wishlist-family modal
→ selected place identity
→ authorised public memories
```

No permanent inline memory panel remains below the flag grid.

Both anonymous visitors and the owner viewing their own public Profile consume the same public projection for modal memories. Private `RegisteredVisit` data is not a fallback source.

The public projection carries the exact original visit presentation identity (`VISITA 1`, `VISITA 2`, ...); the modal does not renumber by public-array position.

The existing Brazil test projection initially lacked `visitLabel`. It was refreshed through the normal owner persistence path; no migration or direct admin fabrication was introduced.

The final Profile sort header restores the compact Map-family layout with title left, toggle right and the flag grid below without overlap.

Final pre-release gates:

```text
Focused C41/public projection: 31 PASS
Full Vitest: 446 PASS / 22 skipped
lint / TypeScript / build / diff-check: PASS
Test Lead local functional/visual QA: PASS
```

Commit: `bb57a004d7b0c64420d093fc4000a67e226eba1e` — `feat(profile): show public memories from flags`. GitHub/Vercel commit status: success.

Final Production smoke remains pending and is the only open C41 closure checkpoint.

## 5. Combined traceability

| Change | Primary risks | Release state |
|---|---|---|
| C39 | QR-05, QR-31, QR-32, QR-34, QR-36 | APPROVED / RELEASED |
| C40 | QR-01, QR-04, QR-05, QR-32, QR-39, QR-40 | APPROVED / RELEASED |
| C41 | QR-31, QR-32, QR-33, QR-36, QR-39, QR-40 | DEPLOYED / Production smoke pending |

## 6. Current release anchor

```text
Product main: bb57a004d7b0c64420d093fc4000a67e226eba1e
C41 Vercel status: success
C41 Production smoke: PENDING
```

Update this document from Active to Completed when the Test Lead records the final C41 Production smoke result.
