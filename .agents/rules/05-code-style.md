# Rule: Code Style

**Applies to:** All Swift source files  
**Mandatory:** Yes

---

## Swift Language

- **Swift 6** strict concurrency mode — no `@unchecked Sendable` shortcuts without a team-reviewed comment explaining why.
- Use `@Observable` (Observation framework) for ViewModels; fall back to `ObservableObject` only when `@Observable` is incompatible with the target API.
- Use `enum` with associated values for state machines (session phases, navigation paths, etc.) — not `Bool` flags.
- Prefer `struct` over `class` for value types. Use `class` only when reference semantics or identity are needed (e.g. `@Model`, `@Observable` classes).
- No force-unwrap (`!`) or `try!` in production code. Use `guard let`, `if let`, or `do-catch`.
- Never nest more than two levels of `if let` / `guard let` — restructure the logic instead.
- Use `throws` for error propagation; never swallow errors silently with an empty `catch {}`.
- Use `Result<Success, Failure>` only when the caller must store or forward the result asynchronously; prefer `throws` for inline call sites.

## Formatting

- **One type per file.** File name must match the type name exactly (e.g. `SessionRepository.swift` for `SessionRepository`).
- Maximum line length: **120 characters**.
- Use 4-space indentation (Xcode default). Do not use tabs.
- Trailing commas are allowed in multi-line collection literals.
- Opening braces go on the same line (`K&R` style), not on a new line.

## Naming Conventions

| Element | Convention | Example |
|---------|-----------|---------|
| Types (class, struct, enum, protocol) | `UpperCamelCase` | `SessionGuidanceViewModel` |
| Properties & methods | `lowerCamelCase` | `heartRateBPM` |
| Constants (`let`) | `lowerCamelCase` | `zone2LowerBound` |
| Enum cases | `lowerCamelCase` | `.warmUp`, `.actualTraining` |
| Repository | `<Feature>Repository` | `SessionRepository` |
| Service | `<Feature>Service` | `SessionService` |
| ViewModel | `<Screen>ViewModel` | `SessionGuidanceViewModel` |
| View | `<Screen>View` | `SessionGuidanceView` |
| Test file | `<Type>Tests` | `SessionServiceTests` |
| Test method | `test_AC<nn>_<description>` | `test_AC01_50MinSessionYields7MinWarmUp` |

## Concurrency

- Use `async/await` and `AsyncStream` exclusively — no `DispatchQueue` or `OperationQueue` in new code.
- Never use `DispatchQueue.main.async` to hop to the main thread; use `@MainActor` or `await MainActor.run { }`.
- Annotate `@MainActor` on ViewModels and any type whose properties are read on the main thread.
- HealthKit callbacks and SwiftData model context operations must be explicitly actor-isolated.
- Do not use `Task.detached` unless you intentionally need to escape the current actor context — prefer structured `Task { }`.
- Session timers: use `WKExtendedRuntimeSession` (watchOS) or `BackgroundTasks` (iOS) — never a `Timer` for background work.

## SwiftUI

- Views must contain **zero** business logic. If a View needs a computed value, the ViewModel exposes it.
- Use `@State` only for transient, view-local UI state (animation toggles, focus state, etc.). Never for domain data.
- Prefer `let` bindings in Views over `@Binding` when the View only reads, not writes.
- Every interactive control must have an `.accessibilityLabel`. Every decorative image must have `.accessibilityHidden(true)`.
- Support **Dynamic Type**: use `.font(.body)`, `.font(.headline)`, etc. — never hard-code font sizes.
- Support **Dark Mode**: use semantic colors from the asset catalog, not hex literals.

## Documentation & Comments

- Every non-trivial `internal` symbol in Services and Repositories must have a `///` DocC comment.
- Write DocC comments for `what` and `why`, not `how` — the implementation explains `how`.
- Business-rule formulas must cite their FR number and source document on the line above:
  ```swift
  // FR-03: W = min(10, 5 + floor((D - 15) / 15))
  // Source: docs/Feature/003-exercise-session-guidance/requirement.md
  let warmUp = min(10, 5 + (totalMinutes - 15) / 15)
  ```
- Inline comments (`//`) are for non-obvious logic only. Do not comment what the code obviously does.
- Remove commented-out dead code before merging — use Git history instead.

## Error Handling

- Define feature-specific error types as `enum <Feature>Error: Error` inside the feature folder.
- Always propagate errors up to the ViewModel; the ViewModel maps them to user-visible state.
- Log unexpected errors with `assertionFailure` in debug builds; handle gracefully in release.

