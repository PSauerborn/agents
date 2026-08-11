# [Spec ID]: [Feature Name]

Spec ID: [Spec ID]
Spec Date: YYYY-MM-DD

(All bracketed tokens, parenthetical guidance, and example entries in this template are placeholders — replace or delete them before the spec is reviewed.)

## 1. Spec Statement

As a ... I want to ... So that ...

## 2. Context and Background

(Include important context about **why** the feature is required, what it accomplishes)

## 3. Scope Definitions

### 3.1 In Scope

- Implement feature x

### 3.2 Out of Scope

- Ignore y

## 4. Requirements

(Enumerate **what** the system must do, independent of implementation. Use stable identifiers so requirements can be referenced from acceptance criteria and PRs. Keep each requirement atomic and testable — one obligation per line.)

- **REQ-1**: `POST /users/new` MUST create a user from the provided payload and return `201` with the created user.
- **REQ-2**: Usernames MUST be unique; creating a user with an existing username MUST return `409`.
- **REQ-3**: `GET /users/me` SHOULD resolve the current user from the `X-Authenticated-UserId` header and return `404` when no matching user exists.

## 5. Acceptance Criteria

Acceptance criteria live in `acceptance/features/`, organized by capability rather than by spec. Scenarios verifying this spec are tagged `@[lowercase(Spec ID)]` and can be run in isolation using `godog --tags='@[lowercase(Spec ID)]'`.

Feature files are cross-cutting by design — feature files contain scenarios tagged with other spec IDs. Scenarios can also be tagged with multiple spec IDs. The tag, not the file, is the unit of ownership. Only scenarios tagged with `@[lowercase(Spec ID)]` should be considered included as acceptance criteria for this spec.

(Map each requirement to the Gherkin acceptance-test scenarios that verify it, per the `identify-acceptance-criteria` skill. Each criterion carries a stable `AC-*` ID so it can be referenced from work plans, task files, and PRs. Maintained by the spec author and read-only for agents — scenarios in the suite are named to match these entries verbatim.)

| Criterion ID | Requirement | Scenario (tagged `@spec-NNN`) |
| ------------ | ----------- | ----------------------------- |
| AC-1         | REQ-1       | A new user is created from a valid payload |

### 5.1 Additional Acceptance Criteria

(Spec-specific criteria that cannot be cleanly expressed as a Gherkin scenario — performance targets, data-migration outcomes, operational or non-functional conditions. Each carries an `AC-*` ID continuing the mapping table's sequence and references the requirement it verifies. Like the mapping table, maintained by the spec author and read-only for agents. Write `None` if not applicable.)

- **AC-2** (REQ-1): User creation completes within 200ms at p95 under nominal load.

## 6. Contracts and Constraints

(Specify the **interfaces and invariants** the implementation must honor: API request/response shapes, event schemas, data-model changes, and non-functional constraints such as performance, security, or backwards-compatibility. Link to the authoritative source where one exists.)

- **API Contract**: `POST /users/new` accepts `{"username": string, "name": string, "age": integer}` and returns the created user (see docs/openapi.yaml).
- **Data Contract**: user records conform to the schema defined in SPEC-003 and are written exactly once per creation request.
- **Constraint**: responses MUST remain backwards-compatible with existing API clients.

## 7. Edge Cases and Error Handling

(Call out **abnormal inputs, boundary conditions, and failure modes** and the expected behavior for each. Cover missing/malformed data, concurrency, partial failures, and retries. State whether the system fails fast, degrades gracefully, or retries.)

- **Malformed payload**: `POST /users/new` returns `400` without persisting anything.
- **Duplicate username race**: concurrent creates with the same username result in exactly one user; the loser receives `409`.
- **Database unavailable**: the API fails fast with `503`; no partial writes are left behind.

## 8. Infrastructure Requirements

(Document required infrastructure changes. Write `None` if not applicable.)

- **Updated IAM permissions**: ensure that IAM permissions are updated to include S3 write

## 9. External Resources

(A list of resources that should be used when implementing this spec. Write `None` if not applicable.)

| Filepath | Description | When to use |
|----------|-------------|-------------|
| docs/openapi.yaml | REST API definition and documentation in OpenAPI format | Use when writing code that interacts with the API |
