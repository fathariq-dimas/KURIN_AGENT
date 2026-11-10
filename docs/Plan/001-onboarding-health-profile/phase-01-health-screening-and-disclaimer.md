# Implementation Phase Plan — Health Screening and Conditional Disclaimer

> **Feature ID:** `001`  
> **Phase sequence:** `01`  
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`  
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-01-health-screening-and-disclaimer.md`  
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md`  
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md`  
> **Task ID:** `001`  
> **Target branch:** `feat/001-health-screening-flow`  
> **Base branch:** `Unresolved — user selection required by Rule 06a`  
> **Prepared by:** `@planner`  
> **Executing agent:** `@builder` after approval  
> **Status:** `Draft — blocked; not authorized for builder execution`

## 1. Vertical-Slice Outcome and Phase Boundary

The user can complete the first onboarding slice in the exact order diagnoses → symptoms → injuries. The system
produces one deterministic `hasHealthIssue` result, optionally presents the approved Health Disclaimer, and emits a
screening-complete event for Phase 02. An injury follows the same issue rule as any other selection and does not create
a separate onboarding branch.

This phase covers `FR-01`–`FR-04` and `AC-01`–`AC-03`. It does not request HealthKit, capture profile or goal data,
assign the training type, create a schedule, or persist completed onboarding. Source: Guide 2 §2.4; Guide 3 §3.3;
user decisions 2026-11-10.

Execution remains blocked until the screening catalogs (`OD-01`), disclaimer policy (`OD-02`), repository-rule impact
(`OD-08`), actual source/test targets (`RB-01`), and base branch (`RB-02`) are resolved in the parent roadmap.

## 2. Scope and Target File Map

These are intended repository-relative paths. `@planner` must reconcile them against the restored/available Xcode
source tree before the phase can become Ready for Review.

| Change | Path | Layer | Purpose | Requirement / decision |
|---|---|---|---|---|
| Create | `Kurin/iOS/Features/Onboarding/Domain/Models/HealthScreening.swift` | Domain model | Represent selected diagnosis, symptom, and injury IDs without framework types. | `FR-01`–`FR-03`, `OD-01` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Models/HealthScreeningEvaluation.swift` | Domain model | Carry `hasHealthIssue` and approved disclaimer outcome. | `FR-03`, `FR-04`, `OD-02` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/HealthScreeningService.swift` | Domain service | Validate approved IDs, compute issue flag, and map disclaimer triggers. | `FR-01`–`FR-04` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation state | Define the explicit diagnoses, symptoms, injuries, disclaimer, and screening-complete states. | `FR-01`–`FR-04`; Rule 05 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | ViewModel | Expose ordered screening/disclaimer states and call the screening service only. | `FR-01`–`FR-04`, `NFR-02`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthScreeningView.swift` | View | Render the active diagnoses, symptoms, or injuries stage. | `FR-01`, `FR-02`, `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthDisclaimerView.swift` | View | Render approved conditional disclaimer content and acknowledgement control. | `FR-04`, `OD-02`, `NFR-01` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/ScreeningSelectionSection.swift` | Support view | Render accessible catalog choices without business logic. | `FR-01`, `FR-02`, `OD-01` |
| Create | `KurinTests/Onboarding/HealthScreeningServiceTests.swift` | Unit tests | Verify selection evaluation and disclaimer mapping. | `AC-01`–`AC-03` |
| Create | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Unit tests | Verify ordered stage transitions and completion emission. | `AC-01`–`AC-03` |
| Create | `KurinTests/TestData/OnboardingTestData.swift` | Test fixtures | Centralize synthetic catalog IDs and non-personal fixtures. | Rule 03 Test Data |

No Data-layer persistence model, repository implementation, or completed-aggregate persistence is introduced in this
phase. Phase 05 owns the screening persistence representation, its mapping, and the atomic completed-aggregate save.

