# Work Plan: Pipeline Downsize and Small-Change Path

Work Plan ID: WP-001
Created Date: 2026-10-05
Type: refactor
Spec: none (plan authored directly from the review discussion on `feature/downsize`)
Scale: Large
Description: Cut four agents and two flows, standardise the remaining conventions, add a spec-free path for small changes, and adopt the handful of spec-kit ideas that fit.
Estimated Impact: ~45 files (13 agents, 12 skills, 8 templates, 13 schemas, README, plugin.json)

## Objective

Reduce the pipeline to the stages that a downstream consumer actually depends on, make every remaining agent, schema, template, and path follow one convention, and give single-function changes an entry point that does not require a spec.

## Background

The current pipeline has 13 agents, 3 procedure flows, 8 artifact templates, and 5 approval options. Review found that:

- The risk agents exist only to differentiate the large flow, and their templates contradict each other (TR-/SR- vs RISK-NNN IDs).
- quality-controller and code-reviewer review the same diff; the quality report has no consumer.
- The documenter's changeset document duplicates `git diff` and the manifest.
- "Approve" and "Approve and enter autonomous mode" behave identically because the flows have no stops between them.
- Small changes still pay for spec review, requirements analysis, planning, approval, decomposition, and a manifest before PROC-001 saves them anything.
- Response field names, artifact paths, model/effort/tool grants, and agent listings have drifted across four places.

GitHub spec-kit was reviewed for ideas worth borrowing; the assessment is in the final section and the adopted items are Phase 4.

## Decisions (resolved 2026-10-05)

1. **Clarification answers are never written into the spec.** They are recorded in the spec review document (`SPEC-*-REVIEW.md`), which `review-spec` already owns and updates across runs, under a `## Clarifications` section with a dated sub-heading. `work-planner` reads that document so the answers reach the plan. The spec stays read-only for agents.
2. **Documenter is removed**, along with the changeset document. README/OpenAPI updates become a planned task when the spec touches them.
3. **The small path is a separate `implement-task` skill**, not a flag on `implement-spec`.
4. **Models stay pinned, deliberately.** Planning is done by `fable` (`work-planner`), coding by `opus` (`task-executor`), mechanical work by `sonnet` (`validation-runner`), everything else `inherit`. The authoring standards document this as the rule rather than listing it as drift.

## Implementation Phases

### Phase 1: Remove (Estimated tasks: 7)

**Purpose**: Delete the stages and mechanisms with no downstream consumer, so Phases 2 to 4 operate on a smaller surface.
**Verification**: `implement-spec` still loads; the orchestration guide references no deleted agent, schema, template, or path; `git grep` for each removed name returns only this plan.

#### Tasks

- Task 1: Delete `risk-analyzer` and `risk-reviewer` (agent files, schemas, risk-plan and risk-review templates, `docs/plans/risk/`, `TASK-RISK-REMEDIATION`). Move the risk plan's "technical risks" intent into the work plan template's Failure Modes section wording. Depends on: none.
- Task 2: Merge `quality-controller` into `code-reviewer`: add `coding-standards` to the reviewer's skills, add a `standards` finding category with `ruleId`, delete the QC agent, schema, quality report template, `docs/plans/quality/`, and `TASK-QC-REMEDIATION`. Depends on: none.
- Task 3: Delete `documenter`, its schema, the changeset template, and `docs/plans/changesets/`. Add one sentence to `work-planner` requiring a documentation task when the spec changes setup, usage, configuration, or a public API. Depends on: none.
- Task 4: Collapse `proc-001/002/003` into a single Workflow table inside `implement-spec/SKILL.md`, with one rule: Small scale skips the review and acceptance stages. Delete the procedure-loading step and the "Flow Overview" sections. Depends on: Tasks 1, 2, 3.
- Task 5: Remove autonomous execution mode from `plan-approvals` and `implement-spec`; reduce approval options to Approve / Modify / Reject. Fix the "Use PROACTIVELY" description. Depends on: none.
- Task 6: Trim `requirements-analyzer`: collapse `confidence`, `scopeDependencies`, and `questions` into one `questions` list; remove WebSearch; remove the unused "Mandatory / Not required / Conditionally mandatory" vocabulary. Depends on: none.
- Task 7: Remove the TaskCreate/TaskUpdate grant and ritual from `task-executor`; move `skills/marketing-content` out of the plugin (separate repo or `~/.claude/skills`). Depends on: none.

