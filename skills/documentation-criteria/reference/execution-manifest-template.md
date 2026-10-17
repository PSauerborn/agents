# Execution Manifest

Plan Directory: [docs/plans/WP-NNN or docs/plans/quick/...]
Branch: [branch name at execution start]
Base Commit: [full SHA of HEAD before the first task ran — reviewers reconcile against and diff from this ref]
Last Updated: YYYY-MM-DD HH:MM

Maintained by the orchestrator: commit each task after `task-executor` completes, append a row to Task Results (from the executor's JSON response and the commit SHA), and keep the Changeset section deduplicated. Reviewers first reconcile this file against the run's `TASK` commits since the Base Commit and return `manifest_incomplete` on any discrepancy; once it reconciles they treat it as the definitive changeset — they do not re-derive it from task files or `git status` — and diff each file from the Base Commit to HEAD.

## Task Results

| Task ID | Status | Files Modified | Tests Added | Commit |
| --- | --- | --- | --- | --- |
| TASK-001 | completed | [file paths from executor JSON `filesModified`] | [file paths from executor JSON `testsAdded`] | [short SHA — `TASK 1: ...`] |
| TASK-CODE-REVIEW-REMEDIATION | completed | [...] | [...] | [one short SHA per revision commit — `TASK 1-R.1: ...`, `TASK R.1: ...`] |

## Changeset

Deduplicated union of all files modified across tasks, with the tasks that touched each:

- [file path] — TASK-001, TASK-003
