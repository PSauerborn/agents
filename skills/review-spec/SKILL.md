---
name: review-spec
argument-hint: spec-path
description: Review a spec for clarity, completeness, scope, consistency, and acceptance-criteria traceability, then resolve its ambiguities with the user through a bounded set of questions (five per feature in the spec) whose answers are recorded in the review document.
---

Specs are documents that outline one or more features to be implemented together. A spec may cover several features; the work plan maps each to its own phase. Specs are converted into work plans by agents, so they are reviewed for downstream agent consumption as much as for human readers.

Specs are created from the canonical template at `${CLAUDE_PLUGIN_ROOT}/skills/init-spec/reference/spec-template.md` (via `init-spec`). Expect that structure — numbered sections, `REQ-*` requirements, acceptance criteria as `@spec-NNN`-tagged Gherkin scenarios with an `AC-*`-keyed mapping table, optional Additional Acceptance Criteria — and flag deviations from it.

The spec and the acceptance test suite are user inputs. Never edit either. The review document is the only file this skill writes.

## Step 1: Review

Review the spec at $1 against:

1. **Conciseness** — no unnecessary verbosity while still providing all necessary information.
2. **Clarity** — easy to read and optimized for downstream agent consumption.
3. **Completeness** — contracts between components, interfaces, and edge-case handling are specified.
4. **Scope** — In Scope and Out of Scope are explicit, every requirement falls inside the stated scope, and the features in the spec are related enough to be planned and reviewed as one changeset. Multiple features in one spec is not a finding; an unstated boundary is.
5. **Consistency** — the spec does not contradict itself.
6. **Traceability** — the `REQ` → `AC` → scenario chain is intact: every `AC-*` references a defined `REQ-*`; `AC-*` IDs are unique with no gaps; where the project has an acceptance suite (located per `identify-acceptance-criteria`), every mapped scenario exists verbatim with the spec's tag, and every tagged scenario appears in the table.

Write the review to `docs/specs/SPEC-{ID}-REVIEW.md` (next to the spec). Include specific examples from the spec for each issue and a checklist of suggested fixes. If a review already exists, update it: check off addressed items, add new ones, never delete existing items.

## Step 2: Clarify

From the review, select the ambiguities whose resolution changes what gets built — scope boundaries, behavior under edge cases, contract values, data or state rules, completion signals. Skip stylistic issues and anything the work plan can decide.

A `[NEEDS CLARIFICATION: ...]` marker in the spec (left by `spec-writer` when drafting from a request) is always asked, first, and does not count toward the cap below. Its recorded answer resolves it; the marker stays in the spec as a pointer to the review document.

Count the features the spec covers (the distinct capabilities in its In Scope section; a spec with one capability counts as one). Ask **at most five further questions per feature**, **one at a time**, via **AskUserQuestion**:

- Each question is a complete interrogative ending in `?`, answerable from its own text without reading the spec.
- Each offers 2-4 mutually exclusive options with the recommended option first, plus one sentence on why it matters.
- Stop early when the remaining ambiguities are not material, or when the user says so.

After each answer, append it to the review document under `## Clarifications` with a dated sub-heading:

```md
## Clarifications

### Session 2026-10-05

- Q: Should duplicate usernames be rejected case-insensitively? → A: Yes, case-insensitive.
- Q: ... → A: ...
```

Recorded answers are binding for every downstream agent; `work-planner` reads them via `specReviewPath`. On a re-run, flag any recorded answer the current spec text contradicts — the user may have edited the spec since — rather than silently keeping either.

## Step 3: Report

Summarize the material findings that remain (contradictions, scope problems, broken traceability) separately from the resolved clarifications, so the orchestrator can decide whether the spec needs correction before planning.

## Examples

A bad spec has no structure and gives little context on how logic or edge cases should be handled:

````md
<!-- BAD: unstructured, unscoped, no edge cases -->
Implement a REST API with the following endpoints:
- POST /orders/new
- GET /orders/all
- POST /users/new - create a new user
- GET /users/me - get the current user profile
````

A good spec follows the template, states its scope boundaries explicitly, and specifies contracts and edge cases:

````md
# SPEC-004: Users Router

Spec ID: SPEC-004
Spec Date: 2026-01-15

## 1. Spec Statement

As an API consumer I want user management endpoints So that clients can create users and fetch their own profile.

## 3. Scope Definitions

### 3.1 In Scope

 - `POST /users/new` — create a new user
 - `GET /users/me` — get the current user profile

### 3.2 Out of Scope

 - Authentication and session handling (provided by the gateway)

## 4. Requirements

 - **REQ-1**: `POST /users/new` MUST create a user from `{"username": "j.doe", "name": "John Doe", "age": 25}`.
 - **REQ-2**: Creating a user with an existing username MUST return `409`.

## 5. Acceptance Criteria

Scenarios verifying this spec are tagged `@spec-004` and can be run in isolation using `godog --tags='@spec-004'`.

| Criterion ID | Requirement | Scenario (tagged `@spec-004`) |
| ------------ | ----------- | ----------------------------- |
| AC-1         | REQ-1       | A new user is created from a valid payload |
| AC-2         | REQ-2       | Creating a user with a duplicate username is rejected |

## 7. Edge Cases and Error Handling

 - **Malformed payload**: `POST /users/new` returns `400` without persisting anything.
````
