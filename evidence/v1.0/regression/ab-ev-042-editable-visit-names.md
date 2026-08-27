# AB-EV-042 — Editable visit names

**Status:** CLOSED / PRODUCTION PASS  
**Date:** 27 August 2026  
**Product commit:** 601e34921a7026857bef70c453f7de09c39579ae  
**Commit message:** feat(visits): add editable visit names  
**Vercel Production deployment:** dpl_5Jpxq9DsvoCKrbE5gjd7pF67ueyV — READY / production  
**Decision owner:** Test Lead/Product Owner

## 1. Purpose

This evidence record closes the AtlasBadge V1.0 increment that allows the owner to assign an optional presentation name to an individual RegisteredVisit without changing visit identity, source history, memory ordering or privacy behaviour.

The increment was treated as a product requirement plus regression-sensitive QA change because the new field crosses owner editing, persistence, dirty-state handling, manual memory ordering and the sanitised public-memory projection.

## 2. Approved functional contract

The approved behaviour is:

- the visit keeps its stable technical RegisteredVisit.id;
- the optional field is visitName?: string;
- no custom name uses the existing VISITA n fallback;
- the custom name is edited in the visit editor;
- persistence occurs only through the existing explicit Save action;
- there is no autosave;
- maximum length is 40 characters;
- Save applies trim;
- empty/whitespace-only input removes the custom name and restores VISITA n;
- duplicate custom names inside the same country/territory are rejected case-insensitively after trim;
- Unicode, accents, symbols and emoji are allowed;
- renaming does not change registeredVisits order, memoryOrder, visit count or visit ID;
- a public visit exposes the presentation name only through the existing sanitised PublicMemory.visitLabel;
- a private visit never projects its custom name publicly;
- one visit-name editor is visually active at a time while unsaved drafts in other visits are preserved;
- duplicate-name failure keeps the active editor open for immediate correction.

## 3. Compatibility with C39–C41

### C39 — individual memory privacy

The name does not make a visit public. isMemoryPublic remains the explicit publication control and Save remains explicit.

### C40 — manual memory ordering

memoryOrder remains presentation order only. The custom name stays attached to the visit ID and does not reorder registeredVisits.

### C41 — public memories from earned flags

Public Profile rendering continues to consume the sanitised public projection for owner and anonymous viewers. Public visits use the custom name as visitLabel when present and retain the stable VISITA n fallback when absent. Raw visitName, visit ID and privacy flags are not exposed.

## 4. Implementation and regression evidence

The final product commit changed the domain/editor/persistence/public-projection path and added focused coverage including:

- src/lib/visitName.test.ts;
- src/components/VisitEditor.visitName.test.tsx;
- src/components/CountryActionCard.visitName.test.tsx;
- existing C39/C40/public-projection/serialization regression remained green.

Final pre-release quality gates reported:

- full Vitest suite: **479 PASS / 0 FAIL**;
- ESLint: **PASS**;
- TypeScript tsc --noEmit: **PASS**;
- Next.js production build: **PASS**;
- git diff --check: **PASS**;
- Firestore Rules change: **not required**;
- data migration: **not required**.

The temporary hover-specific Playwright experiment was not retained as permanent repository coverage because the local routing/network harness was unstable for that isolated visual check. Final interaction and visual acceptance were therefore made through focused component coverage plus Test Lead browser QA.

## 5. Manual QA findings and refinements

Manual QA materially improved the final interaction before release:

1. the initial edit affordance could technically focus an off-header input but did not communicate editing clearly to the user;
2. the editor was moved to an inline header interaction;
3. duplicate-name failure originally closed the editor because an unconditional blur handler ran before asynchronous Save validation completed;
4. the lifecycle was corrected so failed name validation keeps the input open and successful Save closes it;
5. local per-component edit state allowed multiple visit-name inputs to remain open simultaneously;
6. edit coordination was moved to the parent country card through one active visit ID while preserving unsaved drafts;
7. several stronger neon hover treatments were rejected during visual QA; the final pencil treatment is deliberately premium and subtle: grey at rest, white on hover, with no glow or scale.

These refinements were classified as requirement clarification / UX adjustment during manual QA rather than separate product defects.

## 6. Production closure

The automatic Vercel deployment for commit 601e34921a7026857bef70c453f7de09c39579ae reached **READY** with target **production**.

The Test Lead then completed focused Production smoke covering the released visit-name behaviour and reported:

**Production PASS**

No manual Vercel deployment, Firebase deployment, Firestore Rules deployment or migration was required for this increment.

## 7. Decision

**C42 / Editable visit names: CLOSED / PRODUCTION PASS.**

The feature is accepted into the AtlasBadge V1.0 regression baseline. Future changes to visit editing, visit identity, manual memory ordering, privacy/public projection or Profile public-memory presentation must preserve this contract.
