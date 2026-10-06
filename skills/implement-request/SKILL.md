---
name: implement-request
argument-hint: [change request]
description: Implement a plain-language request without the user writing a spec. A small change (1-2 files) runs as one task file with full-suite validation; anything larger gets a drafted spec, which the user approves before the full implement-spec workflow runs.
disable-model-invocation: true
skills: subagents-orchestration-guide, documentation-criteria
---

You implement a request given in the user's own words. There is no spec to start from. You delegate all work to subagents, and the end state is a validated changeset ready for the user to commit — you do not commit, deploy, or push.

## Protocol

1. **Delegate all work through the Agent tool**, constructing prompts per the `subagents-orchestration-guide` and handling `blocked` responses per its procedure.
2. **Stop at every [STOP] marker** via **AskUserQuestion**.
3. **On the quick path you write exactly two artifacts**: the task file and the execution manifest. On the spec path you write only the manifest, inside the `implement-spec` workflow.

Permitted tools: Agent, AskUserQuestion, TaskCreate / TaskUpdate, Bash (`ls`, `git status`, `git diff` only — never stage or commit), Read, Write/Edit (task file and manifest only).

## Workflow

### Step 1: Analyze

Invoke **requirements-analyzer** with `requirements` set to the user's request verbatim. Ask any returned `questions` via **AskUserQuestion**, one at a time, and re-run the analyzer if an answer changes the inputs. Keep the answers; both paths below use them.

Route on the analyzer's `small` field: `true` → Quick Path; `false` → Spec Path.

## Quick Path (small change)

### Q1: Write the Task File

Create the plan directory `docs/plans/quick/{YYYY-MM-DD}-{slug}/` (slug: 2-4 lowercase words from the request, hyphenated; add a numeric suffix if the directory exists). Write `tasks/TASK-001.md` from the `documentation-criteria` task template:

- `Work Plan ID: quick`; `Acceptance Criteria Covered: infrastructure`.
- **Change Request**: the request verbatim; for fixes, Observed, Expected, and Reproduction from the analyzer's `purpose`, the user's answers, and your reading of the request; Acceptance as one or two plain-language checks that prove the change is complete.
- **Target Files**: the analyzer's `affectedFiles`.
- **Investigation Targets**: the analyzer's `investigationTargets`.
- **Completion Criteria**: the Acceptance entries plus "all added tests pass".

Present the task file path, the Target Files, and the Acceptance checks via **AskUserQuestion** with options Approve / Modify / Cancel. **[STOP]** On Modify, apply the user's changes to the task file and re-present.

### Q2: Execute

Invoke **task-executor** with the task file path. On completion, create `{planDir}/manifest.md` from the manifest template and record the executor's `filesModified` and `testsAdded`.

### Q3: Validate

Run **validation-runner** against `planDir`, with a cap of **2 iterations**:

1. If `remediationRequired` is false → exit the loop.
2. Otherwise run **task-executor** on the remediation task, update the manifest, and re-run **validation-runner**.
3. If still failing after 2 iterations → present the outstanding failures to the user and wait for direction.

### Q4: Report

State the validation verdict (`verified`, `partial`, or `failed`), the files changed, and the task file path. A `partial` verdict is reported with the not-run or pre-existing failures named; it is never presented as success.

## Spec Path (everything else)

### S1: Draft the Spec

Invoke **spec-writer** with `requirements` (the request verbatim) and `analysisSummary` (the analyzer's purpose, taskType, affectedFiles, investigationTargets, constraints, plus the user's answers from Step 1). It writes `docs/specs/SPEC-NNN.md` and returns the markers it could not resolve.

### S2: Approve the Draft

Present the spec path, its In Scope list, its requirements, and the `clarificationsNeeded` list via **AskUserQuestion** with options Approve / Modify / Cancel. **[STOP]** Tell the user they may edit the spec file directly before approving; it is theirs. On Modify with feedback, re-invoke **spec-writer** in update mode with the feedback as `updateContext` and re-present.

Once approved, the drafted spec is a user input like any other: no agent edits it from here on.

### S3: Run the Spec Workflow

Read `${CLAUDE_PLUGIN_ROOT}/skills/implement-spec/SKILL.md` and execute its Workflow from Step 0 with the approved spec path, exactly as written. Its review gate resolves the remaining `[NEEDS CLARIFICATION]` markers with the user and records the answers in the review document; its Step 1 re-runs the analyzer against the spec, since the user may have edited it.

### Additional Instructions

$1
