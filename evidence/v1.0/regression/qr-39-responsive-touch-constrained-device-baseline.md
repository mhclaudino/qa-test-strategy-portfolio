# AB-EV-018 — QR-39 Responsive, Touch and Constrained-Device Baseline

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-018 |
| Related risks and defects | QR-39; QR-04; QR-01; QR-24; QR-40; AB-DEF-003; AB-DEF-004 |
| Product | AtlasBadge V1.0 |
| Evidence type | Responsive/touch baseline, constrained-condition regression, defect correction and Production validation |
| Owner | Test Lead/Product Owner |
| Execution date | 2026-08-01 |
| Final decision | Passed — QR-39 moved to Regression risk; QR-04 remains Regression risk with extended rapid-mutation coverage |

## 2. Purpose

This evidence establishes the AtlasBadge V1.0 baseline for:

- small-screen reflow;
- portrait and landscape layouts;
- touch interaction;
- virtual-keyboard-sensitive forms;
- slower network conditions;
- temporary loss and recovery of connectivity;
- Chromium CPU throttling;
- production-build CSS delivery;
- Android Chrome Production behaviour.

The work also records two defects found by the Test Lead during physical Android testing:

- **AB-DEF-003:** the application rendered as largely unstyled HTML because cascade-layer CSS was not transformed for the affected browser environment;
- **AB-DEF-004:** rapid same-session status changes triggered a false external-session conflict and could remove the user's latest selection.

Both defects were corrected, covered permanently and approved after controlled deployment and Android retest.

## 3. Approved baseline

### 3.1 Viewports

The automated matrix includes:

| Orientation | Viewport |
|---|---:|
| Portrait | 320 × 568 |
| Portrait | 360 × 800 |
| Portrait | 390 × 844 |
| Portrait | 412 × 915 |
| Landscape | 568 × 320 |
| Landscape | 844 × 390 |
| Landscape | 915 × 412 |
| Tablet portrait | 768 × 1024 |
| Tablet landscape | 1024 × 768 |

### 3.2 Constrained conditions

The covered laboratory conditions include:

- touch-enabled Chromium mobile contexts;
- temporary offline state and recovery;
- Slow 4G-equivalent CDP profile with approximately 150 ms latency, 1.5 Mbps download and 750 Kbps upload;
- Chromium CPU throttling at 4×;
- fresh-cache and reused-cache production-build CSS checks.

These laboratory values are regression controls, not public service-level agreements.

## 4. Initial responsive corrections

The QR-39 implementation introduced small, targeted layout corrections:

| File | Problem | Correction |
|---|---|---|
| `src/components/DeleteAccountModal.tsx` | Content and confirmation input could exceed a low-height mobile viewport. | Constrained the modal with dynamic viewport height and an internally scrollable content area while retaining accessible header/actions. |
| `src/components/StatsCard.tsx` | Long labels could expand a flex item and create page-level horizontal overflow. | Added minimum-width containment and safe word wrapping. |
| `src/app/app/page.tsx` | The search container could force the parent wider than a 320 px viewport. | Added minimum-width containment. |

The initial QR-39 baseline was retained in Git history through `f9a126108d5528abfef1d05963bfe68d02d676dc`.

## 5. AB-DEF-003 — Android CSS compatibility failure

### 5.1 Observed result

During Production smoke on Google Chrome for Android, the Test Lead observed:

- browser-default serif typography;
- unstyled form controls and links;
- oversized AtlasBadge, Google and social SVGs;
- overlapping Home content and map;
- a practically unusable mobile interface.

Representative sanitised evidence is retained in:

- [unstyled Login](../defects/assets/ab-def-003-login-unstyled-android.png);
- [unstyled Home and map](../defects/assets/ab-def-003-home-map-unstyled-android.png);
- [oversized social icons](../defects/assets/ab-def-003-footer-icons-oversized-android.png).

### 5.2 Root cause and correction

The investigation compared generated CSS before and after PostCSS transformation.

The affected build retained untransformed cascade layers. The correction added the cascade-layer transformation to the PostCSS production pipeline so the generated CSS no longer depended on the affected Android browser interpreting those layers directly.

Correction commit:

- `4fdf260e8ca49f346b2366a5e4936c5d8a95f7ef` — `chore: restore PostCSS cascade layers configuration`.

The same physical Android environment that revealed the failure was retested after Production deployment, and the styled layout was approved.

## 6. AB-DEF-004 — Rapid status mutation false conflict

### 6.1 Observed result

Rapid status changes for the same place, in the same tab and session, could:

- remove the visible selection;
- execute an inappropriate rollback;
- show the message that the place had been modified in another session.

Representative sanitised evidence:

- [false external-session conflict](../defects/assets/ab-def-004-false-external-session-conflict.png).

This was incorrect because no independent session had changed the record.

### 6.2 Root cause and correction

The mutation orchestrator previously allowed closely spaced local intents to contend through stale mutation baselines.

The correction introduced a per-scope task queue that coalesces pending same-session work and re-evaluates the latest intent against fresh confirmed state. True stale writes from another browser context remain protected by optimistic concurrency control.

Correction commit:

- `9cdb1808e08213c61940b2308bb253c87eee98fd` — `fix(sync): correct optimistic concurrency race conditions for QR-04`.

Permanent E2E and regression coverage was committed in:

- `eca539ea793a2aadc4be657f0b9dd549f1f04699` — `test(e2e): cover CSS regression and rapid mutations for QR-39/QR-04`.

