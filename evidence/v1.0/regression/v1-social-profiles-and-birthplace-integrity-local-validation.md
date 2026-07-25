# V1 Social Profiles and Birthplace Integrity — Local Validation

## Evidence metadata

| Field | Value |
|---|---|
| Product | AtlasBadge |
| Release | V1.0 |
| Evidence type | Affected-area regression, security rules validation and concurrency testing |
| Validation stage | Local validation before Production deployment |
| Tested commit | `72e7672d1b0681f0f2eee2b648eb8ee9ee487920` |
| Commit message | `feat(v1): finalize social profiles and birthplace integrity` |
| Test Lead conclusion | Local Approved — Production Validation Pending |
| Evidence date | 24 July 2026 |

## Purpose

This evidence records the local quality assessment of a V1.0 refinement package covering two risk-sensitive areas:

1. the public and editable social-profile model; and
2. the integrity of the single birthplace status, labelled **Born there / Nasci lá**.

The validation focused not only on whether automated checks passed, but also on whether the tests could prove the intended behaviour for the correct reason. The package was not considered approved until false-positive risks, concurrency behaviour, security-rule coverage, encoding noise and staged-diff quality had been resolved.

## Scope

### Social profiles

The package was expected to enforce the following public order:

1. Instagram
2. Facebook
3. X
4. LinkedIn

GitHub was removed from:

- onboarding;
- the Edit Profile form;
- public-profile rendering;
- the supported user-profile write model.

GitHub remains only as the final static social link in the application Footer.

### Footer

The expected Footer order was:

1. Instagram
2. Facebook
3. X
4. LinkedIn
5. GitHub

The required version text was:

`AtlasBadge v1.0`

### Birthplace integrity

The package was expected to ensure that:

- a user can have only one birthplace;
- the `born` status and `birthplacePlaceId` pointer are written atomically;
- selecting the same place again is idempotent;
- selecting a second place is rejected;
- removing the current birthplace clears the status and pointer together;
- unrelated statuses, visits and memories remain unchanged;
- concurrent requests cannot create two winners;
- no partial write remains after a rejected request;
- inconsistent legacy states are detected and blocked rather than silently repaired.

## Quality risks addressed

| Risk | Potential impact | Validation response |
|---|---|---|
| Two places saved as birthplace | Corrupted profile and inconsistent public map | Transactional implementation, Emulator concurrency test and Firestore Rules |
| Pointer and place status diverge | Broken reads, blocked updates or incorrect profile data | Bidirectional consistency rules and atomic removal tests |
| GitHub accepted in new profiles | Data model contradicts V1.0 UI and public-profile requirements | Dedicated create and update Rules tests |
| Negative Rules tests pass for the wrong reason | False confidence in security coverage | Valid positive-control batch and isolated negative fixtures |
| Concurrent requests leave partial data | Orphaned place or pointer | Two-client Emulator test and final-state assertions |
| Test-file encoding churn hides real changes | Review noise and increased regression risk | File restored to baseline and minimally reconstructed |
| New lint suppressions hide quality issues | Reduced maintainability and misleading validation | New suppressions rejected and helper fully typed |

## Test environment

| Area | Environment |
|---|---|
| Application code | Local repository on branch `main` |
| Firestore integration | Firebase Emulator |
| Firestore security | Firebase Rules Emulator suite |
| Static type validation | TypeScript `tsc --noEmit` |
| Code quality | Targeted ESLint on all package TypeScript and TSX files |
| Production compatibility | Next.js production build |
| Production deployment | Not executed during this evidence stage |

## Evidence summary

| Validation set | Result |
|---|---:|
| V1 refinement tests | 30 passed, 0 failed |
| Firestore Rules tests | 152 passed, 0 failed, 0 skipped |
| Existing defect regression tests | 35 passed, 0 failed |
| Targeted ESLint | 0 errors, 0 warnings |
| TypeScript | Passed |
| Next.js production build | Passed |
| Generated routes | 11 of 11 |
| Package files | 14 |
| Working tree after commit | Clean |

## V1 refinement test coverage

The dedicated refinement suite covered:

- eight deterministic birthplace-state classifications;
- Portuguese and English conflict-message generation;
- removal of browser-language dependency;
- public social-network order;
- GitHub absence from public and editable profile surfaces;
- Footer order and version text;
- two-client concurrent birthplace selection;
- exactly one accepted concurrent request;
- winner-pointer consistency;
- loser-place integrity;
- idempotent repeat selection;
- atomic birthplace removal;
- successful selection of a different place after removal.

Result:

`30 passed / 0 failed`

## Firestore Rules coverage

The final Rules suite included:

- a valid atomic user-and-username creation control without GitHub;
- seven valid creation batches differing only by the prohibited GitHub value:
  - valid HTTPS string;
  - empty string;
  - `null`;
  - number;
  - boolean;
  - map;
  - list;
