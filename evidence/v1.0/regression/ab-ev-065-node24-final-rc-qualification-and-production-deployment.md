# AB-EV-065 — Node 24 final Release Candidate qualification and exact-SHA Production deployment

**Product:** AtlasBadge V1.0  
**Evidence ID:** AB-EV-065  
**Date:** 9 October 2026  
**Evidence type:** Final Release Candidate / runtime migration / integrated regression / release integrity  
**Related evidence:** AB-EV-061, AB-EV-062, AB-EV-063, AB-EV-064  
**Related risks:** release integrity; QR-01; QR-04; QR-07; QR-25; QR-29; QR-30; QR-31; QR-32; QR-34; QR-39; QR-40  
**Decision state:** FINAL RC TECHNICAL PASS / EXACT-SHA PRODUCTION DEPLOYMENT PASS — final Production smoke, production reset, clean-start confirmation and Test Lead Go/No-Go remain pending

---

## 1. Purpose

This record closes the technical Release Candidate work that followed the V1.0 release-readiness audit and the mandatory Node 24 runtime requirement.

It records:

- the Node 24 dependency/runtime resolution;
- the affected unit, Rules, account-deletion, accessibility and browser-regression checkpoints;
- the permanent final Microsoft Edge regression result;
- the integrity proof used while reconciling the qualified dirty candidate with `origin/main`;
- publication of the exact qualified patch to the Product repository;
- the exact-SHA Vercel Production deployment and Node 24 build proof;
- the explicit Windows Google Chrome coverage limitation;
- the remaining release gates that are intentionally not claimed as complete by this evidence.

AB-EV-062 remains the historical final-RC charter. AB-EV-063 and AB-EV-064 remain the historical lockfile-recovery/runtime-upgrade records. This evidence records the later executed result instead of rewriting those earlier states.

---

## 2. Runtime migration result

### Final local runtime

```text
Node: v24.21.0
npm: 11.19.0
Next.js: 16.3.6
eslint-config-next: 16.3.6
next-intl: 4.14.1
```

The original Node 24 attempt exposed an existing dependency-tree incompatibility around the SWC helper version required by the resolved `@swc/core`. The bounded successful remediation upgraded Next.js and `eslint-config-next` from 16.2.11 to 16.3.6, whose dependency graph resolved `@swc/helpers` 0.5.23 and allowed npm 11 to reproduce the installation cleanly.

Final dependency checkpoints:

```text
npm ci: PASS
npm ls selected runtime/dependency tree: PASS
```

No direct helper override or Product-specific SWC workaround was retained.

---

## 3. Carried-forward and requalified technical gates

The Node 24 cycle did not rerun every historical V1.0 check after every correction. Green checkpoints were carried forward unless a later change invalidated what they had proved.

Final applicable checkpoints included:

| Gate | Final result |
|---|---|
| TypeScript | PASS |
| ESLint | PASS — 0 errors; 38 warnings |
| Production build | PASS — Node 24.21.0 / Next.js 16.3.6 |
| Vitest | 119 files passed, 0 failed, 8 skipped; 879 tests passed, 0 failed, 37 skipped |
| Geography | PASS |
| Firebase Rules | 235 / 235 PASS |
| Account deletion | 82 / 82 PASS |
| Profile Map component | 12 / 12 PASS |
| Profile Map focused Edge | 2 / 2 PASS |
| Public Profile locale-resolution focused Edge | 1 / 1 PASS |
| Flags-header focused Edge | 1 / 1 PASS |
| Badges accessibility focused Axe | 1 / 1 PASS; 0 Axe violations |

The browser qualification used the protected Firebase Emulator environment and retained the `realFirebaseRequests=0` safety proof.

---

## 4. Findings during final browser hardening

The broad Edge run was treated as diagnostic evidence rather than as an instruction to force Product behaviour to satisfy every historical automation assertion.

The final hardening work separated three classes of finding:

1. **stale test or fixture expectations** — including historical localisation labels/fallbacks, route expectations and the old flags-header SVG contract;
2. **test-fixture/reconciliation gaps** — including achievement metadata that the current Product normally derives before rendering;
3. **real Product defects** — notably the Profile map transient-highlight regression and the Badges loading-state contrast violation.

### Profile map regression

