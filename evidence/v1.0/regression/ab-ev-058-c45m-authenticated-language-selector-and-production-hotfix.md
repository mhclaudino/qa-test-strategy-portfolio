# AB-EV-058 — C45M authenticated language selector, dirty-state protection and Production hotfix

**Evidence ID:** AB-EV-058  
**Checkpoint:** C45M — Authenticated Language Selector  
**Related Product Defect:** AB-DEF-021 — authenticated public-Home locale controls diverged after release  
**Decision owner:** Test Lead / Product Owner  
**Product repository:** [mhclaudino/atlasbadge](https://github.com/mhclaudino/atlasbadge)  
**Baseline:** C45L [`6f308276600be2b14b15076def0c33a16c118770`](https://github.com/mhclaudino/atlasbadge/commit/6f308276600be2b14b15076def0c33a16c118770)  
**Feature publication:** [`8e3f97269036deaa26eca17c8d5fc34eefc1bde4`](https://github.com/mhclaudino/atlasbadge/commit/8e3f97269036deaa26eca17c8d5fc34eefc1bde4) — `feat(i18n): add authenticated language selector`; 23 files; Vercel [`dpl_3LrMiQNSMwhTiVKmTGGumrEmPmVc`](https://vercel.com/m-hc/atlas-badge/3LrMiQNSMwhTiVKmTGGumrEmPmVc) **Production / READY / exact SHA**  
**Hotfix publication:** [`447b568bd70c445c0a98220fcd134fd5bb843259`](https://github.com/mhclaudino/atlasbadge/commit/447b568bd70c445c0a98220fcd134fd5bb843259) — `fix(i18n): sync locale selectors on public home`; 3 files; Vercel [`dpl_8LiFm2GyN6cKDuQZdW9i7fLP8VPp`](https://vercel.com/m-hc/atlas-badge/8LiFm2GyN6cKDuQZdW9i7fLP8VPp) **Production / READY / exact SHA**  
**Final Test Lead decision:** **C45M CLOSED / PRODUCTION PASS; AB-DEF-021 CLOSED — 2 October 2026.** The Test Lead confirmed the corrected bidirectional avatar-menu ↔ public-flag locale flow in Production while remaining authenticated. Query/hash preservation is proven by the focused Emulator E2E. Physical-device C45M execution is **NOT EXECUTED**; responsive viewport acceptance at 390×844 and 320×568 is separate evidence.

## 1. Scope carried into this closure

C45M is the final planned C45 localization checkpoint. It adds authenticated locale switching from the avatar menu without changing locale identity, persistence schema, Firestore Rules, authentication rules or user-authored content. The approved menu order starts with **Idioma / Language** before Mapa, Badges, Perfil, Editar perfil and Sair. The language submenu exposes `Voltar` plus six friendly options:

- Português (Brasil) — `pt-BR`
- Português (Portugal) — `pt-PT`
- Español (Latinoamérica) — `es-419`
- Español (España) — `es-ES`
- Français — `fr`
- English (UK) — `en-GB`

The current locale is visibly/semantically selected. Keyboard/focus behavior covers ArrowUp/ArrowDown, Home/End, Tab, Enter/Space, Escape and focus restoration. Choosing the already-active locale closes the menu without a redundant reload.

Locale switching persists `atlasbadge_locale`, keeps the user authenticated, preserves the current route/search/hash contract where applicable, updates server/document locale, and must not break the already-existing anonymous public selectors. Public Home is a special case because the locale is also represented in the canonical route; after AB-DEF-021, both the public flags and authenticated avatar menu are required to converge on the same route, cookie, `html lang` and selected UI state.

## 2. Unsaved-data and saving-state contract

A locale change is a navigation/reload boundary, so C45M explicitly protects in-progress editor state. The feature integrates dirty/saving signals from:

- VisitEditor;
- NonPhysicalMemoryEditor;
- MemoryOrderEditor;
- ManualVisitOrderEditor.

A saving editor blocks switching. A dirty editor raises the existing unsaved-changes confirmation contract rather than silently discarding. Cancel keeps the current locale and dirty state. Confirmed discard permits the pending locale change. A second locale request must not overwrite an existing `ConfirmModal`; the accepted change produces one intentional navigation/reload without a duplicate native `beforeunload` interruption.

Implementation hardening during pre-release included the MutationOrchestrator new-visit replay/remount path, ManualVisitOrderEditor dirty communication and saving-handler priority, page-level confirmation collision/reload guarding, and MemoryOrderEditor cleanup/saving/dirty synchronization. These were resolved before the feature release and are retained as implementation findings/regression targets rather than inflated post-release Product Defect counts.

## 3. Requirement → implementation → verification traceability

| Requirement / risk | Implementation boundary | Verification / decision |
|---|---|---|
| Six authenticated locales + friendly labels | `Header.tsx`; six message catalogues; shared locale config | Header component coverage; final feature E2E; Test Lead visual QA |
| Selected state + keyboard/focus accessibility | Avatar language submenu/menu refs and ARIA state | Header C45M keyboard/focus tests; QR-40 regression |
| Cookie/document/session continuity | `persistLocalePreference`, current locale resolution, reload/navigation contract | Six-locale authenticated E2E; relogin/document-language assertions |
| Current locale no-op | Header selection branch | Component/E2E assertion: closes without reload |
| Dirty/saving protection | page coordination + Visit/Memory/ManualOrder editors | Focused dirty E2E 2/2; editor/integration tests |
| No real Firebase from browser Emulator tests | `emulatorContextGuard.ts` + automatic `emulatorTest` fixture | Browser E2E reports `realFirebaseRequests=0`; final hotfix uses protected `page` fixture |
| Public flag + authenticated menu convergence | public-Home branch in `Header.tsx`; canonical `localizedHomePath`; public selector capture | AB-DEF-021 focused hotfix E2E + Test Lead Production retest |
| Preserve search/hash on public Home | target localized path + current `window.location.search/hash` | Focused hotfix component/E2E assertions |

## 4. Pre-release C45M execution and visual acceptance

The final feature candidate before first Production publication used Node `v22.23.2` and the Firebase Emulator project `demo-atlasbadge-web` with browser isolation against Auth/Firestore/Storage Emulator endpoints.

Authoritative/carry-forward results included:

| Checkpoint | Result | Qualification |
|---|---|---|
| `e2e/localization-authenticated.c45m.spec.ts` final feature state | **7 PASS / 0 FAIL / 0 SKIPPED**, ~35.2 s | Executed after the last relevant spec edit; `realFirebaseRequests=0` |
| Focused dirty-protection C45M E2E | **2/2 PASS** | Covers dirty cancellation/discard, modal protection and one-reload behavior |
| Header C45M component tests | **6/6 PASS** at feature checkpoint | Friendly labels, first-item order, selection and keyboard/focus behavior |
| MutationOrchestrator | **29 PASS** | Supporting persistence/orchestration regression |
| ManualVisitOrderEditor integration | **5 PASS** plus TypeScript | Supporting dirty/saving/order integration |
| TypeScript | **PASS** | Final feature release gate |
| Lint | **PASS — 0 errors / 34 warnings** | Warnings reported as existing/non-blocking |
| Build | **PASS** | Next.js 16.2.11 / Node 22.23.2 release candidate |
| Visual/responsive Test Lead QA | **PASS** at 390×844 and 320×568 | Language submenu order, friendly labels, no wrapping/clipping and final width accepted; viewport evidence is not a physical-device claim |

Earlier partial/failed diagnostic runs are not counted as passing evidence. Green checkpoints were carried forward only where later changes did not invalidate them.

## 5. First release and AB-DEF-021 discovery

The controlled feature commit `8e3f97269036deaa26eca17c8d5fc34eefc1bde4` was published only after explicit Test Lead push approval. Vercel deployment `dpl_3LrMiQNSMwhTiVKmTGGumrEmPmVc` was independently verified **READY / Production / exact SHA**.

A non-authenticated Production check confirmed the localized public surface rendered successfully, including locale-specific document language/metadata. The required authenticated Test Lead smoke then found a genuine Product Defect:

**AB-DEF-021 — authenticated public Home ignored avatar language selection after reload**

**Actual:** while logged in on a localized public Home, Avatar → Idioma persisted the newly selected locale but the page remained/reverted to the locale represented by the existing public Home route/flag.

**Expected:** public flags and authenticated avatar menu must both change the active locale and remain synchronized while preserving authentication.

**Root cause:** `HeaderContent` resolved active locale with route-supplied `publicHomeLocale` ahead of context preference. The avatar path persisted `atlasbadge_locale` and unconditionally called `window.location.reload()`; because the localized Home URL did not change, the same `publicHomeLocale` became authoritative after reload. The two controls represented one state but used different transition mechanisms.

This is classified as one Product Defect because valid Production behavior violated the approved requirement. It is not a stale oracle, Emulator failure or Vercel verification problem.

## 6. Hotfix, final-file evidence and release control

The hotfix changed exactly three Product-repository files:

- `src/components/Header.tsx`;
- `src/components/Header.c45m.test.tsx`;
- `e2e/localization-authenticated.c45m.spec.ts`.

For authenticated public Home, the avatar selector now persists the target preference and navigates to `localizedHomePath(targetLocale) + search + hash`. The public flag path is intercepted at the shared Header boundary so it also preserves the current query/hash. Normal authenticated routes such as `/app` retain the established reload behavior. Current-locale no-op remains unchanged.

During hotfix construction, some intermediate green results became stale after later edits; one early test context also used a manually created browser context outside the automatic Emulator guard. Those results are not used as final evidence. The authoritative closure was an explicit **final-file freeze with zero edits**:

| Final hotfix gate | Result |
|---|---|
| `src/components/Header.c45m.test.tsx` | **8/8 PASS**, exit 0 |
| Focused E2E `authenticated public Home keeps avatar and flag locale selectors synchronized` | **1/1 PASS**, exit 0 |
| Browser Firebase isolation | **`realFirebaseRequests=0`** using the protected `page` fixture |
| TypeScript | **PASS** on final Product state |
| `git diff --check` | **PASS** |
| Final inventory | exactly the three intended modified files before commit; no untracked files |

The focused E2E proves pt-BR public Home → avatar English (UK) → en-GB route/cookie/`html lang`/flag/session, then public Brazil flag → pt-BR route/cookie/`html lang`/avatar selection, including preservation of `?foo=bar#section`.

Hotfix commit `447b568bd70c445c0a98220fcd134fd5bb843259` was created locally, stopped for explicit Test Lead push approval, then fast-forwarded to Product `main`. Vercel deployment `dpl_8LiFm2GyN6cKDuQZdW9i7fLP8VPp` is **READY / Production / exact hotfix SHA**. No Firebase Rules/config/package/lock change or Firebase deploy was part of the release.

## 7. Test Lead Production retest and final decision

After the exact-SHA hotfix deployment was READY, the Test Lead repeated the defect path in Production. The corrected flow passed:

1. authenticated public Home → change language through Avatar → Idioma;
2. localized Home/active public flag follow the selected locale and the authenticated session remains active;
3. change locale back through the public flag;
4. Avatar → Idioma reflects the returned locale.

**Decision: C45M PRODUCTION PASS; AB-DEF-021 CLOSED.**

The query/hash case is retained as automated Emulator evidence rather than being overstated as a separate manual Production assertion. C45M has responsive viewport evidence at 390×844 and 320×568, but no physical-device execution is claimed.

## 8. Process and evidence-control history carried from the implementation chat

C45M also produced a material QA-process lesson that was documented before feature closure as LL-66. During the longer investigation, repeated broad Playwright attempts, speculative locator changes, late edits after claimed gates and a temporary real-Firebase local configuration consumed agent capacity without producing stable release evidence. The corrective process introduced mandatory per-task Lessons Learned/Test Plan preflight, bounded objectives, one focused reproduction plus at most one evidence-based retry, explicit environment/oracle/inventory checks and STOP/BLOCKED behavior.

That control mattered during the Production hotfix:

- later edits invalidated prior test PASS and triggered only the smallest affected reruns;
- an E2E `browser.newContext()` path that bypassed the intended automatic guard was replaced by the protected `page` fixture before final evidence;
- temporary diagnostic/scratch files were removed before commit;
- the implementation agent's lack of authenticated Vercel CLI access was treated as a local evidence-channel limitation, not a Product/deployment failure; exact-SHA deployment state was verified through the connected Vercel project;
- disposable QA credentials, passwords, e-mail addresses, Auth payloads and other identifying test data are intentionally excluded from this public record.

These events are process/harness/environment evidence, not extra Product Defects.

## 9. Residuals and regression ownership

C45M closes the planned C45A–C45M V1.0 localization sequence. The following remain explicit residuals/standing regression concerns rather than open C45M implementation:

- broader browser/device coverage and physical-device testing remain non-exhaustive;
- formal native assistive-technology certification is not claimed;
- user-authored content remains intentionally untranslated;
- dirty/saving language-switch protection and public flag ↔ avatar convergence become permanent affected-area regression targets;
- the current root request-time localization architecture remains the separately accepted V1.0 technical trade-off documented by earlier C45 evidence.

Historical C45A–C45L evidence remains unchanged; AB-EV-058 records the new state rather than rewriting earlier decisions.

Refer to the [Evidence Register](../evidence-register.md), [System Test Plan](../../../docs/09-system-test-plan.md) and [Lessons Learned](../../../docs/10-lessons-learned.md).
