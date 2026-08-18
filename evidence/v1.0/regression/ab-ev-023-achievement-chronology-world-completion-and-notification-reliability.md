# AB-EV-023 — Achievement Chronology, World Completion and Notification Reliability

## 1. Evidence identification

| Field | Value |
|---|---|
| Evidence ID | AB-EV-023 |
| Related defects | AB-DEF-008; AB-DEF-009; AB-DEF-010 |
| Related risks | QR-18; QR-25; QR-29; QR-30; QR-39 |
| Product | AtlasBadge V1.0 |
| Evidence type | Achievement data-model hardening, defect closure and Production regression |
| Owner | Test Lead/Product Owner |
| Main product commits | `f140c48`, `44ac35a`, `95a53e1`, `f6004a9`, `047974a`, `8887d2b` |
| Decision | Passed — chronology, attainable World Complete, exactly-once popup and non-blocking unlock UX approved |

## 2. Canonical acquisition chronology

The final model uses one canonical per-user `achievementMetadata` map containing `unlockedAt` and `sequence`, plus monotonic `nextAchievementUnlockSequence`. Sequence is the primary acquisition-order authority; timestamp and achievement ID are defensive fallbacks.

The design supports simultaneous unlocks, same-timestamp ordering, stable Badges/Profile order, relock by removing the current metadata entry, reconquest with a new timestamp/sequence, and no fabricated historical backfill.

The intermediate two-map design was consolidated into one metadata map so Firestore Rules could validate affected entries without expression-limit failure.

## 3. Rules and transaction validation

The final Rules validate approved achievement IDs, exact entry shape, positive timestamp, positive integer sequence, monotonic counter and affected-entry changes through one metadata diff. A real six-achievement simultaneous unlock passed, and the integrated Rules suite completed **211/211 Passed** before Production publication.

## 4. AB-DEF-008 — World Complete was unattainable

The dataset contains a non-selectable United Kingdom aggregate plus four selectable UK constituents. The original World Complete denominator compared a selectable-sovereign numerator with the conceptual 195-country total, allowing only 194/195 and making the achievement impossible.

The correction grants the UK conceptual sovereign point only when England, Scotland, Wales and Northern Ireland are all complete. The technical `gb` aggregate remains non-selectable and receives no persisted travel status.

Focused coverage included ten World Complete scenarios, dataset contract checks, related UK/continent interactions and an Emulator E2E that unlocked only after the final required world action.

## 5. AB-DEF-009 — Full-page loader during unlock

Achievement reconciliation refreshed the profile through the same blocking `profileLoading` state used for initial bootstrap. Every unlock could therefore replace the interactive application with **Carregando seu Atlas...**.

Profile fetching was separated into blocking initial hydration and background refresh when a usable confirmed profile already exists. Initial loading remains intact; achievement refresh no longer blanks the app.

## 6. AB-DEF-010 — Popup lost during reconciliation race

A Production reconquest persisted valid metadata and a new sequence but produced no popup in the action tab. Notification delivery depended exclusively on transaction-level `newlyUnlockedIds`. If another reconciler persisted first, the local retry returned an empty list and silently dropped the notification.

The correction keeps Firestore transaction/final metadata as persistence authority while retaining a locally caused not-earned → earned transition as the notification candidate. Final metadata supplies the confirmed sequence; dedupe remains `uid:achievementId:sequence`; login, reload and remote-only snapshots do not produce historical popups.

## 7. Automated regression

| Gate | Result |
|---|---|
| Achievement-notification unit tests | 11/11 Passed |
| Related notification regression | 47/47 Passed |
| Achievement popup Emulator E2E | 3/3 Passed |
| Full Vitest | 278 Passed; 3 skipped |
| TypeScript | Passed |
| Lint | Passed |
| Next.js production build | Passed |
| `git diff --check` | Passed |

The suite retains first unlock, relock/reconquest, simultaneous ordering, same-timestamp ordering, user isolation, multi-session/remote-tab behaviour, reconciliation races, no historical popup after reload and zero full-page-loader re-entry after initial hydration.

## 8. Production validation

C15 Production confirmed valid first-unlock metadata, one popup, zero loader reappearances, Clear Map relock with monotonic counter, reconquest with a higher sequence, one reconquest popup, persisted reload state and no historical popup. The later controlled Clear Map validator repeated the same relock/reconquest guarantees; see AB-EV-024.

## 9. Traceability

```text
canonical chronology requirement
→ C4 / C4A transactional metadata
→ C5 chronological Badge/Profile presentation
→ AB-DEF-008 impossible World Complete
→ C8 virtual-UK correction
→ AB-DEF-009 global loader on unlock
→ C11 background profile refresh
→ AB-DEF-010 notification lost under reconciliation race
→ C15 local-transition + final-metadata notification authority
→ Emulator + Production retest
→ AB-EV-023
```

## 10. Final conclusion

Achievement calculation, current-acquisition chronology, relock/reconquest, exactly-once notification and non-blocking unlock UX are protected by automated regression and approved Production evidence.