The historical C32 contract expected a temporary `ring-4` visual highlight when public Profile map geography navigates to the earned flag card.

The current implementation had mixed imperative DOM class mutation with React-rendered selected-state classes. A later selected-country/modal coupling also meant a map geography click could open the Public Memories modal unintentionally.

The final Product correction made the transient highlight declarative and separated:

- map geography click → scroll + temporary highlight only;
- flag-card click → selected country/modal + scroll + temporary highlight.

Focused result:

```text
CountryBadgeGrid component: 12 / 12 PASS
Profile Map Edge desktop/mobile: 2 / 2 PASS
realFirebaseRequests=0
```

### Badges loading-state accessibility defect

The final broad Edge qualification initially reached 221 / 222 because Axe reported a serious `color-contrast` violation on:

```text
Carregando conquistas...
```

The loading copy used `text-zinc-500` on the AtlasBadge dark background and was reported at approximately 4.1:1 against the 4.5:1 normal-text requirement enforced by the WCAG AA baseline.

The contained Product correction changed only:

```text
text-zinc-500
→
text-zinc-400
```

Focused Axe result after correction:

```text
1 discovered
1 passed
0 failed
0 Axe violations
realFirebaseRequests=0
```

The production build was then rerun and passed before the final broad Edge cycle.

---

## 5. Final permanent Edge qualification

### Environment

```text
OS/browser family: Windows / Microsoft Edge
Playwright project: edge
Browser channel: msedge
Default locale: pt-BR
Firebase project: demo-atlasbadge-web
Auth Emulator: 127.0.0.1:9099
Firestore Emulator: 127.0.0.1:8080
Storage Emulator: 127.0.0.1:9199
```

Permanent command:

```text
npx.cmd firebase emulators:exec --project demo-atlasbadge-web --only auth,firestore,storage "npx.cmd playwright test --project=edge"
```

Final frozen-candidate result:

```text
222 discovered
222 passed
0 failed
0 skipped
Duration: 16.3m
realFirebaseRequests=0
Exit code: 0
```

No tracked or untracked source/test file changed during the execution.

---

## 6. Qualified candidate integrity fingerprint

Before the final Edge run, the complete dirty candidate patch was fingerprinted.

```text
SHA-256:
B4A1B98AA420B9A40F361128A75F2533B6C53672030A021C6A2C859F60F7D088
```

The post-run patch produced the same SHA-256.

The candidate contained 21 tracked modified files, zero untracked files, and `git diff --check` exited 0.

The 21-file set included the Node/Next dependency update, Playwright locale harness adjustment, bounded stale-oracle/fixture corrections, unit-test maintenance, the declarative Profile map correction, the Badges loading contrast correction and the Next-managed `AGENTS.md` block.

---

## 7. Safe reconciliation with `origin/main`

At final qualification time, the local candidate branch was an ancestor of `origin/main` because of two earlier administrative Product commits. Those commits had no net Product-tree effect.

Pre-reconciliation proof:

```text
local HEAD: 3729aa642db29e5d2af23d37e10606a6f9bf3a94
origin/main: 83901837d957fc67316d3bd3df29a8d0444805b4
origin-only / HEAD-only: 2 / 0
merge-base: 3729aa642db29e5d2af23d37e10606a6f9bf3a94
old tree SHA:    2ceef35fbfd3dca228eb03d417fd60de16381f62
remote tree SHA: 2ceef35fbfd3dca228eb03d417fd60de16381f62
committed-tree diff: empty
```

With explicit Test Lead authorisation, the branch was reconciled using only:

```text
git merge --ff-only origin/main
```

Post-reconciliation:

```text
HEAD = origin/main = 83901837d957fc67316d3bd3df29a8d0444805b4
origin-only / HEAD-only = 0 / 0
```

The qualified candidate patch hash remained exactly:

```text
B4A1B98AA420B9A40F361128A75F2533B6C53672030A021C6A2C859F60F7D088
```

Therefore the 222 / 222 Edge result was carried forward without an unnecessary rerun: Git ancestry changed, Product/test bytes did not.

---

## 8. Product publication

With separate explicit Test Lead authorisation, the exact qualified 21-file patch was staged, committed and pushed without force.

Product commit:

