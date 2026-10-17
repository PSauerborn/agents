---
name: subagents-orchestration-guide
description: How orchestrators invoke pipeline subagents — prompt construction, inputs per agent, the execution manifest, the parallel-execution guard, and blocked-response handling.
skills: documentation-criteria
---

Orchestrators determine **what to accomplish** and **where to work**. Specialist subagents determine how to execute.

## Orchestrating Subagents

**Pass to specialists (what/where/constraints):** the plan directory, task file path, or spec path; acceptance criteria and hard constraints from the user or design artifacts.

**Let specialists determine (how):** specific commands, execution order and tool flags, which files to inspect or modify within the given scope.

| Bad (orchestrator prescribes how) | Good (orchestrator passes what) |
| --- | --- |
| "Run these checks: 1. lint 2. test" | "planDir: docs/plans/WP-003" (validation-runner discovers them) |
| "Edit file X and add handler Y" | "taskFilePath: docs/plans/WP-003/tasks/TASK-001.md" |

**Decision precedence when outputs conflict**: (1) user instructions, (2) the spec, spec review clarifications, task files, and design artifacts, (3) objective repo state, (4) specialist judgment. When a subagent output contradicts your expectations, verify against repo state; if repo state confirms the subagent, follow the subagent.

## Context Discipline

- A subagent prompt contains **only** the parameters in its inputs row below, with concrete values — paths to artifacts, not artifact contents. Subagents read the files they need themselves.
- **Pass distilled fields, not whole JSON blobs.** `work-planner`'s `requirementsSummary` receives exactly these `requirements-analyzer` fields: `purpose`, `taskType`, `affectedFiles`, `constraints`. `spec-writer`'s `analysisSummary` receives those plus `investigationTargets` and the user's answers to the analyzer's questions. Otherwise questions are an orchestrator concern — resolve them with the user; do not forward them.
- **The execution manifest is the single changeset source.** Reviewers receive `planDir` and read `{planDir}/manifest.md` — never a list of task files to re-derive the changeset from. Before trusting it, reviewers reconcile it against the run's `TASK` commits since the Base Commit and return `manifest_incomplete` on any discrepancy; once reconciled, the manifest is definitive and the review diff is Base Commit to HEAD.
- Replace every placeholder with a concrete value before invoking the Agent tool.

## Execution Manifest

You own `{planDir}/manifest.md` — the definitive record of what execution changed. Follow the `documentation-criteria` template.

- Create it when task execution begins. Record the current branch and `git rev-parse HEAD` as the Base Commit before the first task runs; every reviewer reconciles against and diffs from this ref.
- After **every** `task-executor` completion (including remediation tasks), commit the task as described below, then append a row from the executor's JSON (`taskId`, `filesModified`, `testsAdded`) plus the commit SHA, and update the deduplicated Changeset section.

## Per-Task Commits

The orchestrator is the only agent that touches the git index. Executors and reviewers never stage or commit.

**Branch guard.** Before creating the manifest, confirm the repository is on a branch other than the default branch (`master`/`main`). If it is on the default branch, stop and ask the user to create or switch to a branch. Never create one on their behalf.

**Committing a task.** After each `task-executor` completion:

1. Run `git status` and confirm the executor changed only its Target Files and the task file.
2. Stage exactly the paths in the executor's `filesModified` and `testsAdded`, plus the task file — by pathspec, never `git add -A` or `git add .`. Parallel tasks have disjoint write sets, so pathspec staging keeps one task's commit free of another's uncommitted work.
3. Commit with the message format below. The message body is the first line of the executor's `changeSummary`, written in the imperative, at most 72 characters.
4. Record the SHA in the manifest row.

**Commit message format.**

| Commit | Format | Example |
| --- | --- | --- |
| Task implementation | `TASK {N}: {message}` | `TASK 3: Add /users route group` |
| Revision linked to one task | `TASK {N}-R.{M}: {message}` | `TASK 3-R.1: Filter expired coupons before summing` |
| Revision not linked to one task | `TASK R.{M}: {message}` | `TASK R.1: Fix lint failures in shared helpers` |

