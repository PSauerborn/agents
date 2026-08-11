# Work Plan: [Feature Name] Implementation

Work Plan ID: WP-[0-9]{3}
Created Date: YYYY-MM-DD
Type: feature|fix|refactor
Spec: [path to spec document this plan implements]
Scale: Small|Medium|Large
Description: [Headline summary of work plan]
Estimated Impact: X files

## Objective

[Why this change is necessary, what problem it solves]

## Background

[Current state and why changes are needed]

## Design-to-Plan Traceability

Every acceptance criterion in the spec appears exactly once — both mapped scenarios
and Additional Acceptance Criteria (spec section 5.1). A requirement may span
multiple rows; requirements with no criteria are omitted. `acceptance-validator`
verifies the implemented changeset against this table.

| Criterion ID | Requirement | Acceptance Criterion (from spec) | Satisfied By |
| --- | --- | --- | --- |
| AC-1 | REQ-2 | [mapped scenario name, e.g. "Creating a user with a duplicate username is rejected"] | Task 2, Task 3 |
| AC-2 | REQ-1 | [additional criterion, e.g. "User creation completes within 200ms at p95"] | Task 2 |

## Implementation Phases

### Phase 1: [Value Unit 1 Name] (Estimated tasks: X)

**Purpose**: [First vertical slice — proves approach works]
**Verification**: [From Verification Strategy: early verification point]

#### Tasks

Identification level only — coverage and dependencies, no per-task implementation
detail (that belongs to `task-decomposer`):

- Task 1: [What it covers, e.g. "User creation endpoint (POST /users/new) including
  duplicate-username handling (409)". Depends on: none]
- Task 2: [Coverage description. Depends on: Task 1]

#### Phase Completion Criteria

- [ ] [Functional criteria only — e.g. "early verification point passed"]

### Phase 2: [Value Unit 2 Name] (Estimated tasks: X)

**Purpose**: [Subsequent value unit]
**Verification**: [From Verification Strategy]

#### Tasks

- Task 3: [Coverage description. Depends on: Task 2]

#### Phase Completion Criteria

- [ ] [Functional criteria]

## Verification Strategy

[How progress is verified during implementation: the early verification point for
Phase 1, per-phase checks, and which tests or commands demonstrate each phase's
completion criteria. Whole-changeset validation runs in the pipeline review stage.]

## Failure Modes

[Known failure modes and edge cases the implementation must handle — checklist form.]

- [ ] [e.g. duplicate username on concurrent requests]

## Reference Contracts

[Contract values downstream agents need: API shapes, status codes, schemas, enum
values, integration points and their contracts.]

## Review Scope

[Focus areas for reviewers and scope boundaries — files or behaviors expected to
remain unchanged, and why.]

## Completion Criteria

- [ ] All phases completed
- [ ] Every acceptance criterion in the traceability table satisfied
- [ ] Necessary documentation updated

Full-suite validation, coding standards, correctness, and security checks are
performed by the pipeline review stage (`validation-runner`, `quality-controller`,
`code-reviewer`, `security-reviewer`) — do not restate them as plan tasks.

## Notes

[Special notes, reference information, important points, etc.]
