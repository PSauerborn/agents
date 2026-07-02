---
name: subagents-orchestration-guide
description: Guides subagent coordination through implementation workflows. Use when orchestrating multiple agents, managing workflow phases, or determining autonomous execution mode.
---

Spec implementation orchestrators determine **what to accomplish** and **where to work**. Specialist subagents determine how to execute autonomously.

## Orchestrating Subagents

**Pass to specialists (what/where/constraints):**

- Target directory, package, or file paths
- Task file path or scope description
- Acceptance criteria and hard constraints from the user or design artifacts

**Let specialists determine (how):**

- Specific commands to run (specialists discover these from project configuration and repo conventions)
- Execution order and tool flags
- Which files to inspect or modify within the given scope

| Bad (orchestrator prescribes how) | Good (orchestrator passes what) |
| --- | --- |
| "Run these checks: 1. lint 2. test" | "Execute all quality checks and fixes" |
| "Edit file X and add handler Y" | "Task file: docs/plans/tasks/003-feature.md" |

**Decision precedence when outputs conflict**:

1. User instructions (explicit requests or constraints)
2. Task files and design artifacts (Design Doc, PRD, work plan)
3. Objective repo state (git status, file system, project configuration)
4. Specialist judgment

When a subagent output contradicts your expectations, verify against objective repo state (item 3). If repo state confirms the subagent, follow the subagent. Override subagent output only when it conflicts with items 1 or 2.

When a subagent cannot determine execution method from repo state and artifacts, the subagent escalates as blocked instead of guessing. The orchestrator then escalates to the user with the subagent's blocked details.

## Subagents

### Available Subagents

| Name | Description | Outputs |
| ------ | ------------- | -------- |
| requirements-analyzer | Assesses task scope, dependencies, and scale to determine the orchestration procedure to follow | |
| work-planner | Converts a user spec into a structured work plan with phases, tasks, and dependencies in `docs/plans/` | |
| risk-analyzer | Analyzes a work plan and produces a single risk plan document identifying risks, impacts, and mitigation strategies | |
| task-decomposer | Decomposes a work plan into independent, single-commit-granularity task files in `docs/plans/tasks/` | |
| task-executor | Implements code changes based on provided specifications and task files | |
| quality-controller | Reviews code changes against coding standards, produces a quality report, and creates remediation tasks for any violations | |
| risk-reviewer | Reviews code changes against a risk plan and creates a remediation task for any risk mitigations required | |

### Invoking Subagents

Subagents are invoked by the orchestrator passing the necessary context, including target directories, task files, and constraints, to the subagent responsible for execution.

When invoking a subagent, every subagent prompt must include:

- Input deliverables with file paths (from previous step or prerequisite check)
- Expected action (what the agent should do)

Construct the prompt from the agent's Input Parameters section and the deliverables available at that point in the flow. When invoking a subagent, make sure that:

- Subagents see only the Agent prompt and files they read. Include required paths, prior JSON, parameters, and scope constraints explicitly.
- Replace every [placeholder] in examples below with concrete values before invoking the Agent tool.

### Subagent Inputs

The following table lists the input parameters for each subagent. When invoking a subagent, provide all required inputs and any optional inputs that are relevant to the task.

| Agent | Input Parameters |
| ------- | ---------------- |
| requirements-analyzer | **requirements** (required): user request describing what to achieve. **context** (optional): recent changes, related issues, or additional constraints. |
| work-planner | **mode** (default `create` \| `update`). **updateContext** (update mode only): path to existing plan and reason for changes. |
| risk-analyzer | **workPlanId** (required): unique identifier for the current work plan. **requirements** (required): user request describing what to achieve. **context** (optional): recent changes, related issues, or additional constraints. |
| task-decomposer | **workPlanId** (required): unique identifier for the work plan to decompose (e.g. `WP-001`). **planPath** (required): path to the work plan document to decompose (e.g. `docs/plans/{workPlanId}.md`). |
| task-executor | **taskFilePath** (required): path to the executable task file (e.g. `docs/plans/tasks/{workPlanId}/TASK-{number}.md`). |
| quality-controller | **workPlanId** (required): unique identifier for the current work plan. **requirements** (required): user request describing what to achieve. **context** (optional): recent changes, related issues, or additional constraints. |
| risk-reviewer | **riskPlanPath** (required): path to the risk plan document. **workPlanId** (required): unique identifier for the current work plan. **requirements** (required): user request describing what to achieve. **context** (optional): recent changes, related issues, or additional constraints. |

### Subagent Responses

Subagents always respond in JSON format. Schemas for responses are defined in the provided reference directory. Minimize context by only analyzing response schemas as and when required.

| Agent | Schema Location |
| ------- | ---------------- |
| requirements-analyzer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/requirements-analyzer.jsonc` |
| work-planner | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/work-planner.jsonc` |
| risk-analyzer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/risk-analyzer.jsonc` |
| task-decomposer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/task-decomposer.jsonc` |
| task-executor | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/task-executor.jsonc` |
| quality-controller | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/quality-controller.jsonc` |
| risk-reviewer | `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/risk-reviewer.jsonc` |
