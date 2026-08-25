# AB-DEF-015 — Public achievement metadata was not synchronised

**Severity:** High  
**Priority:** P0 during release hardening  
**Target release:** AtlasBadge V1.0  
**Final status:** Closed / Production approved  
**Evidence:** AB-EV-033

## Summary

After the public Profile stopped reading the private user document, earned achievement chronology could exist privately without an equivalent sanitised `achievementMetadata` projection under `publicProfiles/{uid}`.

The private source was correct, but the public Profile could fail to render an earned achievement because its public source was incomplete.

## Expected result

Achievement chronology required for public presentation must be projected to the public profile without exposing private root data.

Each public achievement entry is limited to:

```text
unlockedAt
sequence
```

## Observed result

An earned achievement could exist in private state while the public Profile lacked the corresponding public `achievementMetadata`, causing the public presentation to omit valid achievement information.

## Root cause

The achievement reconciler maintained the private chronology but did not consistently update the new sanitised public-profile projection introduced by the public/private source split.

## Correction

The reconciler was updated so that private and public achievement metadata are maintained together when the public root exists.

The public projection is generated from the private source but contains only the approved `{unlockedAt, sequence}` fields.

No private profile fields are copied into the public root as part of this operation.

## Validation

Focused metadata and public-profile regression passed after the correction.

Final Production validation confirmed public `achievementMetadata` as PASS and found no forbidden private fields in the public projection.

## Traceability

| Item | Reference |
|---|---|
| Defect | AB-DEF-015 |
| Evidence | AB-EV-033 |
| Related risks | QR-30, QR-31, QR-32, QR-33 |
| Product/security commit | `276b0c9` — `feat(wishlist): add public wishlist and secure profile projections` |
| Final release SHA | `7bbdb9402145523f6a2f36d41cc74e55479cc664` |
| Production result | PASS |
