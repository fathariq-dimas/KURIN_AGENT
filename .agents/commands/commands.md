# Custom Commands Index

All custom slash commands available to AI agents and developers working on Kurin.

---

## Command Directory

| Command | File | Description |
| :--- | :--- | :--- |
| `/create-branch` | [`create-branch.md`](create-branch.md) | Select a base branch and create a validated working branch |
| `/commit-n-push` | [`commit-n-push.md`](commit-n-push.md) | Commit & push with identity guardrails, file grouping, interactive rephrasing, and Conventional Commits |
| `/create-pr` | [`create-pr.md`](create-pr.md) | Select a destination and create a pull request from the repository template |
| `/implement-feature` | *See below* | Implement a feature from `docs/Feature/` and a verified `docs/Plan/` phase plan |
| `/check-ac` | *See below* | Verify all Acceptance Criteria in `requirement.md` have corresponding tests |
| `/review-layer-violations` | *See below* | Scan source files for architecture and layer boundary violations |
| `/docs-summary` | *See below* | Summarize a feature's requirements and specifications |
| `/session-formula-check` | *See below* | Validate duration calculation formulas against FR-03 and AC-01 |
| `/plan-feature` | *See below* | Invoke @planner to review plans, compress context, and draft specs in `docs/` |

---

## 1. /create-branch

See dedicated guide: **[`create-branch.md`](create-branch.md)**

---

## 2. /commit-n-push

See dedicated guide: **[`commit-n-push.md`](commit-n-push.md)**

---

## 3. /create-pr

See dedicated guide: **[`create-pr.md`](create-pr.md)**

---

## 4. /implement-feature <feature-id>-<feature-slug> <phase-sequence>-<phase-slug>

**Purpose:** Scaffold and implement a full feature from docs.

**Steps:**
1. Read `docs/Feature/<feature-id>-<feature-slug>/requirement.md`
2. Read `docs/Feature/<feature-id>-<feature-slug>/spec.md`
3. Read `docs/Plan/<feature-id>-<feature-slug>/roadmap.md` and confirm the requested phase is approved and predecessors are complete.
4. Read `docs/Plan/<feature-id>-<feature-slug>/phase-<sequence>-<phase-slug>.md` and follow the builder skill.
5. Create layer files (Model → Repository → Service → ViewModel → View).
6. Create test files with AC-numbered test methods (`test_AC01_...`).
7. Report files created and ACs covered.

---

## 5. /check-ac <feature-name>

**Purpose:** Verify that all Acceptance Criteria in a feature's `requirement.md` have a corresponding test.

**Steps:**
1. Read `docs/Feature/<feature-id>-<feature-slug>/requirement.md`
2. Grep test targets for `test_AC` methods matching the feature
3. List any AC numbers without a corresponding test
4. Report pass/fail per AC

---

## 6. /review-layer-violations

**Purpose:** Scan source files for architecture rule violations.

**Checks to perform:**
- Views calling Repositories directly (must go through ViewModel and Service)
- ViewModels calling Repositories directly or containing SwiftData queries (must call Service)
- Services importing `UIKit`, `WatchKit`, or `SwiftUI`
- Repositories or Services using unnecessary protocol abstractions
- Force-unwrap (`!`) in production source files (not tests)
- Missing `///` DocC comments on Service/Repository symbols

**Output:** Table of violations with file name, line number, and rule reference.

---

## 7. /docs-summary <feature-name>

**Purpose:** Summarize a feature's docs in a compact format for quick agent context loading.

**Steps:**
1. Read `docs/Feature/<feature-id>-<feature-slug>/requirement.md`
2. Read `docs/Feature/<feature-id>-<feature-slug>/spec.md`
3. Output compact bullet list: FRs, ACs, data model fields, key business rules

---

## 8. /plan-feature <feature-id>-<feature-slug>

**Purpose:** Invoke `@planner` to update one feature's docs, master roadmap, and next reviewable phase plan in `docs/Plan/`.

**Steps:**
1. Retrieve the existing feature docs, explicit user decisions, and dependency order from `docs/README.md`.
2. Use `docs/guide/README.md` to select detailed product guides. For cross-feature scope, read `docs/guide/14-end-to-end-training-flow.md` first.
3. Search the selected guides for each required formula, rule, calculation, threshold, boundary, and transition; record exact path and section evidence.
4. Compress the sourced context into concise invariants, entities, formulas, dependencies, and open decisions.
5. Before authoring, present every material open decision to the user with its evidence, impact, source-valid options, and any clearly labeled recommendation; ask for explicit decisions and wait until they are closed.
6. Review against Kurin architectural rules (Repo + Service, no protocols, single target, iOS 27 / watchOS 11) and block on unresolved source conflicts.
7. Author or update `docs/Feature/<feature-id>-<feature-slug>/requirement.md` (FRs, ACs, Out-of-Scope), recording resolved user decisions with traceability.
8. Author or update `docs/Feature/<feature-id>-<feature-slug>/spec.md` (Data Model, Flow, Validation, Formulas, guide citations).
9. Author `docs/Plan/<feature-id>-<feature-slug>/roadmap.md` with one-feature scope, phase order, gates, FR/AC coverage, and status evidence.
10. Author only the next eligible `phase-<sequence>-<phase-slug>.md` as a vertical slice with layer breakdown, guide evidence, and AC-to-test mapping.
11. Request user review. Provide a handoff payload for that phase only after approval, no blocking decision remains, and all predecessor phases are complete.


