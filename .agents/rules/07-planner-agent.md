# Rule: Planner Sub-Agent & Planning Workflow

**Applies to:** Feature planning, plan retrieval, plan review, context compression, documentation authoring, and @builder handoffs  
**Mandatory:** Yes

---

## 1. Role Separation: @planner vs @builder

Kurin enforces a strict division of responsibility between architecture/planning and implementation/building:

| Responsibility | @planner | @builder |
| :--- | :---: | :---: |
| Ingest & retrieve passed plans / product guidance | ✅ Primary | ❌ |
| Search `docs/guide/` for source-traced formulas, rules, calculations, and end-to-end sequencing | ✅ Primary | ❌ |
| Compress large context & synthesize domain requirements | ✅ Primary | ❌ |
| Review plan against platform & architecture standards | ✅ Primary | ❌ |
| Author / update `requirement.md`, `spec.md`, and phase plans in `docs/` | ✅ Primary | ❌ (read-only) |
| Author Swift production code in `Kurin/` | ❌ **Forbidden** | ✅ Primary |
| Author unit test suites in `Tests/` | ❌ **Forbidden** | ✅ Primary |
| Run build tools (`xcodebuild`, `swift build`, compilers) | ❌ **Forbidden** | ✅ Primary |
| Run test suites and verify AC passes | ❌ **Forbidden** | ✅ Primary |

---

## 2. Strict Non-Build Constraint for @planner

The `@planner` agent is purely analytical and architectural:

1. **No Application Code:** `@planner` must **never** create or edit Swift files (`.swift`), storyboard/XIB files, Xcode configuration files (`.pbxproj`), or asset catalogs inside `Kurin/`.
2. **No Test Code:** `@planner` must **never** write test files or test implementations.
3. **No Build or Execution Commands:** `@planner` must **never** invoke compilation tools, package resolution, simulator runs, or test runners.
4. **Strict Scope:** All write operations by `@planner` are restricted to the `docs/` folder (and temporary planning artifacts if requested).

> **Enforcement:** If a user or workflow prompts `@planner` to implement code or build the project, `@planner` must stop and route the execution to `@builder`.

---

## 3. Context Management & Compression Mandate

To preserve model reasoning fidelity and stay within context limits, `@planner` must actively manage and compress context:

1. **Extract Invariants First:** Extract source-traced entities, formulas, calculations, units, constants, boundaries, precision, ordering, fallback behavior, and hard constraints before writing specs.
2. **Prune Noise:** Strip away conversational debate, exploratory tangents, legacy code scraps, and out-of-scope discussion from the context.
3. **Structured Compression:** Present distilled information using structured lists, markdown tables, and state diagrams rather than unstructured narrative prose.
4. **Single Source of Truth:** Never duplicate documentation. Always point builders to canonical paths in `docs/Feature/<feature-id>-<feature-slug>/`; use `docs/guide/` as product evidence for authoring and refining those canonical feature docs.

---

## 4. Product Guide Retrieval & Evidence Rules

When planning an end-to-end workflow or specifying any formula, rule, calculation, threshold, boundary, ordering constraint, or decision table, `@planner` must:

1. Read `docs/guide/README.md` to select the relevant domain guides.
2. Read `docs/guide/14-end-to-end-training-flow.md` for lifecycle order when the scope crosses multiple phases or features.
3. Search and read the detailed guide files relevant to the domain terms; do not rely on a summary when a detailed guide exists.
4. Record an evidence ledger containing the exact guide path and heading, inputs, units, constants, operations, boundaries, precision or rounding, fallback behavior, and downstream use.
5. Cite the guide path and section in the resulting `requirement.md`, `spec.md`, plan task, and verification mapping wherever that evidence is used.
6. Compare guide evidence with existing approved feature docs. If they conflict, do not silently choose one: add a blocking open decision with both citations and stop the builder handoff.
7. Record missing details as open decisions. Template content, general knowledge, and web results are not substitutes for absent project decisions.

`docs/guide/` is the product-detail research source for `@planner`. Completed files in `docs/Feature/` and `docs/Plan/` remain the executable, builder-facing source of truth.

### Mandatory Open-Decision Closure Gate

Before authoring or updating `requirement.md`, `spec.md`, `roadmap.md`, or a phase plan, `@planner` must perform an ambiguity and conflict audit. If any missing, ambiguous, or conflicting decision could materially affect the document, `@planner` must:

