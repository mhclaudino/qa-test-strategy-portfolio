# AB-EV-030 — Profile map to earned-flag navigation

**Evidence status:** Production approved  
**Related quality risks:** QR-26, QR-33, QR-39, QR-40  
**Target release:** AtlasBadge V1.0  
**Final status:** C32 FIXED / PRODUCTION PASS

## 1. Evidence purpose

This record documents the C32 public-profile usability refinement that connects the read-only travel map to the existing **Bandeiras Conquistadas** grid.

The goal was to help a profile visitor identify a map location without introducing any editing capability or changing the profile's read-only contract.

## 2. Requirement

When a visitor clicks a conquered place on the Profile map, AtlasBadge must:

1. resolve the clicked place by canonical `countryId`;
2. locate the exact earned-flag card for the same `countryId`;
3. scroll smoothly to that card;
4. position the card in the viewport;
5. apply a temporary visual highlight;
6. remove the highlight automatically;
7. leave persisted travel data unchanged.

If the clicked place has no earned flag, no incorrect card may be highlighted and no mutation may occur.

## 3. Implementation design

The canonical Profile map already supported `onCountryClick` for normal geographies and micro-markers.

C32 connected that existing event to the public profile and added deterministic DOM identity to earned flag cards through:

```text
data-badge-country-id="<countryId>"
```

The handler locates the flag by `countryId`, calls native smooth `scrollIntoView` with centered block positioning and applies a temporary highlight for approximately two seconds.

The implementation does not depend on:

- translated country names;
- grid position;
- `nth-child` selectors;
- alphabetical ordering;
- visit-order position.

## 4. Read-only rule

The Profile remains read-only.

The map click used by C32 does not:

- open the CountryActionCard;
- add or remove statuses;
- modify visits;
- write Firestore data;
- change local travel data;
- change counters;
- change achievements;
- create a virtual UK flag;
- change flag sorting.

A click on the Profile map means only: **navigate to the matching earned flag if one exists**.

## 5. Sorting behaviour

The feature was validated with both supported flag orders:

```text
Alfabética
Ordem de Visita
```

Because navigation resolves the card through `countryId`, reordering the grid does not change the target identity.

## 6. Micro-marker behaviour

C32 reuses the same map callback path for micro-markers introduced and stabilised in C27.

The dedicated Playwright coverage includes San Marino as a micro-marker case and verifies that clicking the marker resolves the corresponding earned flag without changing the C27 marker scale, hit-target or zoom behaviour.

## 7. No-flag fallback

The dedicated E2E scenario clicks a place without an earned flag and verifies that:

- no unrelated card receives the highlight;
- no edit UI opens;
- the page remains read-only;
- the interaction fails silently rather than inventing a destination.

## 8. Mobile behaviour

The C32 E2E suite includes a 375 × 667 mobile viewport.

The scenario validates the map click/tap path, earned-flag resolution and temporary highlight in the responsive profile layout.

The feature was also approved by the Test Lead in local manual validation before deployment.

## 9. Automated coverage

Permanent coverage was added through:

```text
e2e/profile-map-flag-navigation.spec.ts
src/components/CountryBadgeGrid.test.tsx
```

The component coverage verifies deterministic `countryId` identity and preservation across both sorting modes.

The E2E coverage verifies:

- normal geography navigation;
- temporary highlight and timeout;
- switching to a second country;
- micro-marker navigation;
- no-flag fallback;
- read-only behaviour;
- visit-order sorting;
- alphabetical sorting;
- mobile viewport behaviour.

## 10. Quality gates

The C32 pre-commit QA closure recorded:

```text
TypeScript: PASS
Vitest: 304 tests / 41 files PASS
ESLint: PASS
Next.js production build: PASS
C32 Playwright E2E: PASS
flag-sort regression: PASS
```

The E2E flows were executed with the Firebase Auth/Firestore/Storage emulators where required.

## 11. Implementation traceability

The approved C32 implementation was published as:

```text
c75891b68836ca495e319c31878c3cb230f4f92e
feat(profile): navigate map clicks to earned flags
```

The commit changed:

- `src/app/[username]/page.tsx`;
- `src/components/CountryBadgeGrid.tsx`;
- `src/components/CountryBadgeGrid.test.tsx`;
- `e2e/profile-map-flag-navigation.spec.ts`.

## 12. Production validation

The automatic GitHub-to-Vercel deployment reached `READY` on the Production application.

Post-deployment validation included:

- Production Guard / target verification;
- `validate-production.spec.ts` — PASS;
- `validate-clear-map.spec.ts` — PASS;
- Production verification of normal geography navigation;
- second-country targeting;
- micro-marker targeting;
- no-flag fallback;
- temporary highlight removal;
- both flag sorting modes;
- mobile behaviour;
- continued read-only behaviour;
- C27 regression;
- C31 `252 / 195 / 57` counter regression.

The operational C32 report identified the automatic Vercel deployment and `READY` status but did not preserve a literal deployment ID. This public record therefore does not invent one.

## 13. Test Lead decision

**C32 FIXED / PRODUCTION PASS.**

The feature is accepted as a read-only usability improvement to the public Profile. The existing privacy, map-interaction, sorting, micro-marker and counter baselines remain under regression coverage.

## 14. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-030 |
| Related risks | QR-26, QR-33, QR-39, QR-40 |
| Predecessor map/profile evidence | AB-EV-028 |
| Product implementation | `c75891b68836ca495e319c31878c3cb230f4f92e` |
| Permanent E2E | `e2e/profile-map-flag-navigation.spec.ts` |
| Production validation | Production Guard; `validate-production` PASS; `validate-clear-map` PASS |
| Result | C32 FIXED / PRODUCTION PASS |
