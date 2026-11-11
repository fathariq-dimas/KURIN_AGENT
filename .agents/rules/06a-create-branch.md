# Rule: Create Branch

**Applies to:** Branch selection, naming, base selection, and branch creation  
**Mandatory:** Yes

---

## 1. Branching Strategy

`main` is the stable integration branch and must remain buildable. `development` is the daily integration branch. Working branches normally start from and merge back into `development`.

Reference: [GitHub flow](https://docs.github.com/en/get-started/using-github/github-flow).

| Branch | Format | Standard base | Standard merge target | Purpose |
| --- | --- | --- | --- | --- |
| **Main** | `main` | Repository initialization | — | Stable integration and release-ready code |
| **Development** | `development` | `main` | `main` | Daily integration |
| **Feature** | `feat/<task-id>-<summary>` | `development` | `development` | New feature work |
| **Bug fix** | `fix/<task-id>-<summary>` | `development` | `development` | Product defect fix |
| **Refactor** | `refactor/<summary>` | `development` | `development` | Restructure without behavior changes |
| **Documentation** | `docs/<summary>` | `development` | `development` | Documentation or guidance update |
| **Test** | `test/<summary>` | `development` | `development` | Test-suite addition or repair |
| **Maintenance** | `chore/<summary>` or `ci/<summary>` | `development` | `development` | Dependencies, tooling, or CI setup |
| **Experiment** | `spike/<question>` | `development` | Review first | Investigation or proof of concept |

A working branch may use another existing branch as its base for explicitly dependent or stacked work. The selected base must be recorded in the command report and used as the intended PR destination unless the user later selects another destination.

---

## 2. Task ID (`XXX`)

Feature and bug-fix branches use the three-digit Task ID from the implementation order in `docs/README.md`.

| Task ID | Feature docs folder | Feature |
| :---: | --- | --- |
| `001` | `docs/Feature/001-onboarding-health-profile` | Onboarding, Health Screening & Profile |
| `002` | `docs/Feature/002-training-plan-dashboard` | Training Plan Dashboard |
| `003` | `docs/Feature/003-exercise-session-guidance` | Exercise Session Guidance |
| `004` | `docs/Feature/004-rpe-estimation` | RPE Estimation & Confirmation |
| `005` | `docs/Feature/005-training-load-workload` | Training Load & Workload Metrics |
| `006` | `docs/Feature/006-daily-training-adjustment` | Daily Training Adjustment |
| `007` | `docs/Feature/007-weekly-training-adjustment` | Weekly Training Adjustment |

For a feature subtask, use the parent feature ID in the branch name. Track finer-grained identifiers such as `001-1` in planning or issue metadata rather than changing the three-digit branch prefix.

---

## 3. Naming Rules

1. Use only lowercase ASCII letters, digits, and hyphens after the branch type prefix.
2. Keep the summary concise and descriptive; do not use spaces, underscores, or trailing hyphens.
3. `feat/` and `fix/` branches must include a valid three-digit Task ID.
4. Never implement changes directly on `main` or `development`.
5. Never reuse a local or remote branch name that already exists.

Examples:

- `feat/001-health-screening-flow`
- `fix/003-hr-zone2-audio-alert`
- `refactor/session-viewmodel-state`
- `docs/split-git-workflow-rules`
- `test/001-onboarding-acceptance-criteria`
- `chore/update-asset-catalog`
- `spike/watchos-live-workout-streaming`

---

## 4. Base Selection and Creation

1. The user may provide an existing base branch explicitly.
2. If the base is omitted, list the available local and remote branches as numbered choices and ask the user to select one. Mark `development` as recommended when it exists. Do not silently choose a base.
3. The working tree must be clean. Do not automatically stash or discard changes.
4. Fetch the selected remote base and update its local branch using fast-forward-only behavior.
5. Stop if the base does not exist, has diverged, or cannot be synchronized.
6. Create the new branch from the synchronized selected base.
7. Report the resulting branch name, selected base, and base commit.

---

## 5. Command Reference

Use **[`/create-branch`](../commands/create-branch.md)** to select or validate the base, validate the branch name, synchronize the base, create the branch, and verify the result.

