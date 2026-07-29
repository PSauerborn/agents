# PROC-001 - Reduced Flow

## Flow Overview

**Overview**: Reduced flow for small tasks. Single work plan decomposed into one or two tasks; full-suite validation still runs, review stages are omitted.

**Scale**: Small tasks (1-2 files modified)

**Required Subagents**: `work-planner`, `task-decomposer`, `task-executor`, `validation-runner`, `documenter`

## Workflow

| Step | Agent | Purpose | Output |
| ------ | ------- | --------- | -------- |
| 1 | work-planner | Generate a work plan from the spec and the distilled requirements-analyzer summary. | Work plan at its `documentation-criteria` canonical location. |
| 2 | (orchestrator) | Present the work plan for user approval per the `plan-approvals` skill. **[STOP]** | |
| 3 | task-decomposer | Decompose the work plan into task file(s). | Executable task file(s) at the canonical task location. |
| 4 | (orchestrator) | Create the execution manifest per the `subagents-orchestration-guide`. | Execution manifest at its canonical location. |
| 5 | task-executor | Execute the task file(s). | Code changes; JSON change summary. |
| 6 | (orchestrator) | Update the execution manifest from each executor response. | Updated manifest. |
| 7 | validation-runner | Run the full build/test/lint suite (Review & Remediation Loop below). | Validation results; remediation task on failure. |
| 8 | documenter | Update documentation for the manifest changeset. | Changeset document at its canonical location. |

## Review & Remediation Loop

Step 7 is iterative, with a cap of **2 iterations**:

1. Run `validation-runner` against the manifest.
2. If `allPassed` is true → exit the loop and continue to step 8.
3. Otherwise run `task-executor` on `TASK-VALIDATION-REMEDIATION.md`, update the manifest, and re-run `validation-runner`.
4. If checks still fail after 2 iterations → **[STOP]**: present the remaining failures to the user and wait for direction.
