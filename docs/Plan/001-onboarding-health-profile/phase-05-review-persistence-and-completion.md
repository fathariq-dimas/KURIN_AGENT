# Implementation Phase Plan — Review, Persistence, and Completion

> **Feature ID:** `001`
> **Phase sequence:** `05`
> **Task ID:** `001`
> **Parent roadmap:** `docs/Plan/001-onboarding-health-profile/roadmap.md`
> **Output path:** `docs/Plan/001-onboarding-health-profile/phase-05-review-persistence-and-completion.md`
> **Source requirements:** `docs/Feature/001-onboarding-health-profile/requirement.md` — approved 2026-11-10
> **Source specification:** `docs/Feature/001-onboarding-health-profile/spec.md` — amended and approved 2026-11-10
> **Target branch:** `feat/001-onboarding-completion` — intended only; existence and base are not reconciled
> **Base branch:** Unselected and unverified for Phase 05; Rule 06a selection is required before branch creation
> **Prepared by:** `@planner`
> **Executing agent:** `@builder` only after every handoff gate passes
> **Status:** `Draft — planning-only detail; non-executable while Phases 01–04, RB-01, GOV-01, branch/base, verification, and approval gates remain open`

The user explicitly requested the next Feature 001 phase plan on 2026-11-11 while Phases 01–04 remain incomplete.
This authority permits documentation detail only; it does not approve Phase 05, unlock implementation, authorize
parallel execution, select or create a branch, or permit builder handoff. All paths are canonical intended paths from
the approved specification and must be reconciled after the implementation workspace is restored.

## 1. Vertical-Slice Outcome and Phase Boundary

This phase begins at Phase 04's `review` boundary with a complete screening, confirmed profile and BMI, weight-goal
overview, assigned training type, validated schedule, and generated Initial Training Plan held in process memory:

1. Review shows the assigned training type, weekly frequency, duration, selected weekdays, resolved start date, and
   every first-week session date; it provides confirmation only and adds no unsupported edit loop.
2. Explicit confirmation creates one complete in-memory `OnboardingDraft` and enters `saving` before any completion
   state or downstream handoff is reported.
3. A concrete SwiftData repository maps every approved screening, profile/provenance, BMI, goal, assignment, schedule,
   and plan value into one `OnboardingRecord` with ordered `OnboardingSessionRecord` children.
4. The root and all children are inserted and saved atomically. Date-only values are encoded losslessly as Gregorian
   `YYYY-MM-DD`; any validation, encoding, or save failure rolls back the attempted aggregate.
5. Success writes `completedAt`, enters `completed`, and emits only the persisted completion output needed for the
   Feature 002 routing contract. Feature 002 UI and dashboard behavior are not implemented here.
6. A recoverable SwiftData save failure keeps onboarding incomplete, retains the same in-memory draft, presents a
   recoverable failure, and retries the identical save from `saveFailed` without reconstructing or persisting an
   intermediate draft. Invalid aggregate or date encoding remains an internal non-completion failure and must not be
   reported as a successful or blindly retryable save.
7. No incomplete draft is written to SwiftData. Relaunch without a completed `OnboardingRecord` creates a fresh
   in-memory draft and starts at diagnoses; process memory from a failed or abandoned flow is never restored.

Coverage is exactly `FR-28`–`FR-31` and `AC-28`–`AC-31`. Sources: approved requirement §§4, 6, and 8; approved spec
§§2–3 and 6–9; `docs/guide/14-end-to-end-training-flow.md` Phase 1; explicit user decisions dated 2026-11-10 and
2026-11-11.

This phase does not modify prior screening/profile/goal/assignment/schedule rules, provide review editing, persist an
incomplete draft, write to HealthKit, sync remotely, implement the Feature 002 dashboard, execute a scheduled session,
or implement RPE, training load, and plan adjustment.

## 2. Evidence Ledger

