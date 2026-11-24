# Gallery and Manifest

## manifest.json

```json
{
  "qr": "https://example.app",
  "bleed_mm": 2.0, "safe_mm": 3.0, "dpi": 300,
  "concepts": [
    {
      "id": "A", "folder": "a-neubrutalist", "name": "Neubrutalist flat poster",
      "preview": "a-neubrutalist/preview.png",
      "stickers": [
        {
          "stem": "01-mark-diecut", "title": "Mark",
          "description": "Contour-cut mark with a thick dark outline and hard shadow.",
          "trim_mm": [51.8, 51.8], "shape": "contour cut (mark)", "has_qr": false,
          "png": "a-neubrutalist/png/01-mark-diecut-300dpi.png",
          "svg": "a-neubrutalist/svg/01-mark-diecut.svg",
          "pdf": "a-neubrutalist/pdf/01-mark-diecut.pdf"
        }
      ]
    }
  ]
}
```

`build.py` writes it. Each product in a family has its own manifest, with the same shape.

## Review gallery: one page per product

The user reviews on a page, not in a folder of PNGs.

| Part | Content |
|---|---|
| Header | Eyebrow (product · event · venue · date), H1 "<Product> sticker concepts", one line: same messages per concept, sizes are trim, files add bleed and a cut line, where the QRs link |
| Sticky bar | One chip per concept (`A Neubrutalist`, …) plus "Compare all" |
| Concept section | Letter, name, sticker count, folder path; a cutting-mat grid ground; one card per sticker: image (max 200 px tall, drop shadow), number, title, trim `w × h mm` |
| Zoom | Click a sticker to open it larger in a `<dialog>` with a caption |
| Compare | `compare.png` full width |

Build notes:

- Downscale each PNG to about 900 px for the page (the full 300 dpi files stay in the repo).
- Generate `concepts.json` from the folders or the manifest; the page fetches it.
- Theme tokens for light and dark; the grid ground uses tokens too.
- Images and JSON are published next to the page (relative URLs). No external hosts.
- One gallery per product, so a reviewer opens one link per product.
- A new page starts **private**. Tell the user it must be shared before others can open it, and
  note if an existing one is already link-shared, because updates then go live to viewers.

## Before publishing

1. Re-run `zbarimg` on every QR PNG yourself. Don't trust the agent's report alone.
2. Look at `compare.png` once for clipped text, overlap in the mockup, or a claim that isn't
   true.
3. Publish, then send the link with a short table: concepts, the QR target, and the open checks
   (phone scan, CMYK proof, plotter test, any client or organiser OK needed).
