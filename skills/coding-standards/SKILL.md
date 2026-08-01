---
name: coding-standards
description: Find and apply the coding standards relevant to the files being worked on. Use before planning or making any code changes, and whenever another skill needs to know which standards apply.
---

`<standards-dir>/standards-tree.yaml` provides a hierarchical index of coding standards in YAML format. The tree indexes standards documents by `description`, `scope` (file globs), and `topics` (frameworks/tools). Consult it to collect every standard that applies to the current task **before** planning or making code changes.

## Locating the standards directory

Resolve `<standards-dir>` as follows:

1. If the `CODING_STANDARDS_DIR` environment variable is set (check with `echo "$CODING_STANDARDS_DIR"`), use its value.
2. Otherwise, use the default: `/Users/Pascal/Github/psauerborn/standards`.

If you cannot locate the coding standards directory, escalate to the user. Coding tasks must not be attempted without standards to reference.

## Tree structure

The tree has a top-level `nodes` key holding a list of root nodes. Every node has:

- `path` — the standards document, relative to `<standards-dir>`.
- `title` / `description` — human-readable summary of what the standard covers.
- `scope` — a **list** of file globs the standard applies to (`'*.go'`, `'*.py'`; `'*'` matches everything).
- `topics` — frameworks/tools/domains the standard covers (`golang`, `fastapi`, `docker`).
- `children` — nested standards that extend this one (may be empty).

Nodes may also have:

- `aliases` (root nodes) — a map of shorthand terms to canonical topic names (e.g. `go: golang`, `pg: postgresql`). Normalize your context terms through this map before matching topics.
- `examples` — companion example files, each with a `path` (relative to `<standards-dir>`), a `title`, and the list of statement IDs it illustrates (see "Examples" below).

## Searching For Coding Standards

1. Read `<standards-dir>/standards-tree.yaml`.

2. Determine your context:
   - The file extensions you're editing (e.g. `*.go`, `*.py`, `*.tf`).
   - The project's detected frameworks/tools (e.g. `react` in `package.json`, `fastapi` in `pyproject.toml`, `gin-gonic` in `go.mod`).
   - What the task is about (compare against each node's `description`).
   - Normalize shorthand in your context using the root node's `aliases` map (e.g. treat `py` as `python`).

3. Start at the nodes under the top-level `nodes` key. Read any root node whose scope matches the files you're working with or whose scope includes `*`.

4. For each node you read, check its children. Descend into a child if its `scope` or `topics` match your current context.

5. Stop descending a branch when no children match your context.

6. Collect all matching nodes from root to leaf.

**Do not** read any files outside of the YAML tree. The index exists to minimize the amount of context you load - do not fill your context with files that are not indexed.

### Matching a node

A node matches your context when any of the following hold:

- **description**: the node's description relates to the task you're working on.
- **scope**: any glob in the list matches the file extensions you're editing (`*.py`, `*.ts`; `*` matches everything).
- **topics**: a topic matches one of the project's detected frameworks/tools (after alias normalization).

Note that a `scope` of `*` does not make a node universally relevant — topic-scoped standards (e.g. DynamoDB, REST APIs, Docker) use `scope: ['*']` and rely on `topics`/`description` to gate applicability. Only apply them when the task actually involves that topic.

## Applying the collected standards

- Standards apply at every level of the path from root to leaf. A child does
  **not** replace its parent — it adds to it.
- If a child standard contradicts a parent, the child takes precedence.
- The `path` field on each node is relative to `<standards-dir>`. Read the
  referenced document to get the actual standard, then follow it while writing
  or reviewing code.
- Standards documents contain numbered statements tagged with an ID (e.g.
  `[GO-018]`) and a level: **MUST** statements are mandatory; **SHOULD**
  statements are strong defaults to follow unless there is a good reason not to.

### Examples

Standards documents are rules-only; worked code examples live in the companion files listed under a node's `examples` key. Each entry names the statement IDs it illustrates. Do **not** read every example — after identifying which statements are relevant to your task, read only the example files whose `statements` list covers those IDs. Each example file is small and self-contained, so reading the cited file is the minimal read.
