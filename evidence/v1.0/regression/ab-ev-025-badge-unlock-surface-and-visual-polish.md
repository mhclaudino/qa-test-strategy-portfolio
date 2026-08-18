# AB-EV-025 — Badge Unlock Surface Consistency and Visual Polish

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-025 |
| Related risks | QR-30; QR-39; QR-40 |
| Product | AtlasBadge V1.0 |
| Evidence type | Visual regression, responsive surface consistency and Test Lead acceptance |
| Owner | Test Lead/Product Owner |
| Product commits | C18 `d94c84b`; C19 `6bc091e`; C20 `86bb2a0` |
| Final Production deployment | `dpl_66z8qZ6T5YS8JJBRAzMBFkuG1JiV` |
| Final decision | Passed — desktop and mobile visual approval by Test Lead |

## 2. Purpose

This evidence records the final visual refinement of the Badge Unlock popup after its functional notification behaviour had already been approved. The requirement was to share the same base surface as Profile/Mobile Menu while retaining a distinct golden achievement accent.

## 3. C18 — nominal surface reuse

The first correction reused `floating-surface`. Nominal CSS matched, but the painted result still differed because the semi-transparent surface was composited over different backdrop and stacking contexts. The Test Lead correctly rejected nominal class equality as insufficient visual evidence.

## 4. C19 — real surface consistency

The canonical surface became:

```text
background: rgb(9, 9, 11)
backdrop-filter: blur(24px)
border: 1px solid rgba(63, 63, 70, 0.5)
box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25)
opacity: 1
```

Computed-style and painted-pixel checks confirmed Profile, Mobile Menu and Badge Unlock popup shared the same visible dark surface.

## 5. C20 — restore achievement distinction

Git history showed the original achievement distinction was a static golden border and glow rather than keyframe animation. C20 kept `floating-surface` unchanged and added a Badge-only accent:

```text
border: rgba(255, 176, 0, 0.3)
hover border: rgba(255, 176, 0, 0.6)
gold glow: 0 10px 40px rgba(255, 176, 0, 0.15)
```

No width, height, padding, margin, positioning or timing property changed.

## 6. Functional regression

- popup/notification tests: **20/20 Passed**;
- achievement popup Emulator E2E: **3/3 Passed**;
- first unlock exactly once;
- reconquest exactly once;
- remote tab zero historical/local-action popup;
- reload zero historical replay;
- full-page loader zero reappearances;
- TypeScript, lint, production build and `git diff --check`: Passed.

## 7. Production validation

C20 reached `READY` through the Vercel Git integration and the Production alias returned HTTP 200. Automated mobile visual evidence was limited by the available browser surface, so mobile sign-off was explicitly deferred rather than bypassing tooling restrictions.

The Test Lead then approved the real mobile result: equal base backgrounds, Badge-only golden accent present, no clipping and preserved readability.

## 8. Separate observation

During one earlier C19 visual smoke, a Firestore `documents:commit` request returned HTTP 400. It was treated as a separate, unclassified observation and was not silently attributed to the CSS change. It is not used as evidence against the visual result recorded here.

## 9. Traceability

```text
C18 nominal surface reuse
→ Test Lead detects painted-colour mismatch
→ C19 opaque canonical surface
→ Test Lead requests achievement distinction
→ C20 restores historical gold accent
→ technical gates + Production READY
→ Test Lead desktop/mobile approval
→ AB-EV-025
```

## 10. Final conclusion

The final Badge Unlock popup uses the same approved base surface as Profile and Mobile menus while retaining a distinct non-functional golden achievement accent. The visual result is approved in Production by the Test Lead.
