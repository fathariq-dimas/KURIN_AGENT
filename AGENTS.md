# AGENTS.md — Kurin: Weight Loss Training

> **Source of Truth:** All feature requirements and technical specs live in `docs/`.  
> **Always read the relevant `docs/Feature/<feature-id>-<feature-slug>/requirement.md` and `docs/Feature/<feature-id>-<feature-slug>/spec.md` before implementing any feature.**

This file is the primary entry point for every AI coding agent (Antigravity, Claude, Codex, Cursor, Copilot, etc.) working on this project. Read it completely before writing a single line of code.

---

## 0. Instruction Clarity & User Confirmation

These rules are mandatory for every agent and sub-agent:

- If a user instruction is unclear, incomplete, ambiguous, or has multiple materially different interpretations, **stop and ask the user for clarification or confirmation before proceeding**.
- **Do not make assumptions** about the user's intent, requirements, scope, priorities, implementation choices, or desired outcome.
- Do not silently choose defaults or infer missing decisions from surrounding context when the choice could affect the result.
- Agents may provide clearly labeled suggestions, options, or a recommended approach to help the user decide. A suggestion is **not** authorization to act.
- After presenting suggestions, wait for the user's explicit confirmation before planning, editing files, running state-changing commands, or delegating work based on the unresolved choice.
- **Open-decision closure gate for documentation:** Before creating or updating any `requirement.md`, `spec.md`, roadmap, or phase plan, the authoring agent must first audit the available sources for missing, ambiguous, or conflicting decisions that could materially affect the document. For every such open decision, the agent must explain the question, its source or missing evidence, and its impact; ask the user for an explicit decision; and wait for the user's answer before authoring any dependent requirement, specification, or plan content.
- Options and a clearly labeled recommendation may be offered to help the user decide, but the agent must not select an option, treat silence as approval, or preserve a guessed answer as a placeholder. After the user decides, record the answer and its authority as an explicit user decision in the relevant document before continuing.
- If a new open decision is discovered while authoring, stop the affected documentation work, ask the user, and resume only after the decision is closed. No document or phase with an unresolved material open decision may be marked ready, approved, or handed to `@builder`.
- **Sequential documentation approval gate:** Documentation must be created and approved in this order: (1) author `requirement.md`, then obtain explicit user review and approval; (2) only after requirement approval, author `spec.md`, then obtain explicit user review and approval; (3) only after specification approval, author `roadmap.md` and the next eligible phase plan, then obtain explicit user approval for the phase before handing it to `@builder`.
- Approval of a later artifact never substitutes for approval of an earlier artifact. If specification or planning work reveals a missing, ambiguous, or conflicting requirement, return to `requirement.md`, close the decision with the user, and obtain renewed requirement approval before resuming downstream work. If planning reveals a specification gap, return to `spec.md` and obtain renewed specification approval before continuing the plan.
- If instructions are clear and complete, proceed normally without requesting redundant confirmation.

---

## 1. Project Overview

**Kurin** is an iOS & watchOS fitness application that helps overweight individuals (*overweight*), specifically those categorized as pre-obese, build consistent aerobic exercise habits to support weight loss and maintain cardiovascular health.

The application uses an adaptive training approach tailored to the user's capabilities and performance by providing a weekly training plan (*Weekly Training Plan*), real-time workout guidance (*Exercise Guidance*), and performance evaluations to adjust subsequent training loads.

The core training methodology centers on HR Zone 2 workouts and Easy Walking, taking into account intensity, duration, frequency, and the body's physiological response to exercise.

### 1.1. Product Objective

- Help users build consistent exercise habits.
- Provide training plans tailored to the user's capabilities and activity history.
- Help users control and maintain workout intensity during exercise sessions.
- Avoid imposing excessive training loads and prevent overexertion.
- Adjust training duration and frequency based on previous user performance.
- Support exercise activities that are more measurable, comfortable, and sustainable.

| Attribute | Value |
|-----------|-------|
| **App Name** | Kurin: Weight Loss Training |
| **Platforms** | iOS 27 · watchOS 11 |
| **Target Devices** | iPhone 17 · Apple Watch Series 11 |
| **Language** | Swift 6+ |
| **UI Framework** | SwiftUI |
| **Min Deployment** | iOS 27.0 · watchOS 11.0 |
| **Architecture** | MVVM + Clean Architecture |
| **Data** | Apple Health (HealthKit) · SwiftData |

---

## 2. Repository Structure