| Claim | Exact source | Inputs / storage | Boundary or failure behavior | Downstream use |
|---|---|---|---|---|
| Required review content | Approved requirement `FR-28`, `AC-28`; approved spec §3 `review` | Assigned type, frequency, duration, weekdays, resolved start date, ordered first-week dates | Review follows valid generation and precedes persistence; no unsupported edit loop | Review projection and `AC-28` |
| Explicit confirmation gate | Approved requirement `FR-29`; approved spec §3 transition `review → saving` | Valid complete in-memory draft | No save starts without the user confirmation event | ViewModel state and `AC-29` |
| Complete aggregate mapping | Approved requirement `FR-29`; approved spec §2 fields and relationships | Screening IDs/issue, confirmed profile/provenance, BMI, goal/range, type, schedule, plan, sessions | Every required field and every session child belong to the same attempted aggregate | Repository mapping and `AC-29` |
| Atomic save and rollback | Approved requirement `FR-29`, `FR-30`; approved spec §§2, 6–7 and 9 | One root plus one-or-more ordered children in local SwiftData | Success persists all records; any failure leaves no partial root or child and reports no completion | Repository/service and `AC-29`, `AC-30` |
| Completion ordering | Approved requirement `FR-30`; approved spec §3 `saving → completed` | Persisted completion identity and `completedAt` | `completed` and downstream output occur only after atomic save succeeds | ViewModel/coordinator and `AC-29` |
| Same-flow retry | Approved requirement `FR-30`; approved spec §§2–3 and 7 | The unchanged process-memory `OnboardingDraft` | Failure enters `saveFailed`; Retry re-enters `saving`; no reconstruction or intermediate persistence | Recovery UI and `AC-30` |
| No incomplete-draft persistence | Approved requirement `FR-31`; approved spec §2 Relationships and Lifecycle | Process-memory draft versus completed SwiftData aggregate | Relaunch before success discards process memory and starts diagnoses when no completed record exists | Launch routing and `AC-31` |
| Post-success destination contract | `docs/guide/14-end-to-end-training-flow.md` Phase 1; `docs/README.md` Post-Onboarding Navigation | Persisted training type and ordered Initial Training Plan | Feature 001 emits completion only; Feature 002 owns dashboard presentation | Coordinator/app output contract after `AC-29` |
| Local-only persistence | Approved requirement `NFR-04`; Rule 01 HealthKit | SwiftData local store | No HealthKit write, iCloud storage, or remote backend | Data-flow review |

## 3. Scope and Target File Map

