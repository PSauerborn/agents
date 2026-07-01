---
name: review-spec
argument-hint: spec-path
description: Review a feature spec for clarity, conciseness, completeness, scope, and inconsistencies.
---

Specs are documents that outline a set of features that need to be implemented. Specs are converted into implementation plans by agent. Implementations plans are then actioned by agents.

Review the spec at $1 and produce a report on the following criteria:

1. Conciseness - specs should be concise, avoiding unnecessary verbosity while still providing all necessary information.
2. Clarity - specs should be easy to read, and should be optimized for downstream agent consumption.
3. Completeness - specs should be comprehensive, and include details on contracts between components, interfaces, and how various edge cases should be handled.
4. Scope - specs should have a narrow scope, and should focus on a single deliverable.
5. Inconsistencies - specs should be self-consistent, and should not contradict themselves.

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
<!-- GOOD: spec structure provides context for agents -->
# Overview

<!-- GOOD: spec provides overview for context, not just instructions -->
The core REST API manages the core entities present in the PostgreSQL database, including users, orders, and payments.

<!-- GOOD:: spec is scoped to a single, functional deliverable -->
Implement a new users router that includes the following endpoints:

- POST /users/new - create a new user
- GET /users/me - get the current user profile

## Endpoints

### POST /users/new

Create a new user using the following payload:

```json
{
    "username": "j.doe",
    "name": "John Doe",
    "age": 25
}
```
<!-- GOOD: spec provides detail on how edge cases should be handled. -->
If a user with the provided username already exists, return a `409` error.

### GET /users/me

Fetch the user profile from PostgreSQL using the `X-Authenticated-UserId` header provided in the request. If a user does not exist, return a `404` error.

````
