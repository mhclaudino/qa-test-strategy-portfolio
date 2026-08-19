# AB-EV-028 — Map and profile parity Production validation

**Evidence status:** Production approved  
**Related quality risks:** QR-26, QR-33, QR-39 and supporting QR-25 presentation consistency  
**Target release:** AtlasBadge V1.0  
**Production deployment:** `dpl_J6c7iPBrH9FRy5uAMQJAVHbeguey`  
**Deployed commit:** `a8d1d3ec150f9c8fd73263b1e08e558f572ca722`  
**Final status:** Passed

## 1. Evidence purpose

This record summarises the final Production validation of a group of map and layout corrections completed before the next major AtlasBadge development block.

The release included:

- microterritory marker scaling with map zoom;
- reuse of the canonical dashboard map in the public profile;
- read-only enforcement in the profile map;
- horizontal layout-width parity across Map, Badges and Profile;
- map-surface visual parity between Dashboard and Profile.

The evidence was selected because it demonstrates risk-controlled reuse of a sensitive interactive component rather than maintaining two divergent map implementations.

## 2. C27 — Micro-marker zoom scaling

Small territories and entities represented as micro-markers were geographically anchored correctly, but their visual size remained nearly constant while the map zoomed out.

That made the markers disproportionately large at world-level zoom.

The investigation identified the actual dashboard component and its zoom transform.

The previous counter-scale cancelled the parent zoom completely.

The final rule allows micro-markers to grow progressively from the minimum zoom until approximately `2.0x`, then caps them at the historical maximum visual size.

The hit target remains usable and is separate from the visual marker size.

### Commit

```text
6a8482727159cad9a5e687306d25b37a2afb070c
fix(map): scale micro markers with dashboard zoom
```

### Approved behaviour

- low zoom: smaller markers;
- approximately `2.0x`: historical maximum visual size;
- above `2.0x`: no further visual growth;
- geographic anchor preserved;
- pan, wheel zoom, button zoom and reset preserved.

## 3. C28 — Canonical read-only map in Profile

The previous Profile map had accumulated behavioural divergence:

- inconsistent size;
- different zoom behaviour;
- wheel zoom not working as expected;
- marker behaviour after refresh could diverge;
- map logic was separate from the already validated Dashboard implementation.

Instead of correcting each symptom independently, the Profile was migrated to reuse the canonical `AtlasWorldMapV2`.

### Commit

```text
da0d3c1cebc370e72f1577b33794410a29c167b1
refactor(profile): reuse dashboard map in read-only mode
```

### Profile behaviour retained

- wheel zoom;
- `+` and `-` controls;
- reset;
- pan/drag;
- status colours;
- micro-markers;
- responsive sizing;
- refresh/re-render of profile travel data.

### Read-only controls

The public/profile map does not permit:

- country-status mutation;
- travel-data editing;
- Clear Map.

The Dashboard remains fully interactive.

This directly supports the QR-33 public-profile read-only requirement.

## 4. C29 — Main-tab width parity

Desktop measurement showed different structural content widths:

```text
Map: 896 px
Badges: 1024 px
Profile: 1152 px
```

The three main screens were aligned to the same canonical desktop wrapper.

### Commit

```text
1f7a44bc2a411bf11cf99b0042b80119945a1793
style(layout): align main tab content widths
```

After correction:

```text
Map: 1152 px
Badges: 1152 px
Profile: 1152 px
```

Responsive padding and mobile behaviour were retained.

## 5. C30 — Map surface parity

Even after component reuse and width alignment, the Profile map still used a heavier glass surface while the Dashboard map used the darker canonical application surface.

The divergent Profile-only surface class was removed.

### Commit

```text
a8d1d3ec150f9c8fd73263b1e08e558f572ca722
style(map): unify dashboard and profile surfaces
```

Browser computed-style validation confirmed equivalent:

- background;
- border;
- border radius;
- visual depth.

## 6. Local quality gates

Before publication, the release passed:

| Gate | Result |
|---|---|
| TypeScript | Passed |
| Lint | Passed |
| Production build | Passed |
| Full Vitest regression | Passed |
| Targeted affected-area regression | Passed |
| Test Lead desktop visual validation | Passed |

The affected regression covered the UK calculation, map zoom, micro-markers, profile read-only behaviour, layout and Clear Map.

## 7. Deployment verification

The final release was pushed to `main`.

Git reached:

```text
HEAD = origin/main = a8d1d3ec150f9c8fd73263b1e08e558f572ca722
ahead/behind = 0/0
working tree = clean
```

The corresponding Vercel deployment was independently confirmed:

```text
Deployment: dpl_J6c7iPBrH9FRy5uAMQJAVHbeguey
State: READY
Target: production
Commit: a8d1d3ec150f9c8fd73263b1e08e558f572ca722
```

## 8. Production E2E

The canonical Production command completed successfully.

```text
Production Guard: PASS
validate-production: PASS
validate-clear-map: PASS
```

## 9. Production smoke results

### C27

Passed:

- micro-markers scale down at low zoom;
- size is capped at high zoom;
- geographic anchor remains correct;
- pan, wheel zoom and controls remain functional.

### C28

Passed:

- Profile uses the canonical map;
- zoom, wheel, pan and reset work;
- refresh retains expected markers;
- country mutation is unavailable;
- Clear Map is unavailable.

### C29

Passed:

- Map, Badges and Profile use equivalent main content width;
- no horizontal layout jump was observed;
- responsive padding remained coherent.

### C30

Passed:

- Dashboard and Profile map surfaces are visually equivalent;
- border and radius match;
- the component presents a consistent dark premium appearance.

## 10. Test Lead decision

**C27–C30 approved in Production.**

The final release was accepted after local visual approval, technical gates, verified Vercel deployment, Production E2E and focused Production smoke.

## 11. Residual regression coverage

Permanent regression should continue to protect:

- micro-marker zoom scaling and hit targets;
- geographic anchoring during zoom/pan;
- public-profile read-only enforcement;
- Profile and Dashboard map behaviour parity;
- main-tab width parity;
- surface-token consistency;
- responsive rendering;
- Clear Map remaining exclusive to the authenticated Dashboard.

## 12. Traceability

| Item | Reference |
|---|---|
| Evidence | AB-EV-028 |
| Related risks | QR-26, QR-33, QR-39; presentation support for QR-25 |
| C27 | `6a8482727159cad9a5e687306d25b37a2afb070c` |
| C28 | `da0d3c1cebc370e72f1577b33794410a29c167b1` |
| C29 | `1f7a44bc2a411bf11cf99b0042b80119945a1793` |
| C30 / deployed commit | `a8d1d3ec150f9c8fd73263b1e08e558f572ca722` |
| Deployment | `dpl_J6c7iPBrH9FRy5uAMQJAVHbeguey` |
| Production E2E | Passed |
| Decision | Production approved |
