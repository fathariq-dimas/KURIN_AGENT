<!--
TEMPLATE ONLY — NON-AUTHORITATIVE

This file defines document structure only. Every HTML comment is an authoring
instruction, not a product requirement. Replace every angle-bracket placeholder
with source-backed content, remove all instruction comments, and verify that no
placeholder remains before publishing this file under docs/Feature/.

Never infer missing product decisions from this template. Record missing facts
as open decisions instead.
-->

# Requirements — <FEATURE_NAME>

> **Feature ID:** `<FEATURE_ID>`  
> **Product source:** `<SOURCE_DOCUMENT_AND_SECTION>`  
> **Feature directory:** `docs/Feature/<FEATURE_ID>-<FEATURE_SLUG>/`  
> **Prepared by:** `<AUTHOR_OR_AGENT>`  
> **Status:** `<DOCUMENT_STATUS>`

> **Section Definition — Metadata**
>
> - **Meaning:** Establishes the document's identity, authority, ownership, and maturity.
> - **Include:** Canonical feature ID/path, exact product source, author, and status.
> - **Exclude:** Feature behavior, technical design, or uncited source labels.
> - **Completion:** Every value is concrete and status reflects unresolved blockers.

## 1. Overview

> **Section Definition — Overview**
>
> - **Meaning:** Gives a shared understanding of the user problem and feature boundary.
> - **Include:** Target problem, intended outcome, affected users, and high-level boundary.
> - **Exclude:** Implementation details, formulas, exhaustive behavior, or unsupported claims.
> - **Completion:** A reader can explain what the feature does and does not cover.

<OVERVIEW>

## 2. Purpose

> **Section Definition — Purpose**
>
> - **Meaning:** Explains why the feature exists and the value it must create.
> - **Include:** Source-backed user, product, safety, or data objectives, preferably measurable.
> - **Exclude:** System behavior, which belongs in Functional Requirements, or implementation tasks.
> - **Completion:** Each statement is applicable, distinct, and outcome-oriented.

- <PURPOSE>

## 3. Actors and Preconditions

> **Section Definition — Actors and Preconditions**
>
> - **Meaning:** Establishes who participates and what must be true before participation begins.
> - **Include:** The complete actor set and all sourced entry conditions with unmet behavior.
> - **Exclude:** Implementation components, workflow steps, or assumptions not established by a source.
> - **Completion:** The Actors and Preconditions subsections jointly define the feature's valid starting context.

### Actors

> **Subsection Definition — Actors**
>
> - **Meaning:** Identifies people and systems that participate in the feature.
> - **Include:** Every actor, its responsibility, and the source establishing that role.
> - **Exclude:** Passive data objects, implementation classes, or assumed stakeholders.
> - **Completion:** Every interaction in the requirements has a responsible actor.

| Actor | Responsibility | Source |
|---|---|---|
| `<ACTOR>` | <RESPONSIBILITY> | `<SOURCE_REFERENCE>` |

### Preconditions

> **Subsection Definition — Preconditions**
>
> - **Meaning:** Defines what must already be true before the feature or flow can begin.
> - **Include:** Required state, upstream data, permissions, prior flows, and sourced unmet behavior.
> - **Exclude:** Steps performed by the feature itself or invented fallback behavior.
> - **Completion:** Entry conditions and the result of each unmet condition are unambiguous.

| Precondition | Unmet behavior | Source |
|---|---|---|
| <PRECONDITION> | <UNMET_BEHAVIOR> | `<SOURCE_REFERENCE>` |

## 4. Functional Requirements

> **Section Definition — Functional Requirements**
>
> - **Meaning:** States the observable behaviors the product is required to provide.
> - **Include:** Atomic, source-backed behaviors with stable sequential identifiers.
> - **Exclude:** Design choices, implementation tasks, aspirations, or unrelated behaviors in one row.
> - **Completion:** Every behavior traces to a source and maps to at least one acceptance criterion.

| ID | Requirement | Source |
|---|---|---|
| `<FR_ID>` | <REQUIRED_BEHAVIOR> | `<SOURCE_REFERENCE>` |

## 5. Non-Functional Requirements

> **Section Definition — Non-Functional Requirements**
>
> - **Meaning:** Defines measurable constraints on how well or where the feature operates.
> - **Include:** Applicable quality, platform, privacy, accessibility, performance, or architecture constraints.
> - **Exclude:** Functional behavior, generic best practices, or constraints without a source.
> - **Completion:** Every constraint has verification and a source; use `None — <reason>` if none apply.

