# Implementation Phase Plan — Weight Goal and Overview

> **Feature ID:** `001`
> **Phase sequence:** `03`
> **Task ID:** `001`
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-03-weight-goal-and-overview.md`
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md` — approved 2026-11-10
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md` — amended and approved 2026-11-10
> **Target branch:** `feat/001-weight-goal-overview` — intended only; existence and base are not reconciled
> **Base branch:** Unselected and unverified for Phase 03; Rule 06a selection is required before branch creation
> **Prepared by:** `@planner`
> **Executing agent:** `@builder` only after every handoff gate passes
> **Status:** `Draft — planning-only detail; non-executable while Phases 01–02, RB-01, GOV-01, branch/base, verification, and approval gates remain open`

The user explicitly authorized detailing Phase 03 on 2026-11-10 while Phases 01 and 02 remain incomplete because the
current focus is completing planning. This authority does not approve Phase 03, unlock implementation, authorize
parallel execution, create a branch, or permit builder handoff. All paths are canonical intended paths and must be
reconciled after the implementation workspace is restored.

## 1. Vertical-Slice Outcome and Phase Boundary

This phase begins at Phase 02's `weightGoal` boundary with a validated `ConfirmedProfile` and `BMIResult` in memory:

1. The user enters a target body weight in kilograms.
2. Any target greater than zero is accepted, including a target outside the informational recommended range;
   zero or a negative value prevents continuation.
3. The system calculates the raw informational normal-BMI range from confirmed height:
   `minimum = 18.5 × heightMeters²` and `maximum = 24.9 × heightMeters²`.
4. Raw target and range values remain in memory for later completion; the two range bounds display to one decimal
   using the approved positive nearest-rounding policy with `.05` ties upward.
5. Weight Goal Overview shows confirmed profile values, BMI value and category, target weight, and informational range.
6. Continue reaches Phase 04's `finalAssessment` boundary without assigning a training type in this phase.

Coverage is exactly `FR-16`–`FR-17` and `AC-15`–`AC-16`. Sources: approved requirement §§4 and 6; approved spec §§2–8;
Guide 2 §§2.4–2.5; Guide 14 Phase 1; Guide 17 §§3.1 and 3.3.

This phase does not change profile values or BMI, judge whether the target is clinically appropriate, constrain the
target to the recommended range, assign a training type, configure a schedule, generate sessions, or persist data.

## 2. Guide Evidence Ledger

| Claim | Exact source | Inputs / units | Boundary, precision, or fallback | Downstream use |
|---|---|---|---|---|
| Weight Goal follows profile and precedes overview/final assessment | `docs/guide/02-target-user.md` §2.4 steps 5–7; `docs/guide/14-end-to-end-training-flow.md` Phase 1 | Confirmed profile, BMI, target kg | Fixed flow order | ViewModel state transitions |
| Target validation | Approved requirement `FR-16`; approved spec §§4–7 | Target `Double`, kg | Must be `> 0`; no recommended-range gate | Service validation and AC-15/AC-16 |
| Recommended range formula | `docs/guide/17-bmi-formula.md` §3.1 | Confirmed height in meters | `18.5 × h²` through `24.9 × h²` | `WeightGoalService` and overview |
| Range storage/display precision | Approved spec §5 `Recommended Target-Weight Range`; Guide 17 §3.1 | Raw `Double` bounds | Store raw; display one decimal using positive nearest rounding with `.05` ties upward | AC-15 and display data |
| 170 cm worked example | `docs/guide/17-bmi-formula.md` §3.1 | `h = 1.70 m` | `53.465 → 53.5 kg`; `71.961 → 72.0 kg` | AC-15 fixture |
| Overview contents | Approved requirement `FR-17`; Guide 2 §2.4 step 6 | Confirmed profile, BMI/category, target, range | Every listed field must appear | ViewModel/View and AC-15 |

The approved sources do not define display precision for target weight, confirmed height, confirmed body weight, or
DOB on the overview. This phase must preserve their confirmed values and must not invent a new rounding or truncation
rule; only BMI and recommended-range precision already approved in the specification may be asserted by tests.

## 3. Scope and Target File Map

| Change | Canonical intended path | Layer | Purpose | Requirement / source |
|---|---|---|---|---|
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/WeightGoalOverview.swift` | Data / Model | Hold positive target kg and raw recommended minimum/maximum kg for the in-memory draft. | `FR-16`, `FR-17`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/WeightGoalError.swift` | Domain / Error | Represent the approved nonpositive-target validation failure. | `FR-16`; spec §§4, 7 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/WeightGoalService.swift` | Domain / Service | Validate target, calculate raw range, and expose approved display values. | `FR-16`, `FR-17`; Guide 17 §3.1 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation / State | Add `weightGoal`, `goalOverview`, and Phase 04 `finalAssessment` boundary state data. | Spec §3 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | Presentation / ViewModel | Orchestrate target validation, overview construction, in-memory draft update, and Continue transition. | `FR-16`, `FR-17`; spec §§3, 6 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/WeightGoalView.swift` | Presentation / View | Render kilogram target input, validation state, and continuation action with no business logic. | `FR-16`; `NFR-01`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/WeightGoalOverviewView.swift` | Presentation / View | Render all approved overview fields and informational range. | `FR-17`; `AC-15` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/GoalOverviewRow.swift` | Presentation / Support View | Render accessible label/value rows from ViewModel display data. | `FR-17`; `NFR-01` |
| Modify | `Kurin/iOS/App/KurinApp.swift` | App composition | Construct the concrete `WeightGoalService` and inject it into onboarding state. | `NFR-03`; spec §§1, 6 |
| Create | `KurinTests/Onboarding/WeightGoalServiceTests.swift` | Tests | Verify positive/out-of-range targets, rejection boundaries, formula, raw storage, and range display. | `AC-15`, `AC-16` |
| Modify | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Tests | Verify goal → overview → Phase 04 boundary transitions and complete overview data. | `AC-15`, `AC-16` |

No repository, HealthKit operation, SwiftData model, target entitlement, or persistence file is part of this phase.
Phase 02 owns `ConfirmedProfile` and `BMIResult`; Phase 03 consumes them without changing their values.

## 4. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 1 | Reconcile Phase 03 paths and branch contract | Restored source/test targets and Git inventory | `RB-01`; Rule 06a | Phases 01–02 Complete; `RB-01`; explicit phase approval | Verified target membership, selected/synchronized base, unused target branch, and executable verification procedures | Reconciliation report only; branch remains uncreated until approval | `AC-15`, `AC-16` |
| 2 | Define the in-memory overview value and error | `WeightGoalOverview.swift`, `WeightGoalError.swift` | Requirement `FR-16`, `FR-17`; spec §§2, 4, 7 | Step 1 and Phase 02 `ConfirmedProfile`/`BMIResult` | Framework-independent values carrying raw target/range and typed invalid-target failure | Source review proves no persistence and no unsupported bounds | `AC-15`, `AC-16` |
| 3 | Implement target validation and recommended range | `WeightGoalService.swift` | Requirement `FR-16`; spec §5; Guide 17 §3.1 | Step 2 | Concrete synchronous service accepting any target `> 0`, calculating raw bounds, and formatting approved one-decimal range values | Service tests prove worked example, out-of-range validity, raw values, rounding, and rejection | `AC-15`, `AC-16` |
| 4 | Extend ordered observable state | `OnboardingStage.swift`, `OnboardingViewModel.swift`, `KurinApp.swift` | Requirement `FR-16`, `FR-17`; spec §§3, 6; Rules 02 and 05 subject to `GOV-01` | Step 3 | `@MainActor @Observable` state that calls the service only, retains overview in memory, and ends at `finalAssessment` | ViewModel tests prove invalid retention, complete overview, and boundary transition | `AC-15`, `AC-16` |
| 5 | Implement accessible goal and overview presentation | `WeightGoalView.swift`, `WeightGoalOverviewView.swift`, `GoalOverviewRow.swift` | Requirement `FR-16`, `FR-17`, `NFR-01`, `NFR-03`; Guide 2 §2.4 | Step 4 | SwiftUI input/overview with semantic styling, accessibility labels, all required fields, informational wording, and no business logic | Objective VoiceOver, Dynamic Type, Dark Mode, and field-presence inspection after restoration | `AC-15`, `AC-16` |
| 6 | Complete AC-mapped verification and cleanup | `WeightGoalServiceTests.swift`, `OnboardingViewModelTests.swift`, phase-owned sources | Approved spec §8; Rules 03 and 05 | Steps 1–5 | Exact AC-15/AC-16 tests, DocC on internal service symbols, and clean phase boundary | Reconciled test/build procedures pass and Domain-service coverage remains at least 80% | `AC-15`, `AC-16` |

## 5. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file(s) |
|---|---|---|---|---|---|
| `AC-15` | Complete informational overview | `test_AC15_goalOverviewShowsInformationalRange()` | Confirmed 170 cm profile, existing BMI/category, and positive target values both inside and outside the range | Raw range is `53.465...`–`71.961...`; display is `53.5–72.0 kg`; outside-range target remains valid; profile, BMI/category, target, and range all appear | `WeightGoalServiceTests.swift`, `OnboardingViewModelTests.swift`; objective view inspection |
| `AC-16` | Nonpositive target rejection | `test_AC16_nonpositiveTargetIsRejected()` | Target `0` and negative target values at `weightGoal` | Service rejects both, ViewModel stays at goal input, identifies the field, and does not create an overview | `WeightGoalServiceTests.swift`, `OnboardingViewModelTests.swift` |

Supplemental service coverage should include a small positive target and a target above the recommended maximum to
prove the range is informational. It must not create a new AC or add a product constraint.

## 6. Verification Plan

| Verification | Procedure | Environment | Expected result | Status while Draft |
|---|---|---|---|---|
| Source/target reconciliation | Inspect restored Onboarding source/test groups, target membership, and Phase 02 output types. | Restored workspace after Phase 02 | Every planned file has a valid target; upstream types match approved contracts. | Blocked by `RB-01` and predecessors |
| Branch/base reconciliation | Follow Rule 06a to present bases, obtain selection, synchronize it, and verify `feat/001-weight-goal-overview` is unused. | Clean restored Git worktree | Selected base/commit recorded; branch created only after phase approval. | Unselected/unverified |
| Formula and rounding | Run deterministic `WeightGoalService` fixtures for 170 cm and boundary rounding cases. | Restored iOS test target | Raw values are retained; approved one-decimal display and `.05` ties-up policy hold. | Command not yet derivable |
| Validation matrix | Run zero, negative, small-positive, in-range, and outside-range targets. | Synthetic values only | Zero and negative values fail; recommended range never blocks a positive target. | Command not yet derivable |
| Overview completeness | Run ViewModel tests and inspect the rendered overview. | Restored iOS test target / iPhone 17 | Confirmed profile, BMI/category, target, and range all appear before Continue. | Environment unavailable |
| State boundary | Exercise `weightGoal → goalOverview → finalAssessment`. | In-memory ViewModel | No assignment or schedule behavior enters Phase 03. | Command not yet derivable |
| Build and strict concurrency | Derive the exact non-destructive build procedure from the restored shared scheme. | iOS 27 SDK / reconciled destination | Service/ViewModel composition compiles with actor isolation and no `DispatchQueue`. | Blocked by `RB-01` |
| Coverage | Use the restored scheme's coverage report. | Same reconciled test run | `WeightGoalService` and Feature 001 Domain-service aggregate meet at least 80% line coverage. | Procedure unverified |
| Accessibility/presentation | Inspect both screens with VoiceOver, Dynamic Type, Dark Mode, keyboard entry, and smallest supported iPhone layout. | Reconciled iPhone 17 simulator/device | Input/error/Continue are labelled and usable; overview field labels/values remain perceivable. | Environment unavailable |
| Static architecture review | Inspect imports, call direction, formula location, and phase diff. | Source review | No repository, View business logic, protocol abstraction, persistence, or downstream assignment logic. | Blocked by `GOV-01` for handoff |

No build or test command is specified or authorized by this documentation-only plan. Exact commands must be derived and
recorded after `RB-01`; execution belongs to `@builder` only after every handoff gate passes.

## 7. Architecture and Rule Compliance

| Check | Governing source | Planned evidence |
|---|---|---|
| `WeightGoalOverview` is an in-memory value; no Phase 03 persistence | Approved spec §§2 and 6; phase boundary | Model/diff review |
| Concrete `WeightGoalService`; no service or repository protocol | Approved spec §§1 and 6; requirement `NFR-03` | Rule 02 synchronization under `GOV-01`, then source review |
| Formula, validation, and display values live in the service/ViewModel, never Views | Rule 05; `NFR-03` | Call-direction and View inspection |
| `@MainActor @Observable` ViewModel calls the service only | Rule 05; `NFR-02`, `NFR-03` | Strict-concurrency build and source review |
| SwiftUI uses Dynamic Type, semantic colors, and accessibility labels | Rule 01; `NFR-01` | Accessibility/presentation review |
| One type per file and DocC on non-trivial service symbols | Rule 05 | File/documentation review |
| AC-numbered isolated tests and at least 80% Domain-service line coverage | Rule 03; `NFR-05` | Test names, fixtures, and coverage report |
| Phase 03 stops at `finalAssessment` and owns no Phase 04 behavior | Approved spec §3; roadmap boundary | State/diff review |

`GOV-01` remains blocking because current Rule 02 still requires Domain repository protocols while the approved
Feature 001 requirement/specification require concrete repositories/services without protocols. Phase 03 uses no
repository, but its concrete-service direction still cannot be handed off until governance is synchronized.

## 8. Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Recommended range incorrectly blocks a valid positive target | High | Medium | Centralize validation as target `> 0` only and test targets below/above range. | AC-15/AC-16 service tests |
| Formula uses centimeters without conversion or wrong BMI constants | High | Medium | Consume confirmed height, convert once to meters, and assert the 170 cm example. | `test_AC15_goalOverviewShowsInformationalRange()` |
| Rounded bounds replace raw stored values | Medium | Medium | Store raw `Double` bounds and derive display values separately. | Raw and display assertions in AC-15 |
| Wrong rounding policy changes `53.465` or boundary values | Medium | Medium | Use the approved positive nearest/`.05` ties-up formatter in domain/presentation data, not View code. | Formula/rounding fixtures |
| Target outside range is presented as invalid or unsafe | High | Medium | Label range informational and expose no comparison gate. | AC-15 ViewModel/view inspection |
| Overview omits a required profile/BMI/goal field | High | Medium | Build one ViewModel overview projection from the complete service output and assert field presence. | AC-15 ViewModel and objective view check |
| Phase 03 starts training assignment early | High | Low | End state is the `finalAssessment` boundary only; do not construct `InitialPlanService`. | State/diff review |
| Intended paths or test target differ from restored workspace | High | Certain until restoration | Keep Draft and reconcile after `RB-01` and Phase 02 completion. | Reconciliation report |

### Rollback

If later implementation or verification fails, stop before merge and review only the Phase 03 files listed in Section
3. Revert their source/test/composition diff through normal version-control review without discarding unrelated
changes. Phase 03 performs no HealthKit operation and writes no SwiftData record, so rollback requires no entitlement
change, migration, or user-data deletion. Restore the verified Phase 02 `weightGoal` boundary behavior and run the
reconciled predecessor baseline once executable procedures exist.

## 9. Builder Handoff Gate

- [x] Approved requirements and amended/approved specification are cited.
- [x] Phase outcome/exclusions cover only `FR-16`–`FR-17` and `AC-15`–`AC-16`.
- [x] Formula, units, validation, raw storage, one-decimal range display, and overview contents are source-traced.
- [x] Canonical intended paths, dependency-ordered tasks, exact AC mappings, verification, risks, and rollback exist.
- [x] The user explicitly authorized early planning detail only; no implementation parallelism is authorized.
- [ ] Phases 01 and 02 are verified and marked `Complete` by `@planner`.
- [ ] `RB-01` reconciles actual paths, target membership, schemes, and executable build/test/coverage procedures.
- [ ] `GOV-01` synchronizes Rule 02 with the approved concrete no-protocol architecture.
- [ ] Rule 06a base selection, synchronization, target-name availability, and branch contract are verified.
- [ ] Phase 03 receives explicit user review and approval after all readiness blockers are resolved.
- [ ] The parent roadmap records Phase 03 as `Approved`, every predecessor as `Complete`, and builder handoff as ready.
- [ ] `@builder` creates the approved branch and receives this phase only after every preceding gate passes.

**Handoff decision:** Locked. This planning-only Phase 03 detail remains Draft and non-executable.
