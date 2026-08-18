# AB-EV-020 — United Kingdom coverage and geographic counter integrity

**Evidence status:** Production approved  
**Related quality risks:** QR-18, QR-25, QR-29  
**Target release:** AtlasBadge V1.0  
**Final status:** Passed in Production

## 1. Evidence purpose

This record documents the clarification and correction of AtlasBadge country, territory/entity and United Kingdom coverage semantics.

The selected evidence demonstrates the relationship between:

- the 251 selectable places;
- the conceptual 195-country total;
- the territory/entity progress metric;
- the four selectable United Kingdom constituent nations;
- the United Kingdom country credit and achievement rule.

## 2. Catalogue audit

The selectable catalogue was audited rather than changing the displayed totals by hardcoding a new number.

The confirmed model is:

```text
Selectable places: 251
Selectable sovereign-country records: 194
United Kingdom aggregate: non-selectable map-only parent
United Kingdom constituents: 4 selectable constituent-country records
Conceptual country total: 195
Territories and entities total: 56
```

The United Kingdom aggregate is not persisted as its own selectable travel-status record.

England, Scotland, Wales and Northern Ireland are selectable independently.

## 3. Denominator correction

An earlier implementation counted only selected entity classifications and produced a public denominator of 52.

The audited product rule requires the territory/entity metric to represent the selectable records that belong to that progress category, including the four United Kingdom constituents.

The corrected public denominator is therefore:

```text
56 territories and entities
```

The value is derived from the catalogue model rather than introduced as a standalone magic number.

### Relevant correction

```text
71fc58df0e1ba96074308850a38a54a5cff04d2c
fix(coverage): count UK constituents as territory entities
```

## 4. Numerator defect found after denominator correction

A later manual Test Lead check identified a separate numerator defect.

When all four United Kingdom constituents were selected, the application displayed:

```text
4/251 places
1/195 countries
3/56 territories and entities
```

The `1/195` country result was correct, but `3/56` was not.

The incorrect numerator was caused by treating country and territory/entity progress as mutually exclusive partitions.

Conceptually, subtracting the United Kingdom country credit from the four constituent places produced three entities.

That model does not match the approved product rule.

## 5. Canonical rule

The country and territory/entity metrics can overlap.

Each United Kingdom constituent independently contributes to territory/entity progress.

Completing all four additionally satisfies the virtual United Kingdom country and achievement rule.

Therefore:

| UK constituents with qualifying presence | Territory/entity contribution | Country contribution |
|---:|---:|---:|
| 0 | 0 | 0 |
| 1 | 1 | 0 |
| 2 | 2 | 0 |
| 3 | 3 | 0 |
| 4 | 4 | 1 (United Kingdom) |

For all four constituents:

```text
places contribution = 4
territories/entities contribution = 4
country contribution = 1
United Kingdom achievement = eligible
```

This means the visual country and territory/entity counters are not required to partition the places counter.

## 6. Final correction

The territory/entity visited numerator was changed to use the canonical category membership rather than subtracting country progress from place progress.

### Final correction

```text
754fd9be91fc82d7382a802eb8e2ace391c45326
fix(coverage): count all UK constituents as visited entities
```

## 7. Regression coverage

Coverage included:

- England only;
- Scotland only;
- Wales only;
- Northern Ireland only;
- two constituents;
- three constituents;
- all four constituents;
- United Kingdom achievement rule;
- 195-country total;
- 251-place total;
- 56 territory/entity total;
- continent progress;
- world-completion logic;
- achievement chronology and metadata.

Local technical gates passed before deployment.

## 8. Production validation

The final C26–C30 release was deployed from:

```text
a8d1d3ec150f9c8fd73263b1e08e558f572ca722
```

Production validation confirmed:

```text
4 UK constituents
→ 4/56 territories and entities
→ +1 United Kingdom country
→ United Kingdom badge remains correct
```

`validate-production` and `validate-clear-map` both passed on the same Production baseline.

## 9. Test Lead decision

**Approved for AtlasBadge V1.0.**

The corrected model is now treated as a permanent regression rule:

- 251 selectable places;
- 195 conceptual countries;
- 56 territories and entities;
- four UK constituents count individually in territory/entity progress;
- all four also credit the United Kingdom country and achievement.

## 10. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-020 |
| Related risks | QR-18, QR-25, QR-29 |
| Denominator correction | `71fc58df0e1ba96074308850a38a54a5cff04d2c` |
| Numerator correction | `754fd9be91fc82d7382a802eb8e2ace391c45326` |
| Final Production baseline | `a8d1d3ec150f9c8fd73263b1e08e558f572ca722` |
| Result | Passed |
| Decision | Approved; retained in regression coverage |
