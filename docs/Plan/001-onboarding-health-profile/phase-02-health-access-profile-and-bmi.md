# Implementation Phase Plan — Health Access, Profile, and BMI

> **Feature ID:** `001`
> **Phase sequence:** `02`
> **Task ID:** `001`
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-02-health-access-profile-and-bmi.md`
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md` — approved 2026-11-10
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md` — amended and approved 2026-11-10
> **Target branch:** `feat/001-health-profile-bmi` — intended only; existence and base are not reconciled
> **Base branch:** Unselected and unverified for Phase 02; Rule 06a selection is required before branch creation
> **Prepared by:** `@planner`
> **Executing agent:** `@builder` only after every handoff gate passes
> **Status:** `Draft — planning-only detail; non-executable while Phase 01, RB-01, GOV-01, branch/base, verification, and approval gates remain open`

The user explicitly authorized detailing Phase 02 on 2026-11-10 while Phase 01 remains incomplete because the current
focus is completing planning. This authority does not approve Phase 02, unlock implementation, authorize parallel
execution, create a branch, or permit builder handoff. All paths are canonical intended paths from the approved
specification and must be reconciled after the implementation workspace is restored.

## 1. Vertical-Slice Outcome and Phase Boundary

This phase produces an independently verifiable onboarding slice that begins at Phase 01's `healthAccessChoice` state:

1. The user chooses Use HealthKit or Skip.
2. Use HealthKit requests read access only for DOB, height, and body mass; Skip performs no HealthKit request.
3. Unavailable, request-failure, or query-failure paths offer Retry and Continue Manually without claiming detected
   per-type read denial.
4. Height and body weight independently use the readable sample with the latest `endDate`, with no freshness cutoff;
   a quantity with no readable sample remains `nil` and requires manual entry.
5. Readable values prefill as editable `HEALTH`; missing, entered, or edited values become local `MANUAL` values and
   are never written to HealthKit.
6. Valid confirmation requires a past DOB and positive height/body weight, then calculates raw BMI, one-decimal display
   BMI, and one continuous approved category before reaching Phase 03's `weightGoal` boundary state.

Coverage is exactly `FR-07`–`FR-15` and `AC-06`–`AC-14`. Sources: approved requirement §§4 and 6; approved spec §§2–9,
including `UD-SPEC-01`; Guide 2 §2.4; Guide 14 Phase 1; Guide 17 §§1, 1.2, 2, and 3.3.

This phase does not implement screening/disclaimer behavior, target-weight entry or range calculation, training-type
assignment, schedule/plan generation, completed-onboarding persistence, broader HealthKit reads, or HealthKit writes.

## 2. Guide Evidence Ledger

| Claim | Exact source | Inputs / units | Boundary, precision, or fallback | Downstream use |
|---|---|---|---|---|
| Health access precedes profile confirmation | `docs/guide/02-target-user.md` §2.4; `docs/guide/14-end-to-end-training-flow.md` Phase 1 | User choice and readable profile values | Manual recovery remains available | ViewModel state order |
| Feature 001 reads only DOB, height, and body mass | Approved requirement `FR-07`; spec §§4, 6, 9 | DOB characteristic; height and mass quantities | Guide 2's broader list is deferred by the approved Feature 001 requirement | Request-set construction and AC-06 |
| Missing values cannot be described as denied | `docs/guide/02-target-user.md` §2.4 HealthKit privacy note; `FR-10` | Optional readable values | Unavailable/unreadable copy; Retry or manual recovery | Error mapping and AC-08/AC-10 |
| Latest-readable quantity selection | Approved spec §5 `HealthKit Profile Sample Selection`, `UD-SPEC-01` | Readable height and body-mass samples | Select independently by greatest `endDate`; no freshness cutoff; none → `nil` | Repository query and AC-09/AC-10 |
| BMI calculation and units | `docs/guide/17-bmi-formula.md` §§1 and 1.2 | height cm → m; weight kg | Inputs strictly positive | `HealthProfileService` and AC-12/AC-13 |
| BMI classifications | `docs/guide/17-bmi-formula.md` §2 | Raw `Double` BMI | `<18.5`, `18.5..<25`, `25..<30`, `>=30` | Category output and AC-13/AC-14 |
| BMI precision | `docs/guide/17-bmi-formula.md` §3.3; approved spec §5 | Raw and display BMI | Classify with unrounded value; display one decimal, positive nearest with `.05` ties upward | ViewModel display data and boundary tests |

## 3. Scope and Target File Map

| Change | Canonical intended path | Layer | Purpose | Requirement / decision |
|---|---|---|---|---|
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/LocalDate.swift` | Data / Model | Represent DOB as a valid Gregorian date without time or time zone. | `FR-13`; spec §§2, 4 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ProfileValueSource.swift` | Data / Model | Define persisted `HEALTH` and `MANUAL` provenance. | `FR-12`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/HealthProfileSnapshot.swift` | Data / Model | Hold optional readable DOB/latest-readable height/body mass. | `FR-07`–`FR-11`; `UD-SPEC-01` |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/ConfirmedProfile.swift` | Data / Model | Hold validated profile values and provenance for later phases. | `FR-12`–`FR-15` |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/BMIClassification.swift` | Data / Model | Define the four continuous BMI categories. | `FR-15` |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/BMIResult.swift` | Data / Model | Hold unrounded BMI and its classification. | `FR-14`, `FR-15` |
| Create | `Kurin/iOS/Features/Onboarding/Data/Repositories/HealthDataRepository.swift` | Data / Repository | Perform exact read-only authorization and deterministic DOB/quantity queries through concrete closure seams for tests. | `FR-07`–`FR-12`; `UD-SPEC-01` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/OnboardingHealthError.swift` | Domain / Error | Represent unavailable, authorization-request, and query failures without inferred denial. | `FR-09`, `FR-10` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/HealthProfileError.swift` | Domain / Error | Represent invalid DOB, height, and body-weight fields. | `FR-13` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/OnboardingHealthService.swift` | Domain / Service | Orchestrate the concrete repository and typed privacy-safe recovery result. | `FR-07`–`FR-12` |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/HealthProfileService.swift` | Domain / Service | Apply provenance, validate profile values, calculate raw/display BMI, and classify it. | `FR-11`–`FR-15`; Guide 17 §§1–3.3 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation / State | Add requesting, unavailable, profile-confirmation, and Phase 03 boundary states. | Spec §3 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | Presentation / ViewModel | Orchestrate choice, retry/manual recovery, edits, provenance, validation, and BMI output through services only. | `FR-07`–`FR-15` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthAccessChoiceView.swift` | Presentation / View | Render Use HealthKit and Skip actions without business logic. | `FR-07`, `FR-08` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/HealthUnavailableView.swift` | Presentation / View | Render privacy-safe Retry and Continue Manually recovery. | `FR-09`, `FR-10` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/ProfileConfirmationView.swift` | Presentation / View | Render editable DOB/height/weight fields, source state, validation feedback, and confirmation. | `FR-11`–`FR-15` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/ProfileValueField.swift` | Presentation / Support View | Render one accessible profile value from ViewModel-provided state. | `FR-11`–`FR-13`; `NFR-01` |
| Modify | `Kurin/iOS/App/KurinApp.swift` | App composition | Construct the concrete repository/services and inject them into onboarding state. | `NFR-03`; spec §§1, 6 |
| Create/modify | `Kurin/iOS/Resources/Info.plist` | Target configuration | Add the read-purpose HealthKit usage description required by Rule 01. | `FR-07`; Rule 01 |
| Create/modify | `Kurin/Kurin.entitlements` | Target configuration | Enable only the HealthKit capability required by this read-only slice. | `FR-07`; Rule 01 |
| Modify | `Kurin.xcodeproj/project.pbxproj` | Target configuration | Reconcile entitlement, target membership, and source/test files after `RB-01`. | `RB-01`; Rule 01 |
| Create | `KurinTests/Onboarding/HealthDataRepositoryTests.swift` | Tests | Verify exact read set, latest-readable selection, no freshness cutoff, nil fallback, and no write path using closures only. | `AC-06`, `AC-09`, `AC-10` |
| Create | `KurinTests/Onboarding/OnboardingHealthServiceTests.swift` | Tests | Verify unavailable/failure recovery and privacy-safe mapping. | `AC-08`–`AC-10` |
| Create | `KurinTests/Onboarding/HealthProfileServiceTests.swift` | Tests | Verify provenance, validation, BMI formula/display, and continuous classifications. | `AC-09`–`AC-14` |
| Modify | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Tests | Verify Skip, request, retry/manual recovery, edit, confirm, and Phase 03 boundary transitions. | `AC-07`–`AC-12` |

The configuration paths are intended, not verified. `RB-01` requires replacing them with the restored target's actual
paths if they differ. No SwiftData model or incomplete-draft persistence is introduced in this phase.

## 4. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 1 | Reconcile Phase 02 target configuration and branch contract | `Info.plist`, entitlements, project file, restored schemes/targets | `RB-01`; Rule 01; Rule 06a | Phase 01 Complete; `RB-01`; explicit phase approval | Verified paths, target membership, read-purpose copy, HealthKit capability, selected base, and absent target branch | Reconciliation report only; branch remains uncreated until approval | `AC-06` |
| 2 | Define profile, provenance, date, and BMI value types | `LocalDate.swift`, `ProfileValueSource.swift`, `HealthProfileSnapshot.swift`, `ConfirmedProfile.swift`, `BMIClassification.swift`, `BMIResult.swift` | Requirement `FR-11`–`FR-15`; spec §2 | Step 1 | Framework-independent `Sendable` values matching exact units and boundaries | Source review confirms one type per file, no HealthKit type leakage, and no persistence | `AC-09`–`AC-14` |
| 3 | Implement read-only HealthKit access | `HealthDataRepository.swift`, `OnboardingHealthError.swift`, `OnboardingHealthService.swift` | Requirement `FR-07`–`FR-12`; spec §§4–7 and `UD-SPEC-01`; Guide 2 §2.4 | Step 2 | Concrete async repository/service requesting only three types; latest-readable independent quantity selection; typed privacy-safe recovery | Closure-based repository/service tests pass without a real store; no write API exists | `AC-06`, `AC-08`–`AC-10` |
| 4 | Validate profile, provenance, and BMI | `HealthProfileError.swift`, `HealthProfileService.swift` | Requirement `FR-11`–`FR-15`; spec §§4–6; Guide 17 §§1–3.3 | Steps 2–3 | Concrete synchronous service that applies source rules, rejects invalid values, retains raw BMI, and exposes one-decimal display data | Boundary/service tests prove source transitions, formula, rounding, and category continuity | `AC-09`–`AC-14` |
| 5 | Extend ordered observable onboarding state | `OnboardingStage.swift`, `OnboardingViewModel.swift`, `KurinApp.swift` | Spec §3 and §6; Rules 02 and 05 subject to `GOV-01` | Steps 3–4 | `@MainActor @Observable` state that calls concrete services only and ends at `weightGoal` with confirmed profile/BMI in memory | ViewModel tests prove choice, failure/recovery, edit, validation, and boundary transitions | `AC-07`–`AC-12` |
| 6 | Implement accessible Health access and profile presentation | `HealthAccessChoiceView.swift`, `HealthUnavailableView.swift`, `ProfileConfirmationView.swift`, `ProfileValueField.swift` | Requirement `NFR-01`, `FR-07`–`FR-13`; Guide 2 §2.4 | Step 5 | SwiftUI screens with semantic styling, Dynamic Type, labels, privacy-safe copy, and no business logic | Objective accessibility/presentation review after workspace restoration | `AC-06`–`AC-12` |
| 7 | Complete AC-mapped verification and cleanup | Four Phase 02 test files plus phase-owned sources | Approved spec §8; Rules 03 and 05 | Steps 1–6 | Exact AC-06 through AC-14 tests, DocC on non-trivial internal repository/service symbols, and no real HealthKit access | Reconciled test/coverage/build procedures pass; Domain services reach at least 80% line coverage | `AC-06`–`AC-14` |

## 5. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file(s) |
|---|---|---|---|---|---|
| `AC-06` | Exact read authorization set | `test_AC06_requestsOnlyDobHeightAndBodyMass()` | Capture repository request through a closure seam | Read set contains DOB, height, and body mass only; share set is empty | `HealthDataRepositoryTests.swift` |
| `AC-07` | Skip path | `test_AC07_skipAvoidsRequestAndUsesManualProfile()` | ViewModel at `healthAccessChoice` with request-call counter | Skip makes zero HealthKit calls and opens an all-manual profile | `OnboardingViewModelTests.swift` |
| `AC-08` | Unavailable/failure recovery | `test_AC08_healthFailureOffersRetryAndManual()` | Unavailable, throwing request, and throwing query seams | Each produces privacy-safe Retry and Continue Manually; retry re-enters exact request | `OnboardingHealthServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-09` | Full readable prefill | `test_AC09_readableValuesPrefillAsHealth()` | DOB plus multiple readable height/mass samples with distinct `endDate` values | Latest values are independently selected, editable, and `HEALTH`; older/broader metrics are ignored | `HealthDataRepositoryTests.swift`, `HealthProfileServiceTests.swift` |
| `AC-10` | Partial readable prefill | `test_AC10_partialSnapshotRequiresOnlyMissingValues()` | One quantity has an old readable sample; the other has none | Old sample remains eligible with no freshness cutoff; only missing quantity requires `MANUAL` entry | `HealthDataRepositoryTests.swift`, `OnboardingHealthServiceTests.swift` |
| `AC-11` | Manual edit provenance and no write | `test_AC11_manualEditStaysLocalAndChangesSource()` | Full `HEALTH` snapshot and a write-call trap | Edited value becomes `MANUAL`; unedited values remain `HEALTH`; no write occurs | `HealthProfileServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-12` | Profile boundaries | `test_AC12_validatesPastDobAndPositiveMeasurements()` | Today/future DOB, valid past DOB, and zero/negative measurements | Invalid values block confirmation; every past DOB with positive measures succeeds | `HealthProfileServiceTests.swift` |
| `AC-13` | BMI obesity boundary and display | `test_AC13_bmiThirtyDisplaysAndClassifiesObesity()` | Height 170 cm and weight 86.7 kg | Raw BMI is 30 boundary, classification is obesity, and display is `30.0 kg/m²` | `HealthProfileServiceTests.swift` |
| `AC-14` | Continuous BMI categories | `test_AC14_bmiCategoriesAreContinuous()` | Raw results immediately below, at, and above 18.5, 25, and 30 | Every input maps to exactly one approved category with no rounded comparison | `HealthProfileServiceTests.swift` |

## 6. Verification Plan

| Verification | Procedure | Environment | Expected result | Status while Draft |
|---|---|---|---|---|
| Source/target reconciliation | Inspect restored iOS/test targets, schemes, plist, entitlements, groups, and target membership; replace intended paths only if necessary. | Restored workspace | Every planned source, config, and test file belongs to the correct target. | Blocked by `RB-01` |
| Branch/base reconciliation | Follow Rule 06a to present bases, obtain selection, synchronize it, and verify `feat/001-health-profile-bmi` is unused. | Clean restored Git worktree | Selected base and commit are recorded; branch is created only after phase approval. | Unselected/unverified |
| Exact HealthKit request | Run closure-seam repository tests after the test target is restored. | Synthetic HealthKit types only | Exactly DOB/height/body mass reads; no share/write types. | Command not yet derivable |
| Privacy and recovery | Run service/ViewModel failure matrix and inspect user-visible copy. | Synthetic unavailable/throwing seams | No per-type denial claim; Retry and Continue Manually always recover. | Command not yet derivable |
| Sample selection | Supply multiple timestamped height/mass samples independently, including old and empty sets. | Synthetic sample fixtures | Greatest `endDate` wins per quantity; no freshness cutoff; empty set yields `nil`. | Command not yet derivable |
| BMI and profile rules | Run AC-11 through AC-14 service boundaries with deterministic current `LocalDate`. | Restored iOS test target | Provenance, validation, raw formula, display precision, and categories match the spec. | Command not yet derivable |
| Build and strict concurrency | Derive the exact non-destructive build procedure from the restored shared scheme. | iOS 27 SDK / reconciled destination | Actor isolation compiles; no `DispatchQueue`; no forbidden concurrency shortcut. | Blocked by `RB-01` |
| Coverage | Use the restored scheme's coverage report. | Same reconciled test run | Phase 02 Domain services and Feature 001 Domain-service aggregate meet at least 80% line coverage. | Procedure unverified |
| Accessibility and presentation | Inspect choice, unavailable, and profile screens with VoiceOver, Dynamic Type, Dark Mode, and smallest supported iPhone layout. | Reconciled iPhone 17 simulator/device | Controls are labelled, validation is perceivable, and all content remains usable. | Environment unavailable |
| Static architecture/privacy review | Inspect imports, call direction, entitlements, request/write APIs, and generated diff. | Source/config review | Concrete no-protocol design per approved spec, service-only ViewModel calls, zero View business logic, read-only HealthKit. | Blocked by `GOV-01` for handoff |

No build or test command is specified or authorized by this documentation-only plan. Exact commands must be derived and
recorded after `RB-01`; execution belongs to `@builder` only after all handoff gates pass.

## 7. Architecture and Rule Compliance

| Check | Governing source | Planned evidence |
|---|---|---|
| iOS 27 SwiftUI, Dynamic Type, semantic colors, and accessibility labels | Rule 01; requirement `NFR-01` | Reconciled build plus accessibility inspection |
| Least-privilege, read-only HealthKit and local-only manual edits | `FR-07`, `FR-12`; Rule 01 | Entitlement/request inspection and write-call trap |
| Read-authorization privacy is never inferred | `FR-10`; Guide 2 §2.4 | Error matrix and UI-copy inspection |
| Concrete `HealthDataRepository` and concrete services; no protocols | Approved spec §§1 and 6; requirement `NFR-03` | Rule 02 synchronization under `GOV-01`, then source review |
| Framework types remain inside the Data repository | Approved spec §§1, 2, 6 | Import and signature inspection |
| `@MainActor @Observable` ViewModel calls services only | Rule 05; `NFR-02`, `NFR-03` | Strict-concurrency build and call-direction review |
| Views contain no business or HealthKit logic | Rule 05; `NFR-03` | View/ViewModel source inspection |
| One type per file and DocC on non-trivial internal services/repository | Rule 05 | File and documentation review |
| Tests use synthetic closures, never a real HealthKit store or personal data | Rule 03; approved spec §8 | Test fixture/source review |
| Phase 02 adds no persistence or downstream assignment/plan behavior | Phase boundary | Diff review |

`GOV-01` remains blocking because current Rule 02 still requires Domain repository protocols while the approved
Feature 001 requirement/specification require concrete repositories/services without protocols. This plan follows the
approved feature docs but cannot be handed to `@builder` until governance is synchronized.

## 8. Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Broader HealthKit metrics or write authorization enter the request | High | Medium | Construct one exact read set and keep the share set empty. | `test_AC06_requestsOnlyDobHeightAndBodyMass()` plus entitlement review |
| Authorization status or missing data is presented as detected read denial | High | Medium | Map missing values to unavailable/unreadable and centralize privacy-safe failures. | `test_AC08_healthFailureOffersRetryAndManual()` and copy review |
| Height and body mass use the wrong sample or an invented freshness cutoff | High | Medium | Query independently, order by `endDate` descending, use the first readable sample, and test old samples. | AC-09/AC-10 repository fixtures |
| Manual edits preserve `HEALTH` or are written back | High | Medium | Apply provenance in `HealthProfileService`; expose no repository write operation. | `test_AC11_manualEditStaysLocalAndChangesSource()` |
| Height unit conversion corrupts BMI | High | Medium | Convert cm to m once inside the service and test the 170 cm / 86.7 kg boundary. | `test_AC13_bmiThirtyDisplaysAndClassifiesObesity()` |
| Rounded BMI is used for classification | High | Medium | Store raw `Double`, classify raw, and format separately. | AC-13/AC-14 boundary fixtures |
| DOB validation introduces an unsupported age gate | Medium | Low | Compare only to deterministic current local date. | `test_AC12_validatesPastDobAndPositiveMeasurements()` |
| Async HealthKit work updates UI off the main actor | High | Medium | Isolate repository access and keep ViewModel `@MainActor`. | Strict-concurrency build after `RB-01` |
| Intended plist, entitlement, or target paths differ from the restored workspace | High | Certain until restoration | Keep Draft and reconcile before phase review. | `RB-01` reconciliation report |

### Rollback

If later implementation or verification fails, stop before merge and review only the Phase 02 files listed in Section
3. Revert their source/test/config diff through normal version-control review without discarding unrelated changes.
Remove only the Phase 02 HealthKit capability/usage-description changes if they were introduced solely by this phase
and the target returns to its verified pre-phase state. This phase performs read-only HealthKit access and persists no
profile draft, so rollback requires no HealthKit write reversal, SwiftData migration, or user-data deletion. Re-run the
reconciled Phase 01 baseline after rollback once executable procedures exist.

## 9. Builder Handoff Gate

- [x] Approved requirements and amended/approved specification are cited.
- [x] Phase outcome/exclusions cover only `FR-07`–`FR-15` and `AC-06`–`AC-14`.
- [x] `UD-SPEC-01`, HealthKit privacy, BMI formula, raw/display precision, and classifications are source-traced.
- [x] Canonical intended file/config paths, layer ownership, tasks, AC mappings, risks, and rollback are documented.
- [x] The user explicitly authorized early planning detail only; no implementation parallelism is authorized.
- [ ] Phase 01 is verified and marked `Complete` by `@planner`.
- [ ] `RB-01` reconciles actual paths, target membership, schemes, and executable build/test/coverage procedures.
- [ ] `GOV-01` synchronizes Rule 02 with the approved concrete no-protocol architecture.
- [ ] Rule 06a base selection, synchronization, target-name availability, and branch contract are verified.
- [ ] Phase 02 receives explicit user review and approval after all readiness blockers are resolved.
- [ ] The parent roadmap records Phase 02 as `Approved`, every predecessor as `Complete`, and builder handoff as ready.
- [ ] `@builder` creates the approved branch and receives this phase only after every preceding gate passes.

**Handoff decision:** Locked. This planning-only Phase 02 detail remains Draft and non-executable.
