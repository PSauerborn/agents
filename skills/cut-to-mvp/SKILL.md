---
name: cut-to-mvp
description: Ablate a spec's requirements down to the minimum set that satisfies the spec statement, through repeated cutting passes. Preloaded by spec-writer; also usable on any unapproved spec draft that looks over-engineered.
---

# Cut to MVP

This skill reduces a drafted spec to its minimum viable version by ablation: remove requirements, check whether the core outcome still holds, and repeat until nothing more can be removed.

The working definition of an MVP here is the set of `REQ-*` entries where every remaining one is individually necessary. If any one of them were removed, the acceptance scenario would fail. A requirement that is merely useful, sensible, or likely to be needed later does not meet that bar and belongs in Out of Scope.

Drafters tend to add scope because thoroughness feels safe. This process reverses the burden of proof: the default for every requirement is to cut it, and it stays only when the drafter can name the specific thing that breaks without it.

The skill runs without a user in the loop. Where a decision is the user's to make, it is written into the spec as a `[NEEDS CLARIFICATION: ...]` marker rather than asked, so it surfaces through the normal clarification path.

## Step 1: Fix the anchor

Before cutting anything, write down two things and treat them as immutable for the rest of the process:

- **Acceptance scenario.** One concrete scenario in the form "a specific user does a specific thing and gets a specific result." Derive it from the spec's Spec Statement ("As a / I want / So that") and the analysis purpose. If the request does not make the core outcome clear, mark the Spec Statement `[NEEDS CLARIFICATION: <what the core outcome is>]` and run the passes against the most literal reading of the request, because every later decision is judged against this scenario and a wrong anchor produces a wrong MVP.
- **Protected constraints.** Requirements that are never candidates for cutting regardless of the scenario: the analysis constraints, the user's answers to the analyzer's questions, legal and compliance obligations, security, and data integrity. Protection comes from the inputs, not from guessing: a stated requirement that looks like a compliance or security item but is not named as one (an audit log, say) is cut like any other stated requirement, and its marker says it may be an obligation.

If there is more than one core outcome, write one scenario for each and keep the list as short as honesty allows. A long list of scenarios is scope creep entering through the anchor.

## Step 2: Atomize

The spec's requirements rule already makes each `REQ-*` one obligation; split any that still bundles several things ("user accounts with roles, SSO and audit logging" is three or four requirements). Then build a working list, outside the spec, recording for each requirement:

- its `REQ-*` ID
- its origin: stated in the request, inferred from a codebase convention, or added by the drafter
- which other requirements it depends on

## Step 3: Run cutting passes

Run the passes below in order. Each pass asks a different question, because repeating a flat "cut more" instruction stops finding anything after the first round.

**Pass 1: Unrequested.** Cut every requirement the drafter added that the request did not ask for and the acceptance scenario does not need.

**Pass 2: Requested but unnecessary.** For each remaining requirement, including ones the request stated, ask: if this is gone, does the acceptance scenario still pass and do the protected constraints still hold? If yes, cut it.

**Pass 3: Downgrade.** For each requirement that survived, ask whether a narrower outcome would still satisfy the scenario: one supported case instead of the general case, a fixed value instead of a configurable one, a manual step instead of an automated one, the existing behaviour instead of a new one. Replace the requirement with the narrowest outcome that works. Stay at outcome level; how the outcome is built is the work planner's concern.

**Pass 4 onward: Repeat.** Run passes 2 and 3 again on the reduced set. Earlier cuts leave other requirements without a purpose, so later passes regularly find more.

### How to run each pass

1. **Nominate with fresh eyes.** Work only from the anchor and the current requirement list. Do not reread the reasoning that produced the draft or earlier passes' justifications for keeping things, because a reviewer who sees those arguments tends to defer to them.
2. **Require a named break to keep.** A requirement stays only if you can state what specifically fails in the acceptance scenario, or which protected constraint is violated, when it is removed. "Best practice," "users will expect it," and "we will need it eventually" are not breaks.
3. **Verify the cuts.** Walk through the acceptance scenario step by step against the reduced set. If the scenario fails, restore that pass's cuts and remove them again in halves to find which one was load-bearing. Keep that one and let the rest stay cut.
4. **Sweep for orphans.** After the cuts are verified, check the dependency records. Any requirement that existed only to support a removed one is now cut as well.
5. **Log every change.** Record each cut or downgrade with the pass it happened in, its origin, the reason, and the condition that would justify bringing it back. Step 5 writes this log into the spec.

## Step 4: Stop

Stop when a full round of passes 2 and 3 produces no cuts and no downgrades. That is the fixed point: every remaining requirement has a named break.

Also stop after six passes in total even if changes are still appearing. A loop that is still cutting at that point usually has an anchor that is too vague; add `[NEEDS CLARIFICATION: the core outcome is too vague to fix the MVP; which single scenario must this spec satisfy?]` to the Spec Statement.

If the set has been reduced to the point where the acceptance scenario cannot pass, the process has gone wrong. Restore the last verified set and continue from there.

## Step 5: Write the result into the spec

There is no separate report. The outcome lands in the spec's own sections:

- **Requirements (section 4)**: only the surviving `REQ-*` entries, in their downgraded wording where a downgrade happened. Renumber so the IDs are contiguous, and update every `AC-*` reference to match.
- **Out of Scope (section 3.2)**: one bullet per cut, giving what was cut and the condition under which it returns. This list is part of the deliverable: it shows the user the ideas were considered and gives them a backlog for after the MVP ships.
- **Cuts and downgrades of stated requirements**: from pass 2 onward this process overrides things the request asked for, and that is the user's decision to make. Every such cut gets a marker on its Out of Scope bullet: `[NEEDS CLARIFICATION: the request asked for <X>; the MVP does not need it. Defer or keep?]`. Every such downgrade gets a marker on the downgraded `REQ-*`: `[NEEDS CLARIFICATION: the request asked for <X>; narrowed to <Y> for the MVP. Accept or restore?]`. Keep `<X>` to a few words; the bullet's own reason and return condition carry the rest. Drafter-added and convention-inferred cuts need no marker.

Keep the Out of Scope bullets to one line each: what was cut, why, when it comes back.

## Things to watch for

- **Cutting the proof with the feature.** The `AC-*` entries that show the acceptance scenario passed are part of the MVP even though no user asked for them. Cut acceptance criteria only when the requirement they verify is cut.
- **Hidden work inside a kept requirement.** A requirement can survive while still describing a broader outcome than the scenario needs. The downgrade pass exists to catch this, so apply it to every survivor, including the obviously necessary ones.
- **Protected constraints growing.** If the protected list expands during the process, scope is being smuggled back in. Anything added to it that did not come from the request or the analysis needs a `[NEEDS CLARIFICATION]` marker.
- **Over-cutting.** The goal is the smallest set that works, and a set that does not work is not smaller, it is broken. The verification step is what separates the two, so do not skip it to save time.
