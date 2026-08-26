# AB-EV-038 — Manual QA Environment Contract Hardening

**Classification:** QA infrastructure / environment-process hardening  
**Product Defect:** None  

**Baseline:**  
AtlasBadge: `add3b5b27f2e38d3be23f2d7ed4a4c2992599a1c`  
QA Portfolio: `e7379beb104b0e44bbadb3b81e68c88692d650d9`  

## Problem

Earlier environment documentation correctly hardened Emulator isolation, but later troubleshooting made routine manual QA appear dependent on creating/restoring an Emulator identity and browser state.

That was unnecessary for the normal pre-V1.0 Test Lead workflow and created avoidable operational friction.

## Decision

**Default routine manual QA:**
- `http://localhost:3000`
- real Firebase `atlas-badge`
- existing controlled QA account/browser session

**Emulator:**
- automated E2E / Rules / isolated integration / fault injection / scenario-specific manual testing.

## Acceptance criteria

- AC1 — Manual QA default is explicitly real Firebase.
- AC2 — localhost does not imply Emulator.
- AC3 — readiness requires Origin + Backend + Test Lead browser session.
- AC4 — real Firebase identity is not assumed to exist in Auth Emulator.
- AC5 — routine manual QA requires no fake user/seed/migration/bootstrap.
- AC6 — Emulator automation/fault-injection coverage remains unchanged.
- AC7 — no Firebase configuration, Rules or application runtime changed.
- AC8 — no Production deployment is required.
- AC9 — documentation is internally consistent.
- AC10 — no secrets or credentials are exposed.

**Result:** PASS � Test Lead approved � 26 August 2026