| ID | Constraint | Verification | Source |
|---|---|---|---|
| `<NFR_ID>` | <CONSTRAINT> | <VERIFICATION_METHOD> | `<SOURCE_REFERENCE>` |

## 6. Acceptance Criteria

> **Section Definition — Acceptance Criteria**
>
> - **Meaning:** Defines objective pass/fail outcomes proving functional requirements are satisfied.
> - **Include:** Independently verifiable Given/When/Then scenarios with stable IDs and FR mapping.
> - **Exclude:** Internal implementation steps, vague claims, or source-unsupported behavior.
> - **Completion:** All FRs and their required boundary and failure behavior are covered.

| ID | Requirement | Given | When | Then |
|---|---|---|---|---|
| `<AC_ID>` | `<FR_ID>` | <PRECONDITION> | <ACTION_OR_EVENT> | <OBSERVABLE_RESULT> |

## 7. Out of Scope

> **Section Definition — Out of Scope**
>
> - **Meaning:** Makes the feature boundary explicit by naming intentionally excluded work.
> - **Include:** Source-backed exclusions, their reason, and source.
> - **Exclude:** Unknown behavior, deferred required ACs, or irrelevant copied exclusions.
> - **Completion:** Likely boundary misunderstandings are covered; use `None — <reason>` when absent.

| Excluded item | Reason | Source |
|---|---|---|
| <EXCLUDED_ITEM> | <EXCLUSION_REASON> | `<SOURCE_REFERENCE>` |

## 8. Dependencies and Open Decisions

> **Section Definition — Dependencies and Open Decisions**
>
> - **Meaning:** Separates known external contracts from unresolved questions requiring authority.
> - **Include:** All material dependencies plus every ambiguity that can alter requirements or scope.
> - **Exclude:** Internal implementation tasks or resolved decisions without their final source citation.
> - **Completion:** Known contracts are actionable, and unknowns are either resolved or explicitly visible.

### Dependencies

> **Subsection Definition — Dependencies**
>
> - **Meaning:** Records known upstream prerequisites and downstream consumers.
> - **Include:** Direction, canonical dependency name, required contract, and source.
> - **Exclude:** Unresolved choices, which belong in Open Decisions, or incidental details.
> - **Completion:** Every cross-feature or external contract is represented; use `None — <reason>` when absent.

| Direction | Dependency | Required contract | Source |
|---|---|---|---|
| `<UPSTREAM_OR_DOWNSTREAM>` | <DEPENDENCY> | <REQUIRED_CONTRACT> | `<SOURCE_REFERENCE>` |

### Open Decisions

> **Subsection Definition — Open Decisions**
>
> - **Meaning:** Exposes questions with no authoritative answer that could materially change behavior or scope.
> - **Include:** A specific question, decision owner, unresolved impact, and current status.
> - **Exclude:** Tasks, known dependencies, low-value notes, or guessed answers.
> - **Completion:** Every ambiguity has been presented to the user and resolved by an explicit, traceable user decision; write `None` when empty. An unresolved item keeps the document in draft and prevents approval and builder handoff.

| ID | Question | Owner | Impact if unresolved | Status |
|---|---|---|---|---|
| `<DECISION_ID>` | <QUESTION> | <DECISION_OWNER> | <IMPACT> | `<STATUS>` |

## 9. Traceability

> **Section Definition — Traceability**
>
> - **Meaning:** Shows the chain from authoritative source to required behavior and proof.
> - **Include:** Every FR, all ACs verifying it, and its exact product source.
> - **Exclude:** Unmapped identifiers, approximate citations, or implementation links.
> - **Completion:** No functional requirement or acceptance criterion is orphaned.

| Requirement | Acceptance criteria | Product source |
|---|---|---|
| `<FR_ID>` | `<AC_IDS>` | `<SOURCE_REFERENCE>` |

## 10. Quality Gate

> **Section Definition — Quality Gate**
>
> - **Meaning:** Defines the checks required before the requirement document can be approved.
> - **Include:** Checks for sources, FR-to-AC coverage, boundaries, decisions, and template cleanup.
> - **Exclude:** Implementation/build checks or boxes marked complete without review evidence.
> - **Completion:** Every applicable check passes; unresolved blocking decisions keep the document unapproved.

- [ ] <QUALITY_CHECK>
