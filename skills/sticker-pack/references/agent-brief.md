# Agent Brief (one product, parallel run)

Copy this, fill the `<…>` fields, and give one brief to each design agent. Launch them all in
one message so they run concurrently.

```markdown
Design **5 sticker concepts** for **<Product>** (<one-line what it is>) to hand out at <event,
venue, date> to <audience>. Print-ready, not mockups.

**Skills, in this order: `ui-ux-sop` (if installed) → `ui-ux-pro-max` (design-system search for
this product, then a style search and a typography search per concept) → `impeccable` (critique +
audit each concept's proofs; fix what it flags).** The report must quote the skill output per
concept and what impeccable flagged and fixed. The product's own brand wins over the skill's palette.

## Product facts (claim only what's shipped)
- Start with <brief/status file>. Then <workspace CLAUDE.md, docs, web copy paths>.
- Rules: <do-not-claim items, status word to use, client naming rules, spelling of the name>.
- Never open `.env*`, `todo.md`, keys or credentials<, plus any private paths>. No hostnames or
  IPs on stickers. No maker logos. If there's no logo, use a type-only wordmark; never invent an
  official-looking logo.
- Brand assets: <paths>.

## Reference
An existing pack at <path> shows the bar and the file conventions. You may import its
`engine.py` but **never modify it**; copy it into your folder if you need changes.

## Deliverables → `<stickers/products/<slug>/>`
- **5 concepts** in `<a..e>-<style>/`, **6 stickers each**, with clearly different directions
  chosen from the ui-ux-pro-max catalogue and safe for vinyl (no glow, no banding gradients).
- The same 6 messages in every concept: a mark, the core promise, a QR sticker, then shipped
  features or a true joke.
- Per sticker: SVG (Artwork + hidden SafeArea + CutContour, text outlined), a 300 dpi PNG with
  2 mm bleed, and a PDF with a CutContour spot layer. Real mm, 3 mm safe area, text ≥ 6 pt,
  contrast ≥ 4.5:1, SVG icons only (no emoji).
- QR: one per concept, to <URL> (`curl -sI` must return 200 first). Styled: round dots, rounded
  eyes, centre logo, level H. `zbarimg` must decode it exactly at full size and at 25%.
- Per concept: `preview.png`. Product level: `compare.png`, `manifest.json`, `README.md`,
  `build.py`.
- Write **only** in your product folder. Fonts and venvs go in the scratchpad. **Do not commit.**

Report: the facts with file paths, the skill output per concept, the 30 stickers (stem, size,
shape, copy), the QR results, and what you couldn't verify.
```

## Orchestrator checklist, per finished agent

1. Run `zbarimg` on every `*/png/*qr*-300dpi.png` and check each decode matches the URL exactly.
2. Build that product's gallery from its folders, publish it, and record the link.
3. Pass on the agent's open items (client naming, status wording, jokes that need an OK).
4. Commit the product folders after the user has reviewed them, or when the user asks.