## 7. Automated validation

### 7.1 QR-39 responsive suite

The responsive-performance suite executed **31/31 Passed** tests across:

- Home;
- Login;
- authenticated application flows;
- all listed portrait, landscape and tablet viewports;
- touch interactions;
- offline and recovery;
- Slow 4G-equivalent network conditions;
- CPU 4× conditions;
- global overflow assertions;
- functional result assertions.

### 7.2 Production-build CSS suite

The CSS compatibility suite executed against `npm run build` and `npm start`:

- **2/2 Playwright tests Passed**;
- 12 explicit controls Passed;
- stylesheet HTTP status: 200;
- stylesheet MIME: correct CSS;
- expected body typography and computed styles: present;
- AtlasBadge, Google and social icons: constrained;
- map: constrained to its container;
- Android fresh context: Passed;
- Android reused-cache context: Passed;
- desktop context: Passed.

### 7.3 Rapid mutation suite

The Firebase Emulator E2E suite executed **11/11 Passed** scenarios:

| ID | Scenario | Result |
|---|---|---|
| STATUS-RACE-01 | Different statuses with no delay. | Passed |
| STATUS-RACE-02 | Different statuses with 25 ms delay. | Passed |
| STATUS-RACE-03 | Three rapid changes with 50 ms delay. | Passed |
| STATUS-RACE-04 | Same status toggled twice rapidly. | Passed |
| STATUS-RACE-05 | A second intent while the first write is pending. | Passed |
| STATUS-RACE-06 | A snapshot between two local intents. | Passed |
| STATUS-RACE-07 | First write failure and deterministic recovery. | Passed |
| STATUS-RACE-08 | Reload parity with Firestore. | Passed |
| STATUS-RACE-09 | No duplicate visit creation. | Passed |
| STATUS-RACE-10 | Counters and memories preserved. | Passed |
| STATUS-CONFLICT-EXT-01 | Genuine conflict from a separate browser context. | Passed |

The suite confirmed:

- no false external-session message for same-session rapid work;
- deterministic final UI state;
- UI and Firestore parity;
- reload persistence;
- no duplicate visits;
- preserved counters and memories;
- genuine external OCC protection remains active.

### 7.4 Full gates

Final release-candidate gates recorded:

- QR-04 mutation-orchestrator tests: **8/8 Passed**;
- QR-04 context tests: **14/14 Passed**;
- QR-11: **14/14 Passed**;
- QR-13: **12/12 Passed**;
- QR-24: **18/18 Passed**;
- focus trap: **11/11 Passed**;
- full Vitest: **150/150 Passed**;
- QR-40 Axe: **15 scans, 0 Critical and 0 Serious**;
- TypeScript: **0 errors**;
- lint: **0 errors**;
- Next.js production build: **Passed**;
- Git working tree: **clean**.

## 8. Controlled release

The release candidate was published to `main` by fast-forward only.

| Field | Value |
|---|---|
| Final Production commit | `eca539ea793a2aadc4be657f0b9dd549f1f04699` |
| Vercel environment | Production |
| Vercel status | Ready |
| Deployment time | 2026-08-01T20:15:30Z |
| Git ahead/behind after push | `0 / 0` |
| Working tree | Clean |

The non-destructive Production smoke confirmed:

- Home HTTP 200;
- Login HTTP 200;
- CSS HTTP 200;
- correct CSS MIME;
- no CSS asset 4xx/5xx responses;
- basic Android layout restored;
- no HTTP 500 or Next.js overlay.

## 9. Test Lead Android retest

The Test Lead repeated the physical Android smoke after the Production deployment and approved:

- styled Home and Login;
- correctly sized logo, Google and social icons;
- contained map and readable layout;
- portrait and landscape behaviour;
- touch, scroll and mobile controls;
- rapid switching between statuses;
- absence of the false other-session conflict;
- preservation of the final selected state after synchronisation;
- no unintended visit, counter or memory change.

## 10. Risk decision

QR-39 is no longer an `Assessment gap`.

It is retained as **Regression risk** because future changes could reintroduce:

- global horizontal overflow;
- low-height modal obstruction;
- oversized SVGs;
- incompatible production CSS;
- touch-only failures;
- virtual-keyboard obstruction;
- unstable network feedback;
- lost or duplicated actions under constrained CPU/network conditions.

QR-04 remains **Regression risk**. AB-EV-018 extends AB-EV-013 by adding permanent same-session rapid-mutation and real external-conflict coverage.

## 11. Traceability

```text
QR-39 assessment gap
→ responsive/touch/constrained-condition matrix
→ targeted mobile reflow corrections
→ 31 responsive E2E tests
→ Android Production CSS failure
→ AB-DEF-003
→ PostCSS cascade-layer correction
→ rapid status false conflict
→ AB-DEF-004 / QR-04
→ queued and coalesced same-session mutations
→ 11 rapid-mutation E2E scenarios
→ 150-test full regression
→ fast-forward deployment eca539e
→ Vercel Production Ready
→ same-device Android retest approved
→ AB-EV-018
→ QR-39 Regression risk
```

## 12. Final conclusion

The AtlasBadge V1.0 responsive, touch and constrained-device baseline is implemented, protected by permanent automation and approved in Production.

The Android CSS delivery failure and same-session rapid-status conflict were both discovered through physical-device testing, corrected without weakening real concurrency protection, and verified after deployment.
