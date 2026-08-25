# AB-DEF-016 — Mobile dashboard control grid collapsed to zero width

**Severity:** High  
**Priority:** P0 during release hardening  
**Target release:** AtlasBadge V1.0  
**Final status:** Closed / Production approved  
**Evidence:** AB-EV-033

## Summary

Responsive regression found that the first column of the authenticated Map control grid could collapse to zero width on mobile even though the **Atualizar Mapa** heading and controls were still present in the DOM.

This was a product layout defect, not a stale test locator.

## Expected result

At supported mobile widths, the Map controls must remain visible, usable and free from horizontal overflow.

The mobile layout should stack the relevant controls in a single column and preserve the existing two-column desktop composition from the desktop breakpoint.

## Observed result

At a 375 × 667 viewport, the heading existed but Playwright reported it as not visible because its bounding box width was zero. The computed grid resolved effectively to a zero-width first column plus the actions column.

## Root cause

Under Tailwind v4, the intended mobile `max-[639px]:grid-cols-1` rule lost precedence to the arbitrary two-column grid declaration.

The result was a valid DOM structure with an unusable rendered layout.

## Correction

The dashboard grid/actions received explicit semantic classes in the application stylesheet:

- one-column layout by default for mobile;
- two columns from the desktop breakpoint;
- existing desktop behaviour preserved.

## Validation

Post-fix responsive checks confirmed:

- 375 × 667: heading and first column visible;
- 390 × 844: controls visible and usable;
- no horizontal overflow in the validated mobile viewports;
- desktop 1280 layout preserved;
- original mobile sorting regression passed.

Final Production validation recorded desktop PASS, mobile PASS, overflow PASS and Wishlist modal scroll-lock PASS.

## Traceability

| Item | Reference |
|---|---|
| Defect | AB-DEF-016 |
| Evidence | AB-EV-033 |
| Related risks | QR-39, supporting QR-40 |
| Product/security commit | `276b0c9` — `feat(wishlist): add public wishlist and secure profile projections` |
| Final release SHA | `7bbdb9402145523f6a2f36d41cc74e55479cc664` |
| Production result | PASS |
