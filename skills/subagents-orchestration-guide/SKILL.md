---
name: subagents-orchestration-guide
description: Guides subagent coordination through implementation workflows. Use when orchestrating multiple agents, managing workflow phases, or determining autonomous execution mode.
skills: documentation-criteria
---

Spec implementation orchestrators determine **what to accomplish** and **where to work**. Specialist subagents determine how to execute autonomously.

## Orchestrating Subagents

**Pass to specialists (what/where/constraints):**

- Target directory, package, or file paths
- Task file path, manifest path, or scope description
- Acceptance criteria and hard constraints from the user or design artifacts

**Let specialists determine (how):**

- Specific commands to run (specialists discover these from project configuration and repo conventions)
- Execution order and tool flags
- Which files to inspect or modify within the given scope

| Bad (orchestrator prescribes how) | Good (orchestrator passes what) |
| --- | --- |
| "Run these checks: 1. lint 2. test" | "Execute all quality checks" (validation-runner discovers them) |
| "Edit file X and add handler Y" | "Task file: docs/plans/tasks/WP-003/TASK-001.md" |

**Decision precedence when outputs conflict**:

1. User instructions (explicit requests or constraints)
2. Task files and design artifacts (Design Doc, PRD, work plan)
3. Objective repo state (git status, file system, project configuration)
4. Specialist judgment

When a subagent output contradicts your expectations, verify against objective repo state (item 3). If repo state confirms the subagent, follow the subagent. Override subagent output only when it conflicts with items 1 or 2.

## Context Discipline

Subagents perform best when they carry only the context their role requires. When constructing subagent prompts:

- A subagent prompt contains **only** the parameters in its Input Parameters table, with concrete values — paths to artifacts, not artifact contents. Subagents read the files they need themselves.
- **Pass distilled fields, not whole JSON blobs.** When one agent's output feeds another, forward only the fields the receiving agent's inputs call for. Specifically, `work-planner`'s `requirementsSummary` receives exactly these `requirements-analyzer` fields: `purpose`, `taskType`, `scale`, `affectedFiles`, and `technicalConsiderations.constraints`. Questions, scope dependencies, and confidence are orchestrator-level concerns — resolve them with the user; do not forward them.
- **The execution manifest is the single changeset source.** Reviewers and the documenter receive `manifestPath` — never a list of task files to re-derive the changeset from.
- Replace every `[placeholder]` with concrete values before invoking the Agent tool. Subagents see only the prompt and the files they read.

## Execution Manifest

You (the orchestrator) own the execution manifest — the definitive record of what the execution phase changed. Follow the `documentation-criteria` skill for its template and canonical location (`docs/plans/manifests/{workPlanId}-manifest.md`).

- Create the manifest when task execution begins.
- After **every** `task-executor` completion (including remediation tasks), append a row from the executor's JSON (`taskName`/task ID, `filesModified`, `testsAdded`) and update the deduplicated Changeset section.
- This is the only artifact the orchestrator writes; all other artifacts are written by subagents.

## Parallel Execution Guard

Task files declare dependencies, and the `task-decomposer` response includes each task's `targetFiles` (write set). You may run multiple `task-executor` instances in parallel **only** when the tasks have no dependency relationship **and** their Target Files sets are disjoint. Tasks whose write sets overlap run sequentially, even if their declared dependencies would allow parallelism.

## Handling Blocked Responses

Every subagent returns the `agent-response-protocol` envelope: `status: "completed"` or `status: "blocked"` with a typed `reason` and `detail`. On a blocked response, never silently retry the same invocation. Instead:

1. Verify the blocker against objective repo state (does the missing artifact actually not exist? is the path wrong?).
2. If the artifact exists but the path passed was wrong — re-invoke with the corrected path (orchestrator error, not agent error).
3. If a task file is defective (missing Target Files, not self-contained) — re-run `task-decomposer` for that task with the defect described.
4. If the work plan itself is defective — re-run `work-planner` in update mode with the defect described.
5. Otherwise — stop and escalate to the user with the subagent's `reason` and `detail` via **AskUserQuestion**.

## Subagents

### Available Subagents

The following table lists the available subagents. The `documentation-criteria` skill defines the canonical location for each output artifact referenced below.

