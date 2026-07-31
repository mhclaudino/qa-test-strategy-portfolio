# AB-EV-015 — QR-13 Immediate Username Reuse Accepted Behaviour

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-015 |
| Related quality risks | QR-13; affected-area regression for QR-07, QR-11 and QR-12 |
| Product | AtlasBadge V1.0 |
| Evidence type | Product-decision confirmation and permanent automated regression |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-07-31 |
| Final decision | Passed — Accepted behaviour confirmed |

## 2. Purpose

This evidence formalises the existing AtlasBadge rule for a username that is released after a profile change.

It also records the containment audit performed after an incorrect alias-based implementation instruction was stopped.

## 3. Official product rule

When a user changes username:

1. the previous canonical reservation is released;
2. the previous username becomes immediately available;
3. another UID may register it;
4. no historical alias is created;
5. no redirect to the previous owner is created;
6. after a new owner reserves the released username, its public URL resolves to that new owner;
7. the original account remains available under its current username;
8. a casing-only edit remains QR-11 normalisation and does not release the reservation.

The possibility that a previously shared URL may later represent a different account is an explicitly accepted product consequence.

## 4. Containment and integrity audit

A local safety branch was created before the audit.

The protected baseline included:

- QR-11 runtime commit `b7718892a184fbda80df86ab82a1c768347dcba5`;
- regression-mock adjustment `d648a84`.

The audit result was:

```text
No incorrect changes found
```

Repository search confirmed that no implementation of the rejected policy was introduced:

- no `usernameAliases` collection;
- no previous-username alias document;
- no historical redirect;
- no username-history field;
- no alias cleanup in account deletion;
- no Firebase Rules change for aliases;
- no migration script for aliases.

The QR-11 runtime remained unchanged.

## 5. Permanent automated validation

A dedicated test suite was committed:

```text
src/lib/profile.qr13.test.ts
```

Commit:

```text
751e3f6 — test(profile): cover immediate username reuse
```

| ID | Scenario | Result |
|---|---|---|
| QR13-UT-01 | User A changes `oldname` to `newname`. | Passed — `oldname` is released. |
| QR13-UT-02 | User B reserves `oldname` immediately. | Passed — reservation is permitted. |
| QR13-UT-03 | User A remains associated with `newname`. | Passed — UID and profile data are preserved. |
| QR13-UT-04 | `oldname` is looked up after User B reserves it. | Passed — lookup resolves to User B. |
| QR13-UT-05 | Username change is inspected for alias creation. | Passed — no alias document is created. |
| QR13-UT-06 | Username change is inspected for historical redirect creation. | Passed — no redirect is created. |
| QR13-UT-07 | Only casing is changed. | Passed — the reservation is not released. |
| QR13-UT-08 | Username transaction fails. | Passed — previous username remains owned. |
| QR13-UT-09 | Multiple users compete for the released reservation. | Passed — only one transaction wins. |
| QR13-UT-10 | Account deletion removes the current primary reservation. | Passed. |
| QR13-UT-11 | Account deletion is retried. | Passed — behaviour remains idempotent. |
| QR13-UT-12 | Invalid username input is supplied. | Passed — existing QR-11 validation remains active. |

Execution summary:

- focused QR-13 tests: **12/12 Passed**;
- QR-11 integrity tests: **14/14 Passed**;
- full suite after QR-13: **121/121 Passed**;
- directed ESLint validation: **Passed**;
- global lint: **Passed without a new QR-13 violation**;
- Next.js production build: **Passed**;
- repository search for alias implementation: **no results**;
- working tree and `origin/main`: aligned after push.

## 6. Deployment position

Commit `751e3f6` adds permanent automated coverage only.

No QR-13 runtime behaviour was changed, so a new functional deployment or Production smoke was not required to adopt this evidence decision.

The behaviour remains the same runtime rule exercised by the already-approved username transaction and public-profile flows.

## 7. Risk acceptance

QR-13 remains **Accepted behaviour**.

This does not mean the consequence is impossible or harmless. It means the Product Owner knowingly accepts that:

- an old shared URL may later resolve to another account;
- AtlasBadge does not preserve historical ownership;
- AtlasBadge does not maintain alias or redirect records;
- users should treat a released username as no longer controlled by the previous owner.

The risk must be reconsidered if the product later introduces:

- verified identities;
- paid or organisational accounts;
- permanent share links;
- username-based trust indicators;
- anti-impersonation guarantees;
- historical redirects or aliases.

## 8. Traceability

```text
QR-13
→ previous username released immediately
→ no alias
→ no redirect
→ another UID may reserve the released value
→ case-only change remains QR-11 normalisation
→ incorrect alias instruction stopped
→ no incorrect runtime changes found
→ 12 permanent automated tests
→ test commit 751e3f6
→ 121-test full regression
→ AB-EV-015
→ QR-13 Accepted behaviour
```

## 9. Final conclusion

The immediate-reuse rule is implemented as intended, protected by permanent regression tests and explicitly accepted by the Product Owner.

No alias, historical reservation or redirect implementation is required for AtlasBadge V1.0.
