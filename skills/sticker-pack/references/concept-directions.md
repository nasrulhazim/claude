# Concept Directions

Directions that held up in real packs, each with its print adaptation. Always start from the
ui-ux-pro-max output for the specific product and quote it in the report; this list is a
cross-check, not a menu to copy.

## Picking five

- Five **different ideas**: different shapes, typefaces and motifs, not one layout in five
  palettes.
- At least one quiet direction (Swiss, editorial) and one loud one (neubrutalist, Memphis).
- One direction that comes from the product's own world, like a terminal for a CLI, an ECG for
  a monitor, a dash cluster for a vehicle device, or a case file for an audit tool.
- Keep the brand: its mark untouched, its colours as the palette. Swap the skill's suggested
  palette for brand colours, and say so.

## Catalogue

| Direction | What makes it | Print adaptation |
|---|---|---|
| Neubrutalist / flat poster | Solid blocks, thick dark outline, hard 45° offset shadow, Inter 800 | Shadow = a translated copy, 2.2–3 mm; no soft shadow |
| Pixel art / CRT | Stepped 1.5 mm pixel outlines, 8-bit sprites, Press Start 2P / VT323 | Drop scanline glow; keep faint scanlines only as solid lines ≥ 0.3 mm; check the plotter can follow the steps |
| Swiss / minimal line | White ground, strict grid, hairline rules, mono labels, one accent | Rules ≥ 0.25 mm; an ink keyline inside the cut so it shows on silver lids |
| Sketch / doodle | Wobbly outlines (smooth noise), pencil lines drawn twice, post-its, tape, Kalam / Patrick Hand | The cut wobbles with the art; QR stays exact and taped on |
| Vintage analog | Polaroid, cassette, VHS, film strip, stamp, enamel shield; Abril Fatface / Merriweather | Halftone dots instead of grain; no light leaks (they band) |
| Bauhaus | Circles, squares, quarter circles, hard shadow, Outfit 900 | Primaries mapped to brand colours |
| Memphis | Squiggles, triangles, confetti, Fredoka | Confetti pulled onto the shape (no thin necks); brand tints instead of pink and teal |
| Bento | Modular rounded tiles of different sizes | Drop the soft shadows and hover effects |
| HUD / blueprint / scope | Reticles, brackets, technical markers, mono | No glow; lines ≥ 0.45 mm; chamfered or octagon cuts |
| Editorial / magazine / dossier | Cover, pull quote, case file, exhibit label, stamp, redaction bars | Real redaction bars, not asterisks; serif body at ≥ 7 pt |
| Terminal / OLED | Dark window chrome, prompt, comment lines, real commands | Comments in a light tint, not grey-on-black; the wordmark in the title bar |
| Kinetic type | Oversized type as the visual, speed streaks, leaning shapes | Motion frozen; the cut leans with it; echo outlines ≥ 0.4 mm |
| Domain object | ECG chart paper + wristband, dash cluster + gauges, road signs, race livery | Fine grids ≥ 0.16/0.3 mm; generic silhouettes, never a maker's logo |

## Borrowing a site's style (an event or partner)

1. Render the site with a headless browser at desktop size and take full-page screenshots. Read
   the CSS and the computed styles.
2. Extract the **exact hex values**, the typefaces it actually loads (or its system stack), its
   shapes, motifs and patterns (labels, numbered rows, chips, buttons with arrows), and its tone.
3. Run ui-ux-pro-max style and typography searches on that direction to firm it up.
4. Build co-branded ("<product> × <event>") stickers in that language. **Never copy their logo
   or artwork**, and draw any motif fresh. The event name may appear as plain text.
5. If the site's palette clashes with your brand colour, use the site's palette and render your
   mark in one of its colours or in mono.
6. Note in the README: "co-branded; get the organiser's OK before handing out."

## Copy that works on stickers

- Short, true and specific: "Roll back. No rebuild." / "Scan changes nothing." / "Silence is not
  a heartbeat."
- Shipped features only, with the facts file path recorded.
- Status words only if the docs say so ("Live", "Open for beta"); never "GA" or dates unless
  they're confirmed.
- Example numbers ("1,042 events", "12.6 V") labelled as illustrative in the README.
