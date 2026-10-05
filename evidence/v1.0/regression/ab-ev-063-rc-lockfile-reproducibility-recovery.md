# AB-EV-063 — Release Candidate lockfile reproducibility recovery

**Product:** AtlasBadge  
**Target release:** V1.0  
**Evidence type:** Release-control / reproducibility / build qualification  
**Date:** 5 October 2026  
**Owner:** Test Lead/Product Owner  

## 1. Purpose

Record the Release Candidate reproducibility blocker discovered when a clean AtlasBadge clone attempted `npm ci` under the declared Node 22 runtime, the bounded lockfile correction, the independent forensic review of the resulting metadata churn, publication of the correction, and the automatic Production deployment.

This record does not claim final V1.0 release approval. It closes the specific clean-install reproducibility blocker that prevented entry into the Release Candidate technical gates.

## 2. Initial release-control block

The first RC preflight did not proceed because the existing local Product workspace was stale/dirty and the machine exposed Node 24 rather than the Product-declared Node 22 runtime. No protected local changes were reset, stashed, cleaned or overwritten.

A separate clean RC clone was created at the authoritative Product `main` baseline. Node.js `v22.23.3` Windows x64 was downloaded from the official Node distribution, its SHA-256 was verified, and the runtime was used through a session-only PATH change so the global Node 24 installation remained untouched.

The clean clone then exposed the material blocker:

```text
npm ci
→ exit 1
→ package.json and package-lock.json are not in sync
→ Missing: @swc/helpers@0.5.23 from lock file
```

## 3. Root cause

The committed graph contained both of the following valid requirements:

- Next.js `16.2.11` required `@swc/helpers@0.5.15`;
- the `@swc/core@1.16.1` instance used below `next-intl@4.14.1` declared optional peer `@swc/helpers >=0.5.17`.

The committed lockfile contained only the `0.5.15` helper instance, so npm `10.9.9` rejected the lockfile during clean-tree validation. Investigation confirmed that this peer-resolution mismatch pre-dated the Next.js `16.2.11` security upgrade; the security upgrade was therefore not classified as the root cause.

## 4. Correction

The implementation agent regenerated dependency metadata using Node `v22.23.3` / npm `10.9.9` with:

```text
npm install --package-lock-only --ignore-scripts --no-audit --no-fund
```

`package.json` was not changed. Product source, tests, Firebase Rules and build configuration were not changed.

The resulting dependency tree retained:

```text
next@16.2.11
└─ @swc/helpers@0.5.15

next-intl@4.14.1
└─ @swc/core@1.16.1
   └─ @swc/helpers@0.5.23
```

## 5. Lockfile forensic review

Because regeneration produced `79 insertions / 168 deletions`, publication was held for a separate forensic review instead of treating a green build as sufficient evidence.

The review found:

- one required semantic peer-resolution addition: nested `@swc/helpers@0.5.23`;
- no version drift for Next, next-intl, Firebase, React, Playwright, Vitest, TypeScript, Tailwind or ESLint;
- platform/optional-package metadata normalization, including Linux `libc` metadata removal and WASI fallback metadata hydration;
- no functional Product dependency upgrade.

The lockfile diff was therefore accepted as a deterministic reconciliation of the existing dependency graph rather than a Product dependency migration.

Temporary forensic scratch files created during review were explicitly removed before publication; final local state contained only the intended `package-lock.json` modification and no untracked paths.

## 6. Validation

Validated on the exact lockfile candidate before commit:

- Node.js: `v22.23.3`;
- npm: `10.9.9`;
- `npm ci`: PASS / exit `0` / approximately 45.66 s;
- `npm ls @swc/helpers @swc/core next next-intl`: PASS / exit `0`;
- `npm run build`: PASS / exit `0`;
- Next.js production build: all 20 static pages generated;
- `git diff --check`: PASS;
- `package.json`: unchanged.

The clean-install and build checkpoints were carried forward through commit/push because publication did not alter the validated file content.

## 7. Publication

Product commit:

```text
3729aa642db29e5d2af23d37e10606a6f9bf3a94
fix(build): reconcile lockfile for clean installs
```

Parent:

```text
5e9839c2d5f497710119c30d360dfe85713ab037
```

Committed scope:

```text
package-lock.json only
1 file changed, 79 insertions(+), 168 deletions(-)
```

Push was a non-force fast-forward to `main`.

## 8. Production deployment

The Git-connected Vercel deployment triggered automatically from the `main` push:

```text
dpl_9uL3DtCGNww9NAubARAzb3i7pqBF
```

Deployment facts independently verified:

- project: `atlas-badge`;
- target: `production`;
- source: Git;
- Git SHA: `3729aa642db29e5d2af23d37e10606a6f9bf3a94`;
- Git branch: `main`;
- state: `READY`;
- alias error: none.

No manual Vercel deployment was performed.

## 9. Release decision

**Specific blocker decision:** CLOSED — clean-install reproducibility restored for the pre-upgrade Node 22 baseline.

**V1.0 decision:** NOT YET READY FOR GO LIVE.

The Test Lead has decided that AtlasBadge must migrate to Node 24 LTS before V1.0 launch. That migration is a separate controlled Product change and will invalidate only the runtime/build/tooling checkpoints it materially affects. The final V1.0 Release Candidate regression must be executed against the runtime intended for launch after the Node 24 migration is qualified.

## 10. Traceability

```text
Release-readiness audit / AB-EV-061
→ AB-EV-062 final RC charter
→ clean RC preflight
→ npm ci reproducibility failure
→ dependency-graph diagnosis
→ bounded lockfile-only correction
→ forensic lockfile review
→ npm ci / npm ls / build PASS
→ Product commit 3729aa642db29e5d2af23d37e10606a6f9bf3a94
→ Vercel Production dpl_9uL3DtCGNww9NAubARAzb3i7pqBF READY
→ AB-EV-063
→ mandatory Node 24 LTS migration before V1.0 GO LIVE
```
