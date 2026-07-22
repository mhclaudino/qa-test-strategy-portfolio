# [AB-DEF-001] Country status selection is not applied or persisted

**Evidence ID:** AB-EV-001  
**Evidence status:** Public narrative complete; selected supporting artefacts pending sanitisation  
**Classification:** Product defect  
**Severity:** High  
**Priority:** P0 — Immediate  
**Related quality risk:** QR-01  
**Target release:** AtlasBadge V1.0  

## 1. Evidence purpose

This record provides a sanitised public account of the critical country-status persistence failure, its release impact, confirmed technical cause, correction, retesting, regression and Test Lead decision.

It is a selected portfolio artefact rather than the complete operational defect record. Private implementation details, identifiers, complete payloads and unredacted logs are deliberately excluded.

## 2. Environment

The failure and correction were assessed across the applicable AtlasBadge V1.0 workflow, including:

- local development validation;
- Firebase Rules and persistence-related technical validation;
- the integrated deployed application;
- Vercel Production smoke testing.

The exact detection date, original affected commit and correction commit were not supplied for this public evidence summary and are therefore not invented here.

## 3. Detection phase

The issue was identified during incremental V1.0 quality validation of the primary travel-status journey.

Because the essential country-status action was unavailable and the user received no failure feedback, the issue became an immediate P0 correction and release-readiness blocker.

## 4. Observed behaviour

When a user selected a country status:

- the button received the click;
- no visible status change was applied;
- no persistence occurred;
- no error message or recovery feedback was presented.

The failure affected the primary AtlasBadge journey for recording travel status.

## 5. Expected behaviour

A valid status action should:

- update the interface consistently;
- submit a valid owned record;
- persist the intended status;
- remain correct after refresh or later reconstruction;
- provide trustworthy feedback when persistence fails;
- avoid leaving the interface in a false saved state.

## 6. Release impact

The failure blocked the central travel-recording journey and made continued release progression unreliable.

The quality state remained Red until:

- the P0 condition was removed;
- direct retesting completed;
- required regression completed;
- the corrected build was deployed in a controlled manner;
- Production smoke passed;
- the Test Lead accepted the evidence.

## 7. Confirmed root cause

The confirmed failure chain was:

1. the persistence payload did not contain `countryId`;
2. the applicable Firestore Rules rejected the write;
3. an empty `catch` path suppressed the error;
4. the interface gave the user no useful failure feedback.

This record describes the cause without publishing the complete private payload or unnecessary source code.

## 8. Correction summary

The correction introduced:

- a valid country identifier in the persistence flow;
- optimistic mutation for immediate user feedback;
- rollback when persistence fails;
- sanitised error feedback;
- Firestore Rules coverage;
- mutation-related automated coverage.

Regression also identified secondary map and avatar issues. Those behaviours were corrected and included in the required validation before Production approval.

## 9. Direct retest coverage

The direct retest covered the material corrected behaviour:

- selecting a valid status;
- visible optimistic state;
- successful persistence;
- reconstruction after refresh or later access;
- failed-write rollback;
- sanitised user feedback;
- consistency for the applicable new and existing test-account states.

Detailed private execution notes may contain more scenarios than this selected public summary.

## 10. Regression coverage

Affected-area regression included:

- the interactive map;
- selected country state;
- avatar or profile presentation affected by the change;
- counters and dependent presentation where applicable;
- repeat status interaction;
- persistence-related integration behaviour.

The defect remains part of permanent critical-path regression for V1.0.

## 11. Technical checks

The correction added or updated:

- Firestore Rules tests;
- mutation tests;
- applicable persistence validation.

Exact raw command output is not attached to this public record. Any future public copy must include only verified results and must remove private paths, identifiers and configuration.

## 12. Production smoke

The corrected behaviour was deployed and validated in Vercel Production.

Production smoke confirmed the primary status journey and the necessary affected-area behaviour. The Test Lead approved the corrected Production result.

## 13. Test Lead decision

The corrected behaviour was approved after:

- confirmed root-cause investigation;
- direct retesting;
- affected-area regression;
- technical validation;
- controlled deployment;
- Production smoke.

The P0 blocking condition was removed. This decision does not eliminate the need for permanent regression protection.

## 14. Residual risks

Residual considerations include:

- status persistence remains a release-critical regression area;
- failed-write behaviour must continue to be validated;
- the exact correction commit must be added only after verified confirmation;
- other write flows require their own risk-based coverage and are not automatically proven by this result.

## 15. Available public evidence

The public portfolio may safely contain:

- this verified narrative;
- sanitised test summaries;
- sanitised command results;
- a verified commit or deployment reference when confirmed;
- cropped screenshots that show only the intended status result or sanitised error behaviour.

## 16. Evidence still private or pending sanitisation

The following remain private or pending review:

- original screenshots;
- raw console and network logs;
- complete request or stored payloads;
- Rules debug output containing identifiers;
- private source-code excerpts;
- raw AI-assisted implementation reports;
- any account, username or travel data used during testing.
