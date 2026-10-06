---
name: agent-doc-sync
description: Keep the orchestration guide's Subagent Inputs table and the response schemas in sync with the agent definitions in agents/.
disable-model-invocation: true
---

Agents are listed in exactly two places besides their own definition files: the **Subagent Inputs** table in `skills/subagents-orchestration-guide/SKILL.md`, and the response schemas in `skills/subagents-orchestration-guide/reference/responses/{agent-name}.jsonc`. The README's agent table is a third, human-facing copy.

Read every file in `agents/` and reconcile:

- **Subagent Inputs table**: one row per agent, matching each agent's Input Parameters section exactly (names, required/optional, defaults). Add rows for new agents; remove rows for deleted ones.
- **Response schemas**: one file per agent. The schema must match the agent's Output section and blocked reasons. Every reviewer schema keeps the uniform fields `findings[]`, `remediationRequired`, `remediationTaskPath`.
- **README agent table**: one line per agent, matching the agent's description.

Change nothing else. Escalate to the user if an agent's inputs or outputs are ambiguous.
