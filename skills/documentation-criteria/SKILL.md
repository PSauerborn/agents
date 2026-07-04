---
name: documentation-criteria
description: Documents formats, templates, and locations for output artifacts, including design, planning and task documents. Use when writing output artifacts for design, planning, and task documents.
---

Implementation work produces design documents, work plans, and task documents. When writing any of these, follow the directory structure, templates, and conventions below.

## Docs Directory Structure

Outputs should be organized in a clear and consistent directory structure within the `docs/` directory. Use the following structure when writing output files:

```text
docs/
├── plans/                                     # Work Plans
│   ├── {YYYY-MM-DD}-{workPlanId}.md
│   ├── tasks/{workPlanId}/                    # decomposed task files
│   │   ├── TASK-{number}.md
│   │   ├── TASK-RISK-REMEDIATION.md           # Risk remediation task (only if risks found)  
│   │   └── TASK-QC-REMEDIATION.md             # QC remediation task (only if violations found)
│   ├── quality/{workPlanId}/                  # Quality Reports
│   │   └── {workPlanId}-quality-report.md
│   ├── changesets/{workPlanId}/               # Diffs and change reports
│   │   └── {workPlanId}-changeset.md
│   ├── risk/{workPlanId}/                     # Risk Plans
│   │   └── {workPlanId}-risk-plan.md
│   └── usage/{workPlanId}/                    # Usage Reports
│       └── {workPlanId}-usage-report.md
└── project-context/
    └── external-resources.md     # referenced by task-executor for external resources
```

## Available Templates

Use templates to ensure consistency and quality in your documentation. Below are the available templates for different types of documents:

| Document Type | When to Use | Template File |
| --------------- | ---------------- | ---------------- |
| Work Plan | When planning a new piece of work; authored by `work-planner` before decomposition | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/work-plan-template.md` |
| Task Executable File | When decomposing a work plan into single-commit tasks; authored by `task-decomposer` and consumed by `task-executor` | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/task-template.md` |
| Quality Report | When reporting coding-standards review findings; authored by `quality-controller` after execution | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/quality-report-template.md` |
| Usage Report | When summarizing resource/token usage for a work plan run | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/usage-report-template.md` |
| Risk Plan | When identifying and documenting risks for a work plan; authored by the risk analysis agents | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/risk-plan-template.md` |
| Changeset | When summarizing the changeset after code changes; authored by `documenter` (one per work plan, excludes brand-new files) | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/changeset-template.md` |
