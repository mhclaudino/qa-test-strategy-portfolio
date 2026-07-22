# QR-02 — Visit-history preservation

**Evidence ID:** AB-EV-002  
**Evidence status:** Public narrative complete  
**Related quality risk:** QR-02  
**Classification:** Quality-risk investigation and permanent regression coverage  
**Target release:** AtlasBadge V1.0  

## 1. Evidence purpose

This record documents the investigation of the risk that removing and later restoring the `Visited` status could lose visits, memories or related information.

It separates:

- the original quality risk;
- the behaviour of the current V1.0 data model;
- incompatible disposable legacy test data;
- a rejected technical approach;
- the regression protection added after investigation.

It does not present the rejected implementation as a production escape or claim that a production defect was corrected when no current-model failure was reproduced.

## 2. Original risk

The original risk anticipated that a travel-status transition could remove or reset:

- registered visits;
- visit identifiers;
- visit durations;
- visit notes;
- general notes;
- related memories.

The consequence would have been loss of user-created travel history and reduced confidence in status transitions.

## 3. Investigation outcome

The feared behaviour was not reproduced in the current AtlasBadge V1.0 model.

Investigation confirmed that the current model already preserved:

- `registeredVisits`;
- visit IDs;
- durations;
- visit notes;
- `generalNote`;
- related current-model information required to restore the journey.

No functional Production correction was required for the current V1.0 behaviour.

## 4. Legacy test-data limitation

Only an older count-only test-data model was incompatible with the current registered-visit model.

That legacy data:

- belonged to pre-V1.0 testing;
- was disposable;
- was not treated as official or customer data;
- did not require migration because the approved V1.0 launch process includes a complete Production data reset.

The legacy limitation must not be reported as evidence that the current V1.0 model loses valid registered history.

## 5. Rejected implementation and revert

An unsuitable implementation attempted to fabricate registered visits from legacy count-only data.

The approach was rejected during technical review before Test Lead approval because generated visit records would have created data that had never actually existed.

Relevant Git history:

| Commit | Purpose |
|---|---|
| `f6cc331` | Unsuitable implementation that attempted to manufacture visit records from legacy count-only data |
| `322d15b` | Explicit revert of the rejected implementation |
| `482fd51` | Added permanent QR-02 coverage through `scripts/testQR02.ts` |
| `771bf6c` | Cleaned up the QR-02 test implementation |

The explicit revert preserved the Git history and demonstrates that the quality process prevented an inappropriate change from being accepted.

The rejected implementation is not classified here as:

- a Production defect;
- an escaped defect;
- a user-facing correction;
- evidence of current-model data loss.

## 6. Regression protection

The reusable suite `scripts/testQR02.ts` was added to protect the approved current-model behaviour.

The regression intent includes preservation of:

- registered visit records;
- visit identifiers;
- durations;
- visit and general notes;
- related information across the applicable status transition;
- the absence of fabricated migration records.

## 7. Quality-gate reporting

The recorded QR-02 pipeline outcome must remain precise:

| Gate or activity | Recorded result |
|---|---|
| Product validation | Passed |
| Functional and technical QR-02 tests | Passed |
| TypeScript | Passed |
| Build | Passed |
| Whitespace checks | **Failed — Exception Accepted** |
| ESLint | Passed with warnings, or the exact tool result retained in the operational record |
| Overall pipeline | Not fully green |
| Push decision | Authorised by the Test Lead under a limited exception |

The accepted whitespace exception did not convert the failed gate into a pass and did not change the product-validation result.

## 8. Local and Production validation

Local QA approved the current V1.0 preservation behaviour.

Production validation also approved the user-visible behaviour. Because no current-model functional failure was reproduced, no Production behaviour change was necessary.

## 9. Test Lead decision

The Test Lead approved:

- the current V1.0 preservation behaviour;
- rejection and explicit revert of the unsuitable implementation;
- the absence of a legacy-data migration;
- permanent regression coverage through `scripts/testQR02.ts`;
- progression without a functional Production correction.

The Quality Risk Analysis status is intentionally not changed by this evidence task.

## 10. Residual risks and limitations

- QR-02 must remain in permanent regression coverage.
- Legacy count-only data is not supported as a migration source.
- The public record does not include raw account data, visit content or private command logs.
- Future changes to status normalisation, visits, memories or persistence require impact-based re-execution.

## 11. Public evidence

Suitable public evidence includes:

- the verified narrative in this file;
- the four relevant commit references;
- sanitised output from `scripts/testQR02.ts`;
- sanitised quality-gate output;
- a concise Test Lead decision record.

Screenshots are not required when the command, commit, environment, result and decision are sufficiently clear.

## 12. Private or unpublished material

The following must remain private unless separately sanitised:

- real or personally identifiable account data;
- memory and note content;
- complete database documents;
- raw AI-assisted investigation logs;
- source excerpts beyond what is necessary to explain the quality decision.