## 3. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 0 | Resolve phase gates and reconcile paths | Parent roadmap, feature docs, actual Xcode tree | `OD-01`, `OD-02`, `OD-08`, `RB-01`, `RB-02`; Rule 06a | None | Approved catalogs/disclaimer; reconciled files/targets; selected base | Roadmap records no Phase 01 blocker and user approval | All |
| 1 | Define screening domain representations | `HealthScreening.swift`, `HealthScreeningEvaluation.swift` | Requirement `FR-01`–`FR-04`; spec §2 | Step 0 | Framework-independent screening input and evaluation state | Static type review; no invented catalog IDs or persistence types | `AC-01`, `AC-02` |
| 2 | Implement deterministic screening evaluation | `HealthScreeningService.swift` | Guide 2 §2.4; Guide 3 §3.3; approved `OD-01`, `OD-02` | Step 1 | ID validation, OR-based issue flag, disclaimer outcome | Service tests prove each empty/non-empty category and trigger mapping | `AC-01`–`AC-03` |
| 3 | Implement explicit ordered presentation state | `OnboardingStage.swift`, `OnboardingViewModel.swift` | Spec §3 transitions; Rule 05 state/concurrency guidance | Step 2 | Dedicated stage enum plus `@MainActor @Observable` ViewModel ordered diagnoses → symptoms → injuries → disclaimer/completion | State-type review and ViewModel transition tests pass | `AC-01`–`AC-03` |
| 4 | Implement accessible screening views | `HealthScreeningView.swift`, `HealthDisclaimerView.swift`, `ScreeningSelectionSection.swift` | Approved catalog/copy; Rules 01 and 05 | Step 3 | Dynamic Type, semantic colors, labels, no business logic | Accessibility/static review | `AC-01`–`AC-03` |
| 5 | Complete tests and documentation | Test files and all phase production files | Rule 03; Rule 05 DocC | Steps 2–4 | AC-named tests, synthetic fixtures, DocC on non-trivial internal symbols | All Phase 01 tests pass; service coverage ≥80% | `AC-01`–`AC-03` |

## 4. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file |
|---|---|---|---|---|---|
| `AC-01` | Service and ordered ViewModel flow | `test_AC01_emptyOrderedScreeningHasNoHealthIssue()` | Approved catalogs; no selections in all three stages | Stages occur in order and completion result has `hasHealthIssue == false`. | `KurinTests/Onboarding/HealthScreeningServiceTests.swift`, `KurinTests/Onboarding/OnboardingViewModelTests.swift` |
| `AC-02` | Screening evaluation | `test_AC02_anyScreeningSelectionHasHealthIssue()` | One approved diagnosis, symptom, and injury fixture evaluated separately | Every non-empty category produces `hasHealthIssue == true`; injury has no extra branch. | `KurinTests/Onboarding/HealthScreeningServiceTests.swift` |
| `AC-03` | Disclaimer mapping and state transition | `test_AC03_disclaimerAppearsOnlyForApprovedTriggers()` | One approved trigger and one non-trigger result | Only the trigger enters disclaimer before emitting screening complete. | `KurinTests/Onboarding/HealthScreeningServiceTests.swift`, `KurinTests/Onboarding/OnboardingViewModelTests.swift` |

## 5. Verification Plan