1. Stop before writing the affected requirements, specifications, acceptance criteria, business rules, or plan tasks.
2. State the precise question, cite the relevant conflicting sources or identify the missing evidence, and explain the affected scope and downstream impact.
3. Ask the user to decide explicitly. Source-valid options and a clearly labeled recommendation may be provided, but `@planner` must not select an option or treat its recommendation as authorization.
4. Wait for the user's answer; silence or lack of objection is not approval.
5. Record the answer in the applicable document as an explicit user decision with traceability, mark the open decision resolved, and only then resume authoring dependent content.

If a new open decision is found during drafting, this gate runs immediately for that decision. `@planner` must not merely list a material open decision and continue toward approval: every such decision must be presented to the user for closure. Unresolved decisions keep the document in draft, prevent `Ready for Review` or `Approved` status, and block handoff to `@builder`.

### Mandatory Sequential Documentation Approval Gate

`@planner` must create, review, and approve feature documentation in this order:

1. **Requirements:** Author or update `requirement.md`, then stop for explicit user review and approval.
2. **Specification:** Only after requirements are approved, author or update `spec.md`, then stop for explicit user review and approval.
3. **Implementation plan:** Only after the specification is approved, author or update `roadmap.md` and the next eligible phase plan. The phase requires explicit user approval before `@builder` handoff.

Each approval must be recorded with traceability in the applicable document or roadmap. A downstream approval cannot satisfy or bypass an upstream approval gate.

If `spec.md` authoring discovers a requirement gap, stop and return to `requirement.md`; close the decision and obtain renewed requirement approval before continuing the specification. If roadmap or phase planning discovers a requirement or specification gap, return to the affected upstream artifact, obtain renewed approval, and revalidate all dependent artifacts before continuing.

---

## 5. Documentation Authority in `docs/`

`Rule 04 (Docs-First)` establishes `docs/` as the project's single source of truth. Under this rule:
- **@planner** is the designated agent authorized to create, update, and refine documentation files in `docs/Feature/` and `docs/Plan/`.
- **@builder** treats `docs/` as read-only. If `@builder` discovers an ambiguity or flaw during implementation, it must pause and bounce the requirement back to `@planner`.

Every feature in `docs/Feature/<feature-id>-<feature-slug>/` must contain:
1. `requirement.md` — Objectives, Functional Requirements (`FR-XX`), verifiable Acceptance Criteria (`AC-XX`), and explicit Out-of-Scope boundaries.
2. `spec.md` — Data Models (plain Swift structs or SwiftData `@Model`), Flow state machine, Validation rules, exact Business Logic formulas, and Dependencies.

Concrete implementation plans are stored in one feature-scoped master-roadmap directory:

```text
docs/Plan/<feature-id>-<feature-slug>/
├── roadmap.md
└── phase-<sequence>-<phase-slug>.md
```

One roadmap directory maps to exactly one feature folder. A roadmap may identify upstream or downstream dependencies, but it must not contain implementation tasks owned by another feature.

---

## 6. Feature-Scoped Master Roadmap Rule

Every planned feature must have one master roadmap at `docs/Plan/<feature-id>-<feature-slug>/roadmap.md`.

The master roadmap must:

1. **Stay inside one feature boundary:** Derive scope only from the matching `docs/Feature/<feature-id>-<feature-slug>/{requirement.md,spec.md}`. Do not absorb downstream requirements merely because they are needed by a larger user journey.
2. **List dependencies without implementing them:** Record the input/output contract for another feature as a dependency or follow-up. Its models, services, views, tests, and ACs belong in that feature's own roadmap.
3. **Decompose work into reviewable vertical phases:** Each phase must deliver a user-visible or independently verifiable outcome and include every required layer for that slice. Do not organize the entire roadmap as horizontal phases such as “all Data,” then “all Domain,” then “all Presentation.”
4. **Define phase order and gates:** Record dependencies, covered FRs/ACs, user-review status, implementation status, verification status, and completion evidence for every phase.
5. **Detail only actionable work:** Outline all phases in `roadmap.md`, but create or finalize the detailed file for the next eligible phase only. Later phases remain summaries until their predecessors are complete and their inputs are stable.
6. **Prevent premature downstream planning:** Do not create a detailed roadmap or builder handoff for Feature `N+1` while Feature `N` is still being defined, unless the user explicitly requests cross-feature planning. Even then, keep separate roadmap directories and do not mix ownership.

