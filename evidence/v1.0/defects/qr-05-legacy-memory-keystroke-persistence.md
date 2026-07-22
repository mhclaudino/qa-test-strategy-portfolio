# QR-05 — Legacy memory editor persisted text on every keystroke

**Evidence ID:** AB-EV-004  
**Evidence status:** Production approved  
**Related quality risk:** QR-05  
**Classification:** Product defect and regression risk  
**Correction commit:** `e73fad9` — `fix(memories): require explicit save for non-visit notes`  
**Target release:** AtlasBadge V1.0  
**Test Lead decision:** Approved for AtlasBadge V1.0  

## 1. Evidence purpose

This record documents the verified AtlasBadge V1.0 defect in which a legacy memory editor persisted non-visit memory text on every keystroke.

It records:

- the affected user journey;
- the official explicit-save product rule;
- the observed legacy behaviour;
- the rejected debounce proposal;
- the final correction;
- automated and structural coverage;
- manual local QA;
- Production smoke;
- the Test Lead decision.

This public record does not present `generalNote` as a separate user-facing V1.0 feature. The field remains an internal compatibility mechanism for memories attached to countries without a registered physical visit.

## 2. Affected journey

AtlasBadge allows memories to be associated with any travel status.

For countries without a registered physical visit, the application stores the memory internally through `generalNote`.

The affected journey applied to statuses without physical presence, including examples such as:

- `Want to visit`;
- `Nationality`;
- other applicable non-visit statuses.

The user-facing requirement is an explicit-save memory workflow, regardless of the internal storage field used.

## 3. Expected behaviour

The official AtlasBadge V1.0 rule is:

- typing changes only the local draft;
- no persistence occurs while the user is typing;
- selecting **Save** performs the persistence operation;
- navigating away without saving may discard the draft;
- no autosave is used;
- no debounce is used;
- no artificial `RegisteredVisit` is created;
- a memory without a visit does not change `visitsCount` or **Total Visits**;
- the existing `VisitEditor` continues to use explicit Save.

This behaviour makes the persistence action deliberate and visible to the user.

## 4. Observed legacy behaviour

A legacy memory editor was still presented for statuses without a physical visit.

In that path:

- the text field invoked `updateGeneralNote` directly from `onChange`;
- each text change triggered persistence;
- typing therefore generated a persistence operation for each keystroke;
- the behaviour contradicted the explicit-save model defined for AtlasBadge V1.0.

The defect was in the active legacy editor path exposed by the interface, not in the continued existence of the internal `generalNote` field.

## 5. Root cause

The non-visit memory journey still used an older editor implementation.

That implementation connected the text field's `onChange` event directly to the persistence mutation rather than maintaining a local draft until user confirmation.

The internal `generalNote` field was still valid for model compatibility, but the editor did not apply the approved V1.0 interaction rule.

## 6. Rejected debounce proposal

The initial investigation considered preserving the legacy autosave behaviour and reducing write frequency through a 750 ms debounce.

The Test Lead rejected that proposal after validating the real product requirement.

The proposal was rejected because:

- autosave was not the approved interaction model;
- debounce would optimise an incorrect journey rather than correct it;
- navigation and user confirmation semantics would remain inconsistent with the rest of the memory-editing experience;
- the product already required explicit Save.

No public evidence should describe debounce, autosave or a reduction from a fixed number of writes to one as the approved QR-05 correction.

## 7. Final correction

Commit:

```text
e73fad9 fix(memories): require explicit save for non-visit notes
```

The final correction replaced the legacy persistence path with:

- immediate local draft updates while typing;
- an unsaved-change indicator;
- an explicit **Save** button;
- persistence only after user confirmation;
- continued use of `generalNote` internally for compatibility;
- no artificial visit creation;
- no change to `visitsCount` or **Total Visits** for non-visit memories.

The correction aligned non-visit memories with the official V1.0 explicit-save rule instead of introducing debounce.

## 8. Automated and structural coverage

The verified technical pipeline completed with:

| Check | Result |
|---|---|
| Firestore Rules | 70/70 passed |
| `testStatusMutation.ts` | Passed |
| `testQR02.ts` | Passed |
| `testQR03.ts` | Passed |
| `testQR05.ts` | Passed as a structural regression check |
| ESLint | Passed |
| TypeScript | Passed |
| Production build | Passed |

`scripts/testQR05.ts` is a structural regression check.

It must not be described as:

- eighteen behavioural interface tests;
- complete automated validation of the user journey;
- a replacement for manual browser QA.

The full user-interface behaviour was validated manually by the Test Lead.

## 9. Manual local QA

Manual local validation confirmed:

- memories remained available for any applicable status;
- typing changed only the local draft;
- leaving without selecting Save did not persist the draft;
- selecting Save persisted the memory;
- refreshing with `F5` restored the saved value;
- applicable status transitions preserved `generalNote`;
- no artificial visit was created;
- **Total Visits** remained correct;
- `VisitEditor` continued to use explicit Save;
- QR-01, QR-02 and QR-03 regression checks remained satisfactory.

Artificial and disposable QA content was used.

## 10. Production smoke

The corrected journey was deployed and validated in Production.

Production smoke confirmed:

- the explicit-save behaviour for non-visit memories;
- no persistence during typing;
- successful persistence after selecting Save;
- restoration of saved content after refresh;
- preservation across applicable status transitions;
- no artificial visit creation;
- correct **Total Visits** behaviour;
- continued explicit-save behaviour in `VisitEditor`.

The Test Lead approved the Production result.

## 11. Test Lead decision

**Approved for AtlasBadge V1.0.**

The correction:

- removed the active legacy per-keystroke persistence behaviour;
- restored the official explicit-save workflow;
- preserved model compatibility;
- avoided artificial visit records;
- preserved visit calculations;
- passed the applicable technical pipeline;
- passed manual local QA;
- passed Production smoke;
- passed the required QR-01, QR-02 and QR-03 regression checks.

QR-05 should become a regression risk during the planned batch update of the Quality Risk Analysis.

## 12. Residual regression risk

Permanent regression coverage should protect:

- local draft behaviour;
- absence of persistence before Save;
- one deliberate persistence action after Save;
- refresh restoration of saved content;
- discard behaviour when navigating without saving;
- preservation of `generalNote` through applicable status transitions;
- absence of artificial `RegisteredVisit` creation;
- unchanged `visitsCount` and **Total Visits** for non-visit memories;
- continued explicit-save behaviour in `VisitEditor`;
- absence of regression in QR-01, QR-02 and QR-03.

Any future cleanup of legacy or dead code is a separate technical-maintenance activity. It does not change the verified product-defect result recorded here.

## 13. Public evidence available

Suitable public evidence includes:

- this verified narrative;
- correction commit `e73fad9`;
- the sanitised technical-pipeline summary;
- the structural role of `scripts/testQR05.ts`;
- the Test Lead local and Production decisions.

No screenshot is required merely to prove every successful step when the commit, checks, environment, behaviour and decision are recorded clearly.

## 14. Evidence pending sanitisation

The following may remain private unless a later portfolio need justifies sanitisation:

- raw Antigravity implementation logs;
- complete terminal output;
- raw browser screenshots or recordings;
- disposable test-account identifiers;
- memory content;
- complete Firestore documents or payloads;
- private source-code excerpts.

Any future public attachment must use artificial QA content and remove identifiers, private URLs, account information and sensitive configuration.
