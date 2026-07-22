# QR-05 — Text write-volume investigation

**Evidence ID:** AB-EV-004  
**Evidence status:** **In progress**  
**Related quality risk:** QR-05  
**Classification:** Quality-risk investigation and optimisation evidence template  
**Test Lead decision:** Implementation strategy approved; final QR-05 decision pending  
**Production validation:** Pending  

> This file is a working template. Empty fields and placeholders must not be interpreted as zero, Passed or Not Applicable.

## 1. Scope

**Behaviour under investigation:**  
General Note text editing persisted every intermediate `onChange` value directly to Firestore.

**Reason for measurement:**  
Evaluate and reduce unnecessary Firestore writes while preserving immediate local feedback, inline autosave and the final persisted value.

**Included fields:**  
General Note.

**Excluded fields:**  
Other text-editing fields remain outside this recorded baseline until they are measured explicitly.

## 2. Environment

| Item | Value |
|---|---|
| Branch | To be recorded |
| Commit or build before | To be recorded |
| Commit or build after | To be recorded |
| Local or deployed environment | To be recorded |
| Firebase project or Emulator | To be recorded |
| Browser and version | To be recorded |
| Device or resolution | To be recorded |
| Language | To be recorded |
| Test-data state | Sanitised QA data only |
| Measurement date | To be recorded |

## 3. Controlled sample text

**Sample-text identifier:** To be recorded  
**Sanitised sample text:** To be recorded  
**Character count:** To be measured  

The public sample must use artificial QA content and must not contain a real memory, note, biography, location, e-mail address, username or personal information.

## 4. Measurement results

### Baseline measurement

| Field | Characters | Local state updates | Writes before | Writes after | Final value correct | Evidence |
|---|---:|---:|---:|---:|---|---|
| General Note | 10 | 10 | 10 | — | Yes | Verified baseline summary; public command output pending |
| General Note | 20 | 20 | 20 | — | Yes | Verified baseline summary; public command output pending |

The before-optimisation baseline showed one Firestore write for each typed character. After-optimisation measurements remain pending.

## 5. Local state updates

- Local state updates observed: 10 updates for 10 typed characters and 20 updates for 20 typed characters.
- Method used to distinguish local state updates from Firestore writes: Verified measurement produced separate local-update and Firestore-write counts.
- Unexpected rerenders or repeated save triggers: No conclusion recorded yet.
- Relevant limitation: The supplied baseline covers General Note only.

## 6. Firestore writes before optimisation

- Measurement source: Verified baseline measurement supplied by the implementation investigation.
- General Note, 10 typed characters: 10 Firestore writes.
- General Note, 20 typed characters: 20 Firestore writes.
- Observed relationship: one write per typed character.
- Retries or duplicate writes beyond the direct `onChange` behaviour: No additional conclusion recorded.
- Confidence in the measurement: Baseline accepted for implementation planning.

## 7. Firestore writes after optimisation

- Measurement source: To be recorded
- Writes per field: To be measured
- Writes for the complete controlled sample: To be measured
- Retries or duplicate writes: To be recorded
- Confidence in the measurement: Result not verified

## 8. Reduction

- Absolute reduction: To be calculated after verified after-optimisation measurements.
- Percentage reduction: To be calculated only when the denominator is explicit and meaningful.
- Baseline interpretation: The existing implementation persisted every intermediate text value.
- Test Lead acceptance of optimisation result: Pending retest and Production validation.

No numerical improvement may be claimed until both before-and-after measurements have been verified under comparable conditions.

## 9. Root cause

The General Note `onChange` handler invoked the persistence mutation directly.

The per-country mutation queue serialised operations but did not coalesce intermediate text values. As a result, each typed character generated its own Firestore write.

## 10. Approved save strategy

**Selected strategy:** Inline autosave with a 750 ms debounce.

Required behaviour:

- immediate local feedback while typing;
- persistence after 750 ms of inactivity;
- immediate flush on blur;
- isolation of pending saves by country;
- cancellation of pending saves on logout;
- cancellation on account change;
- cancellation on account deletion.

