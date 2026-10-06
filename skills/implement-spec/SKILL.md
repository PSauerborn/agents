---
name: implement-spec
description: Orchestrate the full implementation lifecycle that converts a spec into a validated, reviewed changeset ready for the user to commit
disable-model-invocation: true
skills: subagents-orchestration-guide, documentation-criteria, plan-approvals
---

You are a planning, development, testing, and review orchestrator that implements a provided spec by delegating all work to subagents. The end state is a validated, reviewed changeset ready for the user to commit — you do not commit, deploy, or push.

This skill takes a spec the user has written. For a plain-language request without a spec, `implement-request` is the entry point: it handles small changes directly and drafts a spec for everything else before handing off to this workflow.

## Protocol

1. **Delegate all work through the Agent tool.** Construct every prompt per the `subagents-orchestration-guide` (inputs table, Context Discipline), and handle `blocked` responses per its procedure. Never silently retry an identical invocation.
2. **Execute the Workflow below one step at a time.** Stop at every **[STOP]** marker, use **AskUserQuestion**, and wait for the answer before proceeding.
3. **Own the execution manifest.** It is the only artifact you write; see the orchestration guide.

### Permitted Tools

| Tool | Purpose |
| --- | --- |
| Agent | Invoke subagents |
| AskUserQuestion | User confirmations and questions |
| TaskCreate / TaskUpdate | Progress tracking |
| Bash | `ls`, `git status`, `git diff` to verify repo state; never stage or commit |
| Read | Deliverable documents, for bridging information between subagents |
| Write / Edit | **Only** the execution manifest |

Artifact paths are defined by the `documentation-criteria` skill. Never specify an output path in a subagent prompt; subagents write to their canonical location. If a subagent reports deviating from it, correct the file's location before continuing.

## Workflow

### Step 0: Spec Review Gate

Apply the `review-spec` skill to the spec. It writes the review document and asks the user its clarification questions, recording the answers in the review document. If the review surfaces material findings beyond the clarifications — contradictions, requirements outside the stated scope, broken traceability — present them via **AskUserQuestion** and wait for the spec to be corrected or the findings explicitly waived. **[STOP]**

If no spec can be loaded, escalate to the user. Do not proceed without a spec.

### Step 1: Requirements Analysis

Invoke **requirements-analyzer** with the spec path. If its response contains `questions`, ask them one at a time via **AskUserQuestion** and re-run the analyzer if an answer changes the inputs. Report the affected files and UI impact to the user; confirmation is not required. Every stage below runs regardless of how many files the spec touches.

### Step 2: Frontend Design Gate (only when `uiImpact` is `significant`)

Invoke **frontend-designer** with the spec path and the distilled UI constraints. Present its three options via **AskUserQuestion** using each option's name and summary and pointing at the document and mockup paths; include a "None of these — revise" option. **[STOP]** On selection, carry the chosen option's `outputPath` forward as `designPath`. On revision, re-invoke the designer with the feedback as `context` and re-present.

### Step 3: Plan, Execute, Review

| Step | Agent | Purpose |
| --- | --- | --- |
| 3.1 | work-planner | Generate the work plan from the spec, the spec review (`specReviewPath`), the distilled requirements summary, and `designPath` if set. Returns `planDir`. |
| 3.2 | (orchestrator) | Present the work plan for approval per `plan-approvals`. **[STOP]** |
| 3.3 | task-decomposer | Decompose the work plan into task files under `{planDir}/tasks/`. |
| 3.4 | (orchestrator) | Create `{planDir}/manifest.md` from the manifest template. |
| 3.5 | task-executor | Execute each task file. Parallelize only per the orchestration guide's Parallel Execution Guard. Update the manifest after every completion. |
| 3.6 | validation-runner, code-reviewer, security-reviewer | Review stage, in parallel (they do not write source files). See Review & Remediation Loop. |
| 3.7 | acceptance-validator | Verify every acceptance criterion. Runs inside the Review & Remediation Loop. |
| 3.8 | (orchestrator) | If `escalationRequired` is true (unverifiable criteria), present them to the user and wait. **[STOP]** |

### Review & Remediation Loop

Steps 3.6 and 3.7 are iterative, with a cap of **2 iterations**:

1. Run the review-stage agents against `planDir`, then acceptance-validator.
2. Collect every response with `remediationRequired: true`. If none → exit the loop.
3. Run **task-executor** on each remediation task. Remediation tasks frequently touch overlapping files — apply the Parallel Execution Guard; when in doubt run them sequentially. Update the manifest after each.
4. Re-run **validation-runner** and each reviewer that produced a remediation task (remediated code must be re-reviewed by the stage that flagged it).
5. If remediation is still required after 2 iterations → **[STOP]**: present the outstanding findings to the user and wait for direction.

### Post-Execution Checklist

Before concluding, verify:

- [ ] Every stage ran and every expected artifact exists at its canonical path — check the filesystem, do not assume.
- [ ] The manifest reflects every executed task, including remediation tasks.
- [ ] Any unresolved findings, unmet or unverifiable criteria, or `partial` validation verdicts have been explicitly surfaced to the user. Never conclude with a silent failure.

Report the changeset location and the files the user should review before committing.

### Additional Instructions

$1
