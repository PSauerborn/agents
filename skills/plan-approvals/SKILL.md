---
name: plan-approvals
description: How to obtain the user's approval for a work plan before any task file is written or executed.
---

When requesting approval for a work plan:

1. Present a summary of the work plan: phases, the tasks each contains, dependencies, and anything the plan flags as a risk or an open decision.
2. Ask via **AskUserQuestion** with exactly these options:
   - **Approve** — proceed as planned
   - **Modify** — I'll specify changes
   - **Reject** — start over
3. Do not proceed with any work until the user selects Approve.

On **Modify**, re-run `work-planner` in update mode with the user's changes as `updateContext`, then present the updated plan for approval again. Approval of a change is not approval of the plan.

On **Reject**, ask what was wrong before re-planning; do not re-run the planner with the same inputs.

Approval covers the plan as presented. The orchestrator still stops at every `[STOP]` marker in its workflow, and must return to the user if it needs to deviate materially from the approved plan (adding, removing, or reordering tasks; changing the approach), hits a decision that is the user's to make, or faces an action that is destructive or outward-facing and not covered by the plan.
