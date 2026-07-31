# AB-EV-014 — QR-11 Username Case-Normalisation Production Validation

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-014 |
| Related quality risks | QR-11; regression coverage for QR-12 and QR-13 |
| Product | AtlasBadge V1.0 |
| Evidence type | Runtime correction, permanent automated regression and Production smoke |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-07-31 |
| Final decision | Passed — QR-11 moved to Regression risk |

## 2. Purpose

This evidence records the closure of the username case-normalisation assessment gap.

The validated product contract is that usernames are case-insensitive for reservation, profile updates, public-profile lookup and generated URLs.

The change does not redefine the separate allowed-character policy.

## 3. Canonical rule

The shared canonical identity is:

```ts
username.trim().toLowerCase()
```

Consequently:

- `AtlasUser`, `atlasuser` and `ATLASUSER` represent the same username;
- leading and trailing spaces are ignored;
- Firestore reservations use the canonical lowercase key;
- public-profile lookup normalises the route value;
- generated profile links use lowercase;
- a change that modifies casing only is a normalisation/no-op;
- a different UID cannot reserve a casing variant of an occupied username.

## 4. Technical traceability

| Area | Reference |
|---|---|
| Shared normalisation and transactional username update | `src/lib/profile.ts` |
| Public-profile route lookup | `src/app/[username]/page.tsx` |
| Profile-edit input and save flow | `src/components/ProfileEditModal.tsx` |
| Permanent automated coverage | `src/lib/profile.qr11.test.ts` |
| Runtime commit | `b7718892a184fbda80df86ab82a1c768347dcba5` — `fix(auth): enforce canonical username case normalization for QR-11` |
| Regression-mock adjustment | `d648a84` — `test(sync): fix mock for subscribeToUserPlaces` |

The runtime correction also handles a legacy reservation whose document ID contains uppercase characters. A casing-only edit can migrate that reservation to its canonical lowercase key without creating a collision or losing the owning UID.

## 5. Permanent automated validation

| ID | Scenario | Result |
|---|---|---|
| QR11-UT-01 | `AtlasUser` normalises to `atlasuser`. | Passed |
| QR11-UT-02 | External spaces are removed. | Passed |
| QR11-UT-03 | Availability lookup with different casing resolves the same reservation. | Passed |
| QR11-UT-04 | A casing variant of an existing username is rejected. | Passed |
| QR11-UT-05 | Concurrent reservations with different casing allow only one owner. | Passed |
| QR11-UT-06 | A casing-only change is treated as a no-op or canonical migration. | Passed |
| QR11-UT-07 | A genuinely different username is reserved and the former reservation is released correctly. | Passed |
| QR11-UT-08 | A failed username change preserves the previous state. | Passed |
| QR11-UT-09 | Public-profile lookup with mixed casing resolves the same profile. | Passed |
| QR11-UT-10 | Internally generated links use the lowercase canonical URL. | Passed |
| QR11-UT-11 | Account deletion removes the canonical reservation. | Passed |
| QR11-UT-12 | A different UID cannot use a reserved casing variant. | Passed |
| QR11-UT-13 | The owner is not treated as an external collision when checking its own username. | Passed |
| QR11-UT-14 | Existing minimum-length and allowed-character controls remain unchanged. | Passed |

Execution summary:

- focused QR-11 tests: **14/14 Passed**;
- full suite at the final QR-11 checkpoint: **109/109 Passed**;
- clean `npm ci` reproduction: **Passed**;
- directed ESLint validation: **Passed**;
- global lint: **Passed without a new QR-11 violation**;
- Next.js production build: **Passed**;
- `git diff --check`: **Passed**;
- `package.json` and `package-lock.json` remained unchanged;
- HEAD and `origin/main` were aligned after push.

## 6. Production validation

The Test Lead confirmed the Production deployment containing the QR-11 runtime correction and the final regression-mock adjustment.

| ID | Scenario | Result | Observation |
|---|---|---|---|
| QR11-PS-01 | Canonical lowercase profile URL opens. | Passed | Correct profile loaded. |
| QR11-PS-02 | Mixed-case and uppercase route variants are used. | Passed | All variants resolved to the same UID and profile. |
| QR11-PS-03 | AtlasBadge generates a profile link. | Passed | Link used the canonical lowercase username. |
| QR11-PS-04 | Username is saved with casing only changed. | Passed | Reservation and profile data remained intact. |
| QR11-PS-05 | A second controlled account tries a casing variant of an occupied username. | Passed | Collision was rejected. |
| QR11-PS-06 | A controlled account changes to a free username entered with uppercase characters. | Passed | Stored identity and public URL were canonical lowercase. |
| QR11-PS-07 | Map, visits, profile and authentication data are checked after username changes. | Passed | Existing user data remained associated with the same UID. |
| QR11-PS-08 | Original username and controlled test state are restored. | Passed | Temporary reservation was released and no orphan was observed. |

No real account identifier, private profile content, authentication value or raw screenshot is included in this public record.

## 7. Risk decision

QR-11 is no longer an `Assessment gap`.

It is retained as **Regression risk** because a future profile or authentication change could:

- bypass the shared canonical function;
- reserve a casing variant under another UID;
- generate a non-canonical public URL;
- resolve route casing inconsistently;
- reintroduce a legacy uppercase reservation;
- break account deletion or concurrent-reservation integrity.

## 8. Traceability

```text
QR-11
→ case-insensitive username decision
→ shared trim + lowercase normalisation
→ canonical Firestore reservation
→ canonical public-profile lookup
→ legacy uppercase reservation migration
→ 14 permanent automated tests
→ runtime commit b7718892a184fbda80df86ab82a1c768347dcba5
→ regression adjustment d648a84
→ Production smoke
→ original test state restored
→ AB-EV-014
→ QR-11 Regression risk
```

## 9. Final conclusion

Username creation, change, reservation, lookup and generated links use the same case-insensitive canonical identity.

QR-11 is approved for AtlasBadge V1.0 and remains under permanent regression coverage.
