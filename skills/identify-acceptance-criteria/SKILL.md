---
name: identify-acceptance-criteria
description: Conventions for identifying acceptance criteria from specs and tracing them to tagged Gherkin scenarios. Use when reviewing or running acceptance tests, or when linking acceptance criteria to requirements.
---

Acceptance criteria define the observable behavior that proves a spec is implemented. They are expressed directly as tagged Gherkin scenarios in the acceptance test suite: each spec (`docs/specs/SPEC-NNN.md`, structured per the canonical template at `skills/init-spec/reference/spec-template.md`) declares its tag in its Acceptance Criteria section, and the scenarios carrying that tag are the spec's acceptance criteria. The spec and the scenarios are both user inputs: agents read, run, and report against them, and never create, modify, or remove them — every change to either is the user's alone. Follow the conventions below.

## Identifying Criteria from a Spec

- The scenarios tagged with the spec's ID are the authoritative criteria. The spec's Acceptance Criteria section declares the tag and maps each `REQ-*` requirement to the scenarios that verify it.
- Each `REQ-*` maps to zero or more scenarios — a single requirement may need several (happy path, rejection, edge cases from the spec's Edge Cases and Error Handling section). Some requirements may not have any mapped scenarios.

## Locating the Acceptance Test Suite

The location and runner of the acceptance test suite (godog, behave, cucumber, …) are project-defined — discover them from the project's own configuration and existing feature files. The default location for feature files is `acceptance/features/`, but this may vary on a project-by-project basis. Each spec's Acceptance Criteria section states how to run its scenarios in isolation (e.g. `godog --tags='@spec-002'`).

If the project has no established acceptance test suite, escalate to the user rather than inventing a location.

## Traceability: Spec-Tagging Convention

Feature files are organized by capability, not by spec, and are cross-cutting by design: one file may hold scenarios belonging to different specs. The tag, not the file, is the unit of ownership. Every scenario carries tags that map it back to its source requirements:

- **`@spec-NNN`** (required, per scenario): the lowercased spec ID, matching the spec filename — a scenario verifying `SPEC-002.md` is tagged `@spec-002`. A scenario verifying criteria from several specs carries one tag per spec.
- **Component tag** (required): the surface the scenario exercises, e.g. `@api`, `@ui`. Placed alongside `@spec-NNN`, or once at the `Feature:` level when it applies to every scenario in the file.
- **Domain tag** (optional, `Feature:` level): the functional domain, e.g. `@contacts`, for selective runs.

If implementing a spec would break an existing scenario, that is a conflict between user inputs — report it and let the user resolve it by editing the affected specs or scenarios themselves; never touch a scenario to make it pass.

```gherkin
@contacts
Feature: Contacts
    Visitors can reach me through the site, and I can review what they sent.

    @api @spec-002
    Scenario: A new visitor submits a contact message
        ...

    @ui @spec-002 @spec-003
    Scenario: Submitted messages appear in the admin inbox
        ...
```

## Traceability: Requirement-to-Scenario Mapping

Tags trace a scenario back to its specs; the mapping table in each spec's Acceptance Criteria section traces the opposite direction, listing per requirement the scenarios that verify it:

| Criterion ID | Requirement | Scenario (tagged `@spec-002`) |
| ------------ | ----------- | ----------------------------- |
| AC-1         | REQ-1       | A new visitor submits a contact message |

- Each row carries a stable `AC-*` criterion ID — the identifier work plans, task files, and PRs use to reference the criterion.
- Scenario names are the join key into the suite: each `Scenario:` line must match its table entry verbatim, and scenario names must be unique among a spec's scenarios.
- A table row with no matching tagged scenario in the suite, a tagged scenario with no table row in that spec, or a naming mismatch is a defect in the user's inputs — report it for the user to resolve. A `REQ-*` absent from the table is not a defect — not every requirement has a mapped scenario. Never edit the spec or the suite to reconcile them.

Together these complete the chain in both directions: given a scenario, each `@spec-NNN` tag locates a spec whose mapping table ties the scenario to its `REQ-*` requirements; given a requirement, the mapping table names the exact scenarios that verify it.

## Additional Acceptance Criteria

Not every criterion is expressible in Gherkin. A spec's Additional Acceptance Criteria section (5.1) holds spec-specific criteria that cannot be cleanly expressed as scenarios — performance targets, migration outcomes, operational conditions — each carrying an `AC-*` ID that continues the mapping table's sequence and referencing the `REQ-*` requirement it verifies. They are user inputs like the rest of the spec: never added, edited, or removed by an agent. They are verified through code inspection or targeted commands rather than the acceptance suite, and they belong in work-plan traceability alongside mapped scenarios. A requirement may be covered by scenarios, additional criteria, both, or neither.
