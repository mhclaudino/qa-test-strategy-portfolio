# AtlasBadge V1.0 Test Evidence

This directory contains selected test evidence produced during the quality validation of AtlasBadge V1.0.

The evidence is organised by testing purpose rather than by test tool.

## Directory structure

```text
evidence/
└── v1.0/
    ├── environments/
    ├── smoke/
    ├── regression/
    ├── defects/
    └── production-reset/
```

## Evidence categories

### `environments`

Evidence related to the environments, browsers, devices, resolutions and configurations used during testing.

Examples may include:

* browser and device information;
* confirmed browser versions;
* desktop resolution evidence;
* mobile orientation evidence;
* environment configuration checks;
* Firebase Emulator verification;
* Playwright environment verification.

### `smoke`

Evidence from production and release smoke testing.

Examples may include:

* post-deployment smoke results;
* desktop browser smoke;
* mobile smoke;
* language smoke in `pt-BR` and `en-GB`;
* confirmation that essential flows remain available after deployment.

### `regression`

Selected evidence from quick regression, affected-area regression and final V1.0 regression.

Examples may include:

* regression execution summaries;
* screenshots of validated critical flows;
* browser and device coverage;
* language coverage;
* retest evidence following corrections.

### `defects`

Selected examples of defects identified, investigated, corrected and retested.

Evidence may include:

* defect descriptions;
* expected and observed results;
* screenshots;
* affected environment;
* severity or priority;
* correction summaries;
* retest results;
* regression decisions.

This directory is not intended to contain every defect identified during the project. Evidence is selected according to its professional and portfolio value.

### `production-reset`

Evidence related to the final removal of all test accounts and test data before the official V1.0 launch.

Evidence may include:

* Firebase Authentication cleanup;
* Cloud Firestore cleanup;
* Firebase Storage cleanup;
* removal of test usernames and public profiles;
* final disposable-account smoke;
* confirmation that the product starts empty;
* confirmation that cache or fallback mechanisms do not repopulate data;
* Test Lead approval of the clean-start state.

## Evidence standards

Evidence should provide enough context to understand:

* what was tested;
* which environment was used;
* the expected result;
* the observed result;
* whether the test passed, failed or remained inconclusive;
* any defect, limitation or residual risk identified;
* any retest or regression performed.

Screenshots should be accompanied by a short textual explanation when the result is not self-explanatory.

## Privacy and security

Only evidence suitable for publication may be committed to this repository.

Evidence must not expose:

* passwords;
* authentication tokens;
* API keys;
* private environment variables;
* `.env` files;
* session cookies;
* personal user data;
* real user accounts;
* sensitive Firebase or Vercel configuration;
* information that could compromise the product or its users.

Names, email addresses, usernames, project identifiers and other potentially sensitive information must be removed or masked when necessary.

Disposable test accounts and artificial test data should be used wherever possible.

## Evidence selection

Not every item produced during testing must be published.

The Test Lead selects evidence that:

* demonstrates the testing approach;
* supports important quality decisions;
* provides useful traceability;
* illustrates defect management and retesting;
* demonstrates release readiness;
* is safe and appropriate for a public professional portfolio.

The responsible technical role may adjust the directory structure when implementation needs require it, provided that traceability, privacy, clarity and Test Lead review are preserved.
