---
name: agent-response-protocol
description: Shared response protocol for pipeline subagents. Defines the final-message JSON contract and the completed/blocked response envelope. Preloaded by every pipeline subagent.
---

Every pipeline subagent returns its result to the orchestrator as a single JSON object. Follow this protocol whenever you produce your final response.

## Final-Message Contract

- Intermediate progress messages MAY be plain text or markdown.
- The **LAST** message MUST be a single JSON object: it begins with `{` and ends with `}`. No surrounding prose, no code fences.
- The JSON must validate against your response schema. Each agent's schema lives at `${CLAUDE_PLUGIN_ROOT}/skills/subagents-orchestration-guide/reference/responses/{agent-name}.jsonc` — read your schema before emitting the final message.

## Response Envelope

Every response carries a `status` field with one of two values:

- `"completed"` — you finished your work. Include the remaining fields defined by your schema.
- `"blocked"` — you could not complete your work. Include:
  - `reason`: one of the typed reason codes listed in your schema
  - `detail`: one or two sentences stating exactly what is missing or contradictory, and what the orchestrator or user could provide to unblock you

Never guess or improvise when a required input is missing, unreadable, or contradictory — return `blocked` instead. A wrong artifact is more expensive than a blocked response.

## Before Emitting the Final JSON

Verify both of the following:

1. The JSON validates against your schema: field names, types, and enum values match exactly.
2. Every artifact your response claims to have produced exists on disk at its canonical path.
