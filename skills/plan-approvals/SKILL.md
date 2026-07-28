---
name: plan-approvals
description: Provides detailed instructions on how to obtain approvals for work plans. Use PROACTIVELY when planning work and asking for user approval.
---

When requesting approval for a work plan, follow these steps:

1. Present a summary of the work plan to the user, highlighting key tasks, phases, and dependencies.
2. Present the user with the following discreet, labeled options for approval as a set of multiple-choice questions:
   - Approve and enter autonomous execution mode — proceed as planned
   - Approve — proceed as planned
   - Modify — I'll specify changes
   - Reject — start over
   - Clarify — ask me questions first
3. Do not proceed with any work until the user has provided explicit approval. If the user requests modifications or clarifications, address them before seeking approval again.

If you are prompted with a question, or with a request to make changes to the work plan, make sure to prompt the user for approval again. Do not treat the approval of a change as approval of the entire plan.

## Autonomous Execution Mode

When the user selects "Approve and enter autonomous execution mode", they are approving the entire plan *and* granting you authority to carry it out end-to-end without pausing for approval at each step. In this mode:

- Execute every task and phase in the approved plan in order, without stopping to ask for confirmation between steps.
- Do not re-prompt for approval of individual steps, sub-tasks, or intermediate results that fall within the scope of the approved plan.
- Keep the user informed as you go: report progress at meaningful checkpoints (e.g. when a phase completes) rather than seeking permission to continue.

Autonomous execution applies only to the plan as approved. You must stop and return to the user for explicit approval when:

- You need to deviate materially from the approved plan (adding, removing, or reordering tasks; changing the approach).
- You encounter a decision that is genuinely the user's to make, or an ambiguity the plan does not resolve.
- An action is destructive, hard to reverse, or outward-facing (e.g. deleting data, pushing, deploying, publishing) and was not explicitly covered by the plan.
- You hit a blocking error that cannot be resolved within the scope of the plan.

In these cases, follow the normal approval flow above: present the change or question, obtain explicit approval, and only then resume. Approval to enter autonomous execution mode is not approval for work outside the plan's scope.
