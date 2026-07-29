# Task: [Task Name]

Work Plan ID: WP-[0-9]{3}
Task ID: TASK-[0-9]{3}
Created Date: YYYY-MM-DD
Description: [Headline summary of task contents]
Acceptance Criteria Covered: [Criterion IDs from the work plan traceability table, e.g. AC-1, AC-3. Omit if none.]

## Implementation Content

[What this task will achieve]
*Reference dependency deliverables if applicable

## Target Files

- [ ] [Implementation file path]
- [ ] [Test file path]

## Investigation Targets

Files to read before starting implementation (file path, with optional search hint):

- [e.g., src/orders/checkout (processOrder function) — determined during task decomposition based on task nature]

## Change Category

(Include this field only when the task is a bug fix, regression, state-change, or boundary-change — populated during task decomposition. Omit otherwise.)

`Change Category: <one or more of bug-fix, regression, state-change, boundary-change — comma-separated>`

When present, the implementation sweeps the cases sharing the same path, contract, persisted state, or external boundary for the same class of defect (see Implementation Steps Red Phase).

## Investigation Notes

(Implementation observations are appended here before implementation begins.)

## Task Dependencies

(Tasks that must complete before this task can start. Omit rows if none.)

| Task ID | Title | Dependency Type | Deliverable Consumed |
| --- | --- | --- | --- |
| TASK-[0-9]{3} | [Dependency task title] | [blocks / informs] | [Artifact, contract, or output this task relies on] |

## Remediation Context

(Include this section only for remediation tasks — TASK-*-REMEDIATION files authored by reviewer agents. Omit otherwise.)

- Source: [validation-runner | quality-controller | code-reviewer | security-reviewer | risk-reviewer]
- Finding / failing command: [exact command or finding reference]
- Evidence: [failure output excerpt or finding evidence]
- Verification: [command or check that must pass for this task to complete]

For remediation without a testable behavior change (e.g. lint, format, build fixes), replace the TDD cycle below with: reproduce the failure, apply the fix, re-run the Verification command until it passes.

## Implementation Steps (TDD: Red-Green-Refactor)

### 1. Red Phase

- [ ] Read all Investigation Targets and record key observations
- [ ] (When Change Category is set) Sweep the adjacent cases sharing the same path/contract/state/boundary for the same class of defect; fold any found within scope into the failing tests
- [ ] Review dependency deliverables (if any)
- [ ] Verify/create contract definitions
- [ ] Write failing tests
- [ ] Run tests and confirm failure

### 2. Green Phase

- [ ] Add minimal implementation to pass tests
- [ ] Run only added tests and confirm they pass

### 3. Refactor Phase

- [ ] Improve code (maintain passing tests)
- [ ] Confirm added tests still pass

## Completion Criteria

- [ ] [Task-specific behavioral criterion, e.g. "POST /users/new returns 409 for duplicate username" — instantiated by task-decomposer]
- [ ] All added tests pass
- [ ] (Remediation tasks only) Verification command from Remediation Context passes

## Notes

- Impact scope: [Areas where changes may propagate]
- Scope boundary: [Files to preserve unchanged — path and reason]
