<!--
TEMPLATE ONLY — NON-AUTHORITATIVE

This file defines technical-specification structure only. Every HTML comment is
an authoring instruction, not a technical decision. Replace every angle-bracket
placeholder with content traced to requirement.md, an authoritative product
source, or an explicit decision. Remove all instruction comments before
publishing this file under docs/Feature/.

Do not copy data fields, limits, formulas, states, errors, dependencies, or
interfaces from another feature. If a required decision has no source, record it
as open instead of inventing it.
-->

# Technical Specification — <FEATURE_NAME>

> **Feature ID:** `<FEATURE_ID>`  
> **Feature directory:** `docs/Feature/<FEATURE_ID>-<FEATURE_SLUG>/`  
> **Source requirements:** [`requirement.md`](requirement.md)  
> **Prepared by:** `<AUTHOR_OR_AGENT>`  
> **Status:** `<DOCUMENT_STATUS>`

> **Section Definition — Metadata**
>
> - **Meaning:** Establishes the specification's identity, requirement authority, ownership, and maturity.
> - **Include:** Canonical feature ID/path, exact requirement document, author, and status.
> - **Exclude:** Technical behavior or an approval status that ignores unresolved blockers.
> - **Completion:** Every value is concrete and status reflects document readiness.

## 1. Scope and Layer Placement

> **Section Definition — Scope and Layer Placement**
>
> - **Meaning:** Assigns each required technical responsibility to an architecture layer and source location.
> - **Include:** Responsibility, layer, exact target path, and governing requirement.
> - **Exclude:** Speculative files, unused layers, or product requirements not present in requirement.md.
> - **Completion:** Every in-scope responsibility has one clear owner and valid placement.

| Responsibility | Layer | Target path | Governing requirement |
|---|---|---|---|
| <RESPONSIBILITY> | `<LAYER>` | `<TARGET_PATH>` | `<FR_OR_NFR_ID>` |

## 2. Data Model

> **Section Definition — Data Model**
>
> - **Meaning:** Defines the information structures the feature owns or uses.
> - **Include:** Required types, fields/cases, persistence, constraints, relationships, lifecycle, and migration impact.
> - **Exclude:** Convenience models, speculative fields, or types without a requirement or approved decision.
> - **Completion:** Every type and member is source-backed and its storage/lifecycle semantics are explicit; use `None — <reason>` if no model is needed.

### Types

> **Subsection Definition — Types**
>
> - **Meaning:** Provides the inventory and ownership of entities, value objects, and enumerations.
> - **Include:** Type name, kind, persistence strategy, owning layer, and source.
> - **Exclude:** Fields or cases, which belong in the next subsection.
> - **Completion:** Every type referenced elsewhere in this specification is declared or identified as an external dependency.

| Type | Kind | Persistence | Ownership | Source |
|---|---|---|---|---|
| `<TYPE_NAME>` | `<TYPE_KIND>` | `<PERSISTENCE_STRATEGY>` | `<OWNING_LAYER>` | `<SOURCE_REFERENCE>` |

### Fields or Cases

> **Subsection Definition — Fields or Cases**
>
> - **Meaning:** Defines the members and raw representations of each declared type.
> - **Include:** Swift type/raw value, optionality, constraints/defaults when applicable, and source.
> - **Exclude:** Unjustified convenience members or relationship behavior.
> - **Completion:** Every required member has an unambiguous representation and constraint.

| Parent type | Name | Swift type or raw value | Optional | Constraints | Source |
|---|---|---|:---:|---|---|
| `<TYPE_NAME>` | `<FIELD_OR_CASE>` | `<TYPE_OR_VALUE>` | `<YES_OR_NO>` | <CONSTRAINTS> | `<SOURCE_REFERENCE>` |

### Relationships and Lifecycle

> **Subsection Definition — Relationships and Lifecycle**
>
> - **Meaning:** Explains how data instances relate and change over time.
> - **Include:** Ownership, cardinality, uniqueness, creation/update, deletion, archival, and migration when applicable.
> - **Exclude:** UI navigation or workflow state transitions.
> - **Completion:** Persistence and lifecycle consequences are unambiguous; use `Not applicable — <reason>` for non-persisted independent values.

<RELATIONSHIPS_AND_LIFECYCLE>

## 3. Flow and State Machine

> **Section Definition — Flow and State Machine**
>
> - **Meaning:** Defines how the feature progresses in response to events and conditions.
> - **Include:** Required states, events, guards, transitions, side effects, terminal states, and recovery paths.
> - **Exclude:** Generic loading/error states or transitions not required by an AC or approved decision.
> - **Completion:** Every relevant AC path is represented; otherwise state why a simpler sourced flow is sufficient.

### States

