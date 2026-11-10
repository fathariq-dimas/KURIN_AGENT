# .agents — Kurin Agent Configuration

This folder contains rules, skills, and commands shared across all AI coding agents working on this project.

## Folder Structure

| Folder | Purpose |
|--------|---------|
| `agent/` | Sub-agent definitions (e.g. `@planner` for review & spec drafting) |
| `rules/` | Mandatory rules every agent must follow |
| `skills/` | Reusable task-specific guidance agents can invoke |
| `commands/` | Custom slash commands / agent shortcut definitions |
| `template/` | Canonical templates for `spec.md`, `requirement.md`, feature roadmaps, and phase plans |

## Sub-Agents

- **@planner** ([`agent/planner.md`](agent/planner.md)): Architect and requirements analyst sub-agent. Retrieves product evidence from `docs/guide/` for formulas, rules, calculations, and end-to-end sequencing; authors `requirement.md` and `spec.md` in `docs/Feature/`; writes implementation plans in `docs/Plan/`; and hands concrete workflows to `@builder`. Has strictly no build capabilities.
- **@builder** ([`agent/builder.md`](agent/builder.md)): Implementation and execution specialist. Translates verified plans into SwiftData models, concrete Repositories & Services, `@Observable` ViewModels, SwiftUI Views, and AC-mapped unit tests. Executes builds and tests.

## Quick Start for Agents

1. Read `../AGENTS.md` (project root) first — it is the full project guide.
2. Source of truth for all features: `../docs/`
3. Apply all files in `rules/` unconditionally.
4. Load a skill from `skills/` when working on the matching task type.

> This folder is compatible with: **Antigravity (AGY)**, **Claude Code**, **OpenAI Codex**, **Cursor**, **GitHub Copilot Workspace**, and any agent that respects `AGENTS.md` / `.cursorrules` conventions.