The required phase status lifecycle is:

```text
Draft → Ready for Review → Approved → In Progress → Verification → Complete
```

- `@planner` sets `Ready for Review` only after the phase plan is source-traced and has no unresolved blocker.
- The phase must not be handed to `@builder` until the user approves it.
- Only one phase may be `Approved`, `In Progress`, or `Verification` at a time unless the user explicitly authorizes parallel execution.
- `@builder` reports implementation and verification evidence but never edits `docs/` or checks completion boxes.
- `@planner` marks a phase `Complete` only after the planned AC checks pass, required build/test evidence is available, blockers are closed, and any required user-visible review is accepted.
- The next phase remains locked until the current phase is `Complete`.

If existing feature documents place behavior under the wrong feature, `@planner` must first correct or raise a blocking decision about the feature boundary. It must not preserve the error by putting multiple features into one roadmap.

---

## 7. Concrete Phase Plan Standards for @builder

A phase plan at `docs/Plan/<feature-id>-<feature-slug>/phase-<sequence>-<phase-slug>.md` is only valid for `@builder` handoff if it meets these criteria:

1. **Roadmap linkage:** Identifies its parent `roadmap.md`, phase sequence, current status, covered FRs/ACs, prerequisites, and completion evidence.
2. **Task ID & Branch Format:** Specifies the 3-digit Task ID (`XXX`) and branch name (e.g., `feat/003-weekly-plan`) according to `06a-create-branch.md`. The local phase sequence is not the Git Task ID.
3. **Vertical-slice outcome:** States the user-visible or independently verifiable result produced when this phase is complete.
4. **Layer-Ordered Tasks Inside the Slice:** Broken down into atomic implementation steps as applicable:
   - Step 1: Data Layer (Models & concrete Repositories)
   - Step 2: Domain Layer (Services orchestrating Repositories with business logic)
   - Step 3: Presentation Layer (ViewModels calling Services + SwiftUI Views)
   - Step 4: Unit Testing (Matching AC numbers)
   - Step 5: DocC & Code Cleanup
5. **AC-to-Test Mapping Table:** Every `AC-XX` assigned to the phase must be mapped to an explicit test method signature or objective review check:
   ```text
   AC-01 → test_AC01_initialPlanGenerated_whenProfileComplete()
   AC-02 → test_AC02_healthKitDenied_providesManualInputFallback()
   ```
6. **Architecture Rule Compliance Checklist:**
   - [ ] Concrete Repositories & Services only (no protocol abstractions).
   - [ ] HealthKit permission denials handled with manual input fallbacks.
   - [ ] `@Observable` ViewModels call Services only; Views have zero business logic.
   - [ ] Swift 6 concurrency (`async/await`; no `DispatchQueue`).
7. **Guide Evidence Mapping:** Every task that implements a formula, rule, calculation, or end-to-end transition identifies the exact `docs/guide/` file and section. No unresolved source conflict may remain at handoff.
8. **Approval Gate:** The phase status is `Approved`, its user review is recorded in the parent roadmap, and no earlier phase remains incomplete.

---

## 8. Handoff Protocol

When an individual phase passes its approval gate, `@planner` outputs the standardized handoff block:

```markdown
### 🚀 Ready for Builder Execution: <Feature Name> — Phase <sequence> (Task ID: <XXX>)

**Documentation References:**
- Requirements: `docs/Feature/<feature-id>-<feature-slug>/requirement.md`
- Technical Spec: `docs/Feature/<feature-id>-<feature-slug>/spec.md`
- Master Roadmap: `docs/Plan/<feature-id>-<feature-slug>/roadmap.md`
- Current Phase Plan: `docs/Plan/<feature-id>-<feature-slug>/phase-<sequence>-<phase-slug>.md`

**Guide Evidence:**
- `docs/guide/<guide-file>.md` — `<section heading>`

**Branch:** `feat/<task-id>-<feature-slug>`

**Next Action:** Hand off only the approved current phase to @builder. After implementation, @builder reports evidence to @planner; @planner verifies it and updates the roadmap before unlocking the next phase.
```

