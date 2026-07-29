# {designSetId} Option {n}: {Option Name}

- **Design Set**: {designSetId}
- **Option**: {n} of 3
- **Spec**: {path to the spec this design addresses}
- **Mockup**: {path to the self-contained HTML mockup}

## Design Concept

{The core idea of this option in 2-3 sentences: what shape the UI takes and why this approach fits the spec.}

## Layout

```text
{ASCII wireframe of the primary screen(s)}
```

{Description of the layout: regions, hierarchy, responsive behavior.}

## Component Inventory

| Component | Source | Notes |
| --------- | ------ | ----- |
| {component name} | reused — {existing component path} | {adaptation needed, if any} |
| {component name} | new | {what it renders and where it lives} |

## Interaction & States

- **Primary interactions**: {clicks, edits, navigation flows}
- **Loading**: {what the user sees while data loads}
- **Empty**: {what the user sees with no data}
- **Error**: {how failures are surfaced}

## Accessibility Notes

{Keyboard navigation, focus order, ARIA considerations, contrast concerns specific to this option.}

## Trade-offs

- {What this option does well}
- {What it sacrifices relative to the other options}
