---
name: coding-standards
description: Find and apply the coding standards relevant to the files being worked on. Use before planning or making any code changes, and whenever another skill needs to know which standards apply.
---

`/Users/Pascal/.stdidx/standards-tree.yaml` provides a hierarchical index of coding standards in YAML format. The tree indexes standards documents by `description`, `scope` (file globs/types), and `topics` (frameworks/tools). Consult it to collect every standard that applies to the current task **before** planning or making code changes.

## Searching For Coding Standards

1. Read `/Users/Pascal/.stdidx/standards-tree.yaml`.

2. Determine your context:
   - The file extensions you're editing (e.g. `*.go`, `*.py`, `*.tf`).
   - The project's detected frameworks/tools (e.g. `react` in `package.json`, `fastapi` in `pyproject.toml`, `gin-gonic` in `go.mod`).
   - What the task is about (compare against each node's `description`).

3. Start at the root nodes. Read any root node whose scope matches the
   files you're working with or whose scope is `*`.

4. For each node you read, check its children. Descend into a child if its
   `scope` or `topics` match your current context.

5. Stop descending a branch when no children match your context.

6. Collect all matching nodes from root to leaf.

### Matching a node

A node matches your context when any of the following hold:

- **description**: the node's description relates to the task you're working on.
- **scope**: a glob matches the file extensions you're editing (`*.py`, `*.ts`, `*` matches everything).
- **topics**: a topic matches one of the project's detected frameworks/tools.

## Applying the collected standards

- Standards apply at every level of the path from root to leaf. A child does
  **not** replace its parent — it adds to it.
- If a child standard contradicts a parent, the child takes precedence.
- The `path` field on each node is relative to the standards tree directory
  (`/Users/Pascal/.stdidx/`). Read the referenced document to get the actual
  standard, then follow it while writing or reviewing code.
