---
name: review-spec
argument-hint: spec-path
description: Review a feature spec for clarity, conciseness, completeness, scope, and inconsistencies.
---

Specs are documents that outline a set of features that need to be implemented. Specs are converted into implementation plans by agent. Implementations plans are then actioned by agents.

Specs are normally created from the canonical template at `skills/init-spec/reference/spec-template.md` (via the `init-spec` skill) — expect that structure (numbered sections, `REQ-*` requirements, `AC-*` acceptance criteria) and flag deviations from it.

Review the spec at $1 and produce a report on the following criteria:

1. Conciseness - specs should be concise, avoiding unnecessary verbosity while still providing all necessary information.
2. Clarity - specs should be easy to read, and should be optimized for downstream agent consumption.
3. Completeness - specs should be comprehensive, and include details on contracts between components, interfaces, and how various edge cases should be handled.
4. Scope - specs should have a narrow scope, and should focus on a single deliverable.
5. Inconsistencies - specs should be self-consistent, and should not contradict themselves.

Write a MD report to summarize your findings based on the criteria above at `SPEC-*-REVIEW.md`. Include specific examples from the spec to support your evaluation. The report must contain a checklist of suggested fixes for each issue identified.

If a spec review already exists, update it with the new findings rather than creating a new file. Check off any items in the checklist that have been addressed, and add new items for any newly identified issues. Do not delete any existing items from the checklist, even if they have been addressed.

## Examples

The following is an example of a bad spec. It has no inherent structure, and gives little/no context on how the logic should be implemented, and how edge cases should be handled.

````md
<!-- BAD: spec is not structured, provides little context -->
Implement a REST API with the following endpoints:

<!-- BAD: spec is not clearly scoped -->
- POST /orders/new
- GET /orders/all
- POST /users/new - create a new user
- GET /users/me - get the current user profile
````

Conversely, the following is an example of a good spec. It is clearly structured, contains details on how features should be implemented and provides useful context on how to handle edge cases. It is well scoped to a single, coherent deliverable rather than implementing multiple components at once.

````md
<!-- GOOD: spec follows the canonical template structure -->
# SPEC-004: Users Router

Spec ID: SPEC-004
Spec Date: 2026-01-15

## 1. Spec Statement

As an API consumer I want user management endpoints So that clients can create users and fetch their own profile.

## 2. Context and Background

<!-- GOOD: spec provides overview for context, not just instructions -->
The core REST API manages the core entities present in the PostgreSQL database, including users, orders, and payments. User management endpoints are currently missing.

## 3. Scope Definitions

### 3.1 In Scope

<!-- GOOD: spec is scoped to a single, functional deliverable -->
 - `POST /users/new` — create a new user
 - `GET /users/me` — get the current user profile

### 3.2 Out of Scope

 - Authentication and session handling (provided by the gateway)

## 4. Requirements

<!-- GOOD: stable REQ IDs make requirements referenceable and testable -->
 - **REQ-1**: `POST /users/new` MUST create a user from `{"username": "j.doe", "name": "John Doe", "age": 25}`.
 - **REQ-2**: Creating a user with an existing username MUST return `409`.
 - **REQ-3**: `GET /users/me` MUST resolve the user from the `X-Authenticated-UserId` header and return `404` when no user exists.

## 5. Acceptance Criteria

<!-- GOOD: each criterion is observable and maps to a requirement -->
 - **AC-1** (REQ-1): Given a valid payload, when `POST /users/new` is called, then a `201` is returned and the user is persisted.
 - **AC-2** (REQ-2): Given user `j.doe` exists, when `POST /users/new` is called with username `j.doe`, then a `409` is returned.
 - **AC-3** (REQ-3): Given no matching user, when `GET /users/me` is called, then a `404` is returned.

## 6. Contracts and Constraints

 - **API Contract**: request and response shapes are defined in docs/openapi.yaml.

## 7. Edge Cases and Error Handling

<!-- GOOD: spec provides detail on how edge cases should be handled -->
 - **Malformed payload**: `POST /users/new` returns `400` without persisting anything.

<!-- Sections 8 and 9 omitted for brevity — write `None` when not applicable -->
````