#### Phase Completion Criteria

- [x] 9 agents remain: requirements-analyzer, frontend-designer, work-planner, task-decomposer, task-executor, validation-runner, code-reviewer, security-reviewer, acceptance-validator.
- [x] One workflow table, no `reference/proc-*.md` files.
- [x] Three approval options; no mention of autonomous mode anywhere.

### Phase 2: Standardise (Estimated tasks: 6)

**Purpose**: One convention for every response field, path, grant, and listing, so the orchestrator treats every reviewer identically and agents stop inventing filler.
**Verification**: every reviewer schema has exactly `remediationRequired` and `remediationTaskPath`; every artifact-producing schema has `outputPath`; `grep -r 'Users/Pascal'` returns nothing.

#### Tasks

- Task 8: Standardise response schemas. Reviewers: `findings[]`, `remediationRequired`, `remediationTaskPath`. Producers: `outputPath`. Rename `planOutputPath`, `qcRemediationRequired`, `riskRemediationRequired` and friends. Update every agent's Final Verification wording to match. Depends on: Phase 1.
- Task 9: Flatten the artifact layout to one directory per work plan: `docs/plans/{workPlanId}/work-plan.md`, `tasks/TASK-NNN.md`, `manifest.md`, `designs/` (when the design gate ran). Update `documentation-criteria`, every agent that writes an artifact, the manifest template, and the `.gitignore` files. Note: the root `.gitignore` entry `PLAN.md` matches `plan.md` case-insensitively on macOS, so the file must not be named `plan.md`; remove that ignore entry as part of this task. Depends on: Phase 1.
- Task 10: De-duplicate agent listings. Keep the Input Parameters section in each agent file and the inputs table in the orchestration guide (the orchestrator loads only the guide). Delete the guide's "Available Subagents" table and replace the 13-row schema-path table with one sentence. Slim the README table to one line per agent. Delete or rewrite `agent-doc-sync` so it maintains only the two surviving lists. Depends on: Phase 1.
- Task 11: Normalise frontmatter. Model tiers per Decision 4: `work-planner: fable`, `task-executor: opus`, `validation-runner: sonnet`, all others `inherit`. One documented effort rule in `agent-authoring-standards` (`high` for scale determination and planning, `medium` otherwise) applied uniformly. Every reviewer that is told to use `git diff` gets Bash. Rewrite the authoring standards' tier table to state these tiers and the reason for each (planning quality, coding quality, cost for mechanical work). Depends on: Phase 1.
- Task 12: Trim templates. Work plan: drop Background, Review Scope, Notes; keep Objective, Traceability, Phases, Verification Strategy, Failure Modes, Reference Contracts, Completion Criteria. Task file: drop Investigation Notes and Change Category; keep Target Files, Investigation Targets, Dependencies, Remediation Context, TDD steps, Completion Criteria. Depends on: Task 9.
- Task 13: Make `CODING_STANDARDS_DIR` required in `coding-standards` (block with a clear message when unset) and remove the hardcoded home-directory default. Depends on: none.

#### Phase Completion Criteria

- [x] Every reviewer response is shaped identically; the remediation loop in `implement-spec` needs no per-agent special casing.
- [x] One directory per work plan holds every artifact for that plan.
- [x] Agents are listed in exactly two places.

### Phase 3: Small-Change Path (Estimated tasks: 4)

**Purpose**: Let a single-function change run without a spec, work plan, decomposition, manifest, or plan approval, while reusing the same executor and validator.
**Verification**: a one-line fix request produces one task file, one executor run, one validation run, and at most one `[STOP]`; a request that is actually medium scale is refused with a pointer to `init-spec`.

#### Tasks