**Decision rationale:** Preserve the current inline-autosave experience while reducing intermediate writes and preventing a pending save from crossing account or lifecycle boundaries.  
**Data-loss protection:** Blur flush and final-value verification are mandatory.  
**Failure feedback:** To be verified during implementation retest.  

## 11. Behavioural validation

### Debounced autosave decision

- Expected trigger: One persistence operation after 750 ms without further input.
- Immediate local feedback: Required.
- Observed trigger: To be recorded after implementation.
- Duplicate or premature writes: To be measured.
- Result: Not executed.

### Blur behaviour

- Pending value flushed immediately on blur: Not executed.
- Final value saved on blur: Not executed.
- Empty or unchanged value behaviour: Not executed.
- Rapid focus change: Not executed.
- Error handling: Not executed.

### Navigation behaviour

- Navigate within the application after editing: Not executed
- Navigate before the debounce interval or explicit save: Not executed
- Browser back or route change: Not executed
- Final persisted value: Result not verified

### Logout and account-lifecycle behaviour

- Logout after editing: Not executed.
- Pending save cancelled on logout: Not executed.
- Pending save cancelled on account change: Not executed.
- Pending save cancelled on account deletion: Not executed.
- Per-country isolation preserved: Not executed.
- Private cache cleanup preserved: Not executed.
- Final persisted value after later login: Result not verified.

## 12. Errors and resilience

- Firestore rejection: Not executed
- Network interruption: Not executed
- Retry behaviour: Not executed
- Duplicate submission: Not executed
- Sanitised user feedback: Not executed
- Rollback or retained local value: Not executed

## 13. Tests added or updated

| Test or check | Purpose | Status | Result |
|---|---|---|---|
| To be recorded | Write-count or save-trigger validation | Planned | Not executed |
| To be recorded | Final-value persistence | Planned | Not executed |
| To be recorded | Blur, navigation or logout protection | Planned | Not executed |
| To be recorded | Regression of existing text behaviour | Planned | Not executed |

## 14. Pipeline

| Gate | Before change | After change | Evidence |
|---|---|---|---|
| Targeted functional validation | Not recorded | Not recorded | Pending |
| Automated tests | Not recorded | Not recorded | Pending |
| TypeScript | Not recorded | Not recorded | Pending |
| ESLint | Not recorded | Not recorded | Pending |
| Build | Not recorded | Not recorded | Pending |
| Version-control checks | Not recorded | Not recorded | Pending |

A failed gate remains failed even if the Test Lead later accepts a limited exception.

## 15. Local retest

- Build or commit: To be recorded
- Fields covered: To be recorded
- Browser and environment: To be recorded
- Before-and-after measurement repeated: Not executed
- Final value verified: Result not verified
- Regression identified: To be recorded
- Test Lead conclusion: Pending

## 16. Production smoke

- Deployment or commit: To be recorded
- Production measurement required: To be decided
- Essential text-editing smoke: Not executed
- Final persisted value: Result not verified
- Error or data-loss behaviour: Not executed
- Test Lead decision: Pending

## 17. Limitations

Record any limitation involving:

- measurement accuracy;
- Firebase tooling;
- Emulator versus real-project behaviour;
- browser instrumentation;
- sample size;
- field differences;
- debounce timing;
- background writes;
- cache behaviour;
- Production observability.

Current limitation: the verified baseline covers General Note only. The implementation, after-optimisation measurements, failure behaviour, pipeline, local retest and Production smoke remain incomplete.

## 18. Final Test Lead decision

**Readiness:** Implementation in progress  
**Formal decision:** The 750 ms inline-autosave strategy is approved for implementation; the quality result remains pending evidence.  
**QR-05 status recommendation:** Pending implementation, after-optimisation measurement and Production validation.  
**Required follow-up:** Complete the implementation, repeat the 10- and 20-character measurements, verify blur flush and lifecycle cancellation, review the pipeline, execute local retest and complete Production smoke as applicable.
