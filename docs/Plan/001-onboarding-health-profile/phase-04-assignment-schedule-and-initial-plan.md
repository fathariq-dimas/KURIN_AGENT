# Implementation Phase Plan — Assignment, Schedule, and Initial Plan

> **Feature ID:** `001`
> **Phase sequence:** `04`
> **Task ID:** `001`
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-04-assignment-schedule-and-initial-plan.md`
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md` — approved 2026-11-10
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md` — amended and approved 2026-11-10
> **Target branch:** `feat/001-initial-training-plan` — intended only; existence and base are not reconciled
> **Base branch:** Unselected and unverified for Phase 04; Rule 06a selection is required before branch creation
> **Prepared by:** `@planner`
> **Executing agent:** `@builder` only after every handoff gate passes
> **Status:** `Draft — planning-only detail; non-executable while Phases 01–03, RB-01, GOV-01, branch/base, verification, and approval gates remain open`

The user explicitly requested Phase 04 planning on 2026-11-11 while Phases 01–03 remain incomplete. This authority
permits documentation detail only; it does not approve Phase 04, unlock implementation, authorize parallel execution,
select or create a branch, or permit builder handoff. All paths are canonical intended paths from the approved
specification and must be reconciled after the implementation workspace is restored.

## 1. Vertical-Slice Outcome and Phase Boundary

This phase begins at Phase 03's `finalAssessment` boundary with a completed screening result, unrounded `BMIResult`,
and `WeightGoalOverview` retained in the in-memory onboarding draft:

1. Final Assessment assigns HR Zone 2 Activity only when there is no screening issue and unrounded BMI is below 30;
   any screening issue or unrounded BMI at least 30 assigns Easy Walking.
2. Easy Walking exposes a fixed 3 sessions/week × 15 minutes and permits changes only to workout weekdays and start.
3. HR Zone 2 exposes frequencies 3, 4, and 5 and only 5-minute duration values from 15 through 100, 75, or 60 minutes
   respectively.
4. The user selects exactly the frequency count of unique workout weekdays and chooses a start weekday from that set.
5. A start weekday matching the onboarding weekday offers Start Today or Same Day Next Week; a different weekday
   resolves to its nearest future occurrence without a deferral choice.
6. The service generates an ascending, Gregorian Monday–Sunday first-week plan: a current-week start includes only
   selected dates from the start through Sunday, while a next-week start includes every selected weekday in that week.
7. Every generated session uses a date-only `LocalDate`, so a later time-zone change cannot move its calendar day.
8. Successful generation reaches Phase 05's `review` boundary with the assignment, schedule, and plan in memory.

Coverage is exactly `FR-18`–`FR-27` and `AC-17`–`AC-27`. Sources: approved requirement §§4 and 6; approved spec §§2–8;
`docs/guide/03-training-types.md` §3.3; `docs/guide/04-weekly-training-plan.md` §§4.1–4.6;
`docs/guide/14-end-to-end-training-flow.md` Phase 1; `docs/guide/17-bmi-formula.md` §§3.2–3.3.

This phase does not change screening/profile/goal values, calculate target ranges, persist any draft or completed
record, implement the Phase 05 review/confirmation UI, report onboarding complete, generate recurring weekly plans, or
implement exercise guidance and post-session adjustment.

## 2. Guide Evidence Ledger

| Claim | Exact source | Inputs / units | Boundary, precision, or fallback | Downstream use |
|---|---|---|---|---|
| Training type assignment | `docs/guide/03-training-types.md` §3.3; `docs/guide/17-bmi-formula.md` §§3.2–3.3 | `hasHealthIssue`, unrounded `Double` BMI | Zone 2 only for no issue and BMI `< 30`; otherwise Easy Walking; never compare rounded display BMI | Assignment service and `AC-17`–`AC-19` |
| Easy Walking baseline | `docs/guide/03-training-types.md` §3.3; `docs/guide/04-weekly-training-plan.md` §§4.1–4.2 | sessions/week, minutes/session | Fixed `3 × 15` during onboarding; weekdays and start remain selectable | Schedule constraints and `AC-18`–`AC-20` |
| HR Zone 2 schedule bounds | `docs/guide/04-weekly-training-plan.md` §§4.1–4.2 | frequency `F`; whole minutes | `F ∈ {3,4,5}`; duration is a 5-minute value from 15 through `300/F`, yielding maxima 100/75/60 | Duration options, validation, and `AC-21` |
| Workout weekday validity | `docs/guide/04-weekly-training-plan.md` §§4.1, 4.5; approved requirement `FR-22`, `FR-23` | selected `Set<Weekday>` and start weekday | Unique count equals frequency; start weekday belongs to selected set | Schedule validation and `AC-20`, `AC-22` |
| Same-day start choice | `docs/guide/04-weekly-training-plan.md` §4.5; `docs/guide/14-end-to-end-training-flow.md` Phase 1 | onboarding date and matching selected weekday | Offer only Today or same weekday `+7` days | Start-choice presentation and `AC-23` |
| Different-day start resolution | `docs/guide/04-weekly-training-plan.md` §4.5; `docs/guide/14-end-to-end-training-flow.md` Phase 1 | onboarding date and different selected weekday | Use nearest future occurrence; never choose a past date and offer no deferral | Start resolution and `AC-24` |
| Monday–Sunday plan coverage | `docs/guide/04-weekly-training-plan.md` §§4.4–4.6 | resolved start and selected weekdays | Current week: start…Sunday only; next week: every selected date, including dates before the anchor | Plan generation and `AC-25`, `AC-26` |
| Partial-week planned count | `docs/guide/04-weekly-training-plan.md` §4.6 | generated session array | Planned frequency for the initial partial week is the exact generated session count, not nominal frequency | `InitialTrainingPlan` output contract for downstream weekly evaluation |
| Time-zone-stable dates | Approved requirement `FR-27`; approved spec §§2, 4–5 | Gregorian `LocalDate` encoded as `YYYY-MM-DD` only | No time or time-zone conversion; a later zone change preserves year/month/day | Session model and `AC-27` |

## 3. Scope and Target File Map

| Change | Canonical intended path | Layer | Purpose | Requirement / source |
|---|---|---|---|---|
| Modify | `Kurin/iOS/Features/Onboarding/Data/Models/LocalDate.swift` | Data / Model | Add or confirm deterministic Gregorian weekday, date addition, Monday/Sunday bounds, and lossless date-only encoding needed by plan generation. | `FR-23`–`FR-27`; spec §§2, 4–5 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/TrainingType.swift` | Data / Model | Represent the assigned `easyWalking` or `hrZone2Activity` track. | `FR-18`, `FR-19`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/Weekday.swift` | Data / Model | Represent Monday = 1 through Sunday = 7 without locale-dependent ordering. | `FR-22`–`FR-26`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/StartTimingChoice.swift` | Data / Model | Represent `today`, `sameDayNextWeek`, or service-resolved nearest future day. | `FR-24`, `FR-25`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ScheduleConfiguration.swift` | Data / Model | Carry validated frequency, duration, weekdays, selected start weekday, and resolved start date in memory. | `FR-20`–`FR-27`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/InitialTrainingSession.swift` | Data / Model | Carry one generated local session date and inherited duration. | `FR-26`, `FR-27`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/InitialTrainingPlan.swift` | Data / Model | Carry assigned type, Monday/Sunday bounds, and ascending first-week sessions. | `FR-18`–`FR-27`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/InitialPlanError.swift` | Domain / Error | Represent invalid frequency, duration, weekday count, start membership/choice, and date-generation failures. | `FR-20`–`FR-26`; spec §§4, 7 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/InitialPlanService.swift` | Domain / Service | Assign the training type, expose allowed schedule options, validate configuration, resolve start, and generate the Initial Training Plan. | `FR-18`–`FR-27`; spec §§4–6 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation / State | Add assignment/schedule state data and the Phase 05 `review` boundary carrying a valid generated plan. | Spec §3 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | Presentation / ViewModel | Orchestrate deterministic assignment, track-specific controls, validation, start-choice behavior, in-memory draft update, and review-boundary transition. | `FR-18`–`FR-27`; spec §§3, 6 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/ScheduleSetupView.swift` | Presentation / View | Render assigned track, allowed frequency/duration controls, weekdays, start weekday, conditional timing choice, validation, and generation action without business logic. | `FR-18`–`FR-26`; `NFR-01`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/TrainingAssignmentCard.swift` | Presentation / Support View | Present the assigned track and fixed/selectable schedule constraints from ViewModel display data. | `FR-18`–`FR-21`; `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/WeekdaySelectionGrid.swift` | Presentation / Support View | Render accessible unique weekday selection and selected start weekday state. | `FR-22`, `FR-23`; `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/StartTimingChoiceSection.swift` | Presentation / Support View | Render Today / Same Day Next Week only when the selected start weekday matches onboarding weekday. | `FR-24`, `FR-25`; `NFR-01` |
| Modify | `Kurin/iOS/App/KurinApp.swift` | App composition | Construct the concrete `InitialPlanService` and inject it into onboarding state. | `NFR-03`; spec §§1, 6 |
| Create | `KurinTests/Onboarding/InitialPlanServiceTests.swift` | Tests | Verify assignment, option sets, schedule validation, start resolution, coverage, ordering, and date-only output. | `AC-17`–`AC-27` |
| Create | `KurinTests/Onboarding/LocalDateTests.swift` | Tests | Verify Gregorian arithmetic/encoding and calendar-day stability across time-zone changes. | `AC-23`–`AC-27` |
| Modify | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Tests | Verify assignment-to-schedule orchestration, control state, validation retention, conditional start choices, and Phase 05 boundary. | `AC-17`–`AC-26` |

No repository, SwiftData model, HealthKit operation, entitlement, persistence transaction, or Phase 05 review view is
part of this slice. `LocalDate` is created in Phase 02; Phase 04 may extend only its approved date-only behavior.

## 4. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 1 | Reconcile Phase 04 inputs, targets, and branch contract | Restored source/test targets, Phase 01–03 outputs, Git inventory | `RB-01`; Rule 06a; parent roadmap | Phases 01–03 Complete; `RB-01`; explicit phase approval | Verified upstream type signatures, target membership, selected/synchronized base, unused target branch, and executable verification procedures | Reconciliation report only; branch remains uncreated until approval | `AC-17`–`AC-27` |
| 2 | Define assignment, schedule, and plan values | `TrainingType.swift`, `Weekday.swift`, `StartTimingChoice.swift`, `ScheduleConfiguration.swift`, `InitialTrainingSession.swift`, `InitialTrainingPlan.swift`, `InitialPlanError.swift` | Requirement `FR-18`–`FR-27`; spec §2 | Step 1 and Phase 02/03 model outputs | Framework-independent `Sendable` values with exact whole-minute, weekday, and date-only contracts | Source review proves one type per file, no persistence/framework leakage, and no unsupported schedule state | `AC-17`–`AC-22`, `AC-27` |
| 3 | Implement assignment and track-specific schedule options | `InitialPlanService.swift` | Requirement `FR-18`–`FR-22`; spec §§4–6; Guides 3 §3.3, 4 §§4.1–4.2, and 17 §§3.2–3.3 | Step 2 | Concrete synchronous service using raw BMI, fixed Easy Walking `3 × 15`, exact Zone 2 frequency/duration sets, and unique weekday-count validation | Service tests prove raw-BMI assignment boundaries, all option sets, and rejection of invalid track values/counts | `AC-17`–`AC-22` |
| 4 | Implement Gregorian start resolution and first-week generation | `LocalDate.swift`, `InitialPlanService.swift` | Requirement `FR-23`–`FR-27`; spec §§4–6; Guide 4 §§4.4–4.6; Guide 14 Phase 1 | Step 3 | Deterministic same-day choices, nearest-future resolution, Monday/Sunday bounds, ascending partial/full-week sessions, and date-only output | Service/model tests prove Thursday examples, no past dates, sorted uniqueness, and time-zone stability | `AC-23`–`AC-27` |
| 5 | Extend ordered observable onboarding state | `OnboardingStage.swift`, `OnboardingViewModel.swift`, `KurinApp.swift` | Requirement `FR-18`–`FR-27`; spec §§3, 6; Rules 02 and 05 subject to `GOV-01` | Steps 3–4 | `@MainActor @Observable` state that calls the concrete service only, retains valid assignment/schedule/plan in memory, and ends at `review` | ViewModel tests prove deterministic assignment, allowed controls/choices, validation retention, and boundary transition | `AC-17`–`AC-26` |
| 6 | Implement accessible assignment and schedule presentation | `ScheduleSetupView.swift`, `TrainingAssignmentCard.swift`, `WeekdaySelectionGrid.swift`, `StartTimingChoiceSection.swift` | Requirement `FR-18`–`FR-26`, `NFR-01`, `NFR-03`; Guides 3 §3.3 and 4 §§4.1–4.5 | Step 5 | SwiftUI schedule setup with semantic styling, Dynamic Type, labelled controls, fixed Easy Walking fields, exact Zone 2 options, and conditional start choices; no business logic | Objective VoiceOver, Dynamic Type, Dark Mode, control-state, and conditional-choice inspection after restoration | `AC-17`–`AC-24` |
| 7 | Complete AC-mapped verification and cleanup | `InitialPlanServiceTests.swift`, `LocalDateTests.swift`, `OnboardingViewModelTests.swift`, phase-owned sources | Approved spec §8; Rules 03 and 05 | Steps 1–6 | Exact AC-17 through AC-27 tests, DocC on non-trivial internal symbols, and clean Phase 05 boundary | Reconciled test/build procedures pass; Domain-service coverage remains at least 80%; accessibility inspection passes | `AC-17`–`AC-27` |

## 5. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file(s) |
|---|---|---|---|---|---|
| `AC-17` | Zone 2 assignment | `test_AC17_noIssueAndBmiBelowThirtyAssignsZone2()` | No screening issue; raw BMI fixtures in underweight, normal, and overweight ranges below 30 | Every fixture assigns HR Zone 2 Activity using raw BMI | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-18` | Screening-issue assignment | `test_AC18_screeningIssueAssignsEasyWalking()` | Any positive screening result and raw BMI below 30 | Easy Walking is assigned with immutable frequency 3 and duration 15 | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-19` | BMI 30 boundary assignment | `test_AC19_bmiThirtyAssignsEasyWalking()` | No issue and raw BMI exactly 30 | Easy Walking is assigned with immutable frequency 3 and duration 15 | `InitialPlanServiceTests.swift` |
| `AC-20` | Easy Walking control bounds | `test_AC20_easyWalkingLocksThreeByFifteen()` | Easy Walking assignment at schedule setup | Frequency/duration are fixed; only exactly three unique weekdays and a valid start are accepted | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift`; objective view inspection |
| `AC-21` | Zone 2 option sets | `test_AC21_zone2AllowsFiveMinuteDurationSets()` | Zone 2 with frequencies 3, 4, and 5 | Only 15…100, 15…75, and 15…60 in 5-minute steps are exposed/accepted; gaps and out-of-range values fail | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-22` | Weekday count/uniqueness | `test_AC22_weekdayCountMustEqualFrequency()` | Duplicate-source selection normalized to fewer unique days and other mismatched sets | Generation is rejected unless the unique weekday count exactly equals frequency | `InitialPlanServiceTests.swift` |
| `AC-23` | Same-weekday timing choices | `test_AC23_sameWeekdayOffersTodayOrNextWeek()` | Onboarding Thursday; Thursday selected and chosen as start | Exactly Today and Same Day Next Week are offered/resolved; no third timing option appears | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-24` | Different-weekday resolution | `test_AC24_differentWeekdayUsesNearestFutureDate()` | Onboarding Thursday; selected Saturday chosen as start | Nearest future Saturday resolves automatically with no deferral option | `InitialPlanServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-25` | Current-week partial plan | `test_AC25_currentWeekPlanExcludesPastSelectedDays()` | Thursday onboarding; Monday/Thursday/Saturday; Start Today | Monday–Sunday bounds are current week and sessions are Thursday/Saturday only, ascending | `InitialPlanServiceTests.swift`, `LocalDateTests.swift` |
| `AC-26` | Next-week full plan | `test_AC26_nextWeekPlanIncludesDaysBeforeAnchor()` | Thursday onboarding; Tuesday/Thursday/Saturday; Same Day Next Week | Next-week bounds contain Tuesday/Thursday/Saturday, including Tuesday before Thursday anchor | `InitialPlanServiceTests.swift`, `LocalDateTests.swift` |
| `AC-27` | Time-zone-stable local dates | `test_AC27_localDateDoesNotShiftWithTimeZone()` | Generated session encoded as `YYYY-MM-DD`; decode/inspect under two distinct time zones | Year/month/day and encoded value remain identical; no absolute instant is stored | `LocalDateTests.swift`, `InitialPlanServiceTests.swift` |