`N` is the task number from the task ID (`TASK-003` → `3`). `M` is the revision count: per task for linked revisions (the first revision to TASK-003 is `3-R.1`, the second `3-R.2`), and a single running count across the plan for unlinked revisions.

**Committing a remediation task.** A remediation task's `filesModified` usually spans several original tasks. Group the files by the task that owns them in the manifest's Changeset section and make one commit per group:

- Files owned by exactly one task → a linked revision commit for that task.
- Files owned by several tasks, or by none → one unlinked revision commit for all of them together.

Record every SHA in the manifest row for the remediation task.

**History is append-only.** Never amend, rebase, squash, reset, or force-push. A mistake is corrected by a further commit. Squashing before merge, if wanted, is the user's decision after the pipeline ends.

## Parallel Execution Guard

Task files declare dependencies, and the `task-decomposer` response includes each task's `targetFiles` (write set). Run multiple `task-executor` instances in parallel **only** when the tasks have no dependency relationship **and** their Target Files sets are disjoint. Tasks whose write sets overlap run sequentially, even if their declared dependencies would allow parallelism.

## Handling Blocked Responses

Every subagent returns the `agent-response-protocol` envelope: `status: "completed"` or `status: "blocked"` with a typed `reason` and `detail`. On a blocked response, never silently retry the same invocation. Instead:

1. Verify the blocker against repo state (does the artifact actually not exist? is the path wrong?).
2. If the artifact exists but the path passed was wrong — re-invoke with the corrected path.
3. If a reviewer returns `manifest_incomplete` — the manifest is yours. Correct the Changeset and Commit entries named in `detail` from the executor responses and `git log`, then re-invoke the reviewer. Never resolve a discrepancy by rewriting history.
4. If a task file is defective (missing Target Files, not self-contained) — re-run `task-decomposer` with the defect described.
5. If the work plan is defective (including `coverage_gap`) — re-run `work-planner` in update mode with the defect described, then re-decompose.
6. Otherwise — stop and escalate to the user with the subagent's `reason` and `detail` via **AskUserQuestion**.

## Subagent Inputs

Construct each prompt from the row below and the deliverables available at that point in the flow. `planDir` is the plan directory defined in `documentation-criteria`.

| Agent | Inputs |
| --- | --- |
| requirements-analyzer | **requirements** (required): spec path or plain request text. **context** (optional). |
| spec-writer | **requirements** (required): the request verbatim. **analysisSummary** (required): purpose, taskType, affectedFiles, investigationTargets, constraints, answered questions. **context** (optional). **mode** (`create` default \| `update`). **updateContext** (update mode only). |
| frontend-designer | **specPath** (required). **uiScope** (optional): distilled UI-relevant requirements. **context** (optional). |
| work-planner | **specPath** (required). **requirementsSummary** (required): purpose, taskType, affectedFiles, constraints. **specReviewPath** (optional). **designPath** (optional). **mode** (`create` default \| `update`). **updateContext** (update mode only). |
| task-decomposer | **planDir** (required). |
| task-executor | **taskFilePath** (required). |
| validation-runner | **planDir** (required). |
| code-reviewer | **planDir** (required). **specPath** (required). |
| security-reviewer | **planDir** (required). |
| acceptance-validator | **planDir** (required). **specPath** (required). |

## Subagent Responses

Every subagent's final message is a single JSON object per the `agent-response-protocol` skill. Schemas live at `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/{agent-name}.jsonc`; read a schema only when you need to interpret that agent's response.

Every reviewer (validation-runner, code-reviewer, security-reviewer, acceptance-validator) returns the same three fields the remediation loop depends on: `findings[]`, `remediationRequired`, `remediationTaskPath`. Validation-runner adds `verdict`; acceptance-validator adds `escalationRequired`.
