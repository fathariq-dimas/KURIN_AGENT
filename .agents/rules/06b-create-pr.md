# Rule: Create Pull Request

**Applies to:** Pull-request destination selection, readiness, content, and creation  
**Mandatory:** Yes

---

## 1. Destination Branch Selection

1. The user may provide an existing destination branch explicitly.
2. If the destination is omitted, list the available remote branches as numbered choices and ask the user to select one. Mark the standard destination as recommended when it can be inferred, but do not select it silently.
3. The standard destination for working branches is `development`.
4. The standard destination for `development` is `main`.
5. A stacked working branch may target its parent working branch.
6. Never open a PR from `main`, target the current head branch, or use a destination that does not exist on the remote.
7. When the user selects a non-standard destination, show the head and destination pair explicitly before drafting the PR.

---

## 2. Readiness Rules

Before drafting a PR:

1. The working tree must be clean.
2. Every intended commit must be pushed to the head branch's upstream remote.
3. Required tests and verification must be complete, or the PR must clearly explain what remains unverified and why.
4. Review the diff against the selected destination for unrelated files, secrets, credentials, developer identity, and sensitive health data.
5. Do not push, merge, comment on, or otherwise modify a PR implicitly.

---

## 3. PR Content

Use `.github/pull_request_template.md` and replace every placeholder. The PR must include:

- A concise summary of the problem solved or capability added.
- Notable changes grouped by area.
- Relevant phase or milestone information, or an explicit `N/A`.
- Verification evidence, including tests and applicable Acceptance Criteria.
- The Task ID and canonical `docs/Feature/<feature-id>-<feature-slug>/` reference when applicable.
- Reviewer notes, limitations, edge cases, or follow-up work.

The PR title must use a Conventional Commit type, match the branch scope, and contain at most 72 characters.

---

## 4. Safety Checklist

- [ ] The destination branch was explicitly provided or selected by the user.
- [ ] The head branch is fully pushed and the working tree is clean.
- [ ] The PR template has no remaining placeholders.
- [ ] Relevant tests and Acceptance Criteria are documented.
- [ ] No new force unwraps (`!`) were introduced in production code.
- [ ] No credentials, developer identity, or sensitive health data are included.
- [ ] The user reviewed and explicitly confirmed the final destination, title, and body.

---

## 5. Command Reference

Use **[`/create-pr`](../commands/create-pr.md)** to select or validate the destination, run readiness checks, draft the template, obtain explicit confirmation, and create the PR.

