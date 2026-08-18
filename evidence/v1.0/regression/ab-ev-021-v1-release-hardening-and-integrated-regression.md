# AB-EV-021 — V1.0 Release Hardening and Integrated Regression

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-021 |
| Related risks | QR-01; QR-04; QR-16; QR-18; QR-19; QR-25; QR-29; QR-30; QR-39; QR-40 |
| Product | AtlasBadge V1.0 |
| Evidence type | Release hardening, integrated regression and release-operation readiness |
| Owner | Test Lead/Product Owner |
| Execution period | 2026-08-05 to 2026-08-17 |
| Integrated pre-release baseline | `f6004a9fd69b88ffac08fcd54134a9e9dd3855e4` |
| Decision | Passed — ready for controlled release operations |

## 2. Purpose

This evidence records the integrated V1.0 hardening cycle that converted a large set of local product changes into a controlled, reviewable release candidate before Production validation.

The cycle deliberately separated implementation, test-harness work, Rules publication, Git publication, Vercel deployment and Production validation so that a failure in one boundary could not be misreported as a product pass.

## 3. Release commit set

| Commit | Purpose |
|---|---|
| `2773bfb` | Repository QA workflow and controlled-development instructions |
| `6ebc9a8` | Firestore Rules support for validated status and achievement metadata |
| `3ee18c8` | Explicit status intents and activation chronology |
| `f140c48` | Transactional achievement metadata reconciliation |
| `44ac35a` | Consolidated canonical achievement metadata storage |
| `95a53e1` | Chronological Badge/Profile grids and confirmed unlock popups |
| `cc14db6` | Flag-order persistence and social-input normalisation |
| `0612ff2` | Cross-feature and Production validator alignment |
| `f6004a9` | Attainable World Complete achievement |

The baseline remained clean before remote release operations.

## 4. Integrated automated gate

The final Emulator-safe regression gate executed **25 Playwright specifications** with **119/119 E2E Passed**, no failures and no skips. Coverage included rapid status races, visit and flag ordering, achievement chronology and isolation, simultaneous unlocks, relock/reconquest, World Complete, Badges/Profile responsive grids, social normalisation, Clear Map, accessibility, responsive/touch, constrained network and CPU scenarios.

| Gate | Result |
|---|---|
| Vitest | 258 Passed; 3 conditional/skipped |
| Firestore Rules | 211/211 Passed |
| Playwright Emulator-safe release set | 119/119 Passed |
| TypeScript | Passed |
| ESLint | Passed |
| Next.js production build | Passed |
| `git diff --check` | Passed |
| Release security scan | No real secret, JWT, bearer token, service-account credential or private key found |

## 5. Firestore Rules release gate

Production Rules were published before the application release. The immediately preceding Rules gate remained **211/211 Passed** and the operation deployed Rules only. This ordering prevented the frontend from depending on fields that Production Rules did not yet accept.

## 6. Remote release operations

After Rules publication, `main` was pushed without force, repository synchronisation was confirmed, the Vercel Git integration produced the expected Production deployment, the deployment reached `READY`, and the Production alias returned HTTP 200.

At this point the baseline was ready for controlled Production validation, not yet finally approved.

## 7. Quality decisions

This gate established a clean integrated tree, Rules/frontend release parity, reusable affected-area regression, and a single identified baseline for remote release operations.

## 8. Traceability

```text
C1–C8 controlled release commits
→ 258 Vitest + 211 Rules + 119 E2E
→ security and repository integrity checks
→ Rules-first Production publication
→ Git main push
→ Vercel Production READY
→ controlled Production-validation phase
→ AB-EV-021
```

## 9. Final conclusion

The integrated V1.0 release-hardening baseline passed the required local and Emulator-safe technical gates and was approved to enter controlled Production validation.