| Change | Canonical intended path | Layer | Purpose | Requirement / source |
|---|---|---|---|---|
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/OnboardingDraft.swift` | Data / Model | Assemble the complete screening, profile, BMI, goal, assignment, schedule, and plan values in memory only. | `FR-28`–`FR-31`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/OnboardingRecord.swift` | Data / SwiftData Model | Define the completed aggregate root with every approved persisted scalar/list field and cascade-owned sessions. | `FR-29`–`FR-31`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Models/OnboardingSessionRecord.swift` | Data / SwiftData Model | Persist one ordered session's Gregorian `YYYY-MM-DD` and duration as an aggregate child. | `FR-26`, `FR-27`, `FR-29`; spec §2 |
| Create | `Kurin/iOS/Features/Onboarding/Data/Repositories/OnboardingRepository.swift` | Data / Repository | Detect completed onboarding for launch routing and atomically save or roll back one completed aggregate using SwiftData. | `FR-29`–`FR-31`; spec §§2, 3, 6–7 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/OnboardingCompletionError.swift` | Domain / Error | Distinguish incomplete/invalid aggregate and date-encoding defects from recoverable persistence failure without exposing SwiftData types to presentation. | `FR-29`, `FR-30`; spec §§4, 7 |
| Create | `Kurin/iOS/Features/Onboarding/Domain/Services/OnboardingCompletionService.swift` | Domain / Service | Query completed-onboarding status for launch, validate completion input, call the concrete repository, and return completion identity only after atomic success. | `FR-29`–`FR-31`; spec §§3, 6 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingStage.swift` | Presentation / State | Carry review, saving, save-failed, and completed states with the approved transition payloads. | `FR-28`–`FR-31`; spec §3 |
| Modify | `Kurin/iOS/Features/Onboarding/Presentation/ViewModels/OnboardingViewModel.swift` | Presentation / ViewModel | Load completion status through the service, build review display data, assemble/retain the draft, explicitly confirm, await completion, map failure, and retry recoverable saves. | `FR-28`–`FR-31`; spec §§3, 6–7 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Coordinators/OnboardingCoordinator.swift` | Presentation / Coordinator | Route service-backed ViewModel states to diagnoses or successful completion output without fetching data or implementing Feature 002 UI. | `FR-30`, `FR-31`; Guide 14 Phase 1 |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/PlanReviewView.swift` | Presentation / View | Render all required review fields and the explicit confirmation action with no business logic or edit path. | `FR-28`, `FR-29`; `NFR-01`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/Views/SaveFailureView.swift` | Presentation / View | Render the recoverable failure and same-flow Retry action while the in-memory draft remains owned by the ViewModel. | `FR-30`; `NFR-01`, `NFR-03` |
| Create | `Kurin/iOS/Features/Onboarding/Presentation/SupportViews/PlanReviewRow.swift` | Presentation / Support View | Render accessible review labels and values supplied by the ViewModel. | `FR-28`; `NFR-01` |
| Modify | `Kurin/iOS/App/KurinApp.swift` | App composition | Register the completed models, construct the concrete repository/service/ViewModel/coordinator chain, and consume the completion output contract. | `NFR-03`; spec §§1, 6; Guide 14 Phase 1 |
| Create | `KurinTests/Onboarding/OnboardingRepositoryTests.swift` | Tests | Verify complete mapping, atomic root/children save, rollback, completed-record lookup, and absence of draft persistence. | `AC-29`–`AC-31` |
| Create | `KurinTests/Onboarding/OnboardingCompletionServiceTests.swift` | Tests | Verify success/failure ordering, typed propagation, and same-draft retry through concrete test seams. | `AC-29`, `AC-30`; `NFR-05` |
| Modify | `KurinTests/Onboarding/OnboardingViewModelTests.swift` | Tests | Verify complete review projection, confirmation, saving/completed ordering, failure/retry, and fresh relaunch state. | `AC-28`–`AC-31` |
| Create | `KurinTests/Onboarding/OnboardingCoordinatorTests.swift` | Tests | Verify launch routing and that completion output emits only after persistence succeeds. | `AC-29`, `AC-31` |

No Feature 002 View, ViewModel, Service, Repository, or test belongs to this phase. The coordinator exposes the
post-success output contract; the downstream feature owns its destination UI and behavior.

