# AB-EV-039 — C39 Individual Memory Privacy

## Evidence summary

**Evidence ID:** AB-EV-039  
**Change:** C39 — Individual memory privacy  
**Classification:** V1.0 product enhancement / privacy control; no standalone Product Defect  
**Primary risks:** QR-32, QR-36  
**Supporting risks:** QR-05, QR-31, QR-34  
**AtlasBadge commit:** `daf37e7f24950137473917f6b4e898428b5de20e` — `feat(memories): add per-memory privacy controls`  
**Vercel commit status:** success  
**Test Lead decision:** **C39 APPROVED / RELEASED**

## 1. Requirement and accepted contract

C39 adds explicit publication control to each memory while preserving explicit Save. Missing/legacy privacy flags are private. A visit may be public when `isMemoryPublic === true` and approved duration/date/time/note exists; note is not mandatory. General memory requires `isGeneralNotePublic === true` and non-empty text. Profile-level privacy remains authoritative.

## 2. Public projection

The owner-private source remains under `users/{uid}/places/{placeId}`. The public place projection emits only sanitised `publicMemories` and excludes raw privacy flags, raw `RegisteredVisit`, `generalNote` and private memory content.

## 3. Validation

```text
Focused C39/component/projection: PASS
Full Vitest: 421 PASS
Firestore Rules: 228 PASS
Privacy Emulator E2E: 1/1 PASS
ESLint / TypeScript / Production build / diff-check: PASS
Test Lead manual QA: PASS
```

## 4. Release and decision

Commit `daf37e7f24950137473917f6b4e898428b5de20e`; GitHub/Vercel reports successful deployment completion.

**C39 APPROVED / RELEASED.**

QR-36 moves from Future risk to Regression risk because per-memory visibility is now implemented.

## 5. Traceability

```text
C39 → QR-05/31/32/34/36 → privacy fields → sanitised publicMemories
→ automated gates → Rules/Privacy E2E → Test Lead QA
→ daf37e7f... → Vercel success → C39 APPROVED
```

No credentials, identifiers or raw private memory content are published.
