---
name: review-changeset
description: How a reviewer obtains the changeset to review — reconcile the execution manifest against the run's TASK commits, then diff each manifest file from the Base Commit to HEAD. Preloaded by every reviewer that works from git.
---

Reviewers receive `planDir` and review the changeset recorded in `{planDir}/manifest.md`. The manifest is trusted only after it has been checked against the commit history. Follow both steps in order.

## Step 1: Reconcile the Manifest

Read the manifest's Base Commit (`baseCommit`) and its Changeset and Task Results sections. Then:

1. List the run's commits: `git log --format=%H --grep='^TASK ' {baseCommit}..HEAD`.
2. List the files those commits touched: `git diff-tree --no-commit-id --name-only -r {sha}` per commit, excluding the plan directory.
3. Compare that file list to the Changeset section, and the commit SHAs to the Commit column.

Return blocked (`manifest_incomplete`) with every discrepancy in `detail` if any of the following holds:

- A file appears in the run's commits but not in the Changeset.
- A Changeset file appears in no run commit.
- A Task Results row lacks a SHA the history shows.

Do not review a manifest you could not reconcile; the orchestrator corrects it and re-invokes you.

The check is best-effort. A change the user committed without a `TASK` prefix, or left uncommitted, is theirs and is not a discrepancy. Once the manifest reconciles, treat it as the definitive changeset.

## Step 2: Obtain the Diff

For each file in the manifest changeset run `git diff {baseCommit} HEAD -- {file}`.

The diff is commit to commit, so the user's uncommitted edits in the working tree never enter the review, and a bare `git diff` would miss committed tasks. If the user also committed to a changeset file inside the range, the diff includes their change; review it and note the assumption.

Never stage, commit, or otherwise modify git history.
