---
name: frontend-design
description: Visual design rules for building frontend UIs — hierarchy, spacing, typography, color, depth, and polish, distilled from "Refactoring UI" (Wathan & Schoger). Use when designing, mocking up, or implementing any user-facing frontend code (components, views, templates, stylesheets, HTML mockups).
---

Good interfaces come from constraint systems, not per-decision taste. Define a small set of scales up front — spacing, type sizes, colors, shadows, radii — and make every visual decision by picking from a scale, never by inventing a one-off value. When two options from a scale both look plausible, try each and keep the better one; never split the difference with a new value. If the project already has a design system or tokens, its scales override the defaults given here.

## Start From the Feature, Not the Layout

- Design a piece of real functionality first (a form, a card, a table row); shells, navigation, and page chrome come after there is content to frame.
- Design the narrow (~400px) layout first, then expand it for wider viewports.
- Choose a personality and hold it consistently — it is carried by font choice, color, and border radius. Rounded corners and playful color read friendly; square corners, blue, and a neutral serif/sans read serious. Do not mix personalities in one interface.
- Design in low fidelity for structure decisions and defer detail: get hierarchy and layout right with real content before polishing shadows and color.

## Visual Hierarchy

Hierarchy is the most important design skill: make important elements prominent and — just as deliberately — make everything else recede.

- **Size is not the only lever.** Prefer font weight and color to communicate importance. Use two or three text colors (dark for primary content, grey for secondary, lighter grey for tertiary) and two weights (400/normal, 600–700 for emphasis). Never use font weights under 400 for UI text.
- **Never use grey text on a colored background.** Reduce prominence by picking a color closer to the background hue (e.g. light blue on dark blue), not by lowering opacity or using literal grey.
- **Emphasize by de-emphasizing.** When an element won't stand out, mute its competitors instead of amplifying it.
<!-- markdownlint-disable MD034 -->
- **Labels are a last resort.** Format data so it explains itself ("12 left in stock", "j.doe@example.com"); when a label is needed, treat it as secondary and combine it into the value where possible.
<!-- markdownlint-enable MD034 -->
- **Visual hierarchy is not document hierarchy.** Section titles often work best small and muted — they are labels, not the content users came for. Pick heading sizes for their visual role, not their semantic level.
- **Actions have a hierarchy too.** One primary action per view (solid, high-contrast background); secondary actions get outline or lower-contrast treatment; tertiary actions look like links. Destructive actions are only big and red when they are the primary action — otherwise a secondary/tertiary treatment with a confirmation step is calmer.

## Layout and Spacing

- **Start with too much white space, then remove** until satisfied — not the reverse. Cramped interfaces are the default failure mode.
- **Make spacing unambiguous.** Space between groups must exceed space within groups; every element should sit visibly closer to its own group than to its neighbors (labels near their inputs, headings near their body text).
- **Use a non-linear spacing scale** where adjacent steps differ by ≥ ~25%, e.g. `4, 8, 12, 16, 24, 32, 48, 64, 96, 128px`. All margins, padding, and gaps come from this scale.
- **Don't fill the screen.** Give content a max-width (text ~65ch; forms and cards often 400–600px) and center it. If part of a layout needs to be narrow, keep it narrow even when the page is wide.
- **Prefer fixed widths to percentages** for sidebars and panels; only the main content area should flex. Element internals should not scale proportionally: a large button has proportionally less padding than a small one, a large headline proportionally tighter line-height.

## Typography

- **Use a type scale** of hand-picked pixel values, e.g. `12, 14, 16, 18, 20, 24, 30, 36, 48, 60, 72px`. Avoid em-based sizing for the scale itself.
- **Body text**: 16px default, 45–75 characters per line, line-height 1.5–1.75. Line-height and size are inversely proportional — headlines want 1.1–1.3.
- **Fonts**: the system font stack is a safe default; if choosing a font, prefer one with 5+ weights and avoid condensed faces for UI text.
- **Alignment**: left-align long text (never justify, never center more than ~3 lines); right-align numbers in tables; align mixed font sizes by baseline, not vertical center.
- **Letter-spacing**: leave it alone except to slightly tighten large headlines and to widen all-caps labels (`letter-spacing: 0.05em` with a smaller size and muted weight makes a good section label).
- **Links within UI**: not every link needs blue and an underline. In navs, lists, and cards, links can look like plain text with a hover affordance; reserve loud link styling for links inside prose.

## Color

