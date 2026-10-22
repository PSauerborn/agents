---
name: draft-spec-ac
argument-hint: spec-path
description: Draft the acceptance criteria for a spec as tagged Gherkin scenarios, presenting each one to the user for approval before it is written. Use when a spec has requirements but no, or incomplete, acceptance criteria in its section 5.
disable-model-invocation: true
skills: coding-standards, identify-acceptance-criteria, documentation-criteria
---

# draft-spec-ac

This skill drafts acceptance criteria for a spec as Gherkin scenarios in feature files, and records them in the spec's acceptance criteria mapping table (section 5). Criteria are presented to the user one at a time; the user approves each one, or requests changes, before it is written and before the next one is drafted.

The conventions for tags, the mapping table, `AC-*` IDs, and section 5.1 are defined in the `identify-acceptance-criteria` skill and are not repeated here. That skill also states that scenarios and the mapping table are user inputs that agents never modify. This skill is the one sanctioned exception: it acts on the user's behalf, and every write it makes has been approved by the user first.

Permitted writes: `.feature` files under the project's acceptance suite, and section 5 of the spec being processed. Nothing else is modified.

**Acceptance criteria are live documents; specs are historic.** The suite describes the current behaviour of the system, and a scenario belongs to the most recent spec that touched it. A scenario does not need to keep conforming to an older spec once a newer one has changed the behaviour it verifies.

## Step 1: Load the Spec

The input is the ID or path of a spec in `docs/specs/`. Read it fully, including sections 5 and 7 (edge cases are a frequent source of scenarios). If the spec cannot be found, or more than one spec matches the ID, ask the user which to use. Do not draft anything until the correct spec is loaded.

## Step 2: Read the Acceptance Standards

Before planning or drafting any criterion, load the acceptance testing standard through the `coding-standards` skill. It is indexed under the `testing` and `acceptance-testing` topics (`testing/ACCEPTANCE.md`). Read the whole document, and read every example file cited for the statements in its **Writing Acceptance Criteria** section.

The standard, not this skill, governs how a scenario is written: language, what may be asserted on, when to use a `Scenario Outline`, independence between scenarios, and the scope of a single scenario. Apply its MUST statements without exception and its SHOULD statements unless the user explicitly asks otherwise. If `CODING_STANDARDS_DIR` is unset or the standard is missing, stop and report that to the user; do not draft criteria from memory.

## Step 3: Inventory Existing Coverage

Locate the acceptance suite per `identify-acceptance-criteria`. Before drafting anything new, list the scenarios already tagged with this spec's ID, and read the feature files for the functional domains the spec touches. This prevents drafting a scenario that already exists and identifies which existing scenarios the spec changes.

## Step 4: Plan the Feature Files

Decide which feature files the criteria belong in, following the standard's rule of one functional domain per file. Present the plan to the user via **AskUserQuestion** with options Approve / Modify / Cancel, stating for each file whether it will be created or updated. **[STOP]**

Once approved, create each new file with its `Feature:` declaration and the one-line value statement the standard requires. If an existing file's `Feature:` declaration needs to change, present that change for approval before making it.

## Step 5: Draft Criteria, One Requirement at a Time

Work through the spec's `REQ-*` entries in order. For each requirement, using the inventory from Step 3, propose one of:

- **New scenario(s)**: a requirement may need several (happy path, rejection, edge cases from section 7). Each is presented and approved separately.
- **Update to an existing scenario**: when the spec changes behaviour an existing scenario verifies. Present the full updated scenario alongside the current one.
- **Additional criterion for section 5.1**: when the requirement has an outcome that cannot be expressed as a scenario under the standard (performance targets, migration outcomes, operational conditions). Present the proposed 5.1 entry for approval like any other criterion.
- **None**: a requirement with no observable outcome of its own. State this and move on; not every requirement has criteria.

Present each proposal via **AskUserQuestion** with options Approve / Modify / Skip. **[STOP]** On Modify, apply the user's changes and re-present.

**The user must see the whole criterion before approving it.** Print the complete scenario, including its tags, the `Scenario:` or `Scenario Outline:` line, every step, and any `Examples` table, as a fenced `gherkin` block in the message body *before* calling AskUserQuestion. Do the same for a 5.1 entry and for the mapping table row that will be added. Never put the criterion only in the question text or an option's preview: those surfaces truncate long content and hide steps and tables, and an approval given against a partial view is not an approval. For an update, print the current scenario and the proposed scenario in full, as two separate blocks, not a diff.

On approval, write it immediately so that an interrupted session loses nothing:

- Write the scenario to its feature file, tagged per `identify-acceptance-criteria` with the spec tag and a component tag (or rely on a Feature-level component tag where the standard allows it).
- Add the mapping table row, or the 5.1 entry, to the spec. `AC-*` IDs continue the spec's existing sequence; 5.1 entries continue the same sequence as the table.
- The scenario name in the table must match the `Scenario:` line verbatim.

When updating an existing scenario owned by an earlier spec, replace the earlier spec's tag with this spec's tag and leave the earlier spec's mapping table as it is. The earlier spec is historic and its table is not maintained. Tags for other specs that the scenario still verifies concurrently are kept.

## Step 6: Final Consistency Check

When all requirements have been processed, check and report:

- Every scenario tagged with this spec's ID has a row in the table, and every row names a scenario that exists, verbatim.
- `AC-*` IDs are unique and contiguous across the table and section 5.1.
- Every new or updated feature file passes a parse (run the suite's dry-run or parse command where one exists, e.g. `godog --dry-run`, `behave --dry-run`). Fix any parse failure before finishing.

Report the files written, the number of criteria added and updated, and the requirements that ended with no criteria.
