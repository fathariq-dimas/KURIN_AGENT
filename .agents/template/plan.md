<!--
TEMPLATE ONLY — NON-EXECUTABLE

This file defines implementation-plan structure only. Every HTML comment is an
authoring instruction, not an implementation task. Replace every angle-bracket
placeholder with content traced to approved feature documents and project rules,
then remove all instruction comments before publishing under docs/Plan/.

Do not hand this template directly to @builder. Do not infer files, types,
commands, tests, or implementation decisions from this template.
-->

# Implementation Phase Plan — <PHASE_TITLE>

> **Feature ID:** `<FEATURE_ID>`  
> **Phase sequence:** `<PHASE_SEQUENCE>`  
> **Parent roadmap:** `docs/Plan/<FEATURE_ID>-<FEATURE_SLUG>/roadmap.md`  
> **Output path:** `docs/Plan/<FEATURE_ID>-<FEATURE_SLUG>/phase-<PHASE_SEQUENCE>-<PHASE_SLUG>.md`  
> **Source requirements:** `docs/Feature/<FEATURE_ID>-<FEATURE_SLUG>/requirement.md`  
> **Source specification:** `docs/Feature/<FEATURE_ID>-<FEATURE_SLUG>/spec.md`  
> **Target branch:** `<TARGET_BRANCH>`  
> **Base branch:** `<BASE_BRANCH>`  
> **Prepared by:** `<AUTHOR_OR_AGENT>`  
> **Executing agent:** `<EXECUTING_AGENT>`  
> **Status:** `<DOCUMENT_STATUS>`

> **Section Definition — Metadata**
>
> - **Meaning:** Establishes the plan's identity, authority, execution ownership, and Git context.
> - **Include:** Feature ID, local phase sequence, parent roadmap, canonical source paths, branches, author, executor, and status.
> - **Exclude:** Unverified sources, inferred branches, or executable status while blockers remain.
> - **Completion:** Every value is concrete and all source documents exist and are approved.

## 1. Vertical-Slice Outcome and Phase Boundary

> **Section Definition — Vertical-Slice Outcome and Phase Boundary**
>
> - **Meaning:** Defines only the user-visible or independently verifiable outcome owned by this phase and the boundary needed to execute it safely.
> - **Include:** Phase outcome, covered FRs/ACs, phase-specific prerequisites, phase-specific exclusions, and exact source citations needed by the implementation tasks.
> - **Exclude:** The feature goal or boundary, feature-level dependency/output contracts, roadmap phase ordering or status, open decisions or blockers tracked by the parent roadmap, and background already available in the approved requirement or specification.
> - **Completion:** The executor can state what becomes reviewable when this phase finishes, what this phase must not implement, and which canonical sources govern the work without restating roadmap content.

<PHASE_OUTCOME_AND_BOUNDARY>

## 2. Scope and Target File Map

> **Section Definition — Scope and Target File Map**
>
> - **Meaning:** Defines the complete repository surface expected to change.
> - **Include:** Exact path, change type, owning layer, purpose, and governing requirement/decision.
> - **Exclude:** Speculative files, generic folders, generated outputs, or unrelated cleanup.
> - **Completion:** Every planned deliverable maps to a file and every listed file maps to approved scope.

| Change | Path | Layer | Purpose | Requirement / decision |
|---|---|---|---|---|
| `<CHANGE_TYPE>` | `<REPOSITORY_RELATIVE_PATH>` | `<LAYER>` | <PURPOSE> | `<SOURCE_ID>` |

## 3. Dependency-Ordered Work Breakdown

> **Section Definition — Dependency-Ordered Work Breakdown**
>
> - **Meaning:** Converts approved scope into atomic implementation tasks in safe execution order.
> - **Include:** Exact files, sources, dependencies, deliverable, completion evidence, and AC coverage per task.
> - **Exclude:** Vague phases, duplicate work, unapproved design decisions, or a forced layer sequence that does not apply.
> - **Completion:** Tasks are independently actionable and collectively cover the full file map and AC set.

| Step | Task | Files | Inputs / sources | Depends on | Deliverable | Completion evidence | AC coverage |
|---:|---|---|---|---|---|---|---|
| `<SEQUENCE>` | <TASK> | `<PATHS>` | `<SOURCE_REFERENCES>` | `<DEPENDENCIES_OR_NONE>` | <DELIVERABLE> | <EVIDENCE> | `<AC_IDS>` |

## 4. Acceptance Criteria to Test Mapping

