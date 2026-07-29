# PROC-003 - Full Flow

## Flow Overview

**Overview**: Full flow for large tasks with architectural impact. Adds risk planning up front and risk review to the review stage; all reviews run inside the same bounded remediation loop so late remediation is never left unreviewed. Acceptance is validated before documenting.

**Scale**: Large tasks (6+ files modified) with architectural changes.

**Required Subagents**: `work-planner`, `risk-analyzer`, `task-decomposer`, `task-executor`, `validation-runner`, `quality-controller`, `code-reviewer`, `security-reviewer`, `risk-reviewer`, `acceptance-validator`, `documenter`

## Workflow

| Step | Agent | Purpose | Output |
| ------ | ------- | --------- | -------- |
| 1 | work-planner | Generate a work plan from the spec and the distilled requirements-analyzer summary. | Work plan at its `documentation-criteria` canonical location. |
| 2 | risk-analyzer | Analyze the work plan and produce an independent risk plan. | Risk plan at its canonical location. |
| 3 | (orchestrator) | Present the work plan **and** risk plan for user approval per the `plan-approvals` skill. **[STOP]** | |
| 4 | task-decomposer | Decompose the work plan into executable task files. | Executable task files at the canonical task location. |
| 5 | (orchestrator) | Create the execution manifest per the `subagents-orchestration-guide`. | Execution manifest at its canonical location. |
| 6 | task-executor | Execute the task files. Parallelize only per the orchestration guide's Parallel Execution Guard (no dependency **and** disjoint Target Files). | Code changes; JSON change summaries. |
| 7 | (orchestrator) | Update the execution manifest from each executor response. | Updated manifest. |
| 8 | validation-runner, quality-controller, code-reviewer, security-reviewer, risk-reviewer | Review stage (Review & Remediation Loop below). May run in parallel. | Reports, risk review, and remediation tasks at their canonical locations. |
| 9 | acceptance-validator | Verify every spec acceptance criterion is demonstrably met. | JSON per-criterion verdicts. |
| 10 | (orchestrator) | If any criterion is `not_met` or `unverifiable`, present the verdicts to the user and wait for direction. **[STOP]** Otherwise continue. | |
| 11 | documenter | Update documentation for the manifest changeset. | Changeset document at its canonical location. |

## Review & Remediation Loop

Step 8 is iterative, with a cap of **2 iterations**:

1. Run `validation-runner`, `quality-controller`, `code-reviewer`, `security-reviewer`, and `risk-reviewer` against the manifest (parallel allowed — they do not write to source files).
2. Collect the remediation tasks reported in their responses. If none → exit the loop and continue to step 9.
3. Run `task-executor` on each remediation task. Remediation tasks frequently touch overlapping files — apply the Parallel Execution Guard; when in doubt run them sequentially. Update the manifest after each.
4. Re-run `validation-runner` **and** each reviewer that produced a remediation task in this iteration (remediated code must be re-reviewed by the stage that flagged it).
5. If remediation is still required after 2 iterations → **[STOP]**: present the outstanding findings to the user and wait for direction.
