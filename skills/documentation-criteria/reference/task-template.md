# Task: [Task Name]

Work Plan ID: [WP-[0-9]{3}, or `quick` for a spec-free task]
Task ID: TASK-[0-9]{3}
Created Date: YYYY-MM-DD
Description: [Headline summary of task contents]
Acceptance Criteria Covered: [Criterion IDs from the work plan traceability table, e.g. AC-1, AC-3. Write `infrastructure` for a task that covers none by design.]

## Change Request

(Quick-path tasks only — written by `implement-request` from the user's request. Omit for tasks decomposed from a work plan.)

- Request: [the user's request, verbatim]
- Observed: [for fixes — the current behavior]
- Expected: [for fixes — the required behavior]
- Reproduction: [for fixes — steps or command that demonstrate the problem]
- Acceptance: [one or two plain-language checks that prove the change is complete; these are the task's completion criteria]

## Implementation Content

[What this task will achieve. Reference dependency deliverables if applicable.]

## Target Files

The write set — the only files the executor may modify.

- [ ] [Implementation file path]
- [ ] [Test file path]

## Investigation Targets

The read set — files to read before implementing (file path, with optional search hint). Include only files that provide context critical to this task.

- [e.g., src/orders/checkout.py (processOrder — the function being changed)]

## Task Dependencies

(Tasks that must complete before this task can start. Omit if none.)

| Task ID | Title | Dependency Type | Deliverable Consumed |
| --- | --- | --- | --- |
| TASK-[0-9]{3} | [Dependency task title] | [blocks / informs] | [Artifact, contract, or output this task relies on] |

## Remediation Context

(Remediation tasks only — `TASK-*-REMEDIATION.md` files authored by reviewer agents. Omit otherwise.)

- Source: [validation-runner | code-reviewer | security-reviewer | acceptance-validator]
- Finding / failing command: [exact command or finding reference]
- Evidence: [failure output excerpt or finding evidence, with file and line]
- Verification: [command or check that must pass for this task to complete]

For remediation without a testable behavior change (lint, format, build fixes), replace the TDD cycle below with: reproduce the failure, apply the fix, re-run the Verification command until it passes.

## Implementation Steps (TDD: Red-Green-Refactor)

### 1. Red Phase

- [ ] Read all Investigation Targets
- [ ] Review dependency deliverables (if any)
- [ ] Write failing tests covering the completion criteria and the edge cases named in the work plan or Change Request
- [ ] Run tests and confirm failure

### 2. Green Phase

- [ ] Add minimal implementation to pass tests
- [ ] Run only added tests and confirm they pass

### 3. Refactor Phase

- [ ] Improve code (maintain passing tests)
- [ ] Confirm added tests still pass

## Completion Criteria

- [ ] [Task-specific behavioral criterion, e.g. "POST /users/new returns 409 for duplicate username"]
- [ ] All added tests pass
- [ ] (Remediation tasks only) Verification command from Remediation Context passes

## Scope Boundary

[Files or behaviors that must remain unchanged — path and reason. Omit if none.]
