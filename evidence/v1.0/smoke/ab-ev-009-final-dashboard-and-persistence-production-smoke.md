# AB-EV-009 — Final dashboard and persistence Production smoke

**Evidence ID:** AB-EV-009  
**Related evidence:** AB-EV-008  
**Related quality risks:** QR-01, QR-18, QR-19, QR-25 and QR-39  
**Product:** AtlasBadge V1.0  
**Evidence type:** Post-deployment Production smoke and controlled persistence validation  
**Environment:** Vercel Production  
**Browser and platform:** Microsoft Edge on Windows; detailed version retained in the private execution record  
**Execution date:** 2026-07-29  
**Deployed Git reference:** `83f5650`; the full SHA is retained in the private product and deployment record  
**Deployment result:** `Ready`  
**Decision owner:** Test Lead/Product Owner  
**Overall result:** Passed  
**Quality-risk decision:** QR-01 remains **Current gap** because the completed smoke confirms the registered-place map path but does not provide equivalent failure, reload and recovery coverage for every persistence flow in the product  

## 1. Purpose

This record closes the Production-confirmation item that remained outside AB-EV-008.

AB-EV-008 documented the local and emulator investigation, the corrected confirmed-cache contract and the affected-area regression for the AtlasBadge dashboard. This separate record confirms that the final six-commit package was deployed to Vercel Production and that the non-destructive final smoke passed.

## 2. Deployment verification

The Test Lead confirmed the following deployment controls:

| Control | Result |
|---|---|
| Correct AtlasBadge project | Passed |
| Environment was Production | Passed |
| Source branch was `main` | Passed |
| Deployed Git reference matched `83f5650` | Passed |
| Final deployment status was `Ready` | Passed |
| Deployment occurred after the corresponding push | Passed |
| No empty commit or force push was used | Passed |

Raw Vercel details and the full product SHA remain in the private operational record because the product repository and deployment account are not public portfolio assets.

## 3. Non-destructive Production smoke

| ID | Scenario | Result | Public evidence position | Observation |
|---|---|---|---|---|
| SMK-01 | Home availability, Google login entry point and absence of route-level 500 errors or Next.js overlays | Passed | Textual public record; raw screenshots retained privately | Essential entry flow remained available |
| SMK-02 | My Map, Public Profile and Badges routes | Passed | Textual public record | Authenticated essential routes opened normally |
| SMK-03 | 251-place selector, scrolling, search and lazy-loaded flags | Passed | Cross-reference to AB-EV-008 screenshots plus private Production execution | Catalogue rows remained stable and flags loaded during use |
| SMK-04 | Institutional Footer GitHub link and absence of a user-editable GitHub social field | Passed | AB-EV-008 plus private Production execution | GitHub remained an institutional Footer link only |
| SMK-05 | Registered-place filters, name and continent search, logical intersection, active-count layout and Clear filters | Passed | Textual public record | Filters affected presentation only and did not modify persisted data |
| SMK-06 | My Map / Public Profile metric parity using `251 / 195 / 52 / 8` and the canonical Total Visits calculation | Passed | AB-EV-008 sanitised metric screenshots plus private Production execution | The two screens remained aligned |
| SMK-07 | Immediate optimistic visit update without waiting visually for remote confirmation | Passed | Private Production execution | The responsive queue behaviour remained effective |
| SMK-08 | Controlled visit persistence after navigation, refresh and logout/login | Passed | Private Production execution | The added visit remained persisted until deliberate restoration |
| SMK-09 | Removal of the test visit and restoration of all affected status and counter values | Passed | Private Production execution | Test data returned to its initial baseline |
| SMK-10 | No destructive global cleanup and no Production fault injection | Passed | Execution record | No network, Rules or backend failure was injected |

## 4. Controlled persistence result

A restorable place record was used.

The execution confirmed this sequence:

```text
record initial status and visit baseline
→ add one visit
→ observe immediate UI update
→ allow normal remote confirmation
→ navigate to Public Profile without F5
→ confirm the same Total Visits value
→ refresh both screens
→ logout and log in again
→ confirm persistence
→ remove the test visit
→ restore any affected status
→ confirm the original baseline after refresh and relogin
```

The test did not clear global data and did not use a record that could not be restored.

## 5. Data and safety controls

- No Firebase Rules change was made during the smoke.
- No network, Firestore or backend failure was injected in Production.
- No global account or travel-data reset was performed.
- The controlled mutation was reversed.
- Personal identifiers, private URLs, tokens, cookies, UIDs and raw payloads are not included in this public record.
- Raw screenshots and deployment details remain private.

## 6. Traceability

| Artefact | Relationship |
|---|---|
| QR-01 | Registered-place map persistence path confirmed in Production; broader persistence coverage remains incomplete |
| QR-18 | Dashboard totals recalculated correctly after the controlled mutation and restoration |
| QR-19 | Canonical Total Visits remained aligned across My Map and Public Profile |
| QR-25 | `251 / 195 / 52 / 8` taxonomy remained consistent |
| QR-39 | Selector, flags, filters and responsive visit feedback remained usable |
| AB-EV-008 | Local/emulator investigation, implementation traceability and sanitised affected-area evidence |
| `83f5650` | Final Git reference for the deployed six-commit dashboard package |

## 7. Test Lead decision

**Passed — Production deployment and final smoke approved.**

The final dashboard package was available in Vercel Production, the non-destructive smoke passed, persistence survived refresh and relogin, and the test data was restored.

QR-01 is not changed to **Mitigated**. It remains **Current gap** solely because the same failure, reload and recovery depth has not yet been demonstrated for every non-map persistence flow.
