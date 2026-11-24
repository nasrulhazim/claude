---
name: sticker-pack
metadata:
  compatible_agents: [claude-code]
  tags: [design, print, stickers, swag, vinyl, die-cut, qr, branding, events, merch]
description: >
  Print-ready sticker packs for a product or event: several clearly different visual concepts
  (5 by default), each with the same 6–8 messages, so options compare like for like. Facts come
  from the product's own docs, and only shipped features go on a sticker. Each concept direction
  comes from ui-ux-pro-max (design system, style and typography searches), adapted for vinyl:
  no glow, no banding gradients, no opacity. impeccable then critiques and audits the proofs.
  Every sticker ships as an SVG (Artwork, a hidden SafeArea layer and a CutContour layer, with
  text outlined), a 300 dpi PNG with 2 mm bleed, and a PDF with a CutContour spot-colour cut
  layer. Build gates check the 3 mm safe area, a 6 pt minimum, 4.5:1 contrast and contour cuts
  with no holes. QR codes are styled (round dots, rounded eyes, centre logo, level H), point
  only at a live URL, and must decode in zbarimg at full size and at 25%. Output includes a
  preview with a laptop-lid mockup per concept, a compare sheet, a manifest.json, and a gallery
  page (one per product) to review before printing. Many products run in parallel, one agent
  each. Use this skill whenever the user asks to "design stickers", "sticker pack", "event
  swag", "laptop stickers", "die-cut stickers", "stickers for the booth", "sticker concepts for
  each product", "buat sticker", "reka sticker", "sticker untuk event", "sticker laptop",
  "buat sticker untuk setiap produk", or "tambah concept sticker".
---

# Sticker Pack

A sticker a developer actually puts on a laptop is bold, iconic, light on text and true. This
skill turns a product's real facts into **several concepts × the same messages**, built as
print files a sticker shop can cut without a phone call. Every file is checked by a build gate,
not by eye alone.

## Command Reference

| Command | What it does |
|---|---|
| `/sticker pack <product>` | Full flow: facts → messages → 5 concepts → gates → critique → gallery |
| `/sticker concept <product> "<direction>"` | Add one more concept to an existing pack (e.g. "in the style of <event site>") |
| `/sticker products <a,b,c>` | One agent per product, in parallel, each in its own folder (`references/agent-brief.md`) |
| `/sticker gallery <product>` | Build or refresh the review page from `manifest.json` (`references/gallery-and-manifest.md`) |
| `/sticker verify <folder>` | Re-run every gate: QR decode, safe area, text size, contrast, layers, PDF spot colour |
| "design stickers", "buat sticker", "sticker untuk event" | Full flow |

## Output Layout

```
stickers/                                  # or stickers/products/<product>/ for a family
├── a-<style>/  b-<style>/  …              # one folder per concept
│   ├── svg/<nn-name>.svg                  # Artwork + SafeArea (hidden) + CutContour layers, text outlined
│   ├── png/<nn-name>-300dpi.png           # artwork only, 2 mm bleed, transparent outside the bleed
│   ├── pdf/<nn-name>.pdf                  # Artwork OCG + CutContour OCG in a Separation spot colour
│   ├── preview.png                        # all stickers at true relative size + laptop-lid mockup
│   └── build.py
├── compare.png                            # one row per concept
├── manifest.json                          # concepts → stickers (stem, title, trim_mm, shape, has_qr, paths)
├── README.md                              # concepts, sticker matrix, print spec, quantities
├── engine.py / build.py                   # shared helpers; builds everything
└── .gitignore                             # .fonts/ .preview/ __pycache__/
```

---

## The Flow

| Step | Do | Output |
|---|---|---|
| 1. Facts | Read the product's own docs: status tables, README, brand book, web copy, plus any **do-not-claim / do-not-say** list. Mark each fact *shipped / partial / planned*. Never open `.env*`, `todo.md`, keys or credentials | A facts table with a file path for each fact |
| 2. Messages | Pick 6–8 messages that every concept will carry: 1 mark, 1 core promise, 1 QR sticker, then shipped features or a culture joke that stays true | A message matrix |
| 3. Directions | `ui-ux-sop` (when present) → `ui-ux-pro-max --design-system "<product>"`, then a **style** search and a **typography** search per concept. Pick directions that are clearly different. The product's brand (logo, colours) wins over the skill's palette | 5 directions, each with the quoted skill output |
| 4. Vinyl filter | Drop what won't print: glow, neon, soft shadows, gradients that band, noise or grain, transparency, hairlines under 0.3 mm | A print-safe version of each direction (`references/concept-directions.md`) |
| 5. Build | Outlined text (HarfBuzz + fontTools), geometry and contour cuts (shapely), SVG layers, PNG and PDF via rsvg-convert, CutContour spot layer (PyMuPDF). Every gate is an `assert` (`references/engine-recipe.md`) | Files + `preview.png` per concept |
| 6. QR | Styled QR to a URL that `curl -sI` shows returning 200. Gate: zbarimg decodes the **exact** URL at 1.0 and 0.25 (`references/qr-style.md`) | Decode log |
| 7. Critique | `impeccable` critique + audit on the previews and the 300 dpi proofs, run as an isolated review if you can. Fix P0–P2. Record what you kept and why. Its `detect` reads web markup only, so `[]` on print SVGs means "not covered", not "clean" | A flagged → fixed table |
| 8. Review | `compare.png`, `manifest.json`, then a gallery page, **one per product** | A link for the user |
| 9. Commit | Only after the user has looked at the gallery, or when they ask | A commit per pack |

### Messages: same set, every concept