Supplemental service fixtures should cover BMI values immediately below/above 30, invalid frequency/duration choices,
start weekday outside the selected set, a different weekday whose nearest occurrence is next week, Sunday boundaries,
and ascending unique session ordering. They must not add new product rules or acceptance criteria.

## 6. Verification Plan

| Verification | Procedure | Environment | Expected result | Status while Draft |
|---|---|---|---|---|
| Source/target reconciliation | Inspect restored Onboarding source/test groups, Phase 01–03 outputs, target membership, schemes, and actual `LocalDate` API. | Restored workspace after Phase 03 | Every planned file has a valid target; upstream types and date helpers match approved contracts. | Blocked by `RB-01` and predecessors |
| Branch/base reconciliation | Follow Rule 06a to present bases, obtain selection, synchronize it, and verify `feat/001-initial-training-plan` is unused. | Clean restored Git worktree | Selected base/commit is recorded; branch is created only after phase approval. | Unselected/unverified |
| Assignment matrix | Run raw-BMI/no-issue and issue combinations, including immediately below/at/above 30. | Synthetic values only | Only no issue plus raw BMI `< 30` yields Zone 2; every other case yields Easy Walking. | Command not yet derivable |
| Schedule option matrix | Run Easy Walking and Zone 2 frequency/duration/weekday boundary fixtures. | Restored iOS test target | Exact option sets pass; all out-of-set values and mismatched unique-day counts fail. | Command not yet derivable |
| Calendar/start matrix | Run same-day Today/Next Week and different-day current/next-week fixtures across Monday/Sunday boundaries. | Fixed Gregorian dates | No past date; conditional choices and resolved dates match Guides 4 and 14. | Command not yet derivable |
| Plan coverage/order | Run the approved current-week and next-week Thursday examples plus a nearest-next-week different-day case. | Fixed Gregorian dates | Week bounds are Monday/Sunday; session arrays are unique, ascending, and complete for their coverage rule. | Command not yet derivable |
| Time-zone stability | Encode/decode generated `LocalDate` values while changing the calendar time-zone context used only by the test harness. | At least two distinct time zones | Stored/generated `YYYY-MM-DD` and year/month/day never shift. | Command not yet derivable |
| State boundary | Exercise `finalAssessment → scheduleSetup → review` with valid and invalid inputs. | In-memory ViewModel | Invalid input remains at setup with typed feedback; valid generation reaches review without saving or completion. | Command not yet derivable |
| Build and strict concurrency | Derive the exact non-destructive build procedure from the restored shared scheme. | iOS 27 SDK / reconciled destination | Service/ViewModel composition compiles with actor isolation and no `DispatchQueue`. | Blocked by `RB-01` |
| Coverage | Use the restored scheme's coverage report. | Same reconciled test run | `InitialPlanService` and Feature 001 Domain-service aggregate meet at least 80% line coverage. | Procedure unverified |
| Accessibility/presentation | Inspect schedule setup with VoiceOver, Dynamic Type, Dark Mode, every assignment/control state, and the smallest supported iPhone layout. | Reconciled iPhone 17 simulator/device | All controls and errors are labelled/perceivable; fixed fields cannot change; conditional timing choices are correct. | Environment unavailable |
| Static architecture/scope review | Inspect imports, call direction, rule locations, and phase diff. | Source review | No repository, persistence, protocol abstraction, View business logic, review UI, or downstream recurring-plan logic enters Phase 04. | `GOV-01` remains a parent handoff blocker |

