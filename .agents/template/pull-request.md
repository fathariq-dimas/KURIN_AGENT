<!--
TEMPLATE ONLY — NON-AUTHORITATIVE

This file defines pull-request formatting only. Every HTML comment is an
authoring instruction, not a claim about the change. Replace every angle-bracket
placeholder with evidence from the branch, approved documents, and completed
verification. Remove all instruction comments before opening the pull request.

Do not claim a test, review, requirement, or safety check passed without evidence.
-->

# <PR_TITLE>

> **Task / plan:** `<TASK_OR_PLAN_ID>`  
> **Target branch:** `<TARGET_BRANCH>`  
> **Feature documents:** `<FEATURE_DOCUMENT_PATHS>`  
> **Implementation plan:** `<IMPLEMENTATION_PLAN_PATH>`

> **Section Definition — Metadata**
>
> - **Meaning:** Identifies the change, review destination, and authoritative scope references.
> - **Include:** Tracked task/plan, target branch, feature documents, and implementation plan using canonical references.
> - **Exclude:** Guessed links, stale plans, or omitted fields without a stated reason.
> - **Completion:** A reviewer can locate the authority and intended merge destination for the PR.

## Summary

> **Section Definition — Summary**
>
> - **Meaning:** Gives reviewers a concise account of the problem and delivered outcome.
> - **Include:** Problem, user/system outcome, and scope boundary.
> - **Exclude:** Chronological development notes, unsupported claims, or low-level file lists.
> - **Completion:** The summary accurately matches the diff and linked requirements.

<SUMMARY>

## Changes

> **Section Definition — Changes**
>
> - **Meaning:** Explains the material contents of the diff in reviewable groups.
> - **Include:** What changed, why, and key paths/components, including migrations, configuration, docs, and removals when applicable.
> - **Exclude:** Every trivial edit, commit history, or behavior not present in the diff.
> - **Completion:** All material diff areas are represented and traceable to purpose.

| Area | Change | Reason | Key paths |
|---|---|---|---|
| <AREA> | <CHANGE> | <REASON> | `<PATHS>` |

## Acceptance Criteria

> **Section Definition — Acceptance Criteria**
>
> - **Meaning:** Reports whether each in-scope acceptance criterion passed and on what evidence.
> - **Include:** Every affected AC, actual result, and inspectable test/review evidence.
> - **Exclude:** Planned tests, unmapped claims, or completed results without evidence.
> - **Completion:** Every in-scope AC has an honest result; missing or failing verification remains visible.

| AC | Result | Evidence |
|---|---|---|
| `<AC_ID>` | `<RESULT>` | <TEST_OUTPUT_OR_REVIEW_EVIDENCE> |

## Verification

> **Section Definition — Verification**
>
> - **Meaning:** Presents the actual evidence used to evaluate correctness and user-visible quality.
> - **Include:** Automated results, manual observations, and visual evidence as applicable to the diff.
> - **Exclude:** Planned checks, unverifiable claims, credentials, or sensitive data.
> - **Completion:** Every acceptance criterion and material risk has sufficient evidence or a visible explanation for any gap.

### Automated

> **Subsection Definition — Automated Verification**
>
> - **Meaning:** Records reproducible machine-executed checks actually completed for the PR.
> - **Include:** Exact command or CI job, environment, result, and concise relevant output when useful.
> - **Exclude:** Commands not run, secret values, personal data, or unsupported "all tests passed" claims.
> - **Completion:** Every automated claim elsewhere in the PR points to a result here; use `Not run — <reason>` when applicable.

| Command or job | Environment | Result |
|---|---|---|
| `<COMMAND_OR_JOB>` | <ENVIRONMENT> | <RESULT> |

### Manual

> **Subsection Definition — Manual Verification**
>
> - **Meaning:** Records human-executed scenarios used to verify behavior not sufficiently covered by automation.
> - **Include:** Scenario, synthetic setup, action, environment/device when relevant, and observed result.
> - **Exclude:** Planned checks, sensitive data, or expected results presented as observations.
> - **Completion:** Material manual-risk scenarios are covered; otherwise state `Not applicable — <reason>`.

| Scenario | Setup and action | Observed result |
|---|---|---|
| <SCENARIO> | <SETUP_AND_ACTION> | <OBSERVED_RESULT> |

### Visual Evidence

> **Subsection Definition — Visual Evidence**
>
> - **Meaning:** Shows the rendered outcome of user-visible changes for direct reviewer inspection.
> - **Include:** Screenshots/recordings with device, platform, state, and synthetic or redacted data.
> - **Exclude:** Sensitive information, unrelated screens, or images that do not show the claimed state.
> - **Completion:** Each visual change has representative evidence; otherwise state `Not applicable — <reason>`.

<VISUAL_EVIDENCE_OR_NOT_APPLICABLE>

## Risk and Rollback

> **Section Definition — Risk and Rollback**
>
> - **Meaning:** Explains credible adverse effects of merging and the path back to safety.
> - **Include:** Risk, impact, mitigation, and tested rollback or recovery, including affected data/users.
> - **Exclude:** Generic risks, unreasoned ratings, or unsafe/destructive recovery instructions.
> - **Completion:** Every material risk has mitigation and recovery; use `None — <assessment reason>` only after evaluation.

| Risk | Impact | Mitigation | Rollback or recovery |
|---|---|---|---|
| <RISK> | <IMPACT> | <MITIGATION> | <ROLLBACK_OR_RECOVERY> |

## Review Checklist

> **Section Definition — Review Checklist**
>
> - **Meaning:** Provides a final evidence-backed review gate before merge.
> - **Include:** Applicable checks derived from ACs, plan, and project rules for scope, traceability, architecture, privacy, safety, builds, tests, migrations, and visuals.
> - **Exclude:** Boilerplate checks irrelevant to the diff or checked items without evidence elsewhere.
> - **Completion:** Each item has evidence and accurately reflects its current state.

- [ ] <REVIEW_CHECK>

## Related Work

> **Section Definition — Related Work**
>
> - **Meaning:** Connects the PR to work that it depends on, enables, follows, or defers.
> - **Include:** Relationship type, canonical reference, and current status/note.
> - **Exclude:** General background links or hidden blockers.
> - **Completion:** All relevant dependencies and follow-ups are traceable; write `None` when absent.

| Relationship | Reference | Status or note |
|---|---|---|
| `<RELATIONSHIP>` | `<REFERENCE>` | <STATUS_OR_NOTE> |

## Reviewer Notes

> **Section Definition — Reviewer Notes**
>
> - **Meaning:** Directs reviewer attention to decisions and areas requiring judgment beyond mechanical verification.
> - **Include:** Design decisions, tradeoffs, migration concerns, known limitations, blockers, and high-value review areas.
> - **Exclude:** Repeated summary text, routine changes, or concealed unresolved issues.
> - **Completion:** Reviewers know where careful inspection is most valuable; write `None` when no additional note is needed.

<REVIEWER_NOTES>
