---
name: documentation-criteria
description: Defines the canonical location and template for every pipeline artifact — specs, spec reviews, designs, work plans, manifests, and task files. Use when writing or locating any of these.
---

Every pipeline artifact has exactly one canonical location and one template. Agents write to these locations without being told a path; orchestrators locate artifacts from them without being told a path.

## Layout

```text
docs/
├── specs/
│   ├── SPEC-{ID}.md                        # spec, one or more features (init-spec, or spec-writer via draft-spec); read-only once approved
│   └── SPEC-{ID}-REVIEW.md                 # spec review + clarifications (review-spec)
├── designs/{designSetId}/                  # frontend design options (frontend-designer); DES-NNN
│   ├── {designSetId}-option-{n}.md         # n = 1..3
│   └── {designSetId}-option-{n}.html       # self-contained static mockup
└── plans/
    └── {workPlanId}/                       # one directory per work plan; WP-NNN
        ├── work-plan.md                    # work-planner
        ├── manifest.md                     # orchestrator (execution manifest)
        └── tasks/
            ├── TASK-{NNN}.md               # task-decomposer
            ├── TASK-VALIDATION-REMEDIATION.md   # validation-runner, on failure
            ├── TASK-CODE-REVIEW-REMEDIATION.md  # code-reviewer, on findings
            ├── TASK-SEC-REMEDIATION.md          # security-reviewer, on findings
            └── TASK-ACCEPTANCE-REMEDIATION.md   # acceptance-validator, on not_met
```

A **plan directory** (`planDir`) is a `docs/plans/{workPlanId}/` directory. Every reviewer takes `planDir` as input, reads `{planDir}/manifest.md`, and writes its remediation task to `{planDir}/tasks/`. Designs live outside the plan directory because they are produced and selected before a work plan exists; the work plan cites the chosen design path.

Directories are created on first write. Do not create placeholder files.

## Templates

| Artifact | Author | Template |
| --- | --- | --- |
| Spec | user (via `init-spec`) | `${CLAUDE_PLUGIN_ROOT}/skills/init-spec/reference/spec-template.md` |
| Design option | `frontend-designer` | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/design-option-template.md` |
| Work plan | `work-planner` | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/work-plan-template.md` |
| Execution manifest | orchestrator | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/execution-manifest-template.md` |
| Task file (including every remediation task) | `task-decomposer`, reviewers | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/task-template.md` |