- **Work in HSL**, not hex, so relationships between shades are legible.
- **Define the full palette up front**: 8–10 shades of grey (near-black to near-white — the darkest text color is a very dark grey, not `#000`), 5–10 shades of the primary color, and a shade range for each accent (a success green, warning yellow, danger red at minimum). Pick the middle (500) shade first — the one that works as a button background — then fill in light shades (backgrounds, tints) and dark shades (text on tinted backgrounds).
- **Keep saturation up as lightness moves away from 50%**, or light shades go washed-out and dark shades go muddy; rotate hue slightly toward the nearest bright hue (yellow, cyan, magenta) to lighten and away to darken without losing intensity.
- **Greys can be temperature-shifted** — saturate them lightly with blue for a cooler feel or with yellow/orange for warmth — but choose one temperature and use it everywhere.
- **Accessibility**: body text needs 4.5:1 contrast against its background; large text (≥ ~18px bold / 24px regular) needs 3:1. On colored backgrounds, prefer flipping the contrast — dark colored text on a light colored tint — over white text on a middle shade. Never encode meaning in color alone; pair it with an icon, weight, or text change.

## Depth

- **Assume light from above.** Raised elements (buttons, cards) get a subtle lighter top edge and a shadow below; inset elements (wells, checked toggles, input fields) get a small dark inner shadow at the top.
- **Define an elevation scale of ~5 shadows** and assign them by conceptual height: small tight shadows for buttons and inputs, medium for dropdowns and popovers, large soft shadows for modals. Combine two shadows per level — a large soft ambient shadow and a tighter darker contact shadow.
- **Use fewer borders.** Before reaching for `border: 1px solid`, try a box-shadow, a background-color difference between adjacent surfaces, or more spacing. Interfaces with few borders look cleaner.
- Depth also comes from overlap — cards straddling a background transition, avatars overlapping a list edge — and works even in flat designs via solid (non-blurred) shadows and background color steps.

## Images and Media

- Text over an image needs guaranteed contrast: add a dark overlay, colorize the image with a single hue, or lower the image contrast — plus optionally a subtle text shadow. Never rely on the raw photo.
- Everything has an intended size: don't scale icons up (enclose a normal-size icon in a tinted shape instead) and don't scale screenshots down to unreadability (crop to the relevant region or use a simplified illustration).
- User-provided images get fixed-size containers with center-crop (`object-fit: cover`) and a subtle inner shadow or ring so white images don't bleed into white backgrounds.

## Finishing Touches

- Upgrade browser defaults: replace list bullets with icons or checkmarks, style blockquotes and form controls, use accent borders (a colored top edge on a card, a left border on an alert, an underline on the active tab) to add polish cheaply.
- Backgrounds don't have to be white — a slight tint, a gentle gradient (two hues ≤ 30° apart), or a very low-contrast pattern lifts a design.
- **Design the empty state.** For any list/table/dashboard view, the zero-data state shows a prompt and the primary call to action, not an empty grid with disabled chrome.
- Rethink component clichés when it helps the content: dropdown menus can have sections and icons; radio groups can be selectable cards; a table column can combine related fields (name over email) rather than one column per attribute.

## Example: Applying the System

```html
<!-- BAD: one-off values, black text, grey-on-blue, border-heavy, ambiguous spacing -->
<div style="border:1px solid #999; padding:7px; margin-top:13px;">
  <h2 style="font-size:22px; color:#000;">Invoices</h2>
  <p style="color:#ccc; background:#3b5bcc;">3 unpaid</p>
  <button style="background:#3b5bcc; color:#eee; font-weight:300;">New</button>
  <button style="background:#c00;">Delete all</button>  <!-- destructive action shouting -->
</div>

<!-- GOOD: scale values only, shadow instead of border, hierarchy via weight/color,
     one primary action, destructive action demoted to a quiet tertiary link -->
<div style="background:#fff; border-radius:8px; padding:24px;
            box-shadow:0 4px 6px hsl(220 40% 20% / 0.1), 0 1px 3px hsl(220 40% 20% / 0.08);">
  <p style="font-size:12px; font-weight:600; letter-spacing:0.05em;
            text-transform:uppercase; color:hsl(220 10% 55%); margin:0 0 4px;">Invoices</p>
  <p style="font-size:24px; font-weight:700; color:hsl(220 25% 15%); margin:0 0 16px;">
    3 unpaid <span style="font-size:14px; font-weight:400; color:hsl(220 10% 45%);">of 12 total</span>
  </p>
  <button style="background:hsl(230 65% 50%); color:#fff; font-weight:600;
                 padding:8px 16px; border:none; border-radius:6px;">New invoice</button>
  <a href="#" style="font-size:14px; color:hsl(220 10% 45%); margin-left:16px;">Delete all…</a>
</div>
```

## Checklist

Verify every design or frontend change against this rubric before returning it:

- [ ] Every spacing, font-size, color, shadow, and radius value comes from a defined scale (the project's tokens, or the defaults above) — no one-off values.
- [ ] Hierarchy reads at a squint: one clearly primary element per view, secondary content visibly muted via weight/color, one primary action.
- [ ] Spacing between groups exceeds spacing within groups everywhere.
- [ ] Body text: ≥ 4.5:1 contrast, 45–75ch measure, line-height ≥ 1.5; no pure black text, no grey text on colored backgrounds.
- [ ] Borders appear only where a shadow, background shift, or spacing could not do the job.
- [ ] Empty states are designed for any view that can have zero data.
- [ ] The result honors the project's existing design system where one exists.
