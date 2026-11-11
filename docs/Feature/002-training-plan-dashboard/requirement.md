# Requirements — Training Plan Dashboard

> **Feature ID:** `002`
> **Product sources:** `docs/guide/02-target-user.md` §2.4; `docs/guide/04-weekly-training-plan.md`
> §§4.4 and 4.7; `docs/guide/08-rate-of-perceived-exertion.md` §§8.1–8.6;
> `docs/guide/10-acute-chronic-workload.md` §§10.1–10.5;
> `docs/guide/11-rpe-based-duration-scaling.md` §§11.1–11.3;
> `docs/guide/12-daily-training-adjustment.md` §§12.1–12.3;
> `docs/guide/13-weekly-training-adjustment.md` §§13.1–13.6; explicit user decisions dated 2026-11-10;
> user-approved feature renumbering dated 2026-11-11
> **Feature directory:** `docs/Feature/002-training-plan-dashboard/`
> **Prepared by:** `@planner`
> **Status:** `Draft — calorie, HealthKit prompt, and initial workload-state decisions pending`

## 1. Overview

Feature 002 provides a user-facing, read-only Training Plan Dashboard immediately after Feature 001 successfully
persists the onboarding result and Initial Training Plan. The dashboard presents the current Monday–Sunday plan,
session details and statuses, the next week's plan once it has actually been generated, four latest-readable
physiological vital cards, and the latest ACWR and automatic adjustment status with its reason.

The dashboard does not prepare or guide an exercise session and does not calculate RPE, Training Load, EWMA, ACWR,
or duration scaling. It renders authoritative upstream results without allowing the user to edit or override the
plan. Exercise guidance remains a separate page outside this feature. After its initial Feature 001 entry state,
Feature 002 consumes session and adaptive-training outputs from Features 003, 004, 005, 006, and 007 without taking
ownership of their implementations.

## 2. Purpose

- Give the user one place to understand the current calendar week's prescribed training sessions and their status.
- Show the automatically generated next-week plan only when the upstream weekly-adjustment flow has completed it.
- Present Resting Heart Rate, Heart Rate Variability, Blood Oxygen Saturation, and respiratory rate with transparent
  measurement time and availability state.
- Explain whether ACWR is available, its current category, the automatic plan decision, and the reason for that
  decision without requiring the user to interpret raw calculations.
- Preserve an automatic, read-only adaptation flow so dashboard presentation cannot diverge from the authoritative
  generated plan.

## 3. Actors and Preconditions

### Actors

| Actor | Responsibility | Source |
|---|---|---|
| User | Reviews current and generated upcoming plan information, vital measurements, workload status, and adjustment rationale. | User decisions 2026-11-10 |
| Kurin iOS app | Presents authoritative plan, session, vital, ACWR, and adjustment outputs without recalculating or editing them. | User decisions 2026-11-10 |
| Apple Health / HealthKit | Supplies the latest readable RHR, HRV, SpO2, and respiratory-rate samples and their timestamps. | Guide 2 §2.4; user decisions 2026-11-10 |
| Upstream training pipeline | Supplies current and next-week plans, session statuses and causes, RPE outputs, workload/ACWR outputs, and adjustment decisions. | Guides 4, 8, 10, 11, and 13; user decisions 2026-11-10 |

### Preconditions

| Precondition | Unmet behavior | Source |
|---|---|---|
| Feature 001 onboarding has completed and persisted an Initial Training Plan. | The Training Plan Dashboard is not the owner of onboarding or initial-plan creation; the user remains in the Feature 001 flow until that contract exists. | `docs/Feature/001-onboarding-health-profile/requirement.md` `FR-28`–`FR-31` |
| An authoritative current-week plan is available from Feature 001 or the recurring plan pipeline. | The dashboard must not invent session dates, types, durations, or statuses. | Guide 4 §§4.4, 4.7; user decision 2026-11-10 |
| HealthKit may return no readable sample for one or more vital types. | The affected card remains present with the privacy-safe state `Unavailable`; plan presentation remains usable. | Guide 2 §2.4; user decision 2026-11-10 |
| ACWR or the next-week plan may not yet be available from upstream. | The dashboard presents the upstream ACWR availability reason and does not present ungenerated next-week session values. | Guides 10 §§10.4–10.5 and 13 §13.6; user decisions 2026-11-10 |

