# AB-EV-027 — United Kingdom coverage and geographic counter integrity

**Evidence status:** Production approved historical record  
**Related quality risks:** QR-18, QR-25, QR-29  
**Target release:** AtlasBadge V1.0  
**Final status:** C26-specific behaviour Passed in Production; the follow-up QR-25 gap recorded here was subsequently closed by AB-EV-029 / C31

## 1. Evidence purpose

This record documents the clarification and correction of AtlasBadge country, territory/entity and United Kingdom coverage semantics during C26, plus the later Production observation that triggered the C31 counter-integrity audit.

The selected evidence originally examined the relationship between:

- the 251 directly selectable places;
- the conceptual 195-country total;
- the then-current territory/entity progress metric;
- the four selectable United Kingdom constituent nations;
- the United Kingdom country credit and achievement rule.

This document intentionally preserves the historical intermediate interpretation. The canonical model accepted later is documented in [AB-EV-029 / AB-DEF-012](../defects/ab-ev-029-geographic-counter-integrity-252-195-57.md).

## 2. Catalogue audit at C26

The C26 audit confirmed:

```text
Directly selectable records: 251
Selectable sovereign-country records: 194
United Kingdom aggregate: non-selectable map-only parent
United Kingdom constituents: 4 selectable constituent-country records
Conceptual country total: 195
Territory/entity denominator used at that stage: 56
```

The United Kingdom aggregate is not persisted as its own selectable travel-status record.

England, Scotland, Wales and Northern Ireland are selectable independently.

## 3. C26 denominator correction

An earlier implementation counted only selected entity classifications and produced a public denominator of 52.

The C26 rule required the territory/entity metric to include the four United Kingdom constituents. The denominator was therefore corrected to 56 at that stage.

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

The `1/195` country result matched the C26 UK rule, but `3/56` did not.

The incorrect numerator was caused by treating country and territory/entity progress as mutually exclusive partitions and subtracting the derived United Kingdom country credit from the four constituent records.

## 5. C26-specific rule

Each United Kingdom constituent independently contributes to territory/entity progress.

Completing all four additionally satisfies the virtual United Kingdom country and achievement rule.

| UK constituents with qualifying presence | Territory/entity contribution | Country contribution |
|---:|---:|---:|
| 0 | 0 | 0 |
| 1 | 1 | 0 |
| 2 | 2 | 0 |
| 3 | 3 | 0 |
| 4 | 4 | 1 (United Kingdom) |

At the C26 stage, the accepted specific behaviour for all four constituents was:

```text
selectable-place contribution = 4
territories/entities contribution = 4
country contribution = 1
United Kingdom achievement = eligible
```

This specific rule remains valid. What changed later was the definition of the global conceptual Places and Territory/Entity totals.

## 6. C26 final correction

The territory/entity visited numerator was changed to use canonical category membership rather than subtracting country progress from place progress.

### Final correction

```text
754fd9be91fc82d7382a802eb8e2ace391c45326
fix(coverage): count all UK constituents as visited entities
```

## 7. Regression coverage at C26

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
- the then-current 251-place/56-entity presentation;
- continent progress;
- world-completion logic;
- achievement chronology and metadata.

Local technical gates passed before deployment.

## 8. Production validation

The final C26–C30 release was deployed from:

```text
a8d1d3ec150f9c8fd73263b1e08e558f572ca722
```

Production validation confirmed the narrow C26 rule:

```text
4 UK constituents
→ 4 territory/entity contributions
→ +1 United Kingdom country
→ United Kingdom badge remains correct
```

`validate-production` and `validate-clear-map` both passed on the same Production baseline.

## 9. Follow-up Production observation

After C26 had passed its intended Production smoke, the Test Lead identified a new cross-counter consistency observation:

```text
9/251 places visited
5/195 countries visited
5/56 territories and entities
```

The visible conquered set contained nine directly selectable places, while the country and territory/entity counters summed to ten.

This did **not** invalidate the narrow C26 verification that all four UK constituents contribute to the territory/entity metric and collectively credit the United Kingdom country. It did prove that the broader global counter model was not yet internally coherent.

At that point, QR-25 was correctly reopened as a **Current gap** and no new arithmetic assumption was accepted without a complete classification audit.

## 10. Subsequent C31 closure

The follow-up investigation was completed in C31 and is documented by [AB-EV-029 / AB-DEF-012](../defects/ab-ev-029-geographic-counter-integrity-252-195-57.md).

C31 established the final product rule:

```text
251 directly selectable records
252 conceptual Places
195 Countries
57 Territories and Entities
```

with the invariant:

```text
252 = 195 + 57
```

The model adds one derived UK Place when all four UK constituents are complete and includes Antarctica in the 57-item Territory/Entity universe.

The original `9/5/5` scenario therefore becomes:

```text
10/252 Places
5/195 Countries
5/57 Territories and Entities
```

C31 was implemented in:

```text
627f4f948f7b6af0151fec77ffe800380e73e989
fix(stats): align world coverage counters
```

and passed local QA, Production Guard, `validate-production` and `validate-clear-map` before Test Lead sign-off.

## 11. Test Lead decision

**C26 remains Passed for its specific UK constituent rule. The follow-up QR-25 Current gap recorded by this historical evidence is now closed by C31 / AB-EV-029 and retained as Regression risk.**

This document is deliberately preserved because it shows the investigation path from a locally correct rule to a broader cross-counter inconsistency and then to the final audited model.

## 12. Traceability

| Item | Reference |
|---|---|
| Historical evidence | AB-EV-027 |
| Follow-up closure | AB-EV-029 / AB-DEF-012 |
| Related risks | QR-18, QR-25, QR-29 |
| C26 denominator correction | `71fc58df0e1ba96074308850a38a54a5cff04d2c` |
| C26 numerator correction | `754fd9be91fc82d7382a802eb8e2ace391c45326` |
| C26–C30 Production baseline | `a8d1d3ec150f9c8fd73263b1e08e558f572ca722` |
| C31 correction | `627f4f948f7b6af0151fec77ffe800380e73e989` |
| Historical result | C26 Passed; QR-25 reopened after `9/5/5` observation |
| Current result | QR-25 closed as gap and retained as Regression risk through AB-EV-029 |