## 4. Dependency-Ordered Work Breakdown

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| 1 | Reconcile Phase 05 inputs, targets, persistence container, and branch contract | Restored source/test targets, Phase 01–04 outputs, Git inventory | `RB-01`; Rule 06a; parent roadmap | Phases 01–04 Complete; `RB-01`; explicit phase approval | Verified upstream types, target/model-container membership, selected/synchronized base, unused target branch, and executable verification procedures | Reconciliation report only; branch remains uncreated until approval | `AC-28`–`AC-31` |
| 2 | Define the complete memory-only draft and SwiftData aggregate | `OnboardingDraft.swift`, `OnboardingRecord.swift`, `OnboardingSessionRecord.swift`, `OnboardingCompletionError.swift` | Requirement `FR-28`–`FR-31`; spec §2 | Step 1 and verified Phase 01–04 outputs | One non-persistent complete draft plus one root/child completed schema containing exactly the approved fields | Schema/lifecycle review proves no incomplete-draft model, no remote data, valid cascade ownership, and one type per file | `AC-29`–`AC-31` |
| 3 | Implement lossless aggregate mapping and atomic persistence | `OnboardingRepository.swift` | Requirement `FR-29`–`FR-31`; spec §§2, 4, 6–7 and 9 | Step 2 | Concrete model-context-isolated repository that checks completion presence and saves root/ordered children in one atomic operation with rollback on every failure | In-memory SwiftData integration tests prove all required fields/children commit together and failed attempts leave no partial record | `AC-29`–`AC-31` |
| 4 | Implement launch-status and completion orchestration | `OnboardingCompletionService.swift` | Requirement preconditions, `FR-29`–`FR-31`; spec §§3, 6–7 | Step 3 | Concrete async service that queries completed status, accepts only a complete draft, returns identity after success, and distinguishes recoverable save failure from internal invalid/encoding failure | Service tests prove launch status, success ordering, typed failure propagation, and recoverable retry using the unchanged draft | `AC-29`–`AC-31` |
| 5 | Implement review, launch, save, failure, retry, and completion state | `OnboardingStage.swift`, `OnboardingViewModel.swift` | Requirement `FR-28`–`FR-31`; spec §§2–3 and 6–8; Rules 02 and 05 subject to `GOV-01` | Steps 2–4 | `@MainActor @Observable` ViewModel with service-backed launch status, complete review projection, explicit confirm, `saving`, recoverable `saveFailed`, retry, and post-success `completed` output | ViewModel tests prove fresh launch, every required review field, no pre-save completion, same-draft retry, and no draft restoration | `AC-28`–`AC-31` |
| 6 | Implement accessible review and recoverable failure presentation | `PlanReviewView.swift`, `SaveFailureView.swift`, `PlanReviewRow.swift` | Requirement `FR-28`–`FR-30`, `NFR-01`, `NFR-03`; spec §3 | Step 5 | SwiftUI review with exact required fields/confirm action and labelled failure/retry state; no edit or business-logic path | Objective VoiceOver, Dynamic Type, Dark Mode, field-presence, disabled-during-save, and retry inspection after restoration | `AC-28`–`AC-30` |
| 7 | Implement launch and downstream handoff boundary | `OnboardingCoordinator.swift`, `KurinApp.swift` | Requirement preconditions, `FR-30`, `FR-31`; spec §§1–3 and 6; Guide 14 Phase 1 | Steps 3–6 | Coordinator consumes ViewModel state without fetching data, presents a fresh diagnoses flow when no completed record exists, and emits completion only after success; app composition consumes the output without Feature 002 implementation | Coordinator tests and scope review prove failure/relaunch never emits completion, call direction is preserved, and no downstream feature files change | `AC-29`–`AC-31` |
| 8 | Complete AC-mapped verification and cleanup | `OnboardingRepositoryTests.swift`, `OnboardingCompletionServiceTests.swift`, `OnboardingViewModelTests.swift`, `OnboardingCoordinatorTests.swift`, phase-owned sources | Approved spec §8; Rules 03 and 05 | Steps 1–7 | Exact AC-28 through AC-31 tests, DocC on non-trivial internal repository/service symbols, and clean Feature 001 output boundary | Reconciled build/test procedures pass; Domain-service coverage remains at least 80%; accessibility and persistence inspections pass | `AC-28`–`AC-31` |