No build or test command is specified or authorized by this documentation-only plan. Exact commands must be derived and
recorded after `RB-01`; execution belongs to `@builder` only after every handoff gate passes.

## 7. Architecture and Rule Compliance

| Check | Governing source | Planned evidence |
|---|---|---|
| Data values are framework-independent and remain in memory | Approved spec §§1–2, 6; phase boundary | Imports, lifecycle, and diff review |
| `InitialPlanService` is concrete and synchronous; no service/repository protocol is introduced | Approved requirement `NFR-03`; approved spec §§1, 6 | Source review after parent `GOV-01` resolution |
| Raw BMI, schedule validation, and calendar rules live in the service, never Views | Approved spec §§4–6; Rule 05 | Service tests and View inspection |
| `@MainActor @Observable` ViewModel calls the service only | Requirement `NFR-02`, `NFR-03`; Rule 05 | Strict-concurrency build and call-direction review |
| Gregorian `LocalDate` stores no absolute instant or time zone | `FR-27`; approved spec §§2, 4–5 | AC-27 model/service tests and encoding review |
| SwiftUI uses Dynamic Type, semantic colors, and accessibility labels | Rule 01; requirement `NFR-01` | Accessibility/presentation inspection |
| One type per file and DocC on non-trivial internal service/date symbols | Rule 05 | File/documentation review |
| AC-numbered isolated tests and at least 80% Domain-service line coverage | Rule 03; requirement `NFR-05`; spec §8 | Test names, deterministic fixtures, and coverage report |
| Phase 04 stops at `review` and owns no persistence, completion, or downstream recurring plan | Approved spec §3; roadmap Phase 04/05 boundary | State and diff review |
| Branch uses Task ID 001 only after Rule 06a base selection and explicit approval | Rule 06a | Recorded base/commit and later branch-creation report |

