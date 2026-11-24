# Engine Recipe

A small Python engine that turns sticker designs into print files. Every check is an
`assert`, so a build that finishes is a build that passed.

## Dependencies

```bash
brew install librsvg zbar            # rsvg-convert, zbarimg
python3 -m venv .venv && .venv/bin/pip install fonttools uharfbuzz segno pymupdf pillow shapely
```

Fonts: download OFL/Apache fonts (Google Fonts repo) into a gitignored `.fonts/` folder or the
scratchpad on the first run. Instance variable fonts with `fontTools.varLib.instancer` at the
weight you need.

## A design is a dict

```python
d = dict(slug="02-round-push", w=64, h=64, dx=0, dy=0,   # trim size; dx/dy shift art into the bleed box
         art="<svg fragment in mm units>",                # outlined paths only
         cut=shapely_polygon,                             # the die line
         boxes=[(x, y, w, h, "string"), ...],             # every text ink box, for the safe-area gate
         qr=True)
```

## Outlined text (HarfBuzz shaping, fontTools paths)

```python
def text(s, font, size_mm, x, y, fill, anchor="start", track=0.0, maxw=None):
    buf = hb.Buffer(); buf.add_str(s); buf.guess_segment_properties()
    hb.shape(hb_font, buf, {"kern": True, "liga": False})
    k = size_mm / upem
    width = sum(p.x_advance for p in buf.glyph_positions) * k + track * size_mm * (len(s) - 1)
    if maxw and width > maxw: size_mm *= maxw / width          # shrink to fit, then re-shape
    x -= {"start": 0, "middle": width / 2, "end": width}[anchor]
    pen = SVGPathPen(glyph_set); cx = x
    for info, pos in zip(buf.glyph_infos, buf.glyph_positions):
        glyph_set[order[info.codepoint]].draw(TransformPen(pen, (k, 0, 0, -k, cx + pos.x_offset * k, y - pos.y_offset * k)))
        cx += pos.x_advance * k + track * size_mm
    assert size_mm >= 2.117, f"text below 6 pt: {s!r}"
    BOXES[-1].append((x, top, width, bottom - top, s))       # real ink box from glyph extents
    return f'<path fill="{fill}" d="{pen.getCommands()}"/>', width
```

Measure, don't guess. The same `measure()` drives anchors, plates and fit-to-width.

## Geometry and contour cuts (shapely)

```python
def rbox(x0, y0, x1, y1, r):                      # rounded rectangle
    return box(x0 + r, y0 + r, x1 - r, y1 - r).buffer(r, quad_segs=16)

def outline(g, rim=2.8, hull=False):
    """Contour cut: art + rim, notches < 3 mm closed, never a hole."""
    g = (g.convex_hull if hull else g).buffer(rim).buffer(3).buffer(-3)
    return Polygon(g.exterior)

def shadowed(g, k=2.2):                           # hard offset shadow (neubrutalism) = translated copy
    return translate(g, k, k)
```

Brand marks are **parsed from the brand SVG** (path `d`) and placed as they are, never redrawn.

## SVG with layers

```python
def svg_doc(d, with_cut, with_guides=False, clip=False):
    W, H = d["w"] + 2 * BLEED, d["h"] + 2 * BLEED
    tr = f'translate({BLEED + d["dx"]} {BLEED + d["dy"]})'
    layers = [f'<g id="Artwork" inkscape:groupmode="layer" inkscape:label="Artwork" transform="{tr}">{d["art"]}</g>']
    if with_guides:
        layers.append(f'<g id="SafeArea" inkscape:groupmode="layer" style="display:none" transform="{tr}">'
                      f'<path d="{poly_d(d["cut"].buffer(-SAFE))}" fill="none" stroke="#00AEEF" stroke-width="0.2"/></g>')
    if with_cut:
        layers.append(f'<g id="CutContour" inkscape:groupmode="layer" transform="{tr}">'
                      f'<path d="{poly_d(d["cut"])}" fill="none" stroke="#FF00FF" stroke-width="0.25"/></g>')
    return f'<svg xmlns="http://www.w3.org/2000/svg" xmlns:inkscape="…" width="{W}mm" height="{H}mm" viewBox="0 0 {W} {H}">' + "".join(layers) + "</svg>"
```

## PNG and PDF

```python
def rsvg(svg_text, out, fmt, dpi=300):
    subprocess.run(["rsvg-convert", "-f", fmt, "-d", str(dpi), "-p", str(dpi), "-o", str(out)],
                   input=svg_text.encode(), check=True)
```

### CutContour PDF (PyMuPDF)

1. Render the artwork-only SVG to `art.pdf`, and the cut-only SVG (empty art, cut layer on) to `cut.pdf`.
2. In `cut.pdf`, add a colour space object:
   `[/Separation /CutContour /DeviceCMYK << /FunctionType 2 /Domain [0 1] /C0 [0 0 0 0] /C1 [0 1 0 0] /N 1 >>]`
3. Rewrite the stroke operator in the content stream: `1 0 1 RG` → `/CS_Cut CS 1 SCN`. Assert the
   replacement happened, then register `/CS_Cut` in the page's `/Resources /ColorSpace`.
4. In a new document, place both pages on one page with `show_pdf_page(..., oc=doc.add_ocg("Artwork"))`
   and `oc=doc.add_ocg("CutContour")`.
5. Set metadata to the trim size, the bleed and "cut path = spot colour CutContour".

## Gates (all `assert`)

| Gate | How |
|---|---|
| Text ≥ 6 pt | In `text()` |
| Safe area | `cut.buffer(-3).contains(box(*ink_box))` for every text box |
| Contrast ≥ 4.5:1 | Declare each `(fg, bg)` pair per concept and compute WCAG relative luminance |
| QR | zbarimg exact decode at 1.0 and 0.25 (`qr-style.md`) |
| Layers | After the build, parse every SVG (3 layers, 0 `<text>`) and every PDF (2 OCGs + `/Separation /CutContour`) |

## Preview and compare sheets

- Per sticker: a clipped preview at 200 dpi, plus a proof at 300 dpi with the cut and safe
  lines visible.
- `preview.png`: every sticker at true relative size on neutral grey, then a 13-inch laptop lid
  (304 × 212 mm) at the same scale, with the stickers placed slightly rotated and not
  overlapping (check this).
- `compare.png`: one row per concept with that concept's lid mockup.