## 5. Acceptance Criteria to Test Mapping

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file(s) |
|---|---|---|---|---|---|
| `AC-28` | Complete review projection | `test_AC28_reviewShowsCompleteGeneratedPlan()` | Valid Phase 04 assignment, schedule, and generated plan in memory | Review exposes training type, frequency, duration, selected weekdays, resolved start date, and every ordered session date before confirmation | `OnboardingViewModelTests.swift`; objective `PlanReviewView` inspection |
| `AC-29` | Atomic successful completion | `test_AC29_completionFollowsAtomicSave()` | Complete draft and in-memory SwiftData store with deterministic completion seam | Explicit confirmation enters saving; all approved root fields and every session child commit together; only then do completed state and output emit | `OnboardingRepositoryTests.swift`, `OnboardingCompletionServiceTests.swift`, `OnboardingViewModelTests.swift`, `OnboardingCoordinatorTests.swift` |
| `AC-30` | Rollback and same-flow retry | `test_AC30_saveFailureRollsBackAndRetries()` | Concrete repository failure seam for the first save, success on retry, complete in-memory draft | First attempt leaves no root/child, stays incomplete, exposes recoverable retry, and preserves the same draft; retry commits once and then completes | `OnboardingRepositoryTests.swift`, `OnboardingCompletionServiceTests.swift`, `OnboardingViewModelTests.swift` |
| `AC-31` | No incomplete-draft restoration | `test_AC31_relaunchDiscardsIncompleteDraft()` | Abandoned or failed flow with no completed record, then recreate app/coordinator/ViewModel against the same local store | Store contains no draft or partial aggregate and the recreated flow starts at diagnoses; no prior process-memory value is restored | `OnboardingRepositoryTests.swift`, `OnboardingViewModelTests.swift`, `OnboardingCoordinatorTests.swift` |

Supplemental integration coverage should verify every persisted `OnboardingRecord` field, ordered session-child
mapping, lossless date-only strings, completed-record launch detection, repeated failure without duplicate/partial
records, and completion output suppression until success. It must not define Feature 002 presentation behavior.

## 6. Verification Plan

| Verification | Procedure | Environment | Expected result | Status while Draft |
|---|---|---|---|---|
| Source/target reconciliation | Inspect restored Onboarding source/test groups, Phase 01–04 outputs, SwiftData model container, target membership, schemes, and composition root. | Restored workspace after Phase 04 | Every planned file has a valid target; all upstream draft inputs and model-container registrations are identified. | Blocked by `RB-01` and predecessors |
| Branch/base reconciliation | Follow Rule 06a to present bases, obtain selection, synchronize it, and verify `feat/001-onboarding-completion` is unused. | Clean restored Git worktree | Selected base/commit is recorded; branch is created only after phase approval. | Unselected/unverified |
| Review completeness | Run AC-28 ViewModel fixtures and inspect the rendered review. | Restored iOS test target / iPhone 17 | Every required plan value/date appears once in the correct order; confirmation is explicit and no edit path exists. | Command not yet derivable |
| Aggregate mapping | Save a synthetic complete draft containing distinct values for every field and multiple ordered sessions. | In-memory SwiftData container | One root and all ordered children contain exact approved values and date-only encodings. | Command not yet derivable |
| Atomicity/rollback | Inject mapping/encoding/save failures at the concrete repository seam and inspect a fresh context after each attempt. | In-memory SwiftData container | No failed attempt leaves a root, child, completion timestamp, or duplicate; success commits the entire aggregate once. | Command not yet derivable |
| State and retry ordering | Exercise `review → saving → saveFailed → saving → completed` with a recoverable persistence failure and capture completion output. Separately inject invalid/encoding failure. | In-memory ViewModel/service/repository stack | Recoverable failure retains the same draft and emits no completion; retry succeeds before completed/output. Internal invalid/encoding failure never reports success or blind retry. | Command not yet derivable |
| Relaunch behavior | Abandon and fail separate onboarding flows, discard process objects, then recreate composition with no completed record. | Same local in-memory/persistent test container with new process-level objects | New flow starts diagnoses and restores no prior draft values. | Command not yet derivable |
| Downstream boundary | Inspect completion callback/output and changed-file set. | Source review and coordinator test | Feature 001 emits the persisted completion contract only; no Feature 002 file or UI is implemented. | Command not yet derivable |
| Build and strict concurrency | Derive the exact non-destructive build procedure from the restored shared scheme. | iOS 27 SDK / reconciled destination | SwiftData isolation, async service, `@MainActor` ViewModel, and app composition compile without `DispatchQueue`. | Blocked by `RB-01` |
| Coverage | Use the restored scheme's coverage report. | Same reconciled test run | `OnboardingCompletionService` and Feature 001 Domain-service aggregate meet at least 80% line coverage. | Procedure unverified |
| Accessibility/presentation | Inspect review, saving, and failure/retry states with VoiceOver, Dynamic Type, Dark Mode, and the smallest supported iPhone layout. | Reconciled iPhone 17 simulator/device | Required fields, confirmation, progress, failure, and Retry are labelled and remain usable/perceivable. | Environment unavailable |
| Static architecture/privacy review | Inspect imports, call direction, model lifecycle, entitlements, persistence configuration, and phase diff. | Source/config review | Concrete no-protocol chain, local-only completed data, no incomplete draft model, no HealthKit write, no View business logic, and no downstream scope leak. | `GOV-01` remains a parent handoff blocker |