- Task 14: Extend the task template with an optional `## Change Request` header for spec-free tasks: Request (verbatim), Observed / Expected (for fixes), Reproduction (for fixes), Acceptance (one or two plain-language checks). Executor and validator treat Acceptance as the completion criteria when no `AC-*` IDs are present. Depends on: Task 12.
- Task 15: Write `skills/implement-task/SKILL.md`. Steps: run `requirements-analyzer` on the request; if scale is not Small, stop and tell the user to write a spec via `init-spec`; otherwise write the single task file directly from the analyzer output (Target Files = affectedFiles, Investigation Targets = the callers it traced); `[STOP]` to show the task file; run `task-executor`; run `validation-runner` with the remediation loop (cap 2); report. Task location: `docs/plans/tasks/quick/{YYYY-MM-DD}-{slug}.md` or equivalent under the Task 9 layout. Depends on: Tasks 9, 14.
- Task 16: Give `validation-runner` a single `verdict` field: `verified` (all checks run and pass), `partial` (symptom gone but unrelated or not-run checks remain), `failed`. Both entry points use it; a `partial` result is reported, never silently accepted. Depends on: Task 8.
- Task 17: Update `requirements-analyzer` so the small path can use it without a spec file: `requirements` may be a plain request string, and the response gains `investigationTargets` (callers and tests traced in Step 2) so the task file can be written without a second analysis. Depends on: Task 6.

#### Phase Completion Criteria

- [ ] `/implement-task "fix X in Y"` runs end to end on a Small change with one approval stop.
- [ ] The same request at Medium scale is refused before any file is written.

### Phase 4: Spec-kit Adoptions (Estimated tasks: 3)

**Purpose**: Fold in the three spec-kit mechanisms that improve consistency without adding a stage.
**Verification**: the spec review gate asks at most five questions per feature in the spec, one at a time, each with a recommended answer; an acceptance gap produces a remediation task rather than an escalation; a decomposition with an uncovered criterion fails its own Final Verification.

#### Tasks

- Task 18: Rework `review-spec` Step 0 into a clarify gate: keep the written report, but surface at most five high-impact questions per feature in the spec via AskUserQuestion, one at a time, each as multiple choice with a recommended option and a "why it matters" line. Record answers in the spec review document under `## Clarifications` with a dated sub-heading (Decision 1); never edit the spec. Add an optional `specReviewPath` input to `work-planner` so clarifications inform the plan, and have `implement-spec` pass it. Depends on: none.
- Task 19: Make `acceptance-validator` a uniform reviewer: on `not_met` it writes `TASK-ACCEPTANCE-REMEDIATION.md` and returns `remediationRequired`, so it joins the existing remediation loop instead of being a separate `[STOP]`. `unverifiable` still escalates. Depends on: Task 8.
- Task 20: Add a coverage check to `task-decomposer` Final Verification: every `AC-*` in the traceability table is covered by at least one task, and every task cites at least one `AC-*` or is explicitly marked infrastructure. Return `blocked` with `coverage_gap` otherwise. Depends on: Task 12.

#### Phase Completion Criteria

- [x] `review-spec` is the only place clarification happens; `requirements-analyzer` no longer asks user-facing questions except for scale.
- [x] Every reviewer, including acceptance, is handled by one loop.

### Phase 5: Documentation (Estimated tasks: 2)

**Purpose**: Make the README and plugin manifest describe the system that now exists.
**Verification**: the README flow diagram matches the single workflow table; no removed agent is named anywhere outside this plan.

#### Tasks

- Task 21: Rewrite the README: two entry points (`implement-spec`, `implement-task`), one flow diagram, the nine-agent table, the flattened artifact layout. Update `plugin.json` description. Depends on: Phases 1 to 4.
- Task 22: Update `agent-authoring-standards` to reflect the final conventions (model/effort rule, schema field names, two listing locations, uniform reviewer shape). Depends on: Phase 2.

#### Phase Completion Criteria

- [x] A new reader can follow the README from install to both entry points without hitting a stale name.

## Verification Strategy

There is no test suite for this repo, so verification is structural:

- After each phase, `git grep -n` for every removed name (`risk-analyzer`, `quality-controller`, `documenter`, `proc-00`, `autonomous`, `qcRemediationRequired`, `planOutputPath`, `Users/Pascal`) returns only hits inside this plan.
- After Phase 2, a script in the scratchpad validates that every `responses/*.jsonc` reviewer schema contains the two standard fields and nothing else named `*Remediation*`.
- After Phase 3 and 4, a dry run of both entry points against a small throwaway repo (one Small fix, one Medium spec) confirms the stop count and artifact locations.
- `pre-commit run --all-files` passes (markdownlint, secrets).

## Failure Modes

