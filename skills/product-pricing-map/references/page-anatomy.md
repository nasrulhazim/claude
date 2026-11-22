# Page Anatomy

One self-contained HTML file. No build step, no external data. Fonts from Google Fonts and the
diagram library from a CDN are the only external loads; the page must still read if they fail.

## Section order

| # | Section (nav label) | Content |
|---|---|---|
| — | Header | Eyebrow `Product · pricing strategy · internal draft <date>`, H1 "Every feature, how it's charged, and why", a lede naming the rungs, a meta row: sources, "proposed = a suggestion, not decided", currency/FX/tax |
| 1 | Ladder | Rung cards (step + status, type pill, price, one line) and 2–3 callouts |
| 2 | Map / Diagram | Pricing types with what sits under each; see `diagram-patterns.md` |
| 3 | Simulator | See `simulator-patterns.md` |
| 4… | One section per rung | Plan cards (ticket-stub style) or a range table; why under each price |
| n | Per use | Table: item, proposed price, our cost, today, why |
| n+1 | Services & add-ons | Table: item, type pill, price, why |
| n+2 | All features | The feature ledger |
| n+3 | Decisions | Numbered list, most blocking first |
| — | Footer | Provenance with date, which figures are estimates, "Internal — do not share outside <company>" |

## Layout

- One reading column, `max-width: 1080–1120px`, sections in a `display:grid; gap:56px` wrapper.
- Sticky horizontal section nav at the top, scrollable sideways on phones, honouring
  `env(safe-area-inset-*)`; `scroll-margin-top` on sections so anchors don't hide headings.
- Tables sit in an `overflow-x:auto` wrapper with a `min-width` so they scroll rather than
  squash. Wide diagrams scroll sideways in their own box.
- Light and dark themes from `prefers-color-scheme`, plus `:root[data-theme]` overrides.
- Numbers use `font-variant-numeric: tabular-nums`; prices in a display weight.
- Each product gets its own display font and accent (the error tool used a signal-trace line,
  the event tool ticket-stub cards) so the three maps don't look like one template.

## Components

| Component | Notes |
|---|---|
| Pill | `.pill.t-<key>` from the pricing-type tokens; `.t-warn` for state pills |
| Rung | Step label in mono (`1 · decided`, `4 · proposed`), pill, price, one-line purpose |
| Plan card | Head (pill, price with `<small>` unit, sub-line), dashed perforation, body list, `.why` with a left rule; `.hl` border on the recommended plan |
| Callout | Title + 1 paragraph or short list; used for "why this rung exists" and blockers |
| State pill | Live (`t-inc`), Built, off / Not built / No gate (`t-warn`) |
| Decisions | `<ol>`, each item a bold question then the facts needed to answer it |

## Feature ledger data shape

Keep the data as one array in the script; render rows from it.

```js
// [feature, phase, type, today, why]            phase: emoji or MVP/P2/P3
// today: 'live' | 'off' | 'nb' | 'nogate'       type: keys from pricing-types.md
const F = [
  ["Ingest endpoint", "MVP", "inc", "live", "The keystone; any runtime can send."],
  ["Extra seats", "P2", "seat", "off", "Seat model built; price ID not set."],
];
```

Optional extra columns when they carry a decision: "Plan / unit" (which plan or pack holds it),
"Runs on" (their server, our control plane, our storage — decides whether it can cost us money).

Controls: one chip per type in use, plus `gap` ("Not live yet" / "No gate today"), a search
input over name and why, and a count line with `aria-live="polite"`
(`Showing 23 of 48 features · 9 not built or switched off`). An empty filter shows a row saying
nothing matches.

## Accessibility and polish

- Chips are `<button aria-pressed>`; the group has `role="group"` and an `aria-label`.
- Every input has a `<label for>`; focus rings visible on chips, inputs and nav.
- Escape any text injected with `innerHTML`.
- Respect `prefers-reduced-motion` for any transition or animated edge.
- Never use the section-sign character in the page; write "spec 3.2" or "spec, section 3.2".

## Footer

State where "Today" came from (code on a date, not the production database), that an admin
override in production may differ, that costs are estimates, and that the page is internal.
