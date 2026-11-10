# Rule: Git Commit Workflow

**Applies to:** Staging, commits, and pushing Git changes  
**Mandatory:** Yes

---

## 1. Workflow Boundaries

| Activity | Rule | Command |
| --- | --- | --- |
| Create a working branch | [`06a-create-branch.md`](06a-create-branch.md) | [`/create-branch`](../commands/create-branch.md) |
| Commit and push changes | This rule | [`/commit-n-push`](../commands/commit-n-push.md) |
| Create a pull request | [`06b-create-pr.md`](06b-create-pr.md) | [`/create-pr`](../commands/create-pr.md) |

Read the activity-specific rule before running its command.

---

## 2. Commit Message Convention

Commit messages follow **Conventional Commits**:

```text
<type>(<scope>): <deskripsi singkat>
```

- **Type:** `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, or `ci`.
- **Scope (optional):** Feature or layer name, such as `onboarding`, `session`, `repo`, or `ui`.
- **Description:** Lowercase, imperative, concise, and without a trailing period.
- **Subject length:** At most 50 characters.

Examples:

- `feat(onboarding): add health screening question view`
- `fix(session): correct duration calculation`
- `test(rpe): cover post-exercise calculation`

---

## 3. Commit Safety Rules

1. Inspect staged, unstaged, and untracked changes before staging files.
2. Group files into coherent commits; do not mix unrelated changes.
3. Never commit credentials, tokens, certificates, provisioning profiles, personal health data, or developer-specific Xcode identity files.
4. Stage only the files that belong to the confirmed commit group.
5. Do not rewrite published history or force-push unless the user explicitly requests it and understands the impact.
6. Confirm the final commit message with the user before creating the commit.

---

## 4. Command Reference

Use **[`/commit-n-push`](../commands/commit-n-push.md)** for identity and secret checks, logical file grouping, Conventional Commit rephrasing, explicit confirmation, and upstream push.

