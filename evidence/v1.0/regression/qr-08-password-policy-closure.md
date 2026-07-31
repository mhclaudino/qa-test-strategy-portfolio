# AB-EV-012 — QR-08 Password-Policy Closure

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-012 |
| Related quality risk | QR-08 |
| Product | AtlasBadge V1.0 |
| Evidence type | Automated regression and security-policy closure |
| Owner | Test Lead/Product Owner |
| Final decision | Passed — QR-08 moved to Regression risk |

## 2. Purpose

This record closes the public documentation gap for the AtlasBadge V1.0 password policy.

The product rule and functional implementation had already been completed. The final QA work added permanent automated coverage and reconfirmed that the effective Firebase Authentication policy is compatible with the approved rule.

## 3. Approved product rule

AtlasBadge V1.0 uses the following password policy:

- password-based credentials require at least **15 characters**;
- passphrases are allowed;
- spaces may be used in a valid passphrase;
- separate uppercase, lowercase, numeric or symbol quotas are not required by the approved product rule.

No real password, credential payload, account identifier, Firebase secret or environment-variable value is included in this public evidence.

## 4. Technical traceability

| Area | Reference |
|---|---|
| Central policy | `src/lib/passwordPolicy.ts` |
| Registration flow | `src/app/login/page.tsx` |
| Google-account password creation and linking | `src/components/auth/AuthMethods.tsx` |
| Password reset flow | `src/app/auth/action/page.tsx` |
| Permanent automated coverage | `src/lib/passwordPolicy.test.ts` |
| Test commit | `d5934ab80301685659c6a870e323288701803959` — `test(auth): cover QR-08 password policy` |

The policy implementation uses a central minimum-length rule. Existing password-based flows use the shared validation contract rather than independent, conflicting limits.

## 5. Automated validation

| ID | Scenario | Result |
|---|---|---|
| QR08-UT-01 | A 14-character password is rejected. | Passed |
| QR08-UT-02 | A password with exactly 15 characters is accepted. | Passed |
| QR08-UT-03 | A password longer than 15 characters is accepted. | Passed |
| QR08-UT-04 | A valid passphrase containing spaces is accepted. | Passed |
| QR08-UT-05 | An empty password is rejected. | Passed |
| QR08-UT-06 | Password-confirmation mismatch is rejected where confirmation applies. | Passed |
| QR08-UT-07 | A Firebase policy rejection is converted into the expected safe validation result. | Passed |
| QR08-UT-08 | A valid Firebase policy response completes successfully. | Passed |
| QR08-UT-09 | An unexpected Firebase error is handled without exposing credentials or configuration. | Passed |

Execution summary:

- focused QR-08 tests: **9/9 Passed**;
- full Vitest suite at the QR-08 checkpoint: **81/81 Passed**;
- directed ESLint validation: **Passed**;
- Next.js production build: **Passed**;
- staged change contained only `src/lib/passwordPolicy.test.ts`;
- HEAD and `origin/main` were aligned after push;
- no `.env.local`, temporary verifier, log, password or Firebase credential was committed.

## 6. Firebase policy confirmation

The effective Firebase Authentication password policy was confirmed by the Test Lead after the automated environment could not access the authorised console session.

The confirmed backend policy is compatible with:

- the 15-character minimum;
- valid passphrases;
- the absence of an additional composition requirement that would contradict the AtlasBadge rule.

The public portfolio intentionally omits Firebase configuration values and screenshots containing project or account details.

## 7. Deployment position

Commit `d5934ab80301685659c6a870e323288701803959` adds automated test coverage only.

No runtime product behaviour was changed by this commit, so an additional functional Production smoke was not required for the QR-08 test-only change.

## 8. Risk decision

QR-08 is no longer a `Current gap`.

It is retained as **Regression risk** because future authentication changes could:

- reduce the minimum length;
- reject valid passphrases;
- apply a different rule in a new password-based flow;
- diverge between frontend validation and Firebase enforcement;
- expose credentials through logs, screenshots or error handling.

## 9. Traceability

```text
QR-08
→ approved 15-character minimum
→ passphrases and spaces allowed
→ central password-policy implementation
→ all existing password-based flows audited
→ 9 permanent automated tests
→ Firebase policy confirmed by Test Lead
→ commit d5934ab80301685659c6a870e323288701803959
→ AB-EV-012
→ QR-08 Regression risk
```

## 10. Final conclusion

The QR-08 implementation, automated coverage and effective backend policy are approved for AtlasBadge V1.0.

No additional implementation, deployment or smoke action remains open for QR-08.
