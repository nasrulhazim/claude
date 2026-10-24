# Logo Build Pipeline

Wordmarks are built from real glyph outlines and marks from generated geometry, so every SVG renders
identically everywhere, inside `<img>` and in PNG exports alike.

## Setup

```bash
# Fonts from npm (always pin a version); fontTools for outlines
npm pack @fontsource/bricolage-grotesque@5.1.0 && tar xzf fontsource-bricolage-grotesque-5.1.0.tgz
pip install --break-system-packages fonttools brotli pillow
# Rendering: Playwright + Chromium (if preinstalled, use it; never re-download)
NODE_PATH=$(npm root -g) node render.js
```

Font file used: `package/files/<family>-latin-800-normal.woff2`.

## Wordmark From Outlines

```python
from fontTools.ttLib import TTFont
from fontTools.pens.svgPathPen import SVGPathPen
from fontTools.pens.transformPen import TransformPen
from fontTools.pens.boundsPen import BoundsPen

font = TTFont(FONT); gs = font.getGlyphSet(); cmap = font.getBestCmap(); hmtx = font["hmtx"]
upm = font["head"].unitsPerEm; track = -0.02 * upm
x, parts = 0, []
for ch in word:
    g = cmap[ord(ch)]
    if ch == SWAP_CHAR:              # the letter the mark replaces (e.g. "o")
        bp = BoundsPen(gs); gs[g].draw(bp); b = bp.bounds
        mark_at = (x + (b[0] + b[2]) / 2, -(b[1] + b[3]) / 2, (b[3] - b[1]) / 2 * 0.95)
    else:
        sp = SVGPathPen(gs); gs[g].draw(TransformPen(sp, (1, 0, 0, -1, x, 0))); parts.append(sp.getCommands())
    x += hmtx[g][0] + track
# Then: measure total bounds, pad 6% of upm, scale to 120 px tall,
# emit <path d="..."> + the mark at mark_at inside one <g transform>.
```

- Replacing a letter with the mark: size it to ~95% of that glyph's height so it doesn't look heavier
  than the letters around it.
- Apply the same tracking to measuring and drawing, or the bounds drift.

## Mark Geometry (example: an N-blade shutter)

```python
def aperture(cx, cy, R, n=4, s_ratio=0.32, rot=15, sw_ratio=0.09):
    h = (R * s_ratio * 2) / math.sqrt(2)                     # opening half-diagonal
    pts = [(cx + h*math.cos(math.radians(45+rot+360/n*k)),
            cy - h*math.sin(math.radians(45+rot+360/n*k))) for k in range(n)]
    segs = []
    for i in range(n):                                        # extend each side to the rim
        A, B = pts[i], pts[(i+1) % n]
        dx, dy = B[0]-A[0], B[1]-A[1]; L = math.hypot(dx, dy); dx, dy = dx/L, dy/L
        bx, by = B[0]-cx, B[1]-cy; b = 2*(bx*dx+by*dy); c = bx*bx+by*by-R*R
        t = (-b + math.sqrt(b*b - 4*c)) / 2
        segs.append((B, (B[0]+t*dx, B[1]+t*dy)))
    return pts, segs, R * sw_ratio
```

- **Colour variant**: disc in the brand colour, blades in ink. Wrap the blades in a `clipPath` of the disc
  and use `stroke-linecap="square"` so nothing pokes out.
- **Mono variants**: a `<mask>` with a white disc and black blades, applied to one disc in a single colour.
  This gives a true knockout that works on any ground.
- Prefix every `id` per file and per placement, since two inline copies on one page would otherwise share ids.

## Variant Set

| File | Use |
|---|---|
| `<brand>-wordmark.svg` | Primary, on light grounds |
| `<brand>-wordmark-reversed.svg` | On the dark brand block |
| `<brand>-wordmark-mono-ink.svg` | One colour; also on the brand-colour ground |
| `<brand>-mark.svg` | Avatars, in-app ≥ 24 px |
| `<brand>-mark-mono-ink.svg` / `-mono-paper.svg` | One colour on light / dark |
| `<brand>-app-icon.svg` / `-light.svg` | 512 tile, rx 112; favicon source below 24 px |

## Contact Sheet

Render one HTML page with three bands: light ground (all variants), dark ground (reversed + mono
paper), and white at 16/24/28 px. Look at it once, fix, and move on.