```text
Kurin/                             # Root repository
├── AGENTS.md
├── CLAUDE.md
├── .cursorrules
├── .agents/                       # Agent rules, skills, commands, sub-agents
│   ├── agent/                     # Sub-agents (@planner, @builder)
│   ├── rules/
│   ├── skills/
│   ├── template/                  # Templates for requirement.md, spec.md, plan.md
│   └── commands/
├── docs/                          # 📌 SOURCE OF TRUTH — read before coding
│   ├── README.md
│   ├── Feature/
│   │   ├── 001-onboarding-health-profile/
│   │   ├── 002-training-plan-dashboard/
│   │   ├── 003-exercise-session-guidance/
│   │   ├── 004-rpe-estimation/
│   │   ├── 005-training-load-workload/
│   │   ├── 006-daily-training-adjustment/
│   │   └── 007-weekly-training-adjustment/
│   └── Plan/                      # <feature-id>-<feature-slug>/roadmap.md + phase plans
├── Kurin.xcodeproj/
└── Kurin/                         # Root source synced by Xcode
    ├── iOS/
    │   ├── App/
    │   ├── Core/
    │   │   ├── DesignSystem/
    │   │   └── Extensions/
    │   ├── Features/
    │   │   ├── AdaptivePlanning/
    │   │   ├── Onboarding/
    │   │   ├── Profile/
    │   │   ├── TrackingSession/
    │   │   └── TrainingProgress/
    │   ├── Resources/
    │   └── Shared/
    │       ├── Data/
    │       ├── Domain/
    │       └── Presentation/
    └── WatchOS/
```

Each `Features/<Feature>/` and `Shared/` folder uses this internal layout:

```text
Data/
├── Models/        # SwiftData @Model classes and plain Swift structs
└── Repositories/  # Concrete repository classes (no protocol abstraction)
Domain/
└── Services/      # Business logic; orchestrates one or more repositories
Presentation/
├── Coordinators/  # Navigation / flow coordination
├── SupportViews/  # Reusable sub-views scoped to this feature
├── ViewModels/    # @Observable view-models; calls Services only
└── Views/         # SwiftUI screens and components
```

> These are **source-code groupings inside a single Xcode target** — not separate Swift Packages or modules. Layer boundaries are enforced by convention and code review.

Each feature folder in `docs/Feature/` contains:
- `requirement.md` — functional requirements, acceptance criteria, out-of-scope
- `spec.md` — data models, business rules, state flows, test scenarios

---

## 3. Rules Reference

All mandatory rules live in `.agents/rules/`. Load the relevant rule before starting a task.

| File | Covers |
|------|--------|
| [`01-platform-standards.md`](.agents/rules/01-platform-standards.md) | iOS 27, watchOS 11, HealthKit constraints |
| [`02-architecture.md`](.agents/rules/02-architecture.md) | Project structure, layer boundaries, Repo + Service pattern, no-protocol rule |
| [`03-testing.md`](.agents/rules/03-testing.md) | Coverage targets, test naming, isolation, what must be tested |
| [`04-docs-first.md`](.agents/rules/04-docs-first.md) | Docs-first workflow, traceability, feature dependencies |
| [`05-code-style.md`](.agents/rules/05-code-style.md) | Swift style, naming, concurrency, SwiftUI, documentation standards |
| [`06-git-workflow.md`](.agents/rules/06-git-workflow.md) | Commit convention, commit safety, and push workflow |
| [`06a-create-branch.md`](.agents/rules/06a-create-branch.md) | Branch naming, Task ID format (`XXX`), base selection, and creation |
| [`06b-create-pr.md`](.agents/rules/06b-create-pr.md) | PR destination selection, readiness, content, and safety |
| [`07-planner-agent.md`](.agents/rules/07-planner-agent.md) | Role separation (@planner vs @builder), context compression, spec & plan authoring in docs/, strict no-build rule |

---

## 4. Feature Map & Docs Reference

> Always read `docs/Feature/<feature-id>-<feature-slug>/requirement.md` **and** `docs/Feature/<feature-id>-<feature-slug>/spec.md` before starting a feature.

| # | Feature | Docs Folder | Key Dependency |
|---|---------|-------------|----------------|
| 1 | Onboarding, Health Screening & Profile | `Feature/001-onboarding-health-profile` | — |
| 2 | Training Plan Dashboard | `Feature/002-training-plan-dashboard` | Feature 1 initially; Features 6–7 refresh it |
| 3 | Exercise Session Guidance | `Feature/003-exercise-session-guidance` | Feature 2 scheduled session |
| 4 | RPE Estimation & Confirmation | `Feature/004-rpe-estimation` | Feature 3 |
| 5 | Training Load & Workload Metrics | `Feature/005-training-load-workload` | Feature 4 |
| 6 | Daily Training Adjustment | `Feature/006-daily-training-adjustment` | Feature 5; loops to Feature 2 |
| 7 | Weekly Training Adjustment | `Feature/007-weekly-training-adjustment` | Feature 5; loops to Feature 2 |

