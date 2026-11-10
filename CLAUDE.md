# CLAUDE.md — Kurin: Weight Loss Training

> This file is read automatically by Claude Code. For the full guide, see `AGENTS.md`.

## Project

**Kurin** is an iOS 27 + watchOS 11 fitness app for overweight individuals. It provides adaptive aerobic training plans with real-time heart rate guidance during exercise sessions.

- **iPhone 17** · **Apple Watch Series 11**
- Swift 6+ · SwiftUI · SwiftData · HealthKit
- Architecture: Clean Architecture with Repo + Service layer (no protocol abstraction) inside a single target

## Source of Truth

All feature specs live in `docs/`. Always read before planning or coding:

```text
docs/Feature/<feature-id>-<feature-slug>/requirement.md   ← FRs, ACs, out-of-scope
docs/Feature/<feature-id>-<feature-slug>/spec.md           ← data models, flow, business rules
```

**Never modify `docs/` files** unless the user explicitly asks for a documentation update.

## Key Rules

| Rule | Detail |
|------|--------|
| No force-unwrap | No `!` or `try!` in production code |
| Repo + Service | Concrete classes only — no protocol abstraction needed |
| HealthKit denial ≠ crash | Always provide manual input fallback |
| Test naming | `test_AC01_<description>()` to match AC numbers in `requirement.md` |
| Concurrency | `async/await` only; no `DispatchQueue` |
| ViewModels | Use `@Observable`, inject Services via init; never call Repos directly |
| DocC | All non-trivial `internal` Service/Repo symbols need `///` comments |

## Feature Order

1. `001-onboarding-health-profile` → 2. `002-training-type-assignment` → 3. `003-weekly-training-plan` → 4. `004-exercise-session-guidance` → 5. `005-rpe-estimation` → 6. `006-training-load-workload` → 7. `007-daily-training-adjustment` → 8. `008-weekly-training-adjustment`

## Agent Toolkit

- Rules: `.agents/rules/`
  - `01-platform-standards.md`
  - `02-architecture.md`
  - `03-testing.md`
  - `04-docs-first.md`
  - `05-code-style.md`
  - `06-git-workflow.md`
  - `06a-create-branch.md`
  - `06b-create-pr.md`
  - `07-planner-agent.md`
- Sub-Agents: `.agents/agent/`
  - `@planner` ([`planner.md`](.agents/agent/planner.md)): Plan review, context compression, spec & plan authoring in `docs/` (strictly no build capabilities)
  - `@builder` ([`builder.md`](.agents/agent/builder.md)): Concrete code implementation, AC-mapped unit tests, build execution, and verification
- Skills: `.agents/skills/`
  - `planner` ([`skills/planner/SKILL.md`](.agents/skills/planner/SKILL.md))
  - `builder` ([`skills/builder/SKILL.md`](.agents/skills/builder/SKILL.md))
- Commands: `.agents/commands/`
