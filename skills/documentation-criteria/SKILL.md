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
│   ├── risk/{workPlanId}/                     # Risk Plans
│   │   └── {workPlanId}-risk-plan.md
│   └── usage/{workPlanId}/                    # Usage Reports
│       └── {workPlanId}-usage-report.md
└── project-context/
    └── external-resources.md     # referenced by task-executor for external resources
```

## Available Templates

Use templates to ensure consistency and quality in your documentation. Below are the available templates for different types of documents:

| Document Type | Template File |
| --------------- | ---------------- |
| Work Plan | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/work-plan-template.md` |
| Task Executable File | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/task-template.md` |
| Quality Report | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/quality-report-template.md` |
| Usage Report | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/usage-report-template.md` |
| Risk Plan | `${CLAUDE_PLUGIN_ROOT}/skills/documentation-criteria/reference/risk-plan-template.md` |
