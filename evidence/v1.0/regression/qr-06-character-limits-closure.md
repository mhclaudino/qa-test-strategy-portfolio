# QR-06 — Character-limit closure and regression decision

**Evidence ID:** AB-EV-011  
**Related quality risk:** QR-06  
**Product:** AtlasBadge V1.0  
**Evidence type:** Quality-risk closure and permanent regression decision  
**Environment:** Local validation and Vercel Production  
**Validation completion:** Confirmed by the Test Lead before 2026-07-30  
**Build or commit:** Retained in the private AtlasBadge product record; not invented or reproduced in this public portfolio record  
**Decision owner:** Test Lead/Product Owner  
**Final result:** Passed — original gap closed  
**Quality-risk decision:** QR-06 moves from **Current gap** to **Regression risk**  

## 1. Original risk

> Missing character limits may allow oversized notes or profile content, causing layout, validation, storage or document-size problems.

The risk applied to user-editable free-text data, including memory or general-note content and profile fields.

## 2. Closure decision

The Test Lead confirmed that QR-06 implementation, validation, deployment and final approval were completed before this public reconciliation.

The original missing-control condition is therefore no longer represented as an active product gap.

The area remains a **Regression risk** because future UI, validation or persistence changes could:

- remove an approved limit;
- apply different boundary behaviour between screens;
- allow over-limit values to reach persistence;
- break layout or counters at the accepted boundary;
- treat typing, paste and submit paths inconsistently;
- reject existing valid data after a limit-policy change.

## 3. Required permanent coverage

Regression coverage must retain, for every governed field:

| Control | Regression expectation |
|---|---|
| Below-limit input | Accepted and persisted according to the field rule |
| Exact-limit input | Accepted without layout or data corruption |
| Over-limit input | Handled according to the approved product rule and not silently persisted as an invalid value |
| Paste at the boundary | Consistent with typed input |
| Edit of an existing valid value | Preserved without unintended loss |
| Save or submit action | Uses the same limit policy as the visible UI |
| Reload and relogin | Persisted values remain valid and stable |
| Responsive layout | Labels, counters, validation and actions remain usable |
| Private/public presentation | No unexpected exposure or rendering break occurs |

The exact numeric limits are product acceptance criteria and must remain in the private implementation and test source of truth. They are intentionally not guessed in this public record.

## 4. Evidence position

This public artefact records the final risk decision and the required reusable coverage.

It does not publish:

- personal note or biography content;
- private screenshots;
- source excerpts from the private product repository;
- private build output;
- a commit hash that was not independently available to the public portfolio audit.

The Test Lead's completion and approval statement is the decision authority for this reconciliation. Any later public package containing sanitised field-level evidence may reference AB-EV-011 without changing the applied state.

## 5. Traceability

| Artefact | Relationship |
|---|---|
| QR-06 | Original missing-limit gap closed; future failure retained as Regression risk |
| Product acceptance criteria | Source of truth for the approved numeric limits and field-specific behaviour |
| Local and Production validation | Completed and approved by the Test Lead |
| AB-EV-011 | Public closure decision and permanent regression expectation |

## 6. Test Lead decision

**Passed — QR-06 completed and approved.**

Apply the following Quality Risk Analysis update:

```text
QR-06: Current gap → Regression risk
```

Do not reopen QR-06 as a missing implementation unless new evidence demonstrates that an approved field limit or boundary control has regressed.