| Verification | Command or procedure | Environment | Expected result | Risk addressed |
|---|---|---|---|---|
| Source/target reconciliation | Inspect the available Xcode project groups, iOS target, schemes, and test targets; update this plan if paths differ. | Repository checkout with Xcode project | Every path belongs to the intended iOS/test target before implementation. | Invented or stale paths |
| Build discovery | Use the repository-approved Xcode scheme-discovery procedure after `RB-01` is resolved; record the exact build command before handoff. | Xcode with iOS 27 SDK | A valid iOS scheme and destination are recorded in this file. | Unexecutable verification |
| AC unit tests | Run the approved test scheme filtered to `HealthScreeningServiceTests` and Phase 01 `OnboardingViewModelTests`. | iOS unit-test target | All `test_AC01_...` through `test_AC03_...` checks pass. | Incorrect stage/rule behavior |
| Coverage | Collect Xcode line coverage for `HealthScreeningService`. | Same test run | At least 80% line coverage. | Untested decision paths |
| Accessibility | Inspect diagnoses, symptoms, injuries, and disclaimer states with VoiceOver, Dynamic Type, and Dark Mode. | iPhone 17 simulator/device | Every interactive control is labelled and content remains usable. | Inaccessible first-run flow |
| Architecture review | Confirm Views contain no business logic, ViewModel calls only concrete service(s), and no repository is introduced in this slice. | Static review | Layer direction matches the approved rules and `OD-08` resolution. | Premature/incorrect abstraction |

Exact build/test commands remain intentionally absent until `RB-01` identifies real schemes and targets. A builder
handoff may not occur while this table contains an unverified procedure.

## 6. Architecture and Rule Compliance

| Check | Governing rule | Planned evidence |
|---|---|---|
| SwiftUI on iOS 27 with accessibility, Dynamic Type, and semantic colors | Rules 01 and 05 | Accessibility inspection and source review |
| Diagnoses → symptoms → injuries is represented as explicit state, not Boolean navigation flags | Requirement `FR-01`; Rule 05 state-machine guidance | ViewModel tests and state enum review |
| View → ViewModel → concrete Service with no View business logic | Rule 02; AGENTS.md | Import/call-direction review |
| No persistence repository is introduced in this slice | Phase boundary; `OD-08` | File/diff review |
| `@MainActor`, `@Observable`, async/await where needed, no DispatchQueue | Rule 05 | Strict-concurrency build and static review |
| AC-numbered tests and synthetic non-personal fixtures | Rule 03 | Test names and fixture review |
| Only `@planner` edits these docs; `@builder` treats them as read-only | Rule 07 | Diff ownership review |

## 7. Phase Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Invented or clinically incomplete screening choices | High | Certain until `OD-01` resolves | Block implementation and use only approved stable IDs. | Catalog approval recorded in roadmap |
| Incorrect disclaimer trigger/copy | High | Certain until `OD-02` resolves | Block implementation until product/clinical/legal approval. | Trigger and non-trigger AC fixtures |
| Injury accidentally gains a stricter undocumented branch | Medium | Medium | Centralize the OR-based issue rule and test injury separately. | `test_AC02_anyScreeningSelectionHasHealthIssue()` |
| Presentation permits stages out of order | Medium | Medium | Use one explicit stage enum and event guards. | `AC-01` ViewModel transition test |
| Planned paths do not match the actual Xcode project | High | Certain until `RB-01` resolves | Reconcile paths/targets before approval. | Source/target reconciliation procedure |

### Rollback

If Phase 01 verification fails, stop before merging and revert only the phase-owned source/test files on its working
branch through normal version-control review. This phase writes no user screening record to production persistence,
so no data deletion or migration rollback is required. Re-run the pre-phase build/test baseline after reversal and
return any requirement ambiguity to `@planner`.

## 8. Builder Handoff Gate

- [x] Phase outcome, current source citations, intended file map, AC mapping, risks, and rollback are documented.
- [x] No HealthKit, goal, assignment, schedule, or persistence implementation leaks into Phase 01.
- [ ] `OD-01` screening catalogs and none/free-text behavior are approved.
- [ ] `OD-02` disclaimer triggers, copy, and acknowledgement behavior are approved.
- [ ] `OD-08` is resolved or confirmed not to affect this repository-free phase.
- [ ] `RB-01` reconciles the actual source tree, schemes, targets, and exact verification commands.
- [ ] `RB-02` records the user-selected base branch and branch-name preflight.
- [ ] The parent roadmap records this phase as `Approved`, every predecessor as `Complete`, no blocking open decision for this phase, and builder handoff as ready.

**Handoff decision:** Locked. Do not send this phase to `@builder`.