| Name | Description | Outputs |
| ------ | ------------- | -------- |
| requirements-analyzer | Assesses task scope, dependencies, and scale to determine the orchestration procedure to follow | JSON assessment only |
| work-planner | Converts a spec and requirements summary into a structured work plan with phases, tasks, and dependencies | Work plan |
| risk-analyzer | Analyzes a work plan and produces a risk plan identifying risks, impacts, and mitigation strategies | Risk plan |
| task-decomposer | Decomposes a work plan into independent, single-commit-granularity task files | Task files |
| task-executor | Implements exactly one task file (TDD), or executes a remediation task | Code changes; JSON change summary |
| validation-runner | Discovers and runs the project's full build/test/lint/type-check suite against the changeset | JSON results; validation remediation task on failure |
| quality-controller | Reviews the changeset for coding-standards conformance | Quality report; QC remediation task on violations |
| code-reviewer | Reviews the changeset diff for correctness, edge cases, and design | JSON findings; code-review remediation task on findings |
| security-reviewer | Reviews the changeset for security defects (injection, authn/z, secrets, deserialization, traversal/SSRF, dependencies) | JSON findings; security remediation task on findings |
| risk-reviewer | Reviews the changeset against the risk plan to verify mitigations were implemented | Risk review document; risk remediation task on deviations |
| acceptance-validator | Verifies every spec acceptance criterion is demonstrably met by the implementation | JSON per-criterion verdicts |
| documenter | Updates doc strings, API/OpenAPI schemas, and READMEs for the changeset; produces the changeset document | Changeset document; documentation updates |

### Subagent Inputs

When invoking a subagent, provide all required inputs and any relevant optional inputs. Construct the prompt from the agent's Input Parameters and the deliverables available at that point in the flow, per the Context Discipline rules above.

| Agent | Input Parameters |
| ------- | ---------------- |
| requirements-analyzer | **requirements** (required): user request describing what to achieve. **context** (optional): recent changes, related issues, or additional constraints. |
| work-planner | **specPath** (required): path to the spec. **requirementsSummary** (required): distilled requirements-analyzer fields (purpose, taskType, scale, affectedFiles, constraints). **mode** (default `create` \| `update`). **updateContext** (update mode only): path to existing plan and reason for changes. |
| risk-analyzer | **workPlanId** (required). **requirements** (required): user request describing what to achieve. **context** (optional). |
| task-decomposer | **workPlanId** (required, e.g. `WP-001`). **planPath** (required): path to the work plan document. |
| task-executor | **taskFilePath** (required): path to the executable task file (e.g. `docs/plans/tasks/{workPlanId}/TASK-{number}.md`). |
| validation-runner | **workPlanId** (required). **manifestPath** (required): path to the execution manifest. |
| quality-controller | **workPlanId** (required). **manifestPath** (required). |
| code-reviewer | **workPlanId** (required). **manifestPath** (required). **specPath** (required): path to the spec defining intended behavior. |
| security-reviewer | **workPlanId** (required). **manifestPath** (required). |
| risk-reviewer | **riskPlanPath** (required): path to the risk plan. **workPlanId** (required). **manifestPath** (required). |
| acceptance-validator | **specPath** (required). **planPath** (required): path to the work plan. **workPlanId** (required). **manifestPath** (required). |
| documenter | **workPlanId** (required). **manifestPath** (required). |

### Subagent Responses

Subagents always respond per the `agent-response-protocol` skill: the final message is a single JSON object with `status: "completed" | "blocked"`. Schemas are the single source of truth and live in the reference directory below. Minimize context by only reading response schemas as and when required.

| Agent | Schema Location |
| ------- | ---------------- |
| requirements-analyzer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/requirements-analyzer.jsonc` |
| work-planner | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/work-planner.jsonc` |
| risk-analyzer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/risk-analyzer.jsonc` |
| task-decomposer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/task-decomposer.jsonc` |
| task-executor | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/task-executor.jsonc` |
| validation-runner | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/validation-runner.jsonc` |
| quality-controller | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/quality-controller.jsonc` |
| code-reviewer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/code-reviewer.jsonc` |
| security-reviewer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/security-reviewer.jsonc` |
| risk-reviewer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/risk-reviewer.jsonc` |
| acceptance-validator | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/acceptance-validator.jsonc` |
| documenter | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/documenter.jsonc` |