No build or test command is specified or authorized by this documentation-only plan. Exact commands must be derived and
recorded after `RB-01`; execution belongs to `@builder` only after every handoff gate passes.

## 7. Architecture and Rule Compliance

| Check | Governing source | Planned evidence |
|---|---|---|
| One memory-only `OnboardingDraft`; only the successful completed aggregate enters SwiftData | Approved requirement `FR-29`–`FR-31`; approved spec §2 | Model lifecycle and store inspection |
| Concrete `OnboardingRepository` and `OnboardingCompletionService`; no repository/service protocol | Approved requirement `NFR-03`; approved spec §§1, 6 | Source review after parent `GOV-01` resolution |
| ViewModel calls the service only; service calls the repository; Views contain no business logic | Approved requirement `NFR-03`; Rules 02 and 05 | Call-direction and import review |
| Coordinator consumes ViewModel navigation state and performs no data fetching | Rule 02 Coordinator responsibility; approved spec §§1, 3 | Coordinator call-direction review |
| SwiftData model-context access is actor-isolated; async completion uses no `DispatchQueue` | Requirement `NFR-02`; Rule 05 | Strict-concurrency build and source review |
| Root and child save is atomic; every failure rolls back and suppresses completion | `FR-29`, `FR-30`; approved spec §§2, 6–7 | AC-29/AC-30 integration tests and fresh-context inspection |
| Stored dates remain Gregorian `YYYY-MM-DD` values without time-zone conversion | `FR-27`; approved spec §§2, 4–5 | Aggregate mapping and date-encoding assertions |
| Completed data remains local; no HealthKit write, iCloud, or remote transport | Requirement `NFR-04`; Rule 01 | Entitlement, API, and data-flow review |
| SwiftUI uses Dynamic Type, semantic colors, and accessibility labels | Rule 01; requirement `NFR-01` | Accessibility/presentation inspection |
| One type per file and DocC on non-trivial repository/service symbols | Rule 05 | File/documentation review |
| AC-numbered isolated tests, in-memory SwiftData, and at least 80% Domain-service coverage | Rule 03; requirement `NFR-05`; spec §8 | Test names, isolation, fixtures, and coverage report |
| Phase 05 emits the post-success contract but implements no Feature 002 files | Feature boundary; Guide 14 Phase 1; `docs/README.md` | Changed-file and coordinator-output review |
| Branch uses Task ID 001 only after Rule 06a base selection and explicit approval | Rule 06a | Recorded base/commit and later branch-creation report |

`GOV-01` is especially material to this persistence phase: current Rule 02 requires Domain repository protocols while
the approved Feature 001 requirement/specification prohibit repository and service protocols. The plan follows the
approved concrete chain, but builder handoff remains locked until project governance is synchronized.

## 8. Risks and Rollback

