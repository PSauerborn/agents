---
name: documentation-criteria
description: Documents formats, templates, and locations for output artifacts, including design, planning and task documents. Use when writing output artifacts for design, planning, and task documents.
---

Implementation work produces design documents, work plans, task documents, and review artifacts. When writing any of these, follow the directory structure, templates, and conventions below.

## Docs Directory Structure

Outputs should be organized in a clear and consistent directory structure within the `docs/` directory. Use the following structure when writing output files:

```text
docs/
├── plans/                                     # Work Plans
│   ├── {YYYY-MM-DD}-{workPlanId}.md
│   ├── tasks/{workPlanId}/                    # decomposed task files
│   │   ├── TASK-{number}.md
│   │   ├── TASK-VALIDATION-REMEDIATION.md     # Validation remediation task (only if checks fail)
│   │   ├── TASK-QC-REMEDIATION.md             # QC remediation task (only if violations found)
│   │   ├── TASK-CODE-REVIEW-REMEDIATION.md    # Code review remediation task (only if findings)
│   │   ├── TASK-SEC-REMEDIATION.md            # Security remediation task (only if findings)
│   │   └── TASK-RISK-REMEDIATION.md           # Risk remediation task (only if risks found)
│   ├── manifests/                             # Execution manifests (assembled by orchestrator)
│   │   └── {workPlanId}-manifest.md
│   ├── quality/{workPlanId}/                  # Quality Reports
│   │   └── {workPlanId}-quality-report.md
│   ├── changesets/{workPlanId}/               # Diffs and change reports
│   │   └── {workPlanId}-changeset.md
│   └── risk/{workPlanId}/                     # Risk Plans and Risk Reviews
│       ├── {workPlanId}-risk-plan.md
│       └── {workPlanId}-risk-review.md
└── project-context/
    └── external-resources.md     # referenced by task-executor for external resources
```

## Available Templates

Use templates to ensure consistency and quality in your documentation. Below are the available templates for different types of documents:

| Document Type | When to Use | Template File |
| --------------- | ---------------- | ---------------- |
| Work Plan | When planning a new piece of work; authored by `work-planner` before decomposition | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/work-plan-template.md` |
| Task Executable File | When decomposing a work plan into single-commit tasks; authored by `task-decomposer` and consumed by `task-executor`. Also used for all remediation tasks | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/task-template.md` |
| Execution Manifest | Maintained by the orchestrator during execution; the definitive changeset consumed by all reviewers and the documenter | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/execution-manifest-template.md` |
| Quality Report | When reporting coding-standards review findings; authored by `quality-controller` after execution | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/quality-report-template.md` |
| Risk Plan | When identifying and documenting risks for a work plan; authored by `risk-analyzer` | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/risk-plan-template.md` |
| Risk Review | When reviewing a changeset against a risk plan; authored by `risk-reviewer` | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/risk-review-template.md` |
| Changeset | When summarizing the changeset after code changes; authored by `documenter` (one per work plan, excludes brand-new files) | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/changeset-template.md` |
