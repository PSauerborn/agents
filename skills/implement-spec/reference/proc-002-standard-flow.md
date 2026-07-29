# PROC-002 - Standard Flow

## Flow Overview

**Overview**: Standard flow for medium tasks. Generates a plan and task documents, executes with parallelism where safe, then runs the full review stage — validation, standards, correctness, and security — with a bounded remediation loop, and validates acceptance criteria before documenting.

**Scale**: Medium tasks (3-5 files modified)

**Required Subagents**: `work-planner`, `task-decomposer`, `task-executor`, `validation-runner`, `quality-controller`, `code-reviewer`, `security-reviewer`, `acceptance-validator`, `documenter`

## Workflow

| Step | Agent | Purpose | Output |
| ------ | ------- | --------- | -------- |
| 1 | work-planner | Generate a work plan from the spec and the distilled requirements-analyzer summary. | Work plan at its `documentation-criteria` canonical location. |
| 2 | (orchestrator) | Present the work plan for user approval per the `plan-approvals` skill. **[STOP]** | |
| 3 | task-decomposer | Decompose the work plan into executable task files. | Executable task files at the canonical task location. |
| 4 | (orchestrator) | Create the execution manifest per the `subagents-orchestration-guide`. | Execution manifest at its canonical location. |
| 5 | task-executor | Execute the task files. Parallelize only per the orchestration guide's Parallel Execution Guard (no dependency **and** disjoint Target Files). | Code changes; JSON change summaries. |
| 6 | (orchestrator) | Update the execution manifest from each executor response. | Updated manifest. |
| 7 | validation-runner, quality-controller, code-reviewer, security-reviewer | Review stage (Review & Remediation Loop below). May run in parallel. | Reports and remediation tasks at their canonical locations. |
| 8 | acceptance-validator | Verify every spec acceptance criterion is demonstrably met. | JSON per-criterion verdicts. |
| 9 | (orchestrator) | If any criterion is `not_met` or `unverifiable`, present the verdicts to the user and wait for direction. **[STOP]** Otherwise continue. | |
| 10 | documenter | Update documentation for the manifest changeset. | Changeset document at its canonical location. |

## Review & Remediation Loop

Step 7 is iterative, with a cap of **2 iterations**:

1. Run `validation-runner`, `quality-controller`, `code-reviewer`, and `security-reviewer` against the manifest (parallel allowed — they do not write to source files).
2. Collect the remediation tasks reported in their responses. If none → exit the loop and continue to step 8.
3. Run `task-executor` on each remediation task. Remediation tasks frequently touch overlapping files — apply the Parallel Execution Guard; when in doubt run them sequentially. Update the manifest after each.
4. Re-run `validation-runner` **and** each reviewer that produced a remediation task in this iteration (remediated code must be re-reviewed by the stage that flagged it).
5. If remediation is still required after 2 iterations → **[STOP]**: present the outstanding findings to the user and wait for direction.
