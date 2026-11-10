---
description: "Create a validated working branch from a selected base. Usage: /create-branch <type> [task-id] <summary> [--from <base-branch>]"
---

# Command: /create-branch

**Trigger:** `/create-branch <type> [task-id] <summary> [--from <base-branch>]`  
**Description:** Select or validate a base branch, then create a correctly named working branch.

---

## Agent Execution Instructions

When `/create-branch` is invoked, read [`../rules/06a-create-branch.md`](../rules/06a-create-branch.md), then execute this workflow.

### Step 1: Gather and Validate the New Branch Name

1. Accept one of these branch types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`, or `spike`.
2. Require a three-digit Task ID for `feat` and `fix`.
3. Normalize the summary to lowercase kebab-case without changing its meaning.
4. Build the candidate branch name:
   - `feat/<task-id>-<summary>` or `fix/<task-id>-<summary>`
   - `<type>/<summary>` for all other types
5. Ask for missing naming information only when it cannot be inferred safely.

Examples:

```text
/create-branch feat 003 weekly-plan --from development
→ feat/003-weekly-plan based on development

/create-branch docs split-git-workflow-rules
→ ask the user to select the base branch
```

### Step 2: Inspect Repository State

1. Run `git rev-parse --show-toplevel` and stop if the current directory is not a Git repository.
2. Run `git status --short`.
3. If the working tree is not clean, stop and list the pending paths. Do not stash, commit, reset, or discard them automatically.
4. Verify that `origin` exists with `git remote get-url origin`.
5. Fetch branch references with `git fetch origin --prune`.
6. Reject the candidate if it already exists locally or as `origin/<candidate>`.

### Step 3: Select the Base Branch

If `--from <base-branch>` was provided, validate that the branch exists locally or as `origin/<base-branch>`.

If `--from` was omitted:

1. Enumerate local and `origin` branches.
2. Deduplicate branch names and exclude symbolic references such as `origin/HEAD`.
3. Present a numbered list that the user can answer with either the number or exact branch name.
4. Put `development` first and label it **Recommended** when it exists; list the remaining branches alphabetically.
5. Wait for the user's selection. Do not silently choose a base.

Example prompt:

```text
Choose the base branch:
1. development (Recommended)
2. feat/002-training-assignment
3. main

Reply with a number or branch name.
```

### Step 4: Synchronize the Selected Base

For a remote-backed base, run:

```bash
git switch <base-branch>
git pull --ff-only origin <base-branch>
```

If the branch exists only on the remote, create a local tracking branch first. If it exists only locally, use its current commit and clearly report that it has no remote tracking branch.

Stop without creating the new branch if synchronization fails or the selected base has diverged. Never force-update the base.

### Step 5: Create and Verify

1. Create the branch:
   ```bash
   git switch -c <candidate>
   ```
2. Verify it with:
   ```bash
   git branch --show-current
   git status -sb
   git log -1 --oneline
   ```
3. Do not push the empty branch unless the user explicitly asks.

### Step 6: Report

Report:

- Created branch name.
- Selected base branch.
- Base commit hash and subject.
- Whether an upstream branch exists (normally no until `/commit-n-push`).
- Suggested next action.

