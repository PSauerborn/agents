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
- **The execution manifest is the single changeset source.** Reviewers receive `planDir` and read `{planDir}/manifest.md` — never a list of task files to re-derive the changeset from.
- Replace every placeholder with a concrete value before invoking the Agent tool.

## Execution Manifest

You own `{planDir}/manifest.md` — the definitive record of what execution changed. Follow the `documentation-criteria` template.

- Create it when task execution begins.
- After **every** `task-executor` completion (including remediation tasks), append a row from the executor's JSON (`taskId`, `filesModified`, `testsAdded`) and update the deduplicated Changeset section.

## Parallel Execution Guard

Task files declare dependencies, and the `task-decomposer` response includes each task's `targetFiles` (write set). Run multiple `task-executor` instances in parallel **only** when the tasks have no dependency relationship **and** their Target Files sets are disjoint. Tasks whose write sets overlap run sequentially, even if their declared dependencies would allow parallelism.

## Handling Blocked Responses

Every subagent returns the `agent-response-protocol` envelope: `status: "completed"` or `status: "blocked"` with a typed `reason` and `detail`. On a blocked response, never silently retry the same invocation. Instead:

1. Verify the blocker against repo state (does the artifact actually not exist? is the path wrong?).
2. If the artifact exists but the path passed was wrong — re-invoke with the corrected path.
3. If a task file is defective (missing Target Files, not self-contained) — re-run `task-decomposer` with the defect described.
4. If the work plan is defective (including `coverage_gap`) — re-run `work-planner` in update mode with the defect described, then re-decompose.
5. Otherwise — stop and escalate to the user with the subagent's `reason` and `detail` via **AskUserQuestion**.

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
