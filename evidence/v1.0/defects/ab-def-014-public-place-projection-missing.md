# AB-DEF-014 — Public place projection missing for normal public statuses

**Severity:** High  
**Priority:** P0 during release hardening  
**Target release:** AtlasBadge V1.0  
**Final status:** Closed / Production approved  
**Evidence:** AB-EV-033

## Summary

After AtlasBadge moved public-profile rendering to the sanitised `publicProfiles` source, a normal public travel status could be correctly persisted under the owner's private place document while the corresponding public place projection was missing.

The defect was discovered during affected-area regression rather than from the Wishlist happy path itself.

## Expected result

When a place has a status that is intentionally public and the profile is eligible for public presentation, the equivalent `publicProfiles/{uid}/places/{placeId}` projection must exist and contain only the approved public fields.

Anonymous and non-owner viewers must be able to render the public state without reading `users/{uid}` or `users/{uid}/places/**`.

## Observed result

The private owner state was correct, but the public projection could be absent. As a result, the public Profile could omit valid earned-place information.

## Root cause

Public-place synchronisation had been implemented too narrowly around the newer Wishlist path. The architecture now required the public projection to be maintained consistently for all supported public statuses, not only Wishlist-specific mutations.

## Correction

Projection synchronisation was generalised for the supported public statuses.

Private and public changes are kept in the same persistence operation where applicable. Public-only records are removed when no public state remains, while mixed public documents preserve only legitimate public fields.

The projection remains sanitised and does not copy private travel payloads wholesale.

## Validation

Focused projection regression passed, including normal public-status creation/removal, Wishlist privacy boundaries and concurrency-sensitive persistence.

Final Production validation confirmed:

- anonymous/viewer Profile rendering: PASS;
- private reads by viewer: `0`;
- public source: `publicProfiles/{uid}`;
- forbidden public place fields found: NO.

## Traceability

| Item | Reference |
|---|---|
| Defect | AB-DEF-014 |
| Evidence | AB-EV-033 |
| Related risks | QR-01, QR-31, QR-32, QR-33, QR-34 |
| Product/security commit | `276b0c9` — `feat(wishlist): add public wishlist and secure profile projections` |
| Final release SHA | `7bbdb9402145523f6a2f36d41cc74e55479cc664` |
| Production result | PASS |
