---
name: agent-authoring-standards
description: Style guide for authoring pipeline subagent definitions. Use when creating or modifying agent definition files in agents/. Reference for authors — not preloaded by agents at runtime.
---

Agent definition files are system prompts. Every line costs context before the agent reads a single input, and every ambiguity degrades execution. Follow these rules when writing or editing files in `agents/`.

## Frontmatter

- **description** — an accurate inputs → outputs statement: "Takes X; produces Y at Z." The orchestrator selects and prompts agents based on the description, so it must never drift from actual behavior. Do not write "Use PROACTIVELY" — pipeline agents are invoked explicitly by the orchestrator, never self-triggered.
- **tools** — grant the minimum set the role needs. Every reviewer that works from `git diff` gets Bash; an agent that only reads a plan and writes markdown gets no Bash. No agent gets WebSearch or TaskCreate/TaskUpdate.
- **model** — pinned deliberately, by role:

  | Model | Role | Agents |
  | --- | --- | --- |
  | `fable` | Specifying and planning — these documents bound everything downstream | spec-writer, work-planner |
  | `opus` | Coding — implementation quality | task-executor |
  | `sonnet` | Mechanical — running discovered commands | validation-runner |
  | `inherit` | Everything else — analysis, design, decomposition, review | requirements-analyzer, frontend-designer, task-decomposer, code-reviewer, security-reviewer, acceptance-validator |

- **effort** — `high` for the agents whose output gates the rest of the run (requirements-analyzer, spec-writer, work-planner, frontend-designer); `medium` for every other agent.
- **skills** — preload only what the agent uses on every run. All pipeline agents preload `agent-response-protocol`; every agent that writes an artifact preloads `documentation-criteria`.

## Body

- **Address the agent in the second person.** "You create task files; you do not write code."
- **State each requirement once**, in the step where it applies. No pre-/post-execution checklists that restate the steps, no task-registration rituals.
- **End with a Final Verification step** carrying concrete criteria: the final JSON validates against the schema; every claimed artifact exists on disk at its canonical path.
- **Include one annotated example** (good vs. bad) for any agent whose primary output is a document other agents consume.
- **Failure is part of the contract.** Every agent uses the `agent-response-protocol` envelope (`completed` | `blocked` with typed reasons). Never instruct an agent to improvise around a missing or unreadable input.
- **Reviewers get a Review Posture section**: adversarial stance; every finding cites file and line; verify each finding against the actual file content before reporting it.
- **Reviewers share one response shape**: `findings[]`, `remediationRequired`, `remediationTaskPath`, so the orchestrator's remediation loop needs no per-agent handling. A reviewer takes `planDir`, reads `{planDir}/manifest.md`, and writes `{planDir}/tasks/TASK-*-REMEDIATION.md`.
- **Paths come from `documentation-criteria`.** Agents cite `{planDir}`-relative locations; never restate the directory layout.
- **Schemas live in one place.** Output sections reference the agent's schema file under `skills/subagents-orchestration-guide/reference/responses/`. Never duplicate the schema in the agent body.
- **Scope sections name the owner** of each excluded responsibility ("that belongs to `task-executor`") so misdirected work can be rerouted rather than dropped.

## Listings

An agent is listed in exactly two places besides its own file: the Subagent Inputs table in `subagents-orchestration-guide`, and its response schema. The README table is the human-facing copy. Run `agent-doc-sync` after changing any agent's inputs or outputs.
