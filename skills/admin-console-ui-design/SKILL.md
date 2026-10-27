---
name: admin-console-ui-design
description: "Apply a calm, compact, low-noise admin console visual and interaction system (React + Tailwind-style design tokens). Use when creating or updating admin pages, app shells, cards, tables, forms, dialogs, status states, login screens, or shared UI primitives."
---

# Admin Console UI Design Skill

Apply this system to the app's page layer and to the shared UI package. Goal: a calm, compact, multi-tenant admin console that stays readable in more than one language.

## Stack and source of truth

- React 19 function components, a file-based or config-driven router, a data-fetching/caching layer for server state, Tailwind CSS v4.
- Import shared primitives from the shared UI package (e.g. `@app/ui`); app-specific wrappers live in the app's components directory (e.g. `app/src/components`).
- Icons from `lucide-react`; toasts from `sonner`; design tokens defined once in the app's global stylesheet (e.g. `app/src/styles.css`).
- Do not introduce another modal, table, button, form-field, or status component — extend the shared one instead.

## Visual language

- Light, low-noise surface: near-white background, white cards, muted blue-gray text.
- Primary is a strong blue; success/warning/info/destructive appear mainly through badges and small state surfaces.
- Cards: `rounded-xl`, `border-border`, `bg-card`, `shadow-sm`.
- Controls: height 9, `rounded-md`, small shadows; labels are short and placed above fields.
- Density: generous section rhythm (`space-y-6`) but compact rows and controls.
- Motion is subtle: color transitions, row hover, and a small card lift only.
- Use semantic tokens (`background`, `card`, `muted`, `border`, `primary`, `destructive`) instead of hardcoded colors. Small icon tiles may use pastel color pairings; the login hero is the only dark marketing surface.

## App shell

- Desktop: fixed left sidebar, width 64, white card background, right border.
- Sidebar brand row: rounded primary logo tile, product name, short muted tagline.
- Nav items: icon + label, muted by default; hover uses `bg-muted`; active uses `bg-primary/10 text-primary`.
- Sidebar footer: workspace/org block, signed-in user identifier, logout button.
- Header: sticky height 16, blurred `bg-background/90`, current-workspace context, locale switch, and a role pill with a green status dot.
- Mobile: hide the sidebar; show the logo in the header and horizontal scrollable nav chips under the header.
- Main: centered `max-w-7xl`, responsive padding, top padding 6/8.

## Page recipe

1. Start with one page header: `text-2xl font-semibold tracking-tight` title, one-line muted description, right-aligned actions.
2. Wrap the page in `space-y-6`.
3. Prefer:
   - KPI row: `grid gap-4 sm:grid-cols-2 xl:grid-cols-4`.
   - List row: cards in `md:grid-cols-2 xl:grid-cols-3`.
   - Data/detail row: `lg:grid-cols-[1.5fr_1fr]` or another intentional ratio.
4. Show exactly one primary action per page section. Secondary actions are `outline`; tertiary row actions are `ghost`.

## Core patterns

### Stat card

- Card content is a horizontal flex.
- Left: muted label and a large `text-3xl font-semibold tracking-tight` value.
- Right: 44px rounded icon tile with a pastel background.
- Keep tiles consistent: sky/violet/amber/emerald may be used for categorical KPIs.

### Object card

- Rounded icon or glyph tile as the identity mark (no decorative emoji).
- Outline badge for category or domain.
- Title `text-base font-semibold`; description muted and clamped to two lines.
- Add key metadata (counts, owner, locale pair, timestamps) in small muted text.
- Footer separated by a top border, with timestamp and an explicit "open" affordance.
- Card hover: `-translate-y-0.5 shadow-md`.

### Table

- Wrap the table primitive in a card; the table container scrolls horizontally.
- Header cells are small, uppercase, muted, and tracking-wide.
- Rows use hover `bg-muted/40`.
- IDs and hashes use `font-mono text-xs`.
- Primary row link can be `font-medium hover:underline`.
- Row actions are `ghost` or small icon buttons with an `aria-label`.

### Forms and dialogs

- Forms use vertical `space-y-4`; related fields use 2/3-column grids.
- Inputs and selects come from the shared UI package; icons inside inputs use absolute positioning with matching left padding.
- Use the shared dialog primitive with title, optional muted description, body, and right-aligned footer.
- Footer order: outline Cancel, then primary Submit. Disable submit while pending or when required fields are empty.
- Show pending text or a spinner inside the submit button.
- File upload may be an outline button label wrapping a hidden input.

### Status and metadata

Use the shared `StatusBadge` (one component, a semantic tone map) rather than per-page colors. Keep the tone mapping stable across the app:

- `idle` / `draft` / `queued` / `disabled` / `unassigned`: `muted`
- `submitted` / `assigned` / `claimed`: `info`
- `running` / `partial` / `in_progress`: `warning`
- `done` / `active` / `complete` / `published`: `success`
- `failed` / `rejected` / `forbidden`: `destructive`
- anything unmapped: `outline`

Extending the map is a shared-package change: add the domain status there, never inline a class in a page.

Small metadata rows use `rounded-lg bg-muted/60 px-4 py-3` with the label left and the badge/value right.

### Page states

Every fetched screen must handle all four states:

- Loading: centered muted text or skeleton.
- Error: destructive-tinted block with a retry action.
- Empty: dashed border, `bg-card/40`, centered title/description/action.
- Success: normal layout.

On mutations: toast success/error, then invalidate every affected query, and close the dialog only after success.

## Login screen

- Desktop uses a split layout: dark product story on the left, focused form on the right.
- Dark side: `bg-slate-950`, radial blue/green gradients, bold product claim, small `white/5` feature cards.
- Right side: centered `max-w-sm` card with identifier/password fields, icons inside inputs, full-width primary submit, inline destructive error text, and a dashed demo-account hint.
- Keep the form minimal; marketing content stays on the hero side.

## Content rules

- Every user-visible string comes from the app's i18n hook; never hardcode new labels in any language.
- Dates go through the shared date formatter, which takes the active locale.
- Use `→` for directional flows (source → target, before → after).
- Use `—` for absent values.
- Sentence case for controls; no exclamation marks, no decorative copy.
- Icons are decorative unless paired with text or given an `aria-label`.

## Accessibility and interaction

- Focus stays visible on every interactive element; do not remove focus rings.
- Dialog: Escape and close button work, focus traps inside and returns to the trigger.
- Nav and row actions are reachable by keyboard in DOM order.
- Status is never conveyed by color alone — the badge carries text.

## Portability

Placeholders that follow the project, not this skill:

- shared package name (e.g. `@app/ui`), app/page directory layout, global stylesheet path.
- router / query library choice, i18n hook name, date formatter, status vocabulary.
- brand name, tagline, product claim, and role labels.

Everything else (tokens, spacing, card/table/form recipes, state mapping, motion) is normative.

## Definition of done

- Desktop and mobile layouts are verified.
- Loading, error, empty, and success states exist for fetched content.
- Colors use design tokens or the approved status tones.
- Shared primitives are reused; no copied local variants.
- Format and type-check/lint scripts pass.
- Navigation focus, dialog Escape/close behavior, and form labels are intact.
