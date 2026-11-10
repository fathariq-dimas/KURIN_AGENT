# Rule: Testing Standards

**Applies to:** All test targets  
**Mandatory:** Yes

---

## Coverage Target

- Minimum **80% line coverage** on `KurinCore` (business logic package).
- Every Use Case must have at least one unit test per happy path **and** one per error/edge path.

## Test Naming Convention

Tests must reference the Acceptance Criteria (AC) number from `docs/Feature/<feature-id>-<feature-slug>/requirement.md`:

```swift
func test_AC01_50MinSessionYields7MinWarmUp() { ... }
func test_AC02_easyWalkingReceivesNoZone2Guidance() { ... }
```

This convention lets reviewers map every test to a documented requirement.

## Test Isolation Rules

- No test may hit a real HealthKit store — use mock/stub `HKHealthStore`.
- No test may hit a real network or file system outside the test bundle.
- Use an **in-memory SwiftData container** for repository integration tests.
- Use `XCTestExpectation` or `async/await` for async tests; never `sleep()`.

## What Must Be Tested

| Component | Test Type | Location |
|-----------|-----------|----------|
| Use Cases | Unit | `KurinTests/UseCases/` |
| Business-rule formulas (BMI, Training Load, Zone 2, duration) | Unit | `KurinTests/BusinessRules/` |
| Repository (CRUD operations) | Integration (in-memory store) | `KurinTests/Repositories/` |
| ViewModel state transitions | Unit (mock UseCase) | `KurinTests/ViewModels/` |
| Session phase state machine | Unit | `KurinTests/UseCases/` |

## What Does NOT Need Tests

- Pure layout code in SwiftUI Views (snapshot tests are optional, not required).
- Xcode-generated boilerplate files.
- `AppDelegate` / `@main` entry point.

## Test Data

- Keep test fixtures as static constants or enums in `KurinTests/TestData/`.
- Never hard-code real personal health data as test fixtures.