| # | Message type | Example shape |
|---|---|---|
| 01 | Mark only | Die-cut along the mark or a tile |
| 02 | Core promise | Round, 60–64 mm |
| 03 | QR + one line + URL | Rectangle, about 92 × 50 mm, for a laptop |
| 04–06 | One shipped feature each | Shapes that fit the idea (bubble, ticket, terminal, hexagon) |
| 07–08 | Event or culture | A ticket with date and venue, or a dev joke that is still true |

A joke must still be **true**. "Skipped is not green" is fine if the product shows skipped steps.
"One-click undo" is wrong if undo asks for a confirmation.

---

## Parallel Products

For a product family, run **one agent per product**. Brief each one with the template in
`references/agent-brief.md`. The rules that keep parallel agents from colliding:

| Rule | Why |
|---|---|
| Each agent writes only to `stickers/products/<product>/` | Sibling agents are working at the same time |
| The shared `engine.py` is read-only; copy it into your folder before changing it | One agent's change would break the others |
| No agent edits the top-level README, index.html or compare.png | The orchestrator rebuilds those |
| Fonts and venvs go to the scratchpad; `.fonts/` stays gitignored | No binary churn in git |
| Each agent reports facts with paths, skill output per concept, the sticker matrix, QR results and what it couldn't verify | The orchestrator checks; it doesn't take the report on trust |

The orchestrator then re-runs `zbarimg` on every QR PNG itself, builds one gallery per product,
and publishes it.

---

## Companions and Fallbacks

| Companion | Used for | If it's missing |
|---|---|---|
| `ui-ux-sop` | Routes the design work and settles precedence | Apply the order by hand: brief → brand → accessibility floor → direction → data |
| `ui-ux-pro-max` | Design system, style and typography per concept | Pick directions from `references/concept-directions.md` and say in the report that the skill was absent |
| `impeccable` | Critique and audit of the proofs | Hand critique: hierarchy at arm's length, brand presence on every sticker, cut-drift lines, thin necks, copy truth, concepts actually different. List P0–P3 and fix down to P2 |
| `rsvg-convert`, `zbarimg` | Rendering and the QR gate | Install them (`brew install librsvg zbar`). Without zbar there is no QR gate, so don't ship a QR |

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The marketing site says it, so it's fine on a sticker" | Marketing copy runs ahead of the code. A sticker is printed once and can't be patched. Status docs win |
| "zbar can't read the dot QR, but phones will" | Maybe. Without a decode you have no evidence. Gate at 1.0 and 0.25, then also scan a printed proof with a phone |
| "The glow looks great in the preview" | Vinyl can't print light. Glow turns into a muddy halo, and gradients band |
| "6 pt is readable on screen" | It is 2.1 mm on vinyl at arm's length. 6 pt is the floor, not the target |
| "The cut line can follow every notch" | Plotters tear narrow necks and nobody can weed tiny islands. Close notches under 3 mm and never cut a hole |
| "One concept in five colourways is five concepts" | It's one concept. Directions must differ in shape, type and idea |
| "The detector returned [] so the stickers are clean" | `impeccable detect` reads web markup. On outlined print SVGs, `[]` means not covered |
| "I'll use the event's logo to match their look" | Take the style (palette, type, motifs), never their logo or artwork, and get the organiser's OK for co-branding |

## Red Flags

- A QR pointing at a URL nobody checked with `curl -sI`, or one that only decodes at a single size
- `<text>` elements left in an SVG (missing fonts at the print shop)
- A colour that only exists as a CSS gradient or as transparency
- A claim marked partial or planned in the product's status docs
- Hostnames, IPs, credentials, prices or client names that weren't cleared
- A rim, ring or keyline running parallel to the cut within about 3 mm (cut drift will show)
- A maker's logo or trademark (car, bike or tool brand) used as decoration
- Concepts that differ only in colour

## Verification

- [ ] Every claim maps to a shipped fact, with its file path in the README
- [ ] ui-ux-pro-max output is quoted per concept; the brand overrides are noted
- [ ] impeccable flags are listed with fixed / kept (and the reason)
- [ ] Every SVG has Artwork, a hidden SafeArea layer and a CutContour layer, and 0 `<text>` and 0 `<image>` elements
- [ ] Every PNG is trim + 2 × bleed at 300 dpi
- [ ] Every PDF has Artwork + CutContour OCGs and a `/Separation /CutContour` colour space
- [ ] Text: ≥ 6 pt, inside the 3 mm safe area, ≥ 4.5:1 against its actual background
- [ ] Every QR decodes the exact URL at 1.0 and 0.25, and the URL returned 200 that day
- [ ] `preview.png` per concept, `compare.png` and `manifest.json` are written
- [ ] The gallery is published and its link sent; not committed until the user has reviewed
- [ ] Still open (always listed in the report): phone scan of a printed proof, CMYK proof, plotter test of the irregular cuts

## Reference Files

| File | Contents |
|---|---|
| [references/print-spec.md](references/print-spec.md) | Sizes, bleed, safe area, dpi, line weights, cut rules, file layers, vinyl and laminate notes, CMYK caveats |
| [references/engine-recipe.md](references/engine-recipe.md) | The Python build pipeline: outlined text, contour cuts, SVG layers, rsvg, the CutContour PDF recipe, gates |
| [references/qr-style.md](references/qr-style.md) | Styled QR geometry (dots, eyes, centre logo), sizing, the zbar decode gate, the OpenCV and zbar quirks |
| [references/concept-directions.md](references/concept-directions.md) | Directions that worked, with their vinyl adaptations, and how to borrow an event site's style |
| [references/gallery-and-manifest.md](references/gallery-and-manifest.md) | `manifest.json` schema and the review gallery page (one per product) |
| [references/agent-brief.md](references/agent-brief.md) | A copy-ready brief for one product agent in a parallel run |
