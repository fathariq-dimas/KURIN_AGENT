# Implementation Phase Plan — Health Screening and Automatic Disclaimer

> **Feature ID:** `001`  
> **Phase sequence:** `01`  
> **Task ID:** `001`  
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`  
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-01-health-screening-and-disclaimer.md`  
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md` — approved 2026-11-10  
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md` — approved 2026-11-10  
> **Target branch:** `feat/001-health-screening-flow` — intended; verified absent and must not be created before phase approval  
> **Base branch:** `development` — verified at `dd3204e`, tracking `origin/development`  
> **Prepared by:** `@planner`  
> **Executing agent:** `@builder` only after every handoff gate passes  
> **Status:** `Draft — blocked by parent roadmap RB-01 and GOV-01; not Ready for Review`

All paths below are canonical intended paths from the approved specification and `AGENTS.md`. They are not verified
against an Xcode target because the implementation workspace is absent.

## 1. Vertical-Slice Outcome and Phase Boundary

This phase delivers an independently verifiable first onboarding slice:

1. The user progresses in the fixed order diagnosed conditions → active symptoms → ongoing injuries.
2. Each screen uses its authoritative v1 closed multi-select catalog, enforces exclusive None, and provides no free text.
3. Any positive selection produces `hasHealthIssue == true`; all-None produces false.
4. An issue displays the exact v1 disclaimer without acknowledgement and automatically advances after presentation.
5. No issue skips the disclaimer. Both branches end at the `healthAccessChoice` state owned by Phase 02.

Coverage is exactly `FR-01`–`FR-06` and `AC-01`–`AC-05`. Sources: approved
`requirement.md` §§4, 6, 8; approved `spec.md` §2 “Authoritative V1 Catalogs,” §3 states/transitions, §4 screening
validation, and §5 “Screening Selection and Health Issue”; Guide 2 §2.4; Guide 3 §3.3.

This phase does not request HealthKit, capture or persist profile/goal data, calculate BMI, assign a training type,
configure a schedule, generate sessions, save onboarding, or create an incomplete-draft store.

## 2. Scope and Target File Map

| Change | Path | Layer | Purpose | Requirement / decision |
|---|---|---|---|---|
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ScreeningStage.swift` | Data / Model | Define the fixed diagnoses, symptoms, injuries order. | `FR-02`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ScreeningOption.swift` | Data / Model | Represent exact v1 ID, label, stage, and None identity. | `FR-02`, `FR-03`; requirement §8; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ScreeningStageSelection.swift` | Data / Model | Represent one stage's selected ID set and exclusive-None invariant. | `FR-02`, `FR-03`; spec §§2, 4 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/HealthScreeningDraft.swift` | Data / Model | Hold the three stage selections and computed issue result in memory. | `FR-02`–`FR-04`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/HealthScreeningService.swift` | Domain / Service | Supply authoritative catalogs, validate selection transitions, and evaluate `hasHealthIssue`. | `FR-02`–`FR-06`; spec §§4–6 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation / State | Define diagnoses, symptoms, injuries, disclaimer, and Phase 02 boundary states. | `FR-01`–`FR-06`; spec §3 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | Presentation / ViewModel | Orchestrate ordered stage events through the concrete screening service. | `FR-01`–`FR-06`; `NFR-02`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthScreeningView.swift` | Presentation / View | Render the active catalog stage with no business logic or free-text path. | `FR-01`–`FR-03`; `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthDisclaimerView.swift` | Presentation / View | Present the exact v1 message, no acknowledgement control, and automatic presentation-complete event. | `FR-05`, `FR-06`; `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/ScreeningSelectionSection.swift` | Presentation / Support View | Render accessible multi-select options and exclusive None state from ViewModel data. | `FR-02`, `FR-03`; `NFR-01`, `NFR-03` |
| Create | `KurinTests/Onboarding/HealthScreeningServiceTests.swift` | Tests | Verify catalog identity, None semantics, issue evaluation, and disclaimer output. | `AC-02`–`AC-04`; `NFR-05` |
| Create | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Tests | Verify ordered flow and disclaimer/no-disclaimer transitions. | `AC-01`, `AC-04`, `AC-05`; `NFR-05` |

No Data repository, SwiftData model, HealthKit type, App composition edit, or Phase 02 view is part of this slice.