> **Subsection Definition — States**
>
> - **Meaning:** Defines each distinct condition that changes allowed behavior or presentation.
> - **Include:** Meaning, entry condition, exit condition, and source for each state.
> - **Exclude:** Transient implementation flags that do not affect the observable contract.
> - **Completion:** State names are mutually understandable and cover all transition endpoints.

| State | Meaning | Entry condition | Exit condition | Source |
|---|---|---|---|---|
| `<STATE>` | <MEANING> | <ENTRY_CONDITION> | <EXIT_CONDITION> | `<SOURCE_REFERENCE>` |

### Transitions

> **Subsection Definition — Transitions**
>
> - **Meaning:** Defines the permitted movement between states.
> - **Include:** Current state, event, guard, next state, side effect, and covered AC.
> - **Exclude:** Impossible paths, hidden assumptions, or effects with no owner.
> - **Completion:** Each event has deterministic behavior for every relevant state and guard.

| Current state | Event | Guard | Next state | Side effect | AC |
|---|---|---|---|---|---|
| `<CURRENT_STATE>` | `<EVENT>` | <GUARD_OR_NONE> | `<NEXT_STATE>` | <SIDE_EFFECT_OR_NONE> | `<AC_ID>` |

### Diagram

> **Subsection Definition — Diagram**
>
> - **Meaning:** Visualizes non-trivial state or interaction relationships already defined above.
> - **Include:** A Mermaid state or sequence diagram matching the transition table exactly.
> - **Exclude:** New states, transitions, side effects, or decorative detail.
> - **Completion:** Diagram and tables agree; use `Omitted — trivial flow fully represented by the table` when justified.

<FLOW_DIAGRAM>

## 4. Validation Rules and Boundaries

> **Section Definition — Validation Rules and Boundaries**
>
> - **Meaning:** Defines which inputs are valid, who validates them, and what occurs when invalid.
> - **Include:** Type/format, unit, allowed values, nullability/normalization, owner, failure behavior, and source.
> - **Exclude:** Assumed ranges or defaults derived from general knowledge.
> - **Completion:** Every externally supplied or boundary-sensitive input is covered; missing authoritative boundaries become open decisions.

| Input | Type / format | Unit | Allowed values or boundary | Validation owner | Invalid behavior | Source |
|---|---|---|---|---|---|---|
| `<INPUT>` | `<TYPE_OR_FORMAT>` | `<UNIT_OR_NONE>` | <ALLOWED_VALUES_OR_BOUNDARY> | `<OWNER>` | <INVALID_BEHAVIOR> | `<SOURCE_REFERENCE>` |

## 5. Business Logic and Calculations

> **Section Definition — Business Logic and Calculations**
>
> - **Meaning:** Defines the deterministic rules that transform inputs or select outcomes.
> - **Include:** Named inputs/outputs, exact rules or formulas, units, ordering, precision, exceptions, sources, and worked examples.
> - **Exclude:** Uncited constants, implementation syntax, or examples that invent rules.
> - **Completion:** Each rule is independently implementable and checkable; use `Not applicable — <reason>` only when no rule or decision logic exists.

### Rule or Formula: `<RULE_NAME>`

> **Subsection Definition — Rule or Formula**
>
> - **Meaning:** Gives one business rule or calculation a precise, testable contract.
> - **Include:** Inputs, exact operation or decision table, output, precision, exceptional behavior, and source for each.
> - **Exclude:** Multiple unrelated rules under one name or values established only by the example.
> - **Completion:** Two implementers using the section would produce the same output for the same input.

| Item | Definition | Source |
|---|---|---|
| Inputs | <NAMED_INPUTS_WITH_TYPES_AND_UNITS> | `<SOURCE_REFERENCE>` |
| Rule / formula | <EXACT_RULE_OR_FORMULA> | `<SOURCE_REFERENCE>` |
| Output | <OUTPUT_TYPE_UNIT_AND_RANGE> | `<SOURCE_REFERENCE>` |
| Precision | <ROUNDING_AND_PRECISION> | `<SOURCE_REFERENCE>` |
| Exceptional behavior | <EDGE_CASE_BEHAVIOR> | `<SOURCE_REFERENCE>` |

> **Subsection Definition — Worked Example**
>
> - **Meaning:** Demonstrates and checks the preceding rule with concrete source-valid values.
> - **Include:** Sample inputs, every intermediate step, units/rounding, and expected result.
> - **Exclude:** New constants, boundaries, or behavior absent from the formal rule.
> - **Completion:** A reviewer can recalculate the example and obtain the stated result.

<WORKED_EXAMPLE>

## 6. Layer Contracts

