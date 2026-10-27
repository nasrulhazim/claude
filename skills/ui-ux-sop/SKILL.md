---
name: ui-ux-sop
metadata:
  compatible_agents: [claude-code]
  tags: [ui, ux, design, sop, routing, design-system, motion, review, livewire, flux, shadcn]
description: >
  Standard operating procedure for every piece of UI/UX work — the entry point that decides
  which design skills to load, in what order, and which one wins when they disagree. Classifies
  the surface (page, app screen, component, motion, review), routes it to at most three skills
  (design-taste-frontend for marketing pages, interface-design for app screens, impeccable for
  refine/critique/audit/design systems, Emil Kowalski's animate and review-animations for motion,
  break-ui for edge cases, shadcn or livewire-flux to build, ui-ux-pro-max as a data source),
  sets a precedence order (brief → DESIGN.md → accessibility floor → primary skill → data), keeps
  one design memory file (DESIGN.md), and applies house overrides such as never resizing the
  browser. Use this skill before building or changing anything users see — new pages, redesigns,
  landing pages, dashboards, admin screens, components, colour/typography/layout decisions,
  animation, design tokens, UI reviews and audits — including "design this page", "build a
  dashboard", "redesign the settings screen", "make this look less generic", "review this UI",
  "add an animation", "set up a design system", "reka page ni", "buat dashboard", "cantikkan UI
  ni", "nampak macam AI buat", "semak UI ni", "tambah animasi", or "buat design system".
---

# UI/UX SOP

One procedure for all design work, so every session and every subagent reaches for the same
skills in the same order. This skill does not carry design knowledge itself — it routes to the
skills that do and settles their conflicts.

**Load at most 3 skills per task.** The routing table picks them. Loading every design skill
"to be thorough" produces contradictory rules and a muddled result.

## Command Reference

| Command | What it does |
|---|---|
| `/ui-ux-sop` | Classify the current task, name the skills to load, then run the procedure |
| `/ui-ux-sop route <task>` | Print the routing decision only (surface, primary, supporting) |
| `/ui-ux-sop check` | Run the pre-delivery checklist against the current UI change |

---

## When to Use

- Before any change that alters how something looks, feels, moves or is interacted with
- When briefing a subagent (`ui-designer`, `laravel-developer`, `typescript-developer`) on UI work
- When two design skills give conflicting advice

**Not for:** backend logic, APIs, database work, or logos and brand identity (`svg-logo-system`,
`product-brand-kit` own those).

---

## 1. Classify the Surface

| Surface | What it is | Examples |
|---|---|---|
| **Page** | Read and scrolled | Landing, marketing site, product page, portfolio, event page |
| **App screen** | Worked in | Dashboard, admin, settings, billing, filtered tables, inbox, editor |
| **Component** | One piece | Button, modal, form, card, table, chart |
| **Motion** | Something moves | Transitions, gestures, toasts, enter/exit |
| **Review** | Judge what exists | Critique, audit, accessibility check, edge-case stress test |

A product site that shows its app is a **Page**; the app fragments inside it follow App-screen
rules.

---

## 2. Routing

| Task | Primary skill | Supporting (only if needed) |
|---|---|---|
| New page / landing | `design-taste-frontend` | `ui-ux-pro-max` (`--design-system` for product-type palette and font reasoning) |
| New app screen (Laravel) | `interface-design` | `livewire-flux` (build), `ui-ux-pro-max` (UX rules), `dataviz` (charts) |
| New app screen (React/Next, has `components.json`) | `interface-design` | `shadcn` (build), `dataviz` |
| Single component | `livewire-flux` or `shadcn` | `ui-ux-pro-max` Quick Reference for the matching category |
| Design system / tokens | `impeccable` (`init` → `document` / `extract`) | `ui-ux-pro-max --design-system` as raw input |
| Refine existing UI | `impeccable` — `polish` · `bolder` · `quieter` · `distill` · `layout` · `typeset` · `colorize` · `harden` · `onboard` | — |
| UX copy, labels, errors | `impeccable clarify` | `design:ux-copy` |
| Build motion | `animate` | `apple-design` for gestures, springs, sheets |
| Find where motion belongs | `find-animation-opportunities` | — |
| Review motion / fix jank | `review-animations` · `animation-performance` | `improve-animations` for a roadmap |
| Web app must feel native on a phone | `mobile-native` | — |
| Design critique | `impeccable critique` | `emil-design-eng` (Before/After table) |
| Technical audit | `impeccable audit` | `web-design-guidelines`, `ui-accessibility` |
| Edge-case stress test | `break-ui` | — |
| Explore variants | `prototype` (React) or `impeccable live` / `generate` | — |

**Retired from routing:** `frontend-design` and `bencium-controlled-ux-designer` — superseded by
`design-taste-frontend` and `interface-design`. Load them only when the user names them.

Any skill above that is not installed: say so in one line, apply its rules from
[references/design-skills.md](references/design-skills.md) by hand, and keep going.

---

## 3. Precedence

When two skills disagree, the higher row wins.