- confirmation that rejected batches leave neither the user document nor the username reservation;
- normal social-link updates for the four supported networks;
- rejection of newly introduced GitHub values;
- temporary compatibility for an unchanged legacy GitHub value;
- permitted removal of a legacy GitHub value;
- atomic birthplace creation;
- rejection of `born=true` without a matching pointer;
- rejection of a pointer without a matching `born=true` place;
- atomic birthplace removal;
- rejection of arbitrary pointer changes;
- rejection of isolated pointer removal while the matching `born` status remains.

Result:

`152 passed / 0 failed / 0 skipped`

## Important Test Lead interventions

### False-positive detection in GitHub create tests

The first negative GitHub-create tests attempted to create only a user document. The Rules also required an atomic username reservation, which meant those tests could fail because the fixture itself was incomplete rather than because GitHub was prohibited.

The Test Lead rejected the original evidence.

The corrected suite introduced:

- a reusable valid user-and-username creation batch;
- a positive control proving the batch succeeds without GitHub;
- seven negative batches that differ only by `socialLinks.github`;
- post-failure checks proving no partial user or username document remained.

This changed the evidence from “the request failed” to “the request failed specifically because GitHub was prohibited.”

### Dynamic test-count reconciliation

The Rules suite originally used a loop containing five social networks. Removing GitHub from the loop removed eight runtime cases, while explicit social and birthplace tests were added.

The final reconciliation distinguished static declarations from runtime-expanded cases and prevented an incorrect test-count claim.

### Concurrency acceptance criteria

The concurrency test was required to accept either request as the winner. It proved:

- exactly one success;
- exactly one rejection;
- the final pointer matches the winner;
- only the winner has `born=true`;
- the loser has no partial birthplace write;
- repeating the winner is idempotent;
- removal clears the pointer;
- the former loser can be selected afterwards.

### Encoding and diff-quality review

A command-line rewrite created noisy encoding changes in the Rules test file. Although the suite continued to run, the staged diff contained unrelated rewritten lines.

The Test Lead rejected the noisy diff.

The file was restored from the committed baseline and only the intended functional changes were reapplied. This produced a minimal, reviewable diff without altering pre-existing Unicode, emoji or boundary-value coverage.

### Lint suppression rejection

Two new `eslint-disable` comments were temporarily introduced around an `any`-typed test helper.

The Test Lead rejected the suppressions.

The helper was rewritten using the existing Firestore test database type and `Record<string, unknown>`, resulting in:

- no new `any`;
- no new lint suppression;
- zero targeted ESLint errors or warnings.

## Regression and build results

| Check | Result |
|---|---|
| Existing defect suite | 35 passed, 0 failed |
| Targeted ESLint | Passed with no findings |
| TypeScript | Passed |
| Next.js build | Compiled successfully |
| Static-page generation | 11/11 |
| Diff whitespace check | Passed |
| Commit contents | 12 modified files and 2 new files |
| Repository state | Clean after local commit |

The global lint command continued to report pre-existing debt in files outside this package. Those findings were not hidden or modified as part of this scope.

## Defects identified during validation

No new product defect remained open at the end of local validation.

The principal findings were **test-quality and evidence-quality defects**:

1. negative GitHub-create tests could pass for an unrelated username requirement;
2. the reported Rules test-count reconciliation was initially mathematically inconsistent;
3. an automated text rewrite introduced encoding and diff noise;
4. new lint suppressions weakened the credibility of the test helper.

All four findings were corrected and revalidated before local approval.

## Exclusions and limitations

This evidence does not claim:

- Production deployment;
- Production smoke completion;
- browser-based end-to-end validation of the package;
- active dynamic language switching;
- repair of legacy birthplace inconsistencies;
- final V1.0 release readiness.

The application runtime remained Portuguese (`pt-BR`) for this package. English message generation was prepared and unit-tested for future use, but no language selector or locale provider was introduced.

Legacy test data was disposable. An intermediate reset of Authentication, Firestore and Storage was completed after the local commit, but it was intentionally excluded from the final-production-reset evidence because further V1.0 corrections and additional test data are still expected.

## Residual risk

| Risk | Status |
|---|---|
| Production behaviour differs from Emulator/local build | Pending Production validation |
| Browser rendering order differs from structural assertions | Pending manual Production validation |
| Legacy inconsistent birthplace data remains | Mitigated by disposable test data and pre-release reset |
| Existing global lint debt outside this package | Accepted as pre-existing technical debt |
| Dynamic English UI behaviour | Outside this package |

## Test Lead conclusion

The package met the local exit conditions for the affected scope.

The approval was based on:

- correct functional behaviour;
- transactional data integrity;
- concurrency protection;
- security-rule enforcement;
- isolated negative test fixtures;
- absence of partial writes;
- preserved regression coverage;
- clean targeted lint and type validation;
- successful production build;
- minimal and auditable commit contents.

**Decision:** Local Approved — Production Validation Pending.

The record should be updated after the commit is pushed, the Vercel deployment is Ready, the Firestore Rules are published and the affected flows are manually validated in Production.