> **Section Definition — Layer Contracts**
>
> - **Meaning:** Defines how concrete components collaborate across architecture boundaries.
> - **Include:** Concrete type/operation, inputs, outputs, errors, side effects, isolation/concurrency, and governing requirement.
> - **Exclude:** Protocol abstractions forbidden by architecture, speculative operations, or method bodies.
> - **Completion:** Every required cross-layer interaction has an implementable, source-backed contract.

| Layer | Concrete type | Operation | Input | Output | Errors | Side effects | Concurrency | Requirement |
|---|---|---|---|---|---|---|---|---|
| `<LAYER>` | `<TYPE_NAME>` | `<OPERATION>` | `<INPUT>` | `<OUTPUT>` | `<ERRORS_OR_NONE>` | `<SIDE_EFFECTS_OR_NONE>` | `<ISOLATION_OR_ASYNC_CONTRACT>` | `<FR_OR_NFR_ID>` |

## 7. Error Handling

> **Section Definition — Error Handling**
>
> - **Meaning:** Defines required failure detection, propagation, presentation, and recovery behavior.
> - **Include:** Source-backed recoverable/non-recoverable failures, user effect, recovery, and relevant logging/privacy constraints.
> - **Exclude:** Generic errors with no observable contract or implementation-only diagnostics.
> - **Completion:** Every failure path in the ACs, validation, flow, and dependencies has deterministic handling; use `None — <reason>` when no failure contract exists.

| Error | Detection | Propagation | User-visible behavior | Recovery | Source |
|---|---|---|---|---|---|
| `<ERROR>` | <DETECTION> | <PROPAGATION> | <USER_BEHAVIOR> | <RECOVERY> | `<SOURCE_REFERENCE>` |

## 8. Test Scenarios

> **Section Definition — Test Scenarios**
>
> - **Meaning:** Specifies how the technical design will be verified against acceptance criteria and boundaries.
> - **Include:** AC, scenario, setup, action, expected result, target layer, and test type.
> - **Exclude:** Product behavior absent from requirement.md or vague instructions such as "test this."
> - **Completion:** Every AC, boundary, formula, state path, and required failure has objective coverage.

| AC | Scenario | Setup | Action | Expected result | Target layer | Test type |
|---|---|---|---|---|---|---|
| `<AC_ID>` | <SCENARIO> | <SETUP> | <ACTION> | <EXPECTED_RESULT> | `<LAYER>` | `<TEST_TYPE>` |

## 9. Dependencies and Open Decisions

> **Section Definition — Dependencies and Open Decisions**
>
> - **Meaning:** Separates established technical contracts from unresolved implementation-affecting choices.
> - **Include:** All external contracts used by the design and every material unanswered technical question.
> - **Exclude:** Ordinary coding tasks, incidental imports, or silently assumed choices.
> - **Completion:** Dependencies are implementable and all unknowns are resolved or explicitly visible.

### Dependencies

> **Subsection Definition — Technical Dependencies**
>
> - **Meaning:** Records known technical contracts this feature requires but does not own.
> - **Include:** Internal types, platform services, frameworks, or upstream contracts, plus sourced availability behavior.
> - **Exclude:** Package wishes, unresolved technology choices, or incidental imports.
> - **Completion:** Every external contract used by the design is represented; use `None — <reason>` when absent.

| Dependency | Contract used | Availability behavior | Source |
|---|---|---|---|
| <DEPENDENCY> | <CONTRACT> | <UNAVAILABLE_BEHAVIOR> | `<SOURCE_REFERENCE>` |

### Open Decisions

> **Subsection Definition — Open Decisions**
>
> - **Meaning:** Exposes unanswered technical choices that could materially change implementation or verification.
> - **Include:** Specific question, source-valid options, owner, impact, blocking status, and current status.
> - **Exclude:** Implementation tasks, known dependencies, preferred guesses, or options ruled out by project rules.
> - **Completion:** Every ambiguity has been presented to the user and resolved by an explicit, traceable user decision; write `None` when empty. An unresolved item keeps the document in draft and prevents builder handoff.

| ID | Question | Options | Owner | Impact | Blocking | Status |
|---|---|---|---|---|:---:|---|
| `<DECISION_ID>` | <QUESTION> | <OPTIONS> | <OWNER> | <IMPACT> | `<YES_OR_NO>` | `<STATUS>` |

## 10. Quality Gate

> **Section Definition — Quality Gate**
>
> - **Meaning:** Defines the checks required before the specification can be approved.
> - **Include:** Checks for traceability, sourced boundaries/formulas, consistent flow, failure coverage, decisions, and template cleanup.
> - **Exclude:** Build results that belong to implementation or checked boxes without review evidence.
> - **Completion:** Every applicable check passes; unresolved blocking decisions keep the specification unapproved.

- [ ] <QUALITY_CHECK>
