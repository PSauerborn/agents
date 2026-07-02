---
name: agent-doc-sync
description: Analyze available subagents and document inputs and outputs in orchestration guide.
disable-model-invocation: true
---

The `subagents-orchestration-guide` documents available subagents, how to use them, and their inputs and outputs. Analyze the available agents and update the following:

* Available subagents table. Add any new subagents and remove any that are no longer available.
* Subagent input parameters. Update the table with any new or changed input parameters for each subagent.
* Agent response structure. Each agent has a unique response signature documented in `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/{agent-name}.jsonc`. Update the schema if the agent's response structure has changed. Add a schema file for any new agents. Make sure the mapping table in the `Subagent Responses` section is updated to include any new/updated agents and their schema locations.

Make sure you only update the following entities:

* Available subagents table
* Subagent input parameters table
* Subagent response structure table
* Subagent response schema files in `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/`

Escalate to the user if you are unsure about any changes or if you need clarification on the subagent's behavior or response structure.
