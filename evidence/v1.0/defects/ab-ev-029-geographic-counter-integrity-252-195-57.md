# AB-EV-029 / AB-DEF-012 — Geographic counter integrity: 252 / 195 / 57

**Evidence status:** Production approved  
**Related quality risks:** QR-18, QR-25, QR-29, QR-30  
**Target release:** AtlasBadge V1.0  
**Final status:** AB-DEF-012 closed; C31 FIXED / PRODUCTION PASS; QR-25 moved to Regression risk

## 1. Evidence purpose

This record closes the geographic counter-integrity question reopened after AB-EV-027.

The investigation was triggered by a Production state in which nine directly selectable conquered records produced:

```text
9/251 places
5/195 countries
5/56 territories and entities
```

The visible counter relationship was inconsistent because the two category counters summed to ten while the Places counter remained nine. The follow-up investigation therefore audited the catalogue classifications, the derived United Kingdom rule, Antarctica, achievement thresholds and the full-completion arithmetic before accepting any new total.

## 2. Root cause

The previous implementation mixed two different concepts:

- directly selectable catalogue records; and
- conceptual progress records derived from business rules.

The United Kingdom is not directly selectable, but completing England, Scotland, Wales and Northern Ireland earns one conceptual UK country. The previous Places counter did not include that derived UK record.

At the same time, Antarctica is a selectable `special_region` and correctly belongs to the Territory/Entity progress universe under the approved product rule, but the old denominator remained 56.

The result was an internally inconsistent model in which numerators and denominators were derived from different classification assumptions.

## 3. Audited catalogue model

The directly selectable catalogue contains 251 records:

```text
194 sovereign_country
47 territory
5 limited_recognition
4 constituent_country
1 special_region (Antarctica)
--------------------------------
251 directly selectable records
```

The United Kingdom aggregate remains:

```text
non-selectable
map-only / derived
no persisted travel status
```

It is credited only when all four selectable UK constituent countries have qualifying physical presence.

## 4. Canonical progress model

The approved V1.0 progress model is:

```text
Places: 252
Countries: 195
Territories and Entities: 57
```

The totals are defined as follows.

### Countries — 195

```text
194 directly selectable sovereign countries
+ 1 derived United Kingdom
= 195 conceptual countries
```

### Territories and Entities — 57

```text
47 territories
+ 5 limited-recognition entities
+ 4 UK constituent countries
+ 1 special region (Antarctica)
= 57 territories and entities
```

### Places — 252

```text
251 directly selectable records
+ 1 derived United Kingdom
= 252 conceptual Places
```

The required invariant is therefore:

```text
Places = Countries + Territories and Entities
252 = 195 + 57
```

## 5. United Kingdom rule

England, Scotland, Wales and Northern Ireland remain independently selectable records.

Each constituent with qualifying physical presence contributes:

```text
+1 Place
+1 Territory/Entity
```

Completing all four additionally contributes:

```text
+1 derived United Kingdom Country
+1 derived United Kingdom Place
```

No separate persisted `gb` travel-status record is created.

The progression is therefore:

| UK constituents complete | Places contribution | Countries contribution | Territories/Entities contribution |
|---:|---:|---:|---:|
| 0 | 0 | 0 | 0 |
| 1 | 1 | 0 | 1 |
| 2 | 2 | 0 | 2 |
| 3 | 3 | 0 | 3 |
| 4 | 5 | 1 | 4 |

## 6. Antarctica rule

Antarctica is the single selectable `special_region`.

Under the approved product rule it contributes:

```text
+1 Place
+0 Countries
+1 Territory/Entity
```

It therefore participates in the 57-item Territory/Entity universe and in the A32 achievement.

## 7. Original Production scenario after correction

The Test Lead scenario contained these nine directly selectable conquered records:

- Brazil;
- Argentina;
- Portugal;
- Ireland;
- England;
- Scotland;
- Wales;
- Northern Ireland;
- Isle of Man.

Because the four UK constituent countries also derive the United Kingdom, the corrected counters are:

```text
10/252 Places
5/195 Countries
5/57 Territories and Entities
```

The arithmetic is now internally consistent:

```text
5 + 5 = 10
```

Adding Antarctica produces:

```text
11/252 Places
5/195 Countries
6/57 Territories and Entities
```

## 8. Full-completion acceptance values

With all 251 directly selectable records complete and the derived United Kingdom satisfied:

```text
252/252 Places
195/195 Countries
57/57 Territories and Entities
```

No counter may exceed its denominator.

## 9. Achievement alignment

The counter correction was audited against the affected achievement rules.

- **A15 — United Kingdom:** remains based on the four constituent countries, `0/4 → 4/4`.
- **A18 — Lenda Atlas:** complete world coverage is `252/252 Places`.
- **A31 — Mundo Completo:** complete country coverage is `195/195 Countries` and does not require all territories/entities.
- **A32 — Além das Fronteiras:** complete territory/entity coverage is `57/57`, including Antarctica.

The world-explored percentage was aligned to the canonical Places universe so complete coverage resolves to 100%.

## 10. Implementation traceability

The accepted correction was published in AtlasBadge as:

```text
627f4f948f7b6af0151fec77ffe800380e73e989
fix(stats): align world coverage counters
```

The implementation updated the central travel-coverage calculation, dependent statistics/achievement calculations, metadata and regression assertions.

## 11. Validation

Pre-deployment validation included:

- TypeScript compilation;
- Vitest regression, including explicit 252/195/57 and UK-completion assertions;
- ESLint;
- Next.js production build;
- `git diff --check`;
- Test Lead manual local validation.

The C31 implementation report recorded 294 passing Vitest tests across 39 suites at the local sign-off point.

Post-deployment validation included:

- automatic Vercel deployment reaching `READY`;
- Production Guard / Production target verification;
- `validate-production.spec.ts` — PASS;
- `validate-clear-map.spec.ts` — PASS;
- Production verification of the corrected counter model.

The operational report did not preserve a public deployment ID for C31; this portfolio therefore records the verified product commit and Production result without inventing a deployment identifier.

## 12. Test Lead decision

**AB-DEF-012 closed. C31 FIXED / PRODUCTION PASS.**

The previous QR-25 Current gap is closed because the catalogue classification, derived UK behaviour, Antarctica treatment, counter invariant, full-completion values and affected achievements were explicitly defined, automated and validated in Production.

QR-25 remains a **Regression risk** because any future catalogue, classification, achievement or counter change can reintroduce divergence between the 252-, 195- and 57-item universes.

## 13. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-029 |
| Defect | AB-DEF-012 |
| Trigger evidence | AB-EV-027 |
| Related risks | QR-18, QR-25, QR-29, QR-30 |
| Product correction | `627f4f948f7b6af0151fec77ffe800380e73e989` |
| Production counters | `252 / 195 / 57` |
| Production validation | Production Guard; `validate-production` PASS; `validate-clear-map` PASS |
| Result | C31 FIXED / PRODUCTION PASS |
| Risk decision | QR-25 moved from Current gap to Regression risk |
