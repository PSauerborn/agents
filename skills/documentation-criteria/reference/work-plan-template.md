# Work Plan: [Spec Title]

Work Plan ID: WP-[0-9]{3}
Created Date: YYYY-MM-DD
Type: feature|fix|refactor
Spec: [path to the spec this plan implements]
Spec Review: [path to SPEC-*-REVIEW.md, if one exists — its Clarifications section is binding]
Design: [path to the selected design option document, or None]

## Objective

[Why this change is necessary and what it must achieve — two or three sentences.]

## Design-to-Plan Traceability

Every acceptance criterion in the spec appears exactly once — both mapped scenarios
and Additional Acceptance Criteria (spec section 5.1). `task-decomposer` verifies
every row is covered by a task; `acceptance-validator` verifies the changeset
against this table.

| Criterion ID | Requirement | Acceptance Criterion (from spec) | Satisfied By |
| --- | --- | --- | --- |
| AC-1 | REQ-2 | [mapped scenario name] | Task 2, Task 3 |
| AC-2 | REQ-1 | [additional criterion] | Task 2 |

## Implementation Phases

### Phase 1: [Value Unit Name] (Estimated tasks: X)

**Purpose**: [First vertical slice — proves the approach works]
**Verification**: [The check that demonstrates this phase is complete]

#### Tasks

Identification level only — coverage and dependencies, no per-task implementation
detail (that belongs to `task-decomposer`):

- Task 1: [What it covers, e.g. "User creation endpoint (POST /users/new) including
  duplicate-username handling (409)". Depends on: none]
- Task 2: [Coverage description. Depends on: Task 1]

#### Phase Completion Criteria

- [ ] [Functional criterion only]

## Verification Strategy

[How progress is verified during implementation: the early verification point for
Phase 1, per-phase checks, and which tests or commands demonstrate each phase's
completion criteria. Whole-changeset validation runs in the pipeline review stage.]

## Failure Modes

[Known failure modes, edge cases, and technical risks the implementation must
handle, each with the concrete mitigation the code must contain — checklist form.]

- [ ] [e.g. duplicate username on concurrent requests → unique constraint, loser gets 409]

## Reference Contracts

[Contract values downstream agents need: API shapes, status codes, schemas, enum
values, integration points and their contracts.]

## Completion Criteria

- [ ] All phases completed
- [ ] Every acceptance criterion in the traceability table satisfied
- [ ] Documentation task completed, if the spec changes setup, usage, configuration, or a public API

Full-suite validation, correctness, standards, and security checks are performed
by the pipeline review stage — do not restate them as plan tasks.