## 3. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 1 | Define the exact screening values | `ScreeningStage.swift`, `ScreeningOption.swift`, `ScreeningStageSelection.swift`, `HealthScreeningDraft.swift` | Requirement `FR-02`–`FR-04` and §8; spec §2 | Parent roadmap handoff blockers resolved | Framework-independent value types containing only the approved v1 IDs/labels and explicit None identity | Source review proves exact IDs/labels, fixed stage order, and no invented catalog content | `AC-01`–`AC-03` |
| 2 | Implement deterministic selection and issue rules | `HealthScreeningService.swift` | Requirement `FR-02`–`FR-06`; spec §§4–6 | Step 1 | Concrete service with authoritative catalogs, exclusive-None transition, unknown-ID rejection, issue evaluation, and exact disclaimer output | Service tests pass and service coverage contributes to ≥80% Domain-service line coverage | `AC-02`–`AC-04` |
| 3 | Implement ordered observable state | `OnboardingStage.swift`, `OnboardingViewModel.swift` | Requirement `FR-01`–`FR-06`; spec §3; Rules 02 and 05 as governed by `GOV-01` | Step 2 | `@MainActor @Observable` ViewModel that calls the concrete service only and reaches the Phase 02 boundary state | ViewModel tests prove fixed ordering, exact conditional branch, automatic no-ack transition, and no name/reminder state | `AC-01`, `AC-04`, `AC-05` |
| 4 | Implement accessible screening/disclaimer presentation | `HealthScreeningView.swift`, `HealthDisclaimerView.swift`, `ScreeningSelectionSection.swift` | Requirement `NFR-01`, `NFR-03`; exact v1 copy; Rules 01 and 05 | Step 3 | SwiftUI screens using semantic styling, Dynamic Type, accessibility labels, no free text, no acknowledgement control, and no business logic | Objective VoiceOver, Dynamic Type, Dark Mode, and source inspection after workspace restoration | `AC-01`–`AC-05` |
| 5 | Complete AC-mapped verification | `HealthScreeningServiceTests.swift`, `OnboardingViewModelTests.swift` | Approved spec §8; Rule 03 | Steps 2–4 | Exact `test_AC01_...` through `test_AC05_...` tests with synthetic data only | Reconciled test command passes all five tests; Domain service coverage ≥80%; accessibility inspection passes | `AC-01`–`AC-05` |

## 4. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file |
|---|---|---|---|---|---|
| `AC-01` | Ordered ViewModel flow | `test_AC01_startsWithOrderedScreening()` | New in-memory ViewModel | Diagnoses → symptoms → injuries; no name/reminder state | `KurinTests/Onboarding/OnboardingViewModelTests.swift` |
| `AC-02` | Exclusive None behavior | `test_AC02_noneIsExclusiveInEveryCatalog()` | Exercise positive → None and None → positive in each authoritative v1 catalog | None clears positives; a positive clears None; all-None evaluates false | `KurinTests/Onboarding/HealthScreeningServiceTests.swift` |
| `AC-03` | Positive-selection issue evaluation | `test_AC03_anyPositiveSelectionSetsHealthIssue()` | Select each stage's positive v1 option separately | Exact ID is accepted, None clears, and issue evaluates true | `KurinTests/Onboarding/HealthScreeningServiceTests.swift` |
| `AC-04` | Issue disclaimer transition | `test_AC04_issueShowsExactAutomaticDisclaimer()` | Complete injuries with one positive selection, then emit presentation-complete | Exact v1 copy appears, no acknowledgement is stored/required, and state advances automatically | `KurinTests/Onboarding/HealthScreeningServiceTests.swift`, `KurinTests/Onboarding/OnboardingViewModelTests.swift` |
| `AC-05` | No-issue branch | `test_AC05_noIssueSkipsDisclaimer()` | Complete injuries after all three stages select None | ViewModel reaches `healthAccessChoice` without disclaimer | `KurinTests/Onboarding/OnboardingViewModelTests.swift` |

## 5. Verification Plan

| Verification | Command or procedure | Environment | Expected result | Risk addressed |
|---|---|---|---|---|
| Source/target reconciliation | After `RB-01`, inspect restored Xcode groups, iOS target membership, schemes, and test target; update intended paths only if approved structure differs. | Current Git checkout with restored Xcode/source/test workspace | Every planned file belongs to the intended iOS or test target. | Canonical paths may not match the actual project |
| Baseline build | Blocked: derive and record the exact non-destructive build command only after `RB-01` identifies a valid shared scheme and destination. | Restored Xcode project with iOS 27 SDK | Existing baseline and Phase 01 build succeed. | Invented/unexecutable build command |
| AC unit tests | Blocked: derive and record the exact test command only after `RB-01`; filter to the reconciled Phase 01 test classes. | Restored iOS test target | All five named AC tests pass without real HealthKit or external I/O. | Incorrect state or business-rule behavior |
| Coverage | Use the restored scheme's coverage report; exact procedure must be recorded after `RB-01`. | Same reconciled test run | `HealthScreeningService` and applicable Phase 01 Domain services meet ≥80% line coverage. | Untested decision branches |
| Accessibility and presentation | Inspect all three screening stages and disclaimer with VoiceOver, supported Dynamic Type sizes, Dark Mode, and the smallest supported iPhone layout available for the target. | Reconciled iPhone 17 simulator/device | Every control is labelled; content remains usable; disclaimer is presented/announced before automatic advance; no acknowledgement control exists. | Inaccessible or imperceptible automatic disclaimer |
| Static architecture review | Inspect imports and call direction after implementation. | Source review | Models are under Data/Models; ViewModel calls concrete service only; Views contain no business logic; no repository/service protocol exists. | Layer or protocol drift |