### Risks and Mitigations

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| Review omits or reorders required plan information | High | Medium | Build one ViewModel review projection from the validated schedule/plan and assert every field/date. | `test_AC28_reviewShowsCompleteGeneratedPlan()` and view inspection |
| Save begins without explicit user confirmation or completion emits before commit | High | Medium | Guard `review → saving` behind the confirm event and emit identity only from successful service return. | `test_AC29_completionFollowsAtomicSave()` |
| Root saves without all session children or a child survives failed root save | Critical | Medium | Create/map the entire aggregate before one model-context transaction and inspect a fresh context after injected failure. | AC-29/AC-30 repository integration tests |
| A date-only value is persisted as an absolute instant and later shifts | High | Medium | Encode validated `LocalDate` fields only as Gregorian `YYYY-MM-DD`; reject lossless-encoding failure. | Aggregate mapping assertions plus existing AC-27 coverage |
| Save failure discards the draft or leaves the UI stuck in saving | High | Medium | Keep draft ownership in the ViewModel across typed failure and map failure to `saveFailed` with one Retry event. | `test_AC30_saveFailureRollsBackAndRetries()` |
| Retry creates duplicate roots/children or completes twice | High | Medium | Ensure failed transaction rollback and make completion output conditional on the single successful return; inspect counts. | AC-30 integration/ViewModel/coordinator assertions |
| Incomplete draft or partial aggregate survives relaunch | Critical | Medium | Define no persistent draft model and query only completed aggregate presence during launch. | `test_AC31_relaunchDiscardsIncompleteDraft()` and schema review |
| Downstream Feature 002 UI is pulled into Feature 001 | High | Low | Limit the coordinator to a completion output contract and forbid Feature 002 files in the phase diff. | Scope and changed-file review |
| Intended paths, SwiftData container, or upstream APIs differ from the restored workspace | High | Certain until restoration | Keep Draft and reconcile only after `RB-01` and Phases 01–04 completion. | Reconciliation report |

### Rollback

If later implementation or verification fails before merge, stop completion routing and review only the Phase 05
files listed in Section 3. Revert their source/test/composition diff through normal version-control review without
discarding unrelated changes, restore the verified Phase 04 `review` boundary, and run the reconciled Phase 04
baseline once executable procedures exist. Because no production schema precedes this first approved Feature 001
schema, rollback before release requires no migration. If any test or development store was created by the failed
implementation, preserve it for diagnosis or remove it only through the environment's approved recoverable test-data
reset; never delete user data as part of source rollback. Do not report onboarding complete or route downstream while
the persistence phase is rolled back.

## 9. Builder Handoff Gate

- [x] Approved requirements and amended/approved specification are cited.
- [x] Phase outcome/exclusions cover only `FR-28`–`FR-31` and `AC-28`–`AC-31`.
- [x] Review content, explicit confirmation, aggregate mapping, atomic save/rollback, retry, relaunch, and output ordering are source-traced.
- [x] Canonical intended paths, dependency-ordered tasks, exact AC mappings, verification, risks, and rollback exist.
- [x] Feature 002 remains an output contract only; no downstream implementation task is included.
- [x] The user explicitly authorized early planning detail only; no implementation parallelism is authorized.
- [ ] Phases 01–04 are verified and marked `Complete` by `@planner`.
- [ ] `RB-01` reconciles actual paths, target/model-container membership, upstream APIs, schemes, and executable build/test/coverage procedures.
- [ ] `GOV-01` synchronizes Rule 02 with the approved concrete no-protocol architecture.
- [ ] Rule 06a base selection, synchronization, target-name availability, and branch contract are verified.
- [ ] Phase 05 receives explicit user review and approval after all readiness blockers are resolved.
- [ ] The parent roadmap records Phase 05 as `Approved`, every predecessor as `Complete`, and builder handoff as ready.
- [ ] `@builder` creates the approved branch and receives this phase only after every preceding gate passes.

**Handoff decision:** Locked. This planning-only Phase 05 detail remains Draft and non-executable.
