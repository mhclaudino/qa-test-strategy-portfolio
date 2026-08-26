# AB-EV-041 — C41 Public Memories from Earned Flags

## Evidence summary

**Evidence ID:** AB-EV-041  
**Change:** C41 — Public memories from earned country/territory flags  
**Classification:** V1.0 product enhancement with contained pre-release findings; no standalone Product Defect ID  
**Primary risks:** QR-31, QR-32, QR-33, QR-36  
**Supporting risks:** QR-39, QR-40  
**AtlasBadge commit:** `bb57a004d7b0c64420d093fc4000a67e226eba1e` — `feat(profile): show public memories from flags`  
**Vercel commit status:** success  
**Test Lead decision:** **Local functional/visual PASS; final Production smoke pending**

> This record remains open until the final C41 Production smoke is recorded.

## 1. Requirement

Clicking an earned country/territory flag on the public Profile opens only the authorised `publicMemories` for that place. C41 consumes the C39 privacy contract and C40 presentation order.

## 2. UI correction during validation

The first implementation rendered a permanent inline panel below the flag grid. Test Lead QA rejected it. Final accepted behaviour reuses the Wishlist modal family. Wishlist remains independent.

## 3. Public source of truth

Anonymous viewing already consumed the public projection. Owner viewing initially used private place data and therefore lacked `publicMemories`. An intermediate client-side reconstruction from private data was rejected.

Final rule: **anonymous modal source = public projection; owner modal source = the same public projection.**

Private `RegisteredVisit` is not a public-rendering fallback.

## 4. No-note visit, exact label and legacy projection

The Brazil test case contained a public visit with valid date/duration and no note; C41 renders it because C39 does not require a note when another approved shareable value exists.

The initial generic `MEMÓRIA DE VISITA` label was rejected. Current public projection carries exact original `VISITA n` identity and the modal does not renumber by public-array position.

The existing Brazil public document initially lacked the new label. It was refreshed through the normal product persistence path—no migration or direct admin data fabrication.

## 5. Profile sort-toggle regression

Final approved desktop layout:

```text
Bandeiras Conquistadas              [ Ordem Alfabética | Ordem de Visita ]
[ flag grid below, no overlap ]
```

Sorting behaviour itself did not change.

## 6. Validation

```text
Focused C41/public projection: 31 PASS
Full Vitest: 446 PASS / 22 skipped
ESLint / TypeScript / Production build / diff-check: PASS
Test Lead local functional/visual QA: PASS
```

C41 changed no Firestore Rules or Storage Rules.

## 7. Commit/deployment and open checkpoint

Commit `bb57a004d7b0c64420d093fc4000a67e226eba1e`; post-push HEAD matched origin/main and the working tree was clean. GitHub/Vercel commit status is `success`.

Final Production smoke still needs to confirm public Profile load, final sort-header layout/behaviour, Brazil C41 modal, exact refreshed `VISITA n` label, no-note memory visibility, Wishlist independence and modal close.

**Current status: DEPLOYED / PRODUCTION SMOKE PENDING.**

No raw private visit payloads, credentials, tokens or diagnostic scripts are published.