## 4. Functional Requirements

| ID | Requirement | Source |
|---|---|---|
| `FR-01` | The app must provide a user-facing Training Plan Dashboard whose plan and adjustment content is read-only. The dashboard must expose no plan editing, manual override, or manual adjustment action. | User decisions 2026-11-10 |
| `FR-02` | The dashboard must present the current plan within a Monday-through-Sunday calendar-week boundary. | Guide 4 §4.4; user decision 2026-11-10 |
| `FR-03` | For every session in the current plan, the dashboard must show its scheduled calendar date, Training Type, planned duration, and authoritative upstream status. | Guide 4 §§4.4, 4.7; Guide 13 §13.3; user decision 2026-11-10 |
| `FR-04` | The dashboard must present next-week session dates, Training Type, durations, and statuses only after Feature 007 has generated the next-week plan. It must not fabricate or preview ungenerated session values. | Guides 4 §4.7 and 13 §13.6; user decision 2026-11-10 |
| `FR-05` | The dashboard must contain four vital cards using the latest readable HealthKit sample for each type and must show the sample timestamp: RHR in `bpm`, HRV in `ms`, SpO2 in `%`, and respiratory rate in `breaths/min`. | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-06` | If a vital sample is not readable, the affected card must show `Unavailable`, must not claim that per-type read permission was denied, and must not request manual entry. Other readable cards and all plan content must remain available. | Guide 2 §2.4 HealthKit privacy note; user decision 2026-11-10 |
| `FR-07` | RHR, HRV, SpO2, and respiratory rate must be display-only in Feature 002. The sole downstream exception is that a readable RHR sample from the same local calendar day may be supplied as an input to Feature 004; Feature 002 must not estimate or confirm RPE. | Guides 2 §2.4 and 8 §8.2; user decisions 2026-11-10 |
| `FR-08` | The dashboard must show whether ACWR is unavailable or available. When available, it must show the upstream numeric display value, category, and rationale: `< 0.8` Low, `0.8...1.3` Moderate, and `> 1.3` High. When unavailable, it must show the upstream reason, including fewer than 8 recorded calendar days or an invalid Chronic EWMA denominator. | Guide 10 §§10.4–10.5; Guide 13 §13.2; user decisions 2026-11-10 |
| `FR-09` | The dashboard must show the authoritative automatic adjustment outcome and rationale supplied by Feature 007, including the relevant ACWR category, plan adherence, or recorded non-completion cause. Feature 002 must not recalculate or replace that decision. | Guide 13 §§13.1–13.5; user decision 2026-11-10 |
| `FR-10` | V1 automatic adaptation presented by the dashboard must change session duration only. It must preserve the existing weekly frequency and weekdays. | Guides 4 §4.3 and 13 §13.5; user decision 2026-11-10 |
| `FR-11` | When the authoritative upstream decision is caused by ACWR `> 1.3`, the dashboard must show the next scheduled session as Rest and must show subsequent resumed sessions at the upstream decreased duration. | Guides 12 §§12.2–12.3 and 13 §13.4; user decision 2026-11-10 |
| `FR-12` | An injury-cancelled session must display cause `Injury`. The dashboard must present the injury-driven outcome as Rest until the user reports resolution through the owning upstream flow; after resolution, resumed sessions must use the upstream Decrease outcome. | Guides 7 §7.1 and 13 §§13.3–13.5; user decision 2026-11-10 |
| `FR-13` | A fatigue/overtraining cancellation must display cause `Fatigue/Overtrained` for its upstream adjustment branch. An other-complaint cancellation must display cause `Other Complaint`, and the dashboard must present its downstream outcome as Decrease. Each such report cancels the current session. | Guides 7 §7.1 and 13 §13.3; user decisions 2026-11-10 |
| `FR-14` | Cancellation of the final scheduled session of the current week must be treated as the terminal weekly event. Once the owning upstream flow has generated the result, the dashboard must accept the resulting weekly decision and next-week plan without waiting for another completed session. | Guide 13 §13.6; user decision 2026-11-10 |
| `FR-15` | The dashboard must render upstream calculation and scaling outputs without changing them: `EWMA(0) = 0`; full internal EWMA/ACWR precision with rounding only for numeric display; manual-only RPE when required inputs are missing or `HRmax <= HRrest`; `%HRR < 28%` mapped to RPE 1 and `%HRR > 100%` mapped to RPE 10; automated RPE and Weekly Average RPE mapped to the nearest whole tier with `.5` upward; `alpha` taken from the RPE percentage table; and adjusted duration rounded to the nearest 5 minutes before clamping to 15 minutes and the current-frequency maximum. | Guides 8 §§8.1–8.6, 10 §§10.1–10.5, and 11 §§11.1–11.3; user decisions 2026-11-10 |
| `FR-16` | When authoritative upstream plan, status, ACWR, or adjustment data changes, the dashboard must refresh its presentation without creating a second independent plan state. | User decision 2026-11-10 |

## 5. Non-Functional Requirements

| ID | Constraint | Verification | Source |
|---|---|---|---|
| `NFR-01` | The dashboard must support iOS 27 on iPhone 17 using SwiftUI, Dynamic Type, semantic colors, and accessibility labels for every interactive control. | Platform and accessibility review after downstream documentation gates and implementation. | Rule 01 iOS 27; Rule 05 SwiftUI |
| `NFR-02` | Vital and plan data must remain local. Feature 002 must perform no HealthKit write and must send no raw health data to iCloud or a remote backend. | Data-flow, entitlement, and persistence review after implementation. | Rule 01 HealthKit; AGENTS.md §7 |
| `NFR-03` | HealthKit-sourced vital values must preserve `HEALTH` provenance and the source timestamp. | Repository/model verification after implementation. | Rule 01 HealthKit; user decision 2026-11-10 |
| `NFR-04` | Asynchronous refresh must use Swift 6 `async/await` with explicit actor isolation and no `DispatchQueue` flow control. | Strict-concurrency build and static review after builder handoff. | Rule 05 Concurrency |
| `NFR-05` | Feature 002 Domain services must achieve at least 80% line coverage, and every acceptance criterion must map to an automated test or objective UI/accessibility review. | Coverage report and AC verification mapping after builder handoff. | Rule 03 |

## 6. Acceptance Criteria

| ID | Requirement | Given | When | Then |
|---|---|---|---|---|
| `AC-01` | `FR-01`, `FR-16` | Authoritative upstream dashboard data exists | The user opens the Training Plan Dashboard | Current content appears read-only, exposes no edit or override action, and has one authoritative plan state. |
| `AC-02` | `FR-02`, `FR-03` | A current plan contains sessions in one calendar week | The dashboard presents the plan | The week spans Monday–Sunday and every session shows date, Training Type, planned duration, and upstream status. |
| `AC-03` | `FR-04` | Feature 007 has not generated a next-week plan | The dashboard presents current data | No next-week session date, type, duration, or status is fabricated or previewed. |
| `AC-04` | `FR-04`, `FR-16` | Feature 007 generates a next-week plan | The authoritative output becomes available | The dashboard presents exactly that generated plan without a user edit step. |
| `AC-05` | `FR-05` | HealthKit returns readable samples for all four vital types | The vital cards load | Each card shows its latest readable value, timestamp, and exact unit: `bpm`, `ms`, `%`, or `breaths/min`. |
| `AC-06` | `FR-06` | One or more vital types have no readable sample | The vital cards load | Each affected card says `Unavailable`, no copy claims detected denial, no manual field appears, and other cards and plan content remain usable. |
| `AC-07` | `FR-07` | The latest readable RHR is from the same local calendar day | Upstream Feature 004 requests its RHR input | The sample may be supplied with provenance and timestamp, while Feature 002 performs no RPE calculation or confirmation. |
| `AC-08` | `FR-07` | No same-day readable RHR exists | Upstream Feature 004 requests its RHR input | Feature 002 supplies no stale RHR as the RPE input and still displays any latest readable dashboard sample with its actual timestamp. |
| `AC-09` | `FR-08` | Fewer than 8 recorded calendar days exist or the Chronic EWMA denominator is invalid | The workload card loads | ACWR is shown as unavailable with the authoritative reason and no synthetic ratio or category. |
| `AC-10` | `FR-08`, `FR-15` | Upstream supplies an available full-precision ACWR value | The workload card loads | The dashboard shows its numeric display value, Low/Moderate/High category at the exact `0.8` and `1.3` boundaries, and rationale without changing the upstream value used for decisions. |
| `AC-11` | `FR-09`, `FR-10`, `FR-15` | Feature 007 supplies an automatic adjustment and generated durations | The dashboard loads the result | It shows the supplied decision and rationale, preserves frequency/weekdays, and displays the upstream 5-minute-rounded and bounded durations without recalculation. |
| `AC-12` | `FR-11` | Upstream reports ACWR `> 1.3` and applies the approved recovery sequence | The affected plan is presented | The next scheduled session is Rest and later sessions resume at the supplied decreased duration. |
| `AC-13` | `FR-12` | A session is cancelled with cause `Injury` | The dashboard presents the current and subsequent plan states | The cause is visible, Rest remains until upstream reports resolution, and post-resolution sessions use the supplied Decrease outcome. |
| `AC-14` | `FR-13` | A session is cancelled for fatigue/overtraining | The dashboard presents the session | The cause is `Fatigue/Overtrained` and the supplied downstream branch is shown. |
| `AC-15` | `FR-13` | A session is cancelled for another physical complaint | The dashboard presents the session and adjustment | The cause is `Other Complaint`, the session is uncompleted, and the supplied downstream outcome is Decrease. |
| `AC-16` | `FR-14` | The final scheduled session is cancelled | The upstream weekly flow handles the cancellation | The dashboard accepts it as the terminal weekly event and presents the generated weekly outcome and next plan without waiting for another completed session. |
| `AC-17` | `FR-15` | An upstream output was produced using an approved edge or precision rule | The dashboard presents that output | Feature 002 preserves the upstream result and does not run an alternative RPE, EWMA, ACWR, tier, alpha, or duration calculation. |

## 7. Out of Scope

| Excluded item | Reason | Source |
|---|---|---|
| Exercise-session selection, preparation, complaint capture, phase execution, audio/haptic guidance, live Zone 2 guidance, and Zone 2 threshold calculation | These belong to Feature 003's separate exercise page; Feature 002 only consumes resulting session status and cause. | Guides 5–7; Guide 14 Phases 2–4; user clarification 2026-11-10 |
| RPE estimation, missing-input fallback UI, confirmation, or editing implementation | Feature 004 owns RPE behavior; Feature 002 consumes only its outputs and same-day RHR input contract. | Guide 8; user decision 2026-11-10 |
| Session/Daily Training Load, EWMA, and ACWR calculation implementation | Feature 005 owns workload computation; Feature 002 presents its authoritative output. | Guides 9–10; user decision 2026-11-10 |
| Weekly evaluation, RPE tier lookup, duration scaling, Rest/Decrease orchestration, or next-plan generation implementation | Feature 007 owns the automatic adjustment pipeline; Feature 002 presents its authoritative result. | Guides 11 and 13; user decision 2026-11-10 |
| Manual plan editing, frequency/day changes, and manual adjustment overrides | V1 is automatic, duration-only, and read-only. | User decision 2026-11-10 |
| Manual vital entry or use of HRV, SpO2, or respiratory rate as readiness/adaptation inputs | V1 vital cards are HealthKit-backed and display-only; no clinically reviewed readiness algorithm is approved. | Guide 2 §2.4; user decision 2026-11-10 |
| Nutrition, strength training, social/sharing, subscriptions, or remote backend sync | Excluded from the Kurin product scope. | AGENTS.md §7 |

## 8. Dependencies and Open Decisions

### Dependencies

| Direction | Dependency | Required contract | Source |
|---|---|---|---|
| Upstream | Feature 001 — Integrated Onboarding and Initial Plan | Persisted assigned Training Type and ordered current-plan session dates/durations after successful onboarding completion. | `docs/Feature/001-onboarding-health-profile/requirement.md` `FR-18`–`FR-31` |
| Upstream | Feature 003 — Exercise Session Guidance | Authoritative session status; cancellation cause `Injury`, `Fatigue/Overtrained`, or `Other Complaint`; actual final-scheduled cancellation event; injury-resolution signal. | Guides 7 §7.1 and 13 §§13.3, 13.6; user decisions 2026-11-10 and 2026-11-11 |
| Upstream | Feature 004 — RPE Estimation and Confirmation | Final RPE output; manual-only fallback for missing/invalid inputs; approved HRR clamping and nearest-whole `.5`-up rule; same-day RHR input consumption. | Guide 8 §§8.1–8.6; user decisions 2026-11-10 |
| Upstream | Feature 005 — Training Load and Workload Metrics | Authoritative ACWR availability, numeric display value, category, rationale, and full-precision decision value using zero-seeded acute/chronic EWMA. | Guides 9 and 10; user decisions 2026-11-10 |
| Upstream | Feature 006 — Daily Training Adjustment | Authoritative next-session Maintain, Decrease, or Rest result and the approved ACWR `> 1.3` recovery sequence. | Guide 12 §§12.1–12.3; user decision 2026-11-10 |
| Upstream | Feature 007 — Weekly Training Adjustment | Automatic decision/rationale, terminal-event handling, approved Rest/Decrease sequence, duration-only scaling result, and generated next-week plan preserving frequency/weekdays. | Guides 11 and 13; user decisions 2026-11-10 |
| External | HealthKit | Latest readable RHR, HRV, SpO2, and respiratory-rate samples with timestamps and provenance; no write access; privacy-safe unreadable behavior. | Guide 2 §2.4; Rule 01 HealthKit; user decisions 2026-11-10 |

### Open Decisions

| ID | Question | Owner | Impact if unresolved | Status |
|---|---|---|---|---|
| `OD-002-01` | Does the dashboard's `0 kcal` card represent current-week Kurin workout active energy, and how should a completed session with unreadable energy affect the total? | Product / User | Blocks calorie FRs, ACs, HealthKit quantity ownership, aggregation window, and unavailable behavior. | Open — asked 2026-11-11 |
| `OD-002-02` | Should the dashboard request the new vital/energy HealthKit reads automatically on first entry or only after an explicit Connect Apple Health action? | Product / User | Blocks authorization flow, first-entry UX, retry/skip behavior, and AC coverage. | Open — asked 2026-11-11 |
| `OD-002-03` | Before the first completed session, should the dashboard show `ACWR: Unavailable — no training-load history` and `Adjustment: Baseline — no adjustment yet`? | Product / User | Blocks the exact initial workload and adjustment state. | Open — asked 2026-11-11 |

### Resolved User Decisions

| ID | Decision | Authority | Requirement impact |
|---|---|---|---|
| `UD-002-01` | Create a separate user-facing Feature 002 Training Plan Dashboard; exercise guidance remains a different page and is out of scope. Cross-feature requirement planning may list Features 003, 004, 005, 006, and 007 as upstream contracts without authoring them. | Explicit user decision and clarification, 2026-11-10; renumbering approved 2026-11-11 | Feature boundary, dependency order, `FR-01`–`FR-04`, Out of Scope |
| `UD-002-02` | Show current Monday–Sunday plan and authoritative session type, duration, and status; show next-week sessions only after upstream generation; keep the plan read-only and automatically adjusted. | Explicit user decision, 2026-11-10 | `FR-01`–`FR-04`, `FR-16` |
| `UD-002-03` | Show latest-readable RHR (`bpm`), HRV (`ms`), SpO2 (`%`), and respiratory rate (`breaths/min`) with timestamps, no manual entry, and privacy-safe `Unavailable`; values are display-only except same-day RHR may feed Feature 004. | Explicit user decision, 2026-11-10 | `FR-05`–`FR-07` |
| `UD-002-04` | Use zero-seeded EWMA with full internal precision; apply the approved RPE clamp/fallback/rounding rules; map fractional Weekly Average RPE to the nearest tier with `.5` upward; use table percentage as `alpha`; round duration to the nearest 5 minutes before clamping. | Explicit user decision, 2026-11-10 | Upstream contracts, `FR-08`, `FR-15` |
| `UD-002-05` | V1 adaptation changes duration only and preserves frequency/weekdays. | Explicit user decision, 2026-11-10 | `FR-10` |
| `UD-002-06` | ACWR `> 1.3` rests the next scheduled session, then resumes later sessions at decreased duration. Injury rests until user-reported resolution, then resumes with Decrease. | Explicit user decision, 2026-11-10 | `FR-11`, `FR-12` |
| `UD-002-07` | Final-scheduled cancellation is a terminal weekly event. Injury emits `Injury`; fatigue emits `Fatigue/Overtrained`; other complaint emits `Other Complaint` and maps downstream to Decrease. | Explicit user decision and clarification, 2026-11-10 | `FR-12`–`FR-14` |
| `UD-002-08` | Renumber the dashboard from Feature 009 to Feature 002; place it immediately after Feature 001 for initial display and loop Features 006/007 adjustment outputs back into it. Renumber the former Features 004–008 to Features 003–007. | Explicit user decision, 2026-11-11 | Metadata, paths, lifecycle placement, and cross-feature references only |

## 9. Traceability

| Requirement | Acceptance criteria | Product source |
|---|---|---|
| `FR-01` | `AC-01` | User decisions 2026-11-10 |
| `FR-02` | `AC-02` | Guide 4 §4.4; user decision 2026-11-10 |
| `FR-03` | `AC-02` | Guides 4 §4.7 and 13 §13.3; user decision 2026-11-10 |
| `FR-04` | `AC-03`, `AC-04` | Guides 4 §4.7 and 13 §13.6; user decision 2026-11-10 |
| `FR-05` | `AC-05` | Guide 2 §2.4; user decision 2026-11-10 |
| `FR-06` | `AC-06` | Guide 2 §2.4 HealthKit privacy note; user decision 2026-11-10 |
| `FR-07` | `AC-07`, `AC-08` | Guides 2 §2.4 and 8 §8.2; user decision 2026-11-10 |
| `FR-08` | `AC-09`, `AC-10` | Guide 10 §§10.4–10.5; Guide 13 §13.2; user decision 2026-11-10 |
| `FR-09` | `AC-11` | Guide 13 §§13.1–13.5; user decision 2026-11-10 |
| `FR-10` | `AC-11` | User decision 2026-11-10 |
| `FR-11` | `AC-12` | Guides 12 §12.2 and 13 §13.4; user decision 2026-11-10 |
| `FR-12` | `AC-13` | Guides 7 §7.1 and 13 §§13.3–13.5; user decision 2026-11-10 |
| `FR-13` | `AC-14`, `AC-15` | Guides 7 §7.1 and 13 §13.3; user decisions 2026-11-10 |
| `FR-14` | `AC-16` | Guide 13 §13.6; user decision 2026-11-10 |
| `FR-15` | `AC-10`, `AC-11`, `AC-17` | Guides 8, 10, and 11; user decisions 2026-11-10 |
| `FR-16` | `AC-01`, `AC-04` | User decision 2026-11-10 |

## 10. Quality Gate

- [x] Scope is limited to Feature 002 presentation and plan management; upstream calculation and session execution
  remain dependency contracts and explicit exclusions.
- [x] Every vital, workload, plan, Rest, cancellation, scaling, and page-behavior decision is traced to an exact guide
  section or explicit user decision dated 2026-11-10.
- [x] Every `FR-01` through `FR-16` maps to at least one objective `AC-01` through `AC-17`.
- [x] Monday–Sunday plan boundaries, next-plan timing, read-only behavior, vital units, unavailable states, ACWR
  categories, duration-only adaptation, and complaint mappings are explicit.
- [x] Features 001 and 003–007 plus HealthKit are represented
  as upstream dependencies without absorbing their implementation ownership.
- [x] Session guidance, Zone 2 thresholds, RPE UI/calculation, workload calculation, weekly scaling/generation, manual
  plan edits, manual vital entry, and unsupported clinical interpretation are explicitly out of scope.
- [ ] Calorie, HealthKit authorization timing, and initial zero-history workload decisions are resolved before requirement review.
- [x] No specification, roadmap, phase plan, Swift code, build, or test work was created or performed.
- [x] All template comments and placeholders were removed.