> **Section Definition — Acceptance Criteria to Test Mapping**
>
> - **Meaning:** Defines the objective proof planned for every acceptance criterion.
> - **Include:** AC, verification target, exact test/check name, setup, core assertion, and planned file.
> - **Exclude:** Generic testing promises, unmapped ACs, or checks that cannot prove the observable outcome.
> - **Completion:** Every AC has at least one sufficient verification method and no planned test lacks an AC or stated technical risk.

| AC | Verification target | Test or check name | Setup | Core assertion | Planned file |
|---|---|---|---|---|---|
| `<AC_ID>` | `<TARGET>` | `<TEST_OR_CHECK_NAME>` | <SETUP> | <ASSERTION> | `<PATH>` |

## 5. Verification Plan

> **Section Definition — Verification Plan**
>
> - **Meaning:** Defines how implementation correctness and change-specific risks will be checked.
> - **Include:** Exact command/procedure, environment, expected result, and risk addressed for static checks, builds, tests, simulations, or inspections.
> - **Exclude:** Unverified commands, unavailable schemes, or claims that verification has already run.
> - **Completion:** All AC mappings and material risks have an executable verification step.

| Verification | Command or procedure | Environment | Expected result | Risk addressed |
|---|---|---|---|---|
| <VERIFICATION> | `<COMMAND_OR_PROCEDURE>` | <ENVIRONMENT> | <EXPECTED_RESULT> | <RISK> |

## 6. Architecture and Rule Compliance

> **Section Definition — Architecture and Rule Compliance**
>
> - **Meaning:** Identifies project rules that constrain implementation and how compliance will be shown.
> - **Include:** Applicable check, exact governing rule, and planned evidence.
> - **Exclude:** Irrelevant rules, feature behavior disguised as architecture, or evidence-free checkboxes.
> - **Completion:** Every rule relevant to the changed layers, platform, tests, docs, and Git workflow is covered.

| Check | Governing rule | Planned evidence |
|---|---|---|
| <COMPLIANCE_CHECK> | `<RULE_REFERENCE>` | <EVIDENCE> |

## 7. Phase Risks and Rollback

> **Section Definition — Phase Risks and Rollback**
>
> - **Meaning:** Defines how this phase handles adverse implementation outcomes and returns affected code, data, configuration, or user state to safety.
> - **Include:** Material risks introduced by this phase, their mitigations, and a safe recovery path.
> - **Exclude:** Feature-level risks, downstream work, dependency contracts, open decisions, blockers, and follow-ups owned by the parent roadmap.
> - **Completion:** This phase can be executed with understood risk and a reviewable recovery procedure; any newly discovered deferred work has been moved to the parent roadmap instead of duplicated here.

### Risks and Mitigations

> **Subsection Definition — Risks and Mitigations**
>
> - **Meaning:** Identifies credible adverse outcomes introduced by the plan and how they will be reduced.
> - **Include:** Risk, consistent impact/likelihood rating, mitigation, and verification.
> - **Exclude:** Generic possibilities unrelated to the change or unsupported "low risk" claims.
> - **Completion:** Every material implementation, data, privacy, migration, compatibility, and delivery risk is addressed; use `None — <reason>` only after assessment.

| Risk | Impact | Likelihood | Mitigation | Verification |
|---|---|---|---|---|
| <RISK> | `<IMPACT>` | `<LIKELIHOOD>` | <MITIGATION> | <VERIFICATION> |

### Rollback

> **Subsection Definition — Rollback**
>
> - **Meaning:** Defines how to return code, data, configuration, and user state to a safe condition.
> - **Include:** Trigger, ordered recovery steps, irreversible effects, and validation after recovery.
> - **Exclude:** Destructive commands without safeguards or a claim that rollback is unnecessary without rationale.
> - **Completion:** A reviewer can execute or approve a safe recovery path; if impossible, document the alternative recovery procedure.

<ROLLBACK_PROCEDURE>

## 8. Builder Handoff Gate

> **Section Definition — Builder Handoff Gate**
>
> - **Meaning:** Defines the phase-plan readiness conditions before @builder may execute it while treating the parent roadmap as canonical for approval, predecessor, and blocker status.
> - **Include:** Checks for approved sources, complete file map, AC coverage, executable verification, phase risks/rollback, template cleanup, and one reference check against the parent roadmap gate.
> - **Exclude:** Copies of roadmap statuses, open-decision tables, implementation completion checks, or boxes marked complete without document review.
> - **Completion:** Every phase-plan check passes and the parent roadmap records this phase as eligible for handoff; otherwise the plan remains with @planner.

- [ ] <READINESS_CHECK>
- [ ] The parent roadmap records this phase as `Approved`, every predecessor as `Complete`, no blocking open decision for this phase, and builder handoff as ready.