`GOV-01` remains a parent handoff blocker because current Rule 02 requires Domain repository protocols while the
approved Feature 001 requirement/specification prohibit repository/service protocols. Phase 04 introduces no
repository and keeps its service concrete, but builder handoff remains locked until project governance is synchronized.

## 8. Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Rounded display BMI is used for assignment near 30 | High | Medium | Pass and compare only `BMIResult.unroundedValue`; keep formatting outside assignment. | AC-17/AC-19 boundary fixtures |
| Easy Walking frequency or duration becomes editable | High | Medium | Service exposes only the fixed `3 × 15` configuration and ViewModel disables/rejects changes. | `test_AC20_easyWalkingLocksThreeByFifteen()` and control inspection |
| Zone 2 duration includes non-5-minute values or exceeds 300 minutes/week | High | Medium | Derive exact discrete options from the approved frequency set and test every allowed edge/gap. | `test_AC21_zone2AllowsFiveMinuteDurationSets()` |
| Duplicate or mismatched weekday input produces a plan | High | Medium | Normalize to a set and require its count to equal frequency before resolving any date. | `test_AC22_weekdayCountMustEqualFrequency()` |
| A start weekday outside selected days or a past date is accepted | High | Medium | Validate membership first and centralize forward-only Gregorian resolution. | AC-23/AC-24 plus supplemental invalid-start fixtures |
| Same-day and different-day paths show the wrong timing choices | High | Medium | Derive choices solely from weekday equality and test both branches through service/ViewModel. | `test_AC23_...`, `test_AC24_...`, objective UI inspection |
| Next-week plan drops selected days before the anchor | High | Medium | Once start is in next week, enumerate the entire Monday–Sunday week independently of anchor cutoff. | `test_AC26_nextWeekPlanIncludesDaysBeforeAnchor()` |
| Current-week plan includes already-past selected days | High | Medium | Filter current-week sessions to resolved start…Sunday inclusive. | `test_AC25_currentWeekPlanExcludesPastSelectedDays()` |
| Date arithmetic or encoding shifts a session after a time-zone change | High | Medium | Keep year/month/day as the domain value and encode only `YYYY-MM-DD`; never persist an absolute `Date`. | `test_AC27_localDateDoesNotShiftWithTimeZone()` |
| Phase 04 introduces persistence or recurring weekly generation early | High | Low | End at the Phase 05 `review` boundary and exclude repositories/SwiftData/Guide 4 §4.7 implementation. | State and diff review |
| Intended paths or upstream APIs differ from the restored workspace | High | Certain until restoration | Keep Draft and reconcile only after `RB-01` and Phases 01–03 completion. | Reconciliation report |

