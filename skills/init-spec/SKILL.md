---
name: init-spec
argument-hint: [feature-description]
description: Create a new spec in docs/specs from the spec template, with the next sequential spec ID and today's date pre-filled.
---

Specs live in `docs/specs/` and are named `SPEC-{ID}.md`, where `{ID}` is a zero-filled incrementing integer (`SPEC-001.md`, `SPEC-002.md`, …). Create a new spec as follows.

## 1. Determine the next spec ID

Compute the next ID deterministically from the files already in `docs/specs/` (run from the repository root):

```bash
last=$(ls docs/specs 2>/dev/null | sed -nE 's/^SPEC-0*([0-9]+)\.md$/\1/p' | sort -n | tail -1)
printf 'SPEC-%03d\n' $(( ${last:-0} + 1 ))
```

Rules the computation must honor:

- If `docs/specs/` is missing or contains no specs, the first ID is `SPEC-001`.
- The next ID is always `max + 1` over existing IDs — never reuse a gap left by a deleted spec.
- Zero-fill to 3 digits minimum; beyond 999 the number simply grows (`SPEC-1000`).

## 2. Create the spec from the template

1. Create `docs/specs/` if it does not exist.
2. Copy `skills/init-spec/reference/spec-template.md` to `docs/specs/SPEC-{ID}.md`.
3. Pre-populate the metadata fields — and only the metadata fields:
   - Replace every `[Spec ID]` placeholder (title and `Spec ID:` field) with `SPEC-{ID}`.
   - Replace `YYYY-MM-DD` in the `Spec Date:` field with today's date.
4. Leave all other sections exactly as they appear in the template — the placeholders are for the spec author to fill in. If the user supplied a feature description when invoking the skill, derive a short `[Feature Name]` for the title from it and use it to draft the Spec Statement (Section 1) as a starting point; otherwise leave both placeholders untouched.

## 3. Report

Reply with the path of the created spec file (e.g. `docs/specs/SPEC-003.md`) so the user can start filling it in.
