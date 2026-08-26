# AB-EV-040 — C40 Manual Memory Ordering

## Evidence summary

**Evidence ID:** AB-EV-040  
**Change:** C40 — Manual memory ordering  
**Classification:** V1.0 product enhancement; no standalone Product Defect  
**Primary risks:** QR-01, QR-04, QR-32  
**Supporting risks:** QR-05, QR-39, QR-40  
**AtlasBadge commit:** `2db201bf89c649655c6e86ccb4c03dc7a5ac621a` — `feat(memories): add manual memory ordering`  
**Rules-only Production deploy:** SUCCESS — 26 August 2026 18:13:49Z  
**Vercel commit status:** success  
**Test Lead decision:** **C40 APPROVED / RELEASED**

## 1. Requirement and model

C40 introduces `memoryOrder?: string[]` as presentation metadata. `registeredVisits` remains the source/history array. The resolver ignores stale/duplicate IDs, appends current missing visits and preserves original visit identity.

## 2. Persistence and public projection

`updateMemoryOrder` uses the normal replayable country-mutation/orchestrator/`upsertUserPlace` path. Deleting a visit removes its order ID in the same logical mutation. The public projection resolves memory order before privacy filtering and never exposes `memoryOrder`.

## 3. Rules-parity blocker

Before the C40 Rules deploy, real-backend writes failed with `permission-denied / Missing or insufficient permissions` because the client wrote `memoryOrder: []` while deployed Rules still used the previous whitelist.

Classification: **RULES PARITY BLOCKER — NOT PRODUCT DEFECT.**

After Test Lead approval, `firestore:rules` only was deployed successfully to `atlas-badge`.

## 4. Validation

```text
Focused C40: 33 PASS
Full Vitest: 427 PASS / 22 skipped / 0 failed
Rules: 230 PASS
Privacy Emulator E2E: 1/1 PASS
ESLint / TypeScript / Production build / diff-check: PASS
Test Lead manual QA: PASS
```

## 5. Release and decision

Commit `2db201bf89c649655c6e86ccb4c03dc7a5ac621a`; GitHub/Vercel reports successful deployment completion.

**C40 APPROVED / RELEASED.**

No QA-account credentials or raw private Firestore payloads are published.
