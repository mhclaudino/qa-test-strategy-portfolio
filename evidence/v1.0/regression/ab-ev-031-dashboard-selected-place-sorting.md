# AB-EV-031 — Dashboard selected-place sorting

**Evidence status:** Production approved  
**Related quality risks:** QR-18, QR-19, QR-39, QR-40  
**Target release:** AtlasBadge V1.0  
**Final status:** C33 FIXED / PRODUCTION PASS

## 1. Evidence purpose

This record documents C33, which added deterministic sorting controls to the selected-place list in the AtlasBadge Map tab without changing persisted travel data.

The feature was requested as a presentation/usability improvement and was validated as a non-mutating view concern.

## 2. Requirement

The Map tab must allow the owner to switch the selected-place list between:

```text
Alfabética
Ordem de Visita
```

The default Map order is **Ordem de Visita**.

The selected Map sort is local/session presentation state. C33 does not persist a new sort preference to Firestore.

## 3. Visit-order semantics

For the dashboard list:

1. qualifying physical-presence records are ordered before non-physical selected records;
2. the canonical Profile visit-order comparator is reused;
3. manually ranked physical records, when present, precede unranked physical records;
4. unranked physical records retain the existing historical chronology fallback;
5. non-physical selected records remain visible in the normal dashboard list but do not invent visit chronology;
6. deterministic alphabetical fallback uses the `pt-BR` display name and `countryId` tie-breaker.

Sorting therefore changes presentation only. It must not modify status, visit, memory, counter, achievement or Firestore state.

## 4. Layout acceptance

Desktop acceptance preserved two rows:

```text
📍 Atualizar Mapa                [ Alfabética | Ordem de Visita ]
[ Buscar país... ] [ Filtros ] [ Limpar filtros ]
```

The layout was also validated responsively so the new toggle did not break the search/filter surface or create horizontal overflow.

## 5. Interaction/data-integrity acceptance

C33 must preserve:

- search state;
- active filters;
- clear-filter behaviour;
- selected-country interaction;
- CountryActionCard behaviour;
- status/visit persistence;
- global counters;
- Profile flag ordering behaviour;
- the C32 map-to-flag navigation path.

The E2E coverage explicitly verifies that sorting and filtering do not mutate the underlying travel record.

## 6. Implementation traceability

Implementation and test-hardening commits:

```text
dcf083084f2480bcedcf8cf72be3f6dd2c26cb3e
feat(profile): sort dashboard places and fix e2e tests for flag navigation

9671434b1ff5ee818abad3678edf45e4f3d304eb
test(dashboard): complete sorting e2e test with data integrity check

5f5c7b86173268a5b9eb3c9cf4a6f4db30b94bc3
test(dashboard): correct selected places filter assertion and locators

0164b8c6f2cb25dc510248b43d39a7d1ca1dc538
chore(e2e): fix typescript any lint errors in dashboard tests
```

The permanent dashboard sorter is implemented through `sortDashboardPlaces` in `src/lib/badgeSorting.ts` and the Map page local sort state.

## 7. Permanent automated coverage

Primary C33 E2E:

```text
e2e/dashboard-selected-places-sorting.spec.ts
```

Related permanent regression included:

```text
e2e/flag-sort.spec.ts
e2e/profile-map-flag-navigation.spec.ts
```

The final C33 focused E2E closure recorded five passing scenarios and zero failures.

## 8. Quality gates

The final C33 baseline recorded:

```text
TypeScript: PASS
Vitest: 302 tests / 39 files PASS
ESLint: PASS
Next.js production build: PASS
git diff --check: PASS
Focused E2E: 5 passed / 0 failed
Test Lead manual validation: PASS
```

## 9. Production validation

Final C33 Production deployment:

```text
dpl_4a94LAa8p1D57y3cHpzNHRGATQMq
Production / READY
SHA: 0164b8c6f2cb25dc510248b43d39a7d1ca1dc538
```

Production closure included:

- Production Guard — PASS;
- `validate-production.spec.ts` — PASS;
- `validate-clear-map.spec.ts` — PASS;
- C31 counter baseline preserved;
- C32 Profile map-to-flag behaviour preserved;
- final Test Lead visual/functional approval.

## 10. Risk decision

C33 did not receive a defect ID because it was an approved product enhancement rather than a failure against an existing accepted requirement.

It extends regression protection for:

- QR-18 / QR-19 — displayed totals and visit data must not be altered by presentation sorting;
- QR-39 — responsive/touch layout;
- QR-40 — interaction/control regression scope.

**C33 FIXED / PRODUCTION PASS.**

## 11. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-031 |
| Related risks | QR-18, QR-19, QR-39, QR-40 |
| Main implementation | `dcf083084f2480bcedcf8cf72be3f6dd2c26cb3e` |
| Final C33 baseline | `0164b8c6f2cb25dc510248b43d39a7d1ca1dc538` |
| Production deployment | `dpl_4a94LAa8p1D57y3cHpzNHRGATQMq` — READY |
| Permanent E2E | `e2e/dashboard-selected-places-sorting.spec.ts` |
| Production validation | Production Guard; validate-production PASS; validate-clear-map PASS |
| Result | C33 FIXED / PRODUCTION PASS |
