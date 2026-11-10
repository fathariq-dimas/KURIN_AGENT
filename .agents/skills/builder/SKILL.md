---
name: builder
description: >-
  Guides the execution workflow for the @builder sub-agent. Translates verified plans, specs,
  and requirements in docs/ into production Swift code, AC-mapped unit tests, and verified builds
  following Kurin architecture standards.
---

# Builder Skill: Feature Implementation & Verification Workflow

This skill defines the operational procedure for the **@builder** sub-agent to consume specifications from `docs/` and implement production code, tests, and builds in Kurin.

---

## 1. When to Use This Skill

Activate this skill when:
- Executing the single approved current phase authored by `@planner` in `docs/Plan/<feature-id>-<feature-slug>/phase-<sequence>-<phase-slug>.md`.
- Developing SwiftData models, concrete Repositories, Domain Services, ViewModels, or SwiftUI Views.
- Writing Acceptance Criteria-driven unit tests (`test_AC01_<description>()`).
- Performing code-level refactoring within Kurin layer boundaries.

---

## 2. Pre-Execution Checklist

Before writing any code, `@builder` must verify:
- [ ] `docs/Feature/<feature-id>-<feature-slug>/requirement.md` exists and contains numbered `FR-XX` and `AC-XX`.
- [ ] `docs/Feature/<feature-id>-<feature-slug>/spec.md` exists and defines the Data Model, State/Flow, Validation, and Formulas.
- [ ] `docs/Plan/<feature-id>-<feature-slug>/roadmap.md` identifies the current phase as user-approved and all predecessor phases as complete.
- [ ] The approved `phase-<sequence>-<phase-slug>.md` exists with task breakdown and AC mapping table.
- [ ] Target Git branch is created: `feat/<task-id>-<feature-slug>` based on `development`.
- [ ] Dependencies from upstream features in `docs/README.md` are in place.

> **Stop Rule:** If requirements or data models are ambiguous, contradictory, or missing, do NOT improvise. Halt and escalate to `@planner`.

Execute only the ACs and files assigned to the approved current phase. Do not implement future roadmap phases or downstream feature tasks. After verification, report evidence to `@planner`; never edit roadmap status or completion boxes.

---

## 3. Layer Implementation Guide

Implement code strictly in sequential order:

```text
Data (Models & Repos) ➔ Domain (Services) ➔ Presentation (ViewModels & Views) ➔ Tests
```

### 1. Data Layer
Path: `Kurin/iOS/Features/<Feature>/Data/`

**A. Models (`Data/Models/<ModelName>.swift`):**
- Use plain Swift structs for value objects / transient state.
- Use `@Model final class` for persisted entities in SwiftData.
- Mark immutable types with `Sendable`.
- Explicit initializers with sane defaults.

**B. Repositories (`Data/Repositories/<ModelName>Repository.swift`):**
- **Concrete class only (no protocol abstractions).**
- Accepts `ModelContext` via initializer.
- Handles CRUD and persistence queries:
  ```swift
  import SwiftData
  import Foundation

  /// Handles local persistence for weekly training plans.
  public final class WeeklyPlanRepository: Sendable {
      private let modelContext: ModelContext

      public init(modelContext: ModelContext) {
          self.modelContext = modelContext
      }

      public func fetchLatestPlan() throws -> TrainingPlan? {
          var descriptor = FetchDescriptor<TrainingPlan>(sortBy: [SortDescriptor(\.createdAt, order: .reverse)])
          descriptor.fetchLimit = 1
          return try modelContext.fetch(descriptor).first
      }

      public func save(plan: TrainingPlan) throws {
          modelContext.insert(plan)
          try modelContext.save()
      }
  }
  ```

### 2. Domain Layer
Path: `Kurin/iOS/Features/<Feature>/Domain/Services/<ServiceName>.swift`

- **Concrete class only (no protocol abstractions).**
- Injects concrete Repositories.
- Encapsulates calculations, business validation, and state transformations.
- Must not import `SwiftUI`, `UIKit`, or `WatchKit`.
- Add DocC comments (`///`) and traceability tags to `docs/`:
  ```swift
  import Foundation

  /// Service orchestrating weekly training plan calculations and progression.
  ///
  /// Source: docs/Feature/003-weekly-training-plan/spec.md
  public final class WeeklyPlanService: Sendable {
      private let repository: WeeklyPlanRepository

      public init(repository: WeeklyPlanRepository) {
          self.repository = repository
      }

      /// Generates the initial weekly training plan for an onboarded user.
      ///
      /// FR-01, AC-01: Generates plan matching assigned training type and duration.
      public func generateInitialPlan(profile: UserProfile, screening: HealthScreening) async throws -> TrainingPlan {
          // Calculation logic based on spec.md
      }
  }
  ```

### 3. Presentation Layer
Path: `Kurin/iOS/Features/<Feature>/Presentation/`

**A. ViewModels (`Presentation/ViewModels/<Feature>ViewModel.swift`):**
- Annotated with `@Observable` and `@MainActor`.
- Injects Domain Service via `init`.
- Exposes observable state (`isLoading`, `errorMessage`, `plan`).
- Never communicates directly with Repositories.

**B. Views (`Presentation/Views/<Feature>View.swift`):**
- SwiftUI views consuming ViewModel state.
- Uses Kurin Design System typography, color tokens, and layout guidelines.
- Zero business logic in View bodies.

### 4. Testing Layer
Path: `KurinTests/<Feature>Tests/<Feature>Tests.swift`

- Unit test methods named `test_AC01_<description>()` matching the feature's `requirement.md`.
- Use in-memory `ModelContainer` for SwiftData tests.
- Mock external inputs (HealthKit data samples) cleanly in memory.
- Example:
  ```swift
  import XCTest
  @testable import Kurin

  final class WeeklyPlanServiceTests: XCTestCase {
      func test_AC01_generatesInitialPlan_whenProfileComplete() async throws {
          // Given: Valid user profile and screening
          // When: Service generates initial plan
          // Then: Plan matches expected frequency and duration bounds
      }
  }
  ```

---

## 4. Code Standards & Quality Audit

Before submitting code:
1. **Force Unwraps:** Grep for `!` and `try!`. Zero occurrences allowed in production files.
2. **Protocol Rule:** Confirm no unnecessary protocols were added to Repositories or Services.
3. **DocC:** Confirm all `internal` and `public` functions/classes have `///` comments.
4. **HealthKit Handling:** Confirm all HealthKit requests have manual fallback handling.
5. **Docs Integrity:** Confirm no files in `docs/` were modified.

---

## 5. Build & Test Verification

Run build and test suite checks:
1. Compile the target and resolve any Swift 6 concurrency warnings.
2. Run test target to confirm all `test_AC` test cases pass.
3. Emit the final completion report formatted per `.agents/agent/builder.md`.

