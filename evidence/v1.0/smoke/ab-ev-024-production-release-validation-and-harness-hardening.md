# AB-EV-024 — Production Release Validation and Harness Hardening

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-024 |
| Related risks | QR-01; QR-04; QR-15; QR-16; QR-18; QR-19; QR-22; QR-29; QR-30; QR-39; QR-40 |
| Product | AtlasBadge V1.0 |
| Evidence type | Production validation, destructive-user-flow validation and test-harness hardening |
| Owner | Test Lead/Product Owner |
| Product runtime validated | C15 `8887d2b23a3e459e9d5954bdfb63cd0383c984fb` |
| Final harness commits | C16 `90035f72...`; C17 `1b040c29...` |
| Decision | Passed — `validate-production` and `validate-clear-map` passed against the same Production runtime |

## 2. Purpose

This evidence records the controlled V1.0 Production-validation phase and the test-engineering work required to ensure that a green result represented the real Production application rather than localhost, Emulator state, stale locators or optimistic UI only. Harness defects were classified as **test defects**, not misreported as product failures.

## 3. Production runner hardening

A dedicated Playwright Production configuration fixed the Production base URL, removed local `webServer`/Emulator behaviour, used one worker and no retry masking, enforced target/Firebase guards before mutation, added sanitised console/page/network observability, and restricted direct Firestore assertions to the authenticated test account.

## 4. Auth test-data strategy

Investigation proved that a normal user token could not programmatically establish `emailVerified=true` even when the REST update returned HTTP 200. The validators therefore use a pre-existing verified disposable account supplied only through local non-versioned environment variables.

Before browser login the harness requires credential presence, password sign-in success, account lookup success and backend `emailVerified === true`. No password, token or private credential is published.

## 5. Harness defects found and corrected

The Production cycle found and corrected test-only issues involving:

- localhost/Emulator target selection;
- the real `/verify-email` + `Já verifiquei` flow;
- invalid programmatic verification assumptions;
- Clear Map confirmation accessible-name drift;
- using registered-place search instead of the full country selector after Clear Map;
- reloading after optimistic wishlist state before confirmed Firestore convergence;
- an invalid expected status combination instead of real `statusRules` behaviour;
- ambiguous global `Badges`/`Perfil` locators while the user menu was open.

Each was stopped at the first inconsistent boundary, classified and fixed in test-only code before continuing.

## 6. `validate-production` result

| Result | Value |
|---|---:|
| Passed | 1 |
| Failed | 0 |
| Skipped | 0 |
| Test duration | 16.4 s |
| Total execution | 17.8 s |

The scenario confirmed verified-account sign-in, application access, Argentina wishlist ON with Firestore confirmation and reload parity, wishlist OFF with remote convergence and reload parity, and the final status-rules sequence `visited=true`, `born=true`, `wishlist=false`, `nationality=false` with coherent profile/birthplace data.

## 7. `validate-clear-map` result

| Result | Value |
|---|---:|
| Passed | 1 |
| Failed | 0 |
| Skipped | 0 |
| Test duration | 24.8 s |
| Total execution | 26.4 s |

After the real UI Clear Map action, direct account-scoped evidence confirmed `places = 0`, map-earned metadata relocked/removed, the next-sequence counter did not decrease, the profile remained available, the Badges UI showed `a1` locked, reload preserved the same logical state, and no corrective write occurred merely because the application rehydrated.

## 8. Reconquest evidence

The validator then reconquered `a1` through the UI. The new acquisition received a sequence greater than the prior one, advanced the monotonic counter, displayed one popup, re-entered the full-page loader zero times, persisted across reload and did not replay as a historical popup.

## 9. Observability and release decision

The final run reported no functional console/page error, unexpected HTTP 4xx/5xx, permission denial, unrecovered precondition failure or unhandled rejection. Navigation/listener aborts caused by deliberate page changes were classified separately.

Because C16 and C17 changed only test harness code, their successful validators apply to the same C15 Production runtime. The Test Lead approved the combined result as:

```text
PRODUCTION VALIDATION PASS
```

## 10. Traceability

```text
C9 dedicated Production runner
→ Auth/target/observability hardening
→ test-only defects classified and corrected
→ C11/C15 product blockers independently fixed
→ C16 validate-production PASS
→ C17 validate-clear-map PASS
→ same Production runtime C15
→ destructive clear + reload + reconquest verified
→ AB-EV-024
```

## 11. Final conclusion

The controlled Production gate passed with real backend confirmation, deterministic waits, destructive Clear Map verification, reload parity, achievement reconquest and sanitised observability.
