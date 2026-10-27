# Design Skills Catalogue

The skills `ui-ux-sop` routes to. None are vendored in this repo — each installs from its own
upstream (see `install_companions()` in `install.sh`) and keeps its own licence.

## At a Glance

| Skill | Upstream | Licence | Best at | Weak at |
|---|---|---|---|---|
| `ui-ux-pro-max` | [nextlevelbuilder/ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | MIT | Searchable data: 161 palettes, 57 font pairings, 161 product types, 99 UX rules, chart types, per-stack guidance via a Python CLI | No taste filter — its style catalogue happily recommends glassmorphism and gradients; no visual verification loop |
| `design-taste-frontend` | [Leonxlnx/taste-skill](https://github.com/Leonxlnx/taste-skill) | MIT | Marketing pages: dealt directions from a 247-site study, strict anti-slop house rules, a browser inspector that measures them | Opinionated bans (no gradients, no monospace, no eyebrows) can clash with a brief; needs Node 22 + Chrome for its scripts |
| `interface-design` | [Dammyjay93/interface-design](https://github.com/Dammyjay93/interface-design) | MIT | App screens: intent-first, domain exploration, signature element, swap/squint/token tests, subtle layering | Explicitly not for marketing pages; saves to its own `.interface-design/system.md` (overridden → `DESIGN.md`) |
| `impeccable` | [pbakaus/impeccable](https://github.com/pbakaus/impeccable) | Apache-2.0 | Breadth: 23 sub-commands (critique, audit, polish, bolder, quieter, harden, typeset, layout, clarify, live variants), `PRODUCT.md` / `DESIGN.md` context, a design-detector hook, native iOS/Android | Heavy: downloads a checksum-verified binary on first run; many sub-commands to choose from |
| `emil-design-eng`, `animate`, `review-animations`, `improve-animations`, `find-animation-opportunities`, `animation-performance`, `apple-design`, `mobile-native`, `break-ui`, `prototype` | [emilkowalski/skills](https://github.com/emilkowalski/skills) | MIT | Motion craft (easing, springs, interruptibility, composite-only properties), phone-native web feel, worst-case data stress tests | Motion examples lean React / Framer Motion; translate to Alpine/CSS for Livewire |
| `shadcn` | [shadcn-ui/ui](https://github.com/shadcn-ui/ui) | MIT | React projects with `components.json`: CLI search/add, composition rules, semantic tokens, forms with `Field` | Irrelevant to Livewire/Flux projects |

## Compared with ui-ux-pro-max

| Dimension | ui-ux-pro-max | What the others add |
|---|---|---|
| Direction | Reasoning rules pick a style from a catalogue | taste deals researched directions; interface-design derives one from the product's domain |
| Anti-slop | A few anti-patterns per product type | taste house rules + inspector; interface-design swap/signature tests; impeccable craft floor |
| Verification | Static pre-delivery checklist | taste and impeccable look at the rendered result; break-ui renders worst-case data |
| Motion | Duration/easing rules | Emil: a decision framework (should it animate at all?), springs, gestures, performance pipeline |
| Design memory | `design-system/MASTER.md` + page overrides | impeccable `PRODUCT.md` / `DESIGN.md` (adopted as the single file) |
| Component build | Stack guidelines text | shadcn CLI and composition rules; `livewire-flux` in this toolkit |
| Review | Checklist | impeccable critique (heuristic scoring) and audit; emil Before/After table |

**Verdict:** keep ui-ux-pro-max as the data source; let taste, interface-design and impeccable
make the decisions.

## Install

The toolkit installer runs these as companions. To install one by hand:

```bash
npx skills add nextlevelbuilder/ui-ux-pro-max-skill -g -y --agent claude-code
npx skills add Leonxlnx/taste-skill -g -y --agent claude-code
npx skills add Dammyjay93/interface-design -g -y --agent claude-code
npx skills add pbakaus/impeccable -g -y --agent claude-code
npx skills add emilkowalski/skills -g -y --agent claude-code
npx skills add shadcn-ui/ui -g -y --agent claude-code
```

Plugin route, where the upstream offers one: `/plugin marketplace add Leonxlnx/taste-skill`,
`/plugin marketplace add Dammyjay93/interface-design`, `/plugin marketplace add pbakaus/impeccable`.

## Fallback Rules When a Skill Is Missing

Apply these by hand, then keep going.

| Missing | Minimum rules to apply |
|---|---|
| `design-taste-frontend` | Headlines ≤2 lines; one `h1` saying what this is; no eyebrow pills, monospace labels, 01/02/03 numbering, hairline rules, gradients, resting shadows, glass, emoji, icon-card rows, styled accent word; ≤2 typefaces; fill space with scale, real content or real pictures |
| `interface-design` | Name the human, the verb and the feel; pick a signature element; one focal point; one accent colour; one depth strategy; whisper-quiet surface steps; run swap/squint/token tests |
| `impeccable` | Refinement preserves identity and copy; redesign replaces the look but keeps content and function; check every state, i18n length, error paths before calling it polished |
| Emil motion skills | Ask whether it should animate at all; transform/opacity only; ease-out to enter, shorter exit; never from `scale(0)`; origin-aware popovers; interruptible CSS transitions; honour reduced motion |
| `break-ui` | Render with a 60-char name, an unbroken email, a one-letter name, missing fields, 0 and 1,000,000 items, non-Latin text and emoji; fix every overflow |
| `shadcn` | Existing component first; built-in variants; semantic colours; `gap-*` not `space-*`; Dialog/Sheet always titled |
| `ui-ux-pro-max` | AA contrast, 44px touch targets, 16px body, 1.5 line height, 4/8pt spacing, semantic tokens, skeleton after 300ms |
