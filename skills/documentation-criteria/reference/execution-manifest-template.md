# Execution Manifest

Plan Directory: [docs/plans/WP-NNN or docs/plans/quick/...]
Last Updated: YYYY-MM-DD HH:MM

Maintained by the orchestrator: append a row to Task Results after each `task-executor` completion (from the executor's JSON response) and keep the Changeset section deduplicated. Reviewers treat this file as the definitive changeset — they do not re-derive it from task files or `git status`.

## Task Results

| Task ID | Status | Files Modified | Tests Added |
| --- | --- | --- | --- |
| TASK-001 | completed | [file paths from executor JSON `filesModified`] | [file paths from executor JSON `testsAdded`] |

## Changeset

Deduplicated union of all files modified across tasks, with the tasks that touched each:

- [file path] — TASK-001, TASK-003