### Rollback

If later implementation or verification fails, stop before merge and review only the Phase 04 files listed in Section
3. Revert their source/test/composition diff through normal version-control review without discarding unrelated
changes. Restore the verified Phase 03 `finalAssessment` boundary and Phase 02 `LocalDate` behavior; do not remove
upstream types owned by earlier phases. Phase 04 performs no HealthKit operation or SwiftData write, so rollback needs
no entitlement change, migration, or user-data deletion. Run the reconciled Phase 03 baseline after rollback once
executable procedures exist.

## 9. Builder Handoff Gate

- [x] Approved requirements and amended/approved specification are cited.
- [x] Phase outcome/exclusions cover only `FR-18`–`FR-27` and `AC-17`–`AC-27`.
- [x] Assignment, track bounds, weekday/start rules, calendar coverage, and date-only stability are source-traced.
- [x] Canonical intended paths, dependency-ordered tasks, exact AC mappings, verification, risks, and rollback exist.
- [x] The user explicitly authorized early planning detail only; no implementation parallelism is authorized.
- [ ] Phases 01–03 are verified and marked `Complete` by `@planner`.
- [ ] `RB-01` reconciles actual paths, target membership, upstream APIs, schemes, and executable build/test/coverage procedures.
- [ ] `GOV-01` synchronizes Rule 02 with the approved concrete no-protocol architecture.
- [ ] Rule 06a base selection, synchronization, target-name availability, and branch contract are verified.
- [ ] Phase 04 receives explicit user review and approval after all readiness blockers are resolved.
- [ ] The parent roadmap records Phase 04 as `Approved`, every predecessor as `Complete`, and builder handoff as ready.
- [ ] `@builder` creates the approved branch and receives this phase only after every preceding gate passes.

**Handoff decision:** Locked. This planning-only Phase 04 detail remains Draft and non-executable.