No build or test command is executable or authorized from this documentation-only workspace.

## 6. Architecture and Rule Compliance

| Check | Governing rule | Planned evidence |
|---|---|---|
| SwiftUI on iOS 27 with Dynamic Type, semantic colors, and accessibility labels | Rule 01; requirement `NFR-01` | Reconciled build plus accessibility inspection |
| Models use canonical `Data/Models`; business rules use `Domain/Services` | AGENTS.md §2; approved spec §1 | File/target and import review |
| Concrete `HealthScreeningService`; no repository/service protocols | Requirement `NFR-03`; approved spec §§1, 6; parent `GOV-01` | Rule 02 synchronization plus source review |
| `@MainActor @Observable` ViewModel calls service only | Rule 05; requirement `NFR-02`, `NFR-03` | Strict-concurrency build and call-direction review |
| Views contain no business logic or free-text path | Requirement `FR-02`, `NFR-03`; Rule 05 | View and ViewModel source review |
| No HealthKit, persistence, profile, assignment, or schedule work enters Phase 01 | Phase boundary; roadmap Phase 01 | File/diff review |
| AC-numbered isolated tests and ≥80% Domain-service coverage | Rule 03; requirement `NFR-05`; spec §8 | Test names, test isolation, and coverage report |
| Branch base/target follow Task ID 001 convention | Rule 06a | Verified base `development` at `dd3204e`; target-branch creation report only after phase approval |
| Only `@planner` changes planning docs; `@builder` treats them read-only | Rule 07 | Diff ownership review |

## 7. Phase Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Placeholder-looking v1 IDs/labels are “cleaned up” or replaced without an approved requirement revision | High | Medium | Define them once in the concrete service catalog and test exact raw IDs/labels. | Exact catalog fixture assertions and diff review |
| None coexists with a positive ID | High | Medium | Centralize selection transitions and validation in `HealthScreeningService`. | `test_AC02_noneIsExclusiveInEveryCatalog()` |
| A positive option fails to produce Easy Walking input later | High | Low | Represent `hasHealthIssue` as the tested OR of all positive selections; expose one result to later phases. | `test_AC03_anyPositiveSelectionSetsHealthIssue()` |
| Disclaimer adds an acknowledgement control or advances before it is presented accessibly | High | Medium | Keep copy and no-ack transition in the approved state contract; require presentation/accessibility inspection. | `test_AC04_issueShowsExactAutomaticDisclaimer()` and VoiceOver inspection |
| Screens can be completed out of order | Medium | Medium | Use the associated-value `OnboardingStage` state machine and guarded ViewModel events. | `test_AC01_startsWithOrderedScreening()` |
| Intended files or tests do not belong to real targets | High | Certain until `RB-01` resolves | Keep phase Draft and reconcile every path/scheme before phase review. | Source/target reconciliation |

### Rollback

If implementation or verification fails, stop before merge and return the phase-owned source/test diff for review.
On the Phase 01 working branch, revert only the files listed in Section 2 through normal version-control review; do not
discard unrelated changes. This phase writes no SwiftData or HealthKit data, so no user-data migration or deletion is
required. After rollback, run the reconciled baseline build/test procedures and confirm the pre-phase onboarding entry
behavior is restored before closing the rollback.

## 8. Builder Handoff Gate

- [x] Approved requirements and specification are cited.
- [x] Phase outcome and exclusions cover only `FR-01`–`FR-06` / `AC-01`–`AC-05`.
- [x] Canonical intended file paths and layer ownership are documented.
- [x] Every phase AC maps to the exact approved test name.
- [x] Risks, mitigations, rollback, architecture, and accessibility checks are documented.
- [x] Template comments, stale decision references, and superseded flow/status language are removed.
- [x] Base `development` is verified at `dd3204e` and tracks `origin/development`.
- [ ] `RB-01` reconciles actual paths, target membership, schemes, and executable build/test/coverage procedures.
- [ ] `GOV-01` synchronizes Rule 02 with the approved concrete no-protocol architecture.
- [ ] Target `feat/001-health-screening-flow` remains absent until the phase is approved, then is created through Rule 06a.
- [ ] The phase is explicitly reviewed and approved after blockers resolve.
- [ ] The parent roadmap records this phase as `Approved`, every predecessor as `Complete`, no blocking open decision for this phase, and builder handoff as ready.

**Handoff decision:** Locked. This plan remains Draft and must not be sent to `@builder`.
