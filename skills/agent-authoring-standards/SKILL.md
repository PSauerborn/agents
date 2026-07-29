---
name: agent-authoring-standards
description: Style guide for authoring pipeline subagent definitions. Use when creating or modifying agent definition files in agents/. Reference for authors — not preloaded by agents at runtime.
---

Agent definition files are system prompts. Every line costs context before the agent reads a single input, and every ambiguity degrades execution. Follow these rules when writing or editing files in `agents/`.

## Frontmatter

- **description** — an accurate inputs → outputs statement: "Takes X; produces Y at Z." The orchestrator selects and prompts agents based on the description, so it must never drift from actual behavior. Do not write "Use PROACTIVELY" — pipeline agents are invoked explicitly by the orchestrator, never self-triggered.
- **tools** — grant the minimum set the role needs. A reviewer that never leaves the repo gets no WebSearch; an agent that only reads a plan and writes markdown gets no Bash. Every extra tool is an invitation to wander and a way to violate scope.
- **model** — set explicitly and deliberately:

  | Tier | Use for | Current agents |
  | --- | --- | --- |
  | `inherit` | Judgment-heavy roles: analysis, planning, decomposition, implementation, review | requirements-analyzer, work-planner, risk-analyzer, task-decomposer, task-executor, quality-controller, code-reviewer, security-reviewer, risk-reviewer, acceptance-validator |
  | `sonnet` | Mechanical roles: running discovered commands, documentation upkeep | validation-runner, documenter |

- **skills** — preload only what the agent uses on every run. All pipeline agents preload `agent-response-protocol`.

## Body

- **Address the agent in the second person.** "You create task files; you do not write code." Never describe the agent to itself in the third person ("this is handled by the `x` subagent" where `x` is the agent being defined).
- **State each requirement once**, in the step where it applies. Do not add Pre-/Post-Execution checklists that restate the steps.
- **End with a Final Verification step** carrying concrete criteria: the final JSON validates against the schema; every claimed artifact exists on disk at its canonical path. Do not use TaskCreate registration rituals as a substitute for verification. Grant TaskCreate/TaskUpdate only to agents with genuinely multi-phase work (currently only task-executor).
- **Include one annotated example** (good vs. bad, in the style of the `review-spec` skill) for any agent whose primary output is a document other agents consume.
- **Failure is part of the contract.** Every agent uses the `agent-response-protocol` envelope (`completed` | `blocked` with typed reasons). Never instruct an agent to improvise around a missing or unreadable input.
- **Reviewers get a Review Posture section** stating: adversarial stance — assume defects exist and hunt for them; every finding cites file and line; verify each finding against the actual file content before reporting it; a finding you have not verified is a finding you do not report.
- **Schemas live in one place.** Output Format sections reference the agent's schema file under `skills/subagents-orchestration-guide/reference/responses/` — the single source of truth. Never duplicate the schema in the agent body.
- **Scope sections name the owner** of each excluded responsibility ("that belongs to `task-executor`") so misdirected work can be rerouted rather than dropped.
