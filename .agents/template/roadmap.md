<!--
TEMPLATE ONLY — NON-EXECUTABLE

Use this template for one feature-scoped master roadmap. Replace all angle-bracket
placeholders, remove authoring comments, and publish it only at
docs/Plan/<feature-id>-<feature-slug>/roadmap.md.
-->

# Master Roadmap — <FEATURE_NAME>

> **Feature ID:** `<FEATURE_ID>`  
> **Feature docs:** `docs/Feature/<FEATURE_ID>-<FEATURE_SLUG>/`  
> **Output path:** `docs/Plan/<FEATURE_ID>-<FEATURE_SLUG>/roadmap.md`  
> **Prepared by:** `@planner`  
> **Status:** `<ROADMAP_STATUS>`

## 1. Goal and Feature Boundary

<FEATURE_GOAL>

### Included

- <OWNED_SCOPE>

### Excluded and Downstream

- <EXCLUDED_OR_DOWNSTREAM_SCOPE_AND_OWNING_FEATURE>

## 2. Dependency and Output Contracts

| Direction | Feature or system | Contract | Evidence/source | Status |
|---|---|---|---|---|
| `<UPSTREAM_OR_DOWNSTREAM>` | `<DEPENDENCY>` | <INPUT_OR_OUTPUT_CONTRACT> | `<SOURCE>` | `<STATUS>` |

## 3. Phase Status

Use only: `Draft`, `Ready for Review`, `Approved`, `In Progress`, `Verification`, or `Complete`.

| Phase | Reviewable outcome | FR/AC coverage | Depends on | Plan review | Implementation | Verification | Completion evidence |
|---|---|---|---|---|---|---|---|
| `01 — <PHASE_NAME>` | <OUTCOME> | `<FR_AND_AC_IDS>` | `<DEPENDENCY_OR_NONE>` | `<STATUS>` | `<STATUS>` | `<STATUS>` | <EVIDENCE_OR_PENDING> |

## 4. Open Decisions and Blockers

Every material open decision must be presented to the user and resolved explicitly before an affected phase can become `Ready for Review`. Record the resulting user decision and traceability; do not use this table as permission to continue with a guessed or merely recommended answer.

| ID | Decision or blocker | Owner | Affected phase | Status |
|---|---|---|---|---|
| `<ID>` | <QUESTION_OR_BLOCKER> | <OWNER> | `<PHASE>` | `<STATUS>` |

## 5. Current Phase Gate

- **Current eligible phase:** `<PHASE_OR_NONE>`
- **Detailed phase plan:** `phase-<SEQUENCE>-<PHASE_SLUG>.md`
- **User approval:** `<PENDING_OR_RECORDED_DECISION>`
- **Builder handoff:** `<LOCKED_OR_READY>`

The next phase remains locked until `@planner` verifies the current phase's AC, build/test, blocker, and required user-review evidence and marks it `Complete`.
