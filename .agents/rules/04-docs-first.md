# Rule: Docs-First Development

**Applies to:** Every feature task  
**Mandatory:** Yes

---

## The Golden Rule

> **`docs/` is the source of truth. Code serves the docs, not the other way around.**

Before writing any implementation code for a feature, an agent **must** read:

1. `docs/README.md` — feature list, dependency order, implementation sequence.
2. `docs/Feature/<feature-id>-<feature-slug>/requirement.md` — FRs, ACs, and out-of-scope items.
3. `docs/Feature/<feature-id>-<feature-slug>/spec.md` — data models, flow, validation rules, business logic.

## Do Not Modify `docs/`

Agents must **not** edit files in `docs/` unless the user explicitly requests a documentation update. Docs represent decisions made by the product/design team.

## Open-Decision Closure Before Documentation

When the user requests creation or modification of a requirement, specification, roadmap, or phase plan, the authoring agent must complete this gate before writing dependent content:

1. Audit the authoritative sources and existing user decisions for missing, ambiguous, or conflicting information that could materially change scope, behavior, data, validation, flow, acceptance criteria, architecture, or implementation sequencing.
2. For each open decision, present the exact question, the supporting or conflicting source references, and the affected document sections or downstream work.
3. Ask the user for an explicit decision. The agent may present source-valid options and a clearly labeled recommendation, but must not choose for the user.
4. Wait for the user's answer. Silence, an unanswered question, a suggested default, or an agent recommendation does not close the decision.
5. Record the confirmed answer as an explicit user decision with its date or conversation reference in the relevant document, then continue authoring.

If an open decision appears during authoring, stop the affected documentation work and repeat this gate. A material open decision blocks document approval, phase readiness, and handoff to `@builder`.

## Sequential Documentation Approval Gates

The documentation workflow is strictly sequential. `@planner` must not author a downstream artifact until the immediately preceding artifact has received explicit user review and approval:

1. **Requirements gate:** Author or update `requirement.md`, resolve every material open decision, and obtain explicit user approval of its scope, FRs, ACs, and out-of-scope boundaries.
2. **Specification gate:** Only after requirement approval, author or update `spec.md`. Derive its models, flows, validation, business rules, and dependencies from the approved requirements and source evidence, then obtain explicit user approval.
3. **Implementation-planning gate:** Only after specification approval, author or update `roadmap.md` and the next eligible phase plan. Obtain explicit user approval of the phase before handoff to `@builder`.

Approval must be recorded in the relevant document or roadmap with traceability to the user decision. Approval of a specification or plan does not implicitly approve an unapproved requirement.

If specification work exposes a missing, ambiguous, or conflicting requirement, stop, return to `requirement.md`, close the decision with the user, and obtain renewed requirement approval before resuming the specification. If roadmap or phase planning exposes a requirement or specification gap, return to the affected upstream artifact, obtain renewed approval, and then regenerate or revalidate every dependent downstream artifact.

## Traceability

Every implementation decision that stems from a documented rule should leave a breadcrumb:

```swift
// FR-05: HR Zone 2 guidance is only active for HR Zone 2 Activity training type.
// Source: docs/Feature/003-exercise-session-guidance/requirement.md
```

This makes future code reviews and onboarding faster.

## Out-of-Scope Enforcement

If a user or another agent asks you to implement something marked **Out of Scope** in a `requirement.md`, refuse politely and cite the document. Example:

> "The `docs/Feature/003-exercise-session-guidance/requirement.md` lists 'determination of Zone 2 bounds from health profile' as out of scope for this feature. Please raise this as a product decision before I implement it."

## Feature Dependencies

Respect the implementation order from `docs/README.md`:

```
001-onboarding-health-profile
  → 002-training-plan-dashboard
    → 003-exercise-session-guidance
      → 004-rpe-estimation
        → 005-training-load-workload
          → (006-daily-training-adjustment / 007-weekly-training-adjustment)
            → 002-training-plan-dashboard
```

Do not implement a downstream feature if its upstream dependency data models don't exist yet.