| # | Source | Note |
|---|---|---|
| 1 | The user's explicit brief | Pinned fonts, palettes, eras and references are honoured even when a skill bans them |
| 2 | Project `DESIGN.md`, the existing component library (Flux / shadcn), sibling components | Refinement preserves the incumbent look; only a requested redesign replaces it |
| 3 | Accessibility floor | WCAG 2.1 AA, keyboard path, visible focus, contrast in **both** themes, reduced motion. Never traded away |
| 4 | Primary skill for the surface | Pages → taste house rules. App screens → interface-design craft rules |
| 5 | `ui-ux-pro-max` data | Palettes, fonts and product reasoning are inputs, not verdicts. Its style catalogue (glassmorphism, neumorphism, gradients) loses to taste house rules on pages |

---

## 4. The Run

| Step | Do | Output |
|---|---|---|
| 1. Context | Read `DESIGN.md` / `PRODUCT.md` (legacy: `design-system/MASTER.md`, `.interface-design/system.md`). Detect the stack: Flux? `components.json`? Tailwind theme? Look at 2–3 sibling screens | Known constraints |
| 2. Intent | Who is the human, what is the verb, what should it feel like. Vague brief → ask one question, or state the assumption and proceed | Three-line brief |
| 3. Direction | Pages: taste deals directions, pick one. App screens: domain, colour world, signature, defaults rejected. Costly or ambiguous → 2–3 options with a recommendation | Chosen direction |
| 4. Build | Existing components first, semantic tokens only, every state: hover, focus, disabled, loading, empty, error | Working code |
| 5. Gate | Swap, squint, signature and token tests. Pages: taste's inspector. Anything showing user data: `break-ui` | Fix list cleared |
| 6. Look | Render and look at the user's real viewport (§5). Batch fixes, then look once more | Verified UI |
| 7. Persist | Write the decisions to `DESIGN.md` | Updated `DESIGN.md` |

---

## 5. House Overrides

These apply on top of every routed skill.

| Rule | Overrides |
|---|---|
| **One design memory file: `DESIGN.md`** at the project root, plus `PRODUCT.md` for product truth. Don't run `ui-ux-pro-max --persist` and don't create `.interface-design/system.md` — write those decisions into `DESIGN.md` | ui-ux-pro-max Step 2b, interface-design save step |
| **Never resize the browser.** No multi-width screenshot runs and no device emulation unless the user's prompt asks for a responsive or mobile check | taste "three widths", interface-design "desktop and mobile", impeccable "sizes it ships at" |
| Responsive correctness is checked by reading the CSS (breakpoints, `min-h-dvh`, no fixed widths, no horizontal scroll), not by resizing | — |
| Dark mode is mandatory in Kickoff apps: every new token and pattern defined for light and dark | — |
| Don't narrate modes or skill names to the user — give the decision and the reason | — |
| A subagent doing UI gets "load the `ui-ux-sop` skill first" in its brief | — |
| Missing tooling (impeccable binary, Node 22 + Chrome for taste scripts, Python for ui-ux-pro-max) → one line, apply the rules by hand, continue | — |

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "I'll load every design skill to be safe" | They contradict each other (taste bans gradients; ui-ux-pro-max catalogues them). Three skills, picked by the table. |
| "It's just a small component, skip the SOP" | Small components are where defaults leak in. Routing a component takes one row: stack skill + Quick Reference. |
| "The skill says screenshot at three widths" | House override: the user's window is never resized. Read the CSS for responsiveness. |
| "This palette from ui-ux-pro-max looks good, ship it" | It is input. Check it against the brief, `DESIGN.md` and the primary skill's rules first. |
| "I'll save the design system in the skill's own folder" | Then the next skill never finds it. `DESIGN.md` is the one file every skill reads. |
| "Accessibility can wait until polish" | It is precedence row 3 — above every aesthetic skill. Retrofitting costs a rewrite. |

---

## Red Flags

- More than three design skills loaded for one task
- A new page built with no direction chosen — centred headline, eyebrow pill, three icon cards
- `resize_window`, a viewport override, or a "mobile screenshot" the user did not ask for
- `design-system/MASTER.md` or `.interface-design/system.md` created in a project with no prior one
- Raw hex or `bg-blue-500` inside a component instead of a semantic token
- A pattern defined for light mode only
- Missing empty, loading or error state on a data view
- A refinement task that quietly changed the product's identity or copy

---

## Verification

- [ ] Surface classified and routed; no more than three skills loaded
- [ ] `DESIGN.md` read before editing, updated after
- [ ] One focal point per view; squint test readable
- [ ] Swap test: replacing the font or layout with the generic one would visibly hurt
- [ ] Semantic tokens only, defined for light and dark
- [ ] Hover, focus-visible, active, disabled, loading, empty and error states present
- [ ] Contrast AA in both themes, labelled icon buttons, keyboard path, modal focus trap
- [ ] Motion purposeful, transform/opacity only, ≤300ms micro-interactions, reduced-motion honoured
- [ ] Long names, zero items, huge counts and non-Latin text don't break the layout
- [ ] No eyebrow pills, icon-in-tinted-square rows, gradient blobs, emoji icons, grey-bar fake dashboards or invented metrics
- [ ] Looked at the result at the user's real viewport

---

## Reference Files

| File | Contents |
|---|---|
| [references/design-skills.md](references/design-skills.md) | Catalogue of the routed design skills — source, licence, strengths, limits, how each compares to ui-ux-pro-max, install commands, and the rules to apply by hand when one is missing |