```text
12a0dd53dfe789610e6393b31db1c94c311757ff
chore(release): qualify Node 24 LTS candidate
```

Parent:

```text
83901837d957fc67316d3bd3df29a8d0444805b4
```

Integrity proof:

```text
qualified patch SHA-256 = staged patch SHA-256 = committed patch SHA-256
B4A1B98AA420B9A40F361128A75F2533B6C53672030A021C6A2C859F60F7D088
```

After push:

```text
local HEAD = origin/main = 12a0dd53dfe789610e6393b31db1c94c311757ff
origin-only / HEAD-only = 0 / 0
working tree = clean
```

---

## 9. Exact-SHA Vercel Production deployment

The GitHub push triggered the linked Vercel Production deployment.

Deployment:

```text
dpl_Gncwr79sPfZuPLXx5zSiwD1RpqTr
```

Verified deployment metadata:

```text
State: READY
Target: production
Source: git
Branch: main
Git SHA: 12a0dd53dfe789610e6393b31db1c94c311757ff
Project: atlas-badge
Framework: Next.js
Project Node version: 24.x
```

Production aliases include:

```text
atlas-badge.vercel.app
atlas-badge-m-hc.vercel.app
atlas-badge-git-main-m-hc.vercel.app
```

The Vercel build log independently proved the runtime transition rather than relying only on `package.json`:

```text
Skipping build cache since Node.js version changed from "22.x" to "24.x"
Detected Next.js version: 16.3.6
Compiled successfully
TypeScript finished successfully
Generated static pages: 20 / 20
Deployment completed
```

The deployment is therefore an exact-SHA Production deployment of the qualified Node 24 candidate.

---

## 10. Google Chrome on Windows coverage exception

AB-EV-062 originally required a real Google Chrome on Windows gate and explicitly prohibited calling Edge a Chrome PASS.

During final release preparation, the Test Lead confirmed that Google Chrome is not installed on the available Windows workstation.

Therefore:

```text
Google Chrome on Windows: NOT EXECUTED
Reason: browser unavailable on Test Lead workstation
Chrome PASS claimed: NO
```

Microsoft Edge on Windows remains the fully integrated real-browser qualification baseline with 222 / 222 PASS.

Because current Edge and Chrome releases share the Chromium engine family, the unexecuted Chrome scenario is considered a bounded compatibility uncertainty rather than evidence that Chrome passed. The Test Lead directed release work to continue without installing Chrome solely to satisfy the historical checklist item.

This is recorded as an explicit residual compatibility gap for the final release decision. It does not replace or rewrite the historical AB-EV-062 requirement.

---

## 11. Production observation versus formal smoke

After the exact-SHA deployment, the Test Lead reported that the Product appeared correct in Microsoft Edge.

That manual observation strengthens confidence in the deployed artifact, but this record does **not** manufacture detailed smoke evidence that was not explicitly executed/reported.

In particular, the formal V1.0 Production smoke requirement still calls for controlled `pt-BR` and `en-GB` coverage of the essential public/authenticated path. Any required real-backend mutation must remain controlled and separately authorised.

---

## 12. Remaining launch gates

AB-EV-065 closes the Node 24 technical Release Candidate and exact-SHA deployment gates.

It does **not** close the final V1.0 launch by itself.

Remaining gates are:

1. controlled final Production smoke with the required language coverage, unless the Test Lead accepts equivalent already-executed evidence after explicit review;
2. controlled Production test-data reset;
3. technical verification that Auth, Firestore, Storage, username and public-profile test artefacts are removed;
4. clean-start functional verification that Production remains empty and does not repopulate from cache/fallback;
5. final Test Summary Report / residual-risk consolidation;
6. explicit Test Lead V1.0 Go/No-Go decision.

The Production reset is destructive and is not authorised by this evidence record.

---

## 13. Current decision

### Technical Release Candidate

**PASS**

### Exact-SHA Production deployment

**PASS**

### Google Chrome on Windows

**NOT EXECUTED — explicit residual compatibility gap; no Chrome PASS claim**

### Final V1.0 launch

**PENDING**

The Product is on the qualified Node 24 / Next.js 16.3.6 Production deployment and no known local Edge blocker remains. Final launch authority remains with the Test Lead after Production smoke/reset/clean-start evidence and residual-risk review are complete.