Implement the initial path in order, then preserve the adjustment feedback loops:

```text
001 → 002 → 003 → 004 → 005 → (006 / 007) → 002
```

Do **not** jump ahead — later features depend on data models from earlier ones.

---

## 5. Coding Standards & Git

> Full coding standards (Swift style, naming, concurrency, SwiftUI, documentation) →  
> **[`.agents/rules/05-code-style.md`](.agents/rules/05-code-style.md)**

> Full testing standards (coverage, test naming, isolation, what must be tested) →  
> **[`.agents/rules/03-testing.md`](.agents/rules/03-testing.md)**

> Git commits and pushes → **[`.agents/rules/06-git-workflow.md`](.agents/rules/06-git-workflow.md)**  
> Branch creation and Task IDs → **[`.agents/rules/06a-create-branch.md`](.agents/rules/06a-create-branch.md)**  
> Pull requests → **[`.agents/rules/06b-create-pr.md`](.agents/rules/06b-create-pr.md)**

---

## 6. Agent Workflow & Sub-Agents

Kurin enforces a two-tier division of labor between planning and execution:

### Role Breakdown

- **@planner** (`.agents/agent/planner.md`): Retrieves passed plans, searches `docs/guide/` for source-traced formulas/rules/calculations and end-to-end sequencing, compresses large context, performs architectural gap analysis, and authors `requirement.md`, `spec.md`, and concrete workflow plans in `docs/`. **Has strictly no build capabilities** (cannot author Swift code or execute builds).
- **@builder** (`.agents/agent/builder.md`): Executes verified plans from `@planner`. Implements concrete Swift code (Models → Repositories → Services → ViewModels → Views) and writes AC-matched unit tests. Runs builds and tests.

### Lifecycle Flow

1. **Plan & Review (@planner):**
   - Read `docs/README.md`, the relevant feature docs, and `docs/guide/README.md`; for end-to-end scope also read `docs/guide/14-end-to-end-training-flow.md` and retrieve the relevant detailed domain guides.
   - Trace every formula, rule, calculation, threshold, and cross-feature transition to an exact guide path and section; record conflicts or missing details as blocking open decisions.
   - Compress context and resolve open ambiguities.
   - Author or update `docs/Feature/<feature-id>-<feature-slug>/requirement.md` (FRs, ACs, Out-of-Scope), then stop for explicit user review and approval.
   - Only after requirement approval, author or update `spec.md` (Data Model, Flow, Validation, Business Rules), then stop for explicit user review and approval.
   - Only after specification approval, create one feature-scoped master roadmap at `docs/Plan/<feature-id>-<feature-slug>/roadmap.md` and decompose it into sequential, reviewable vertical phase plans.
   - Keep each roadmap inside one feature boundary. Record downstream features only as dependency/output contracts; do not include their implementation tasks.
   - Finalize only the next eligible `phase-<sequence>-<phase-slug>.md`, obtain user approval, and hand off that phase alone to `@builder`.
2. **Execute & Test (@builder):**
   - Consume only the approved current phase from `@planner`.
   - Implement the phase's vertical slice, using Data → Domain → Presentation ordering inside that slice where applicable.
   - Write tests named `test_AC01_...` matching every Acceptance Criterion assigned to the phase.
   - Run tests and verify compliance with `01`–`06` rules.
3. **Docs Integrity:**
   - Only `@planner` modifies `docs/`. `@builder` treats `docs/` as the immutable source of truth during implementation.
   - `@builder` reports completion evidence; `@planner` verifies it, marks the phase complete in `roadmap.md`, and only then unlocks the next phase.

---

## 7. Out-of-Scope (Do Not Implement)

- Social / sharing features
- Paid subscription / in-app purchase logic
- Remote backend / server sync (all data is local + HealthKit)
- Nutrition tracking
- Strength / resistance training types

---

## 8. References

| Resource | Path |
|----------|------|
| Full product & training logic spec | `Kurin_Exercise_Guidance_v1.6.md` |
| Modular product rules and calculations | `docs/guide/README.md` |
| End-to-end training lifecycle | `docs/guide/14-end-to-end-training-flow.md` |
| Feature docs index | `docs/README.md` |
| Agent rules | `.agents/rules/` |
| Agent skills | `.agents/skills/` |
| Custom commands | `.agents/commands/` |

