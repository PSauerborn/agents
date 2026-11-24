---
name: draft-spec
argument-hint: [change request]
description: Draft a spec from a plain-language request, have the user approve it, then draft its acceptance criteria with the user via draft-spec-ac. Stops once the spec and its criteria are written; it never implements the spec.
disable-model-invocation: true
skills: subagents-orchestration-guide, documentation-criteria, coding-standards, identify-acceptance-criteria
---

You turn a request given in the user's own words into an approved spec with acceptance criteria. There is no spec to start from. The end state is `docs/specs/SPEC-NNN.md` and the acceptance suite updated with the user's approval — nothing more.

**You do not implement the spec.** Do not run `implement-spec`, plan, decompose, execute tasks, run reviewers, or commit. When the acceptance criteria are done, report and exit; implementation is a separate run the user starts.

## Protocol

1. **Delegate analysis and drafting through the Agent tool**, constructing prompts per the `subagents-orchestration-guide` and handling `blocked` responses per its procedure.
2. **Stop at every [STOP] marker** via **AskUserQuestion**.
3. **You write nothing yourself outside Step 4.** `spec-writer` writes the spec; in Step 4 you write only what `draft-spec-ac` permits — `.feature` files in the acceptance suite and section 5 of the drafted spec — and only after the user approves each write.

Permitted tools: Agent, AskUserQuestion, TaskCreate / TaskUpdate, Bash (read-only repo inspection, and the acceptance suite's dry-run or parse command in Step 4), Read, Write/Edit (Step 4 only, within the limits above).

## Workflow

### Step 1: Analyze

Invoke **requirements-analyzer** with `requirements` set to the user's request verbatim. Ask any returned `questions` via **AskUserQuestion**, one at a time, and re-run the analyzer if an answer changes the inputs. Keep the answers; Step 2 uses them.

### Step 2: Draft the Spec

Invoke **spec-writer** with `requirements` (the request verbatim) and `analysisSummary` (the analyzer's purpose, taskType, affectedFiles, investigationTargets, constraints, plus the user's answers from Step 1). It writes `docs/specs/SPEC-NNN.md` and returns the markers it could not resolve.

### Step 3: Approve the Draft

Present the spec path, its In Scope list, its requirements, and the `clarificationsNeeded` list via **AskUserQuestion** with options Approve / Modify / Cancel. **[STOP]** Tell the user they may edit the spec file directly before approving; it is theirs. On Modify with feedback, re-invoke **spec-writer** in update mode with the feedback as `updateContext` and re-present. On Cancel, report the spec path and exit.

Once approved, the drafted spec is a user input like any other: no agent edits it from here on, except section 5 through Step 4.

### Step 4: Draft the Acceptance Criteria

Read `${CLAUDE_PLUGIN_ROOT}/skills/draft-spec-ac/SKILL.md` and execute it from Step 1 with the approved spec path, exactly as written. It inventories the existing acceptance suite, plans the feature files with the user, and presents every new or updated scenario and section 5.1 entry for approval before writing it. Existing scenarios whose behaviour this spec changes are updated there, with the user, not left stale.

`spec-writer` has already written section 5.1 entries; new `AC-*` IDs continue that sequence. A requirement still carrying a `[NEEDS CLARIFICATION]` marker is presented with the marker shown, and the user may Skip it until the marker is resolved.

### Step 5: Report and Exit

Report:

- The spec path.
- The feature files created or updated, and the number of criteria added and updated.
- Requirements that ended with no criteria, and any `[NEEDS CLARIFICATION]` markers still open.
- The next step, if the user wants to build it: `/implement-spec docs/specs/SPEC-NNN.md`, whose review gate resolves the open markers.

Then stop. Do not start implementation.

### Additional Instructions

$1
