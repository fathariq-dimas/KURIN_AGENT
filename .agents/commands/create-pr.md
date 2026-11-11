---
description: "Create a rule-compliant pull request to a selected destination. Usage: /create-pr [destination-branch]"
---

# Command: /create-pr

**Trigger:** `/create-pr [destination-branch]`  
**Description:** Select or validate a destination, draft the repository PR template, confirm it, and create the pull request.

---

## Agent Execution Instructions

When `/create-pr` is invoked, read [`../rules/06b-create-pr.md`](../rules/06b-create-pr.md), then execute this workflow.

### Step 1: Verify the Head Branch

1. Run `git branch --show-current` to identify the current head branch.
2. Refuse to open a PR from `main`.
3. Run `git fetch origin --prune` so destination choices reflect the remote.

### Step 2: Select the Destination Branch

If `[destination-branch]` was provided, validate that `origin/<destination-branch>` exists and that it differs from the head branch.

If the destination was omitted:

1. Enumerate remote branches from `origin`.
2. Exclude `origin/HEAD` and the current head branch, then deduplicate the names.
3. Present a numbered list that the user can answer with either the number or exact branch name.
4. Put the standard destination first and label it **Recommended** when present:
   - `main` when the head branch is `development`.
   - `development` for a working branch.
5. List remaining destinations alphabetically.
6. Wait for the user's selection. Do not silently choose a destination.

Example prompt:

```text
Choose the PR destination branch:
1. development (Recommended)
2. feat/002-training-dashboard
3. main

Reply with a number or branch name.
```

After selection, show `head → destination` explicitly.

### Step 3: Verify Clean and Pushed State

1. Run `git status --short` and stop if the working tree is not clean.
2. Run `git status -sb` to check the tracking branch and synchronization state.
3. If the head has no upstream, or local commits are ahead of upstream, stop and direct the user to [`/commit-n-push`](commit-n-push.md).
4. Never push implicitly. Continue only when the current branch is fully pushed.

### Step 4: Gather Context

Collect the information needed to complete the template:

1. **Branch Goal:** Read `.opencode/branch-goals/<branch>.md` if present; otherwise infer the goal from the branch name and commits.
2. **Commits:** Run `git log origin/<destination>..HEAD --oneline --no-merges` to collect commit summaries.
3. **Modified Files:** Run `git diff origin/<destination>...HEAD --stat` and `git diff origin/<destination>...HEAD` to review scope and safety.
4. **Phase Plan:** If the branch has a corresponding plan under `docs/Plan/`, summarize its milestones/phases for the Phases table; otherwise, omit the Phases table with a brief explanatory note.
5. **Requirements:** For feature work, read the relevant `requirement.md` and `spec.md` to identify the Task ID and Acceptance Criteria covered.
6. **Verification Evidence:**
   - Check for recent screenshots in `.opencode/tmp/screenshots/` and any reviewer verdict files.
   - If verification evidence is missing or ambiguous, ask the user what to include in the Verification section.

### Step 5: Draft the PR Body

- Read `.github/pull_request_template.md`.
- Fill every template section (**Summary**, **Phases**, **Changes**, **Verification**, **Related**, **Notes**) using the gathered context.
- **Rule:** Leave zero template placeholders (`<!-- ... -->`) unreplaced.
- Ensure directories exist (`mkdir -p .opencode/tmp`) and write the completed draft to:
  `.opencode/tmp/pr-body.md`

### Step 6: Propose Title

- Format the title using Conventional Commits style (e.g. `feat:`, `fix:`, `docs:`, `refactor:`).
- Maximum title length: **≤ 72 characters**.
- Title must match the branch type and main functional scope.

### Step 7: Show and Confirm

1. Present the proposed PR title and the full content of `.opencode/tmp/pr-body.md` to the user for review.
2. Detect the remote repository with `gh repo view --json nameWithOwner -q .nameWithOwner` and display the exact CLI command to be executed:
   ```bash
   gh pr create --repo <owner/repository> --head <branch> --base <destination> --title "<title>" --body-file .opencode/tmp/pr-body.md
   ```
3. **Wait for explicit user confirmation of the destination, title, and body.** If the user requests edits, update `.opencode/tmp/pr-body.md` and re-confirm before proceeding.

### Step 8: Execution & Reporting

- Once explicitly confirmed by the user, execute the `gh pr create` command.
- On success, return the resulting PR URL and a one-line summary.
- **Rule:** Never automatically merge, comment on, or modify the PR after creation.