- [ ] A removed agent is still named in a skill, template, or README sentence that `grep` missed because of line wrapping.
- [ ] The small path's task file lacks a real write set because the analyzer's `affectedFiles` was a guess; mitigate by making the task file the `[STOP]` artifact the user reviews.
- [ ] Flattening the artifact layout breaks the manifest path the orchestrator writes; mitigate by updating `documentation-criteria` and the manifest template in the same task.
- [ ] Clarification answers recorded in the review document drift from the spec if the user later edits the spec; mitigate by dating each clarification session and having `review-spec` flag answers that contradict the current spec text on re-run.

## Reference Contracts

- Reviewer response shape (after Task 8): `{ status, findings[], remediationRequired, remediationTaskPath }`.
- Producer response shape: `{ status, workPlanId?, outputPath }`.
- Validation verdict enum (Task 16): `verified | partial | failed`.
- Task file location (Task 9): `docs/plans/{workPlanId}/tasks/TASK-NNN.md`; remediation files keep their `TASK-*-REMEDIATION.md` names in the same directory.
- Blocked reason added (Task 20): `coverage_gap`.

## Completion Criteria

- [x] All phases completed.
- [x] Nine agents, two entry points, one flow table, one directory per work plan.
- [x] Every reviewer shares one response shape and one remediation loop.
- [x] README and authoring standards describe the final state.

## Spec-kit Review

GitHub spec-kit (github/spec-kit) was reviewed against both workflows. It is a command-driven system: `constitution` once per project, then `specify → clarify → plan → tasks → implement → converge` per feature, with `analyze` and `checklist` as read-only validators, plus a separate `bug.assess → bug.fix → bug.test` extension and a `lean` preset.

### Adopt

| spec-kit feature | Where it lands | Why |
| --- | --- | --- |
| `bug.assess` assessment shape: symptom vs expected, reproduction steps, suspected code paths, root-cause hypothesis with confidence | Task 14 (Change Request header on the small-path task file) | Gives a spec-free task the minimum structure the executor and validator need, without a spec. |
| `bug.test` verdicts `verified / partial / failed`, with "missing verification is not a success" | Task 16 (`validation-runner` verdict) | A single honest outcome field both entry points can report; replaces per-check booleans the orchestrator has to interpret. |
| `/clarify`: at most five questions, one at a time, multiple choice with a recommended option, answers recorded in a dated `## Clarifications` section | Task 18 (`review-spec` gate), answers stored in the review document rather than the spec; cap scaled to five per feature since specs may hold several | Replaces three overlapping uncertainty channels (review report, `scopeDependencies`, `questions`) with one bounded, user-friendly gate. |
| `/converge`: append-only gap finding that emits new tasks instead of halting | Task 19 (`acceptance-validator` writes a remediation task) | Makes acceptance a reviewer like the others, so one loop handles everything. |
| `/analyze` coverage pass: every requirement has a task, every task has a requirement | Task 20 (`task-decomposer` Final Verification) | Catches coverage gaps before execution instead of at acceptance. Folded into an existing agent rather than added as a stage. |
| `lean` preset existing at all | Phase 3 rationale | spec-kit itself found the full flow too heavy for every case and added a lighter path. |

### Do not adopt

| spec-kit feature | Reason |
| --- | --- |
| `constitution.md` and constitution gates | The standards repo already plays this role, with scoped rules and IDs; a second principles file would split the source of truth. |
| `research.md`, `data-model.md`, `contracts/`, `quickstart.md` per feature | Four more artifacts per feature is the opposite of this plan. The spec's Contracts section and the work plan's Reference Contracts cover the same ground. |
| `checklist` ("unit tests for requirements") | Overlaps `review-spec`; a second spec-quality gate would be redundant. |
| `[P]` parallel markers on tasks | The existing guard (no dependency and disjoint Target Files) is stricter and derived from data the decomposer already emits. |
| Spec template changes (P1/P2/P3 user stories, FR-/SC- numbering, Assumptions) | The REQ/AC/Gherkin chain is already traceable end to end; renumbering would churn every downstream agent for no consistency gain. Worth revisiting only if large specs need slice-by-priority delivery. |
| Branch creation per feature (`create-new-feature.sh`) | The orchestrator deliberately never touches git state; keep that boundary. |
| `taskstoissues`, agent-context, assess extensions | Out of scope for this pipeline. |
