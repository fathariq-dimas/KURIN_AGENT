# Command: /commit-n-push

**Trigger:** `/commit-n-push`  
**Description:** Commit and push pending changes interactively with security checks, file grouping, user-guided commit messaging, Conventional Commits formatting, and upstream push.

---

## Agent Execution Workflow

When `/commit-n-push` is triggered, the agent must execute the following sequential phases:

### Phase 1: Inspect Pending Changes
1. Run `git status --short` to see all staged, unstaged, and untracked files.
2. Run `git diff` (and `git diff --cached`) to inspect the exact diffs and understand what changed.
3. If working directory is clean (no changes), notify the user and exit immediately.

### Phase 2: Enforce `.gitignore` & Identity/Secret Sanitation
Before any staging or committing, verify that no personal developer identity, certificates, or secrets are exposed:
1. **Verify `.gitignore` exists:**
   - If `.gitignore` is missing, create it immediately using standard Apple/Xcode patterns and notify the user.
2. **Identity check via `git check-ignore`:**
   - Run `git check-ignore` over every changed or untracked path.
   - **Refuse to commit** anything matching identity patterns:
     - `xcuserdata/`
     - `*.xcuserstate`
     - `*.pbxuser`
     - `*.mode1v3`
     - `*.mode2v3`
     - `*.perspectivev3`
     - `*.xccheckout`
     - `*.mobileprovision`
     - `*.p12`
     - `*.cer`
     - `*.key`
     - `**/ExportOptions.plist`
   - If any identity file is currently staged, run `git reset HEAD <path>` and ensure it is added to `.gitignore`. Report this to the user.
3. **Verify Xcode Project Development Team ID:**
   - Check all `project.pbxproj` files:
     ```bash
     grep -n "DEVELOPMENT_TEAM = [0-9A-Z]" $(find . -name "project.pbxproj") 2>/dev/null
     ```
   - If found, clear the value first (e.g. `DEVELOPMENT_TEAM = "";`) so `.xcodeproj` never carries a personal bundle or team identifier.
4. **Secrets check:**
   - Check diffs for hardcoded API keys, bearer tokens, passwords, or personal health records. Never proceed if secrets are detected.

---

### Phase 3: Propose a Commit Plan (Grouping)
Group the pending changes logically before touching the index:
- **Single unrelated file:** 1 commit per isolated change (e.g., docs update in one file, typo fix in another).
- **Related files:** 1 commit per group of related files (same feature, layer, or refactoring area), e.g. `OnboardingViewModel.swift` + `OnboardingView.swift` + `HealthScreening.swift`.

**Present the plan to the user clearly:**
For each proposed group, display:
- **Group Number & Name**
- **List of Files** in the group
- **Suggested Commit Subject**
- **Explanation:** Why this group represents a single, coherent change.

---

### Phase 4: Gather User's Commit Message
For each group:
- Ask the user for their commit message ideas.
- The user may:
  1. Provide keywords or a rough draft (e.g. *"tambahin validasi umur"*).
  2. Provide a preferred final message.
  3. Accept the suggested commit message proposed in Phase 3.
- **Rule:** This is the user's message. Do not override their intent.

---

### Phase 5: Rephrase & Confirm (Conventional Commits)
Help refine the user's draft to match repository standards:
1. **Format:** Conventional Commits: `<type>(<scope>): <subject>`
   - Types: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`
   - Subject: Imperative mood, lowercase, no trailing period.
   - Length: Subject line ≤ 50 characters.
2. **Preserve Intent:** Keep the user's exact intent and core terminology; only polish grammar and precision.
3. **Confirmation Display:**
   Show the comparison and ask for user confirmation:
   ```text
   Original : "tambahin validasi umur"
   Proposed : "feat(onboarding): add age validation to date of birth"
   ```
   Wait for user confirmation before executing `git commit`.

---

### Phase 6: Stage and Commit per Group
Execute commits sequentially per approved group:
1. Stage only the files belonging to the group:
   ```bash
   git add <file1> <file2> ...
   ```
2. Commit with the confirmed message:
   ```bash
   git commit -m "<confirmed-message>"
   ```
3. Repeat for each subsequent group.

---

### Phase 7: Review, Push, and Final Report
1. **Review history:**
   Run `git log --oneline -5` to review recent commits.
2. **Push upstream:**
   - Attempt push:
     ```bash
     git push
     ```
   - If the branch has no upstream tracking, run:
     ```bash
     git push -u origin <current-branch>
     ```
   - If no remote is configured, inform the user: *"No Git remote configured; local commits were created successfully, but push could not be completed."*
3. **Report to User:**
   Present a summary table/list containing:
   - Commit hash(es) created
   - Commit message(s)
   - Files included in each commit
   - Remote branch and push status

