# Production Readiness

A logo is production-ready when a printer, an embroiderer or a merch supplier can reproduce it
without guessing. That takes three mark levels, one-colour knockout variants, a colour spec for
every process, minimum-size tests per medium, and a vendor pack. Every check below is computed
from the logo geometry, so the result is pass or fail, not an opinion.

## Mark Levels

| Level | Used at | How it differs from the full mark |
|---|---|---|
| **Full** | 48 px and up; print and screen print at normal sizes | The designed mark |
| **Simplified** | 24–47 px; embroidery; deboss; anything under ~30 mm | Fewer repeats (teeth, bars), thicker strokes, wider gaps; still on the φ scale |
| **Micro** | Favicon and anything under 24 px | The one shape people recognise (one bar, the bare letter, the ring). The box may leave 1:1 or 1:φ when the shape demands it. |

The favicon is the one place the mark may fill **80% of the tile** instead of tile ÷ φ, because at
16 px every pixel counts.

## Variant Set

| Variant | Why |
|---|---|
| Colour, reversed | Light and dark grounds |
| **Mono ink, mono paper (knockout)** | One-colour processes. The "white" parts are holes, not white ink, so fabric, metal or paper shows through. Build with `fill-rule="evenodd"` or an SVG `<mask>`, never by painting white. |
| **2-spot** | Screen print charges per screen. If the colour version uses tints or a third colour, make a 2-spot version. |
| **Stacked lockup** | Mark above the name for square spaces: caps, totes, roll-up headers. Wordmark ink height = mark height ÷ φ^1.5, gap = mark height ÷ φ³. |
| **App icon** with optical centre | Raise the mark by tile ÷ φ⁸ (about 2%) so it doesn't look low |
| Clean masters | No gradients, transparency, shadows or live strokes; overlapping shapes merged. Cutters, embroidery digitisers and engravers need closed filled paths. |

## Colour Spec

Every brand colour gets one row:

| Field | Source | Note |
|---|---|---|
| HEX / RGB | The tokens | Screen |
| CMYK | Straight conversion as a starting point | Must be proofed; bright teals, cyans and greens dull in CMYK |
| Pantone C / U | Picked on a physical Formula Guide | Never invent a code. Leave "[match on guide]" until someone checks. |
| Thread | Picked on the embroiderer's thread card | Thread is glossy and reads darker than ink |
| Vinyl | Vendor's vinyl range | Signage, car decals, cut stickers |

Use 100K black for small text and thin lines; rich black only on large solid areas.

Contrast rules on both grounds (white and the dark brand ground):

| Pair | Minimum |
|---|---|
| Text (wordmark) on its ground | 4.5:1 |
| Graphic parts (accent, beam, dot) on their ground | 3:1 |
| Two colours that must be told apart (ink vs accent) | 1.5:1 and a different lightness, not hue alone |

When a colour fails, shift its lightness and keep the hue (the `fit()` helper below) instead of
picking a new colour.

## Minimum Detail by Medium

Measure the smallest stroke, gap, tooth or counter in the mark (mark units), scale it to the real
size, and compare it with the medium's minimum. Figures are typical; confirm with the vendor.

| Medium | Real size used for the test | Variant | Minimum detail |
|---|---|---|---|
| Favicon | 16 px tile, mark at 80% | micro | 1.0 px |
| Small app icon | 32 px tile, mark at tile ÷ φ | simplified | 1.0 px |
| App icon | 64 px tile, mark at tile ÷ φ | full | 1.0 px |
| Embroidery, cap front | mark 45 mm tall | simplified | 1.0 mm |
| Embroidery, left chest | lockup 90 mm wide | simplified | 1.0 mm; wordmark ≥ 5 mm tall |
| Pad print, pen | lockup 35 mm wide | full, 1 colour | 0.2 mm |
| Laser engraving, tumbler | mark 25 mm tall | full, 1 colour | 0.3 mm |
| Deboss, notebook | mark 30 mm tall | simplified | 0.5 mm |
| Screen print, tee front | lockup 260 mm wide | full | 0.4 mm |
| Heat-transfer vinyl | mark 60 mm tall | simplified | 1.5 mm |
| Offset print, name card | mark 10 mm tall | full | 0.15 mm |

```python
def detail_test(min_feature, mark_h, real_mark_size, need):
    """min_feature and mark_h in mark units; real_mark_size in mm or px."""
    value = min_feature / mark_h * real_mark_size
    return round(value, 2), value >= need

# Each mark function returns its features so the test never guesses:
#   feats = dict(tooth_width=..., bar_gap=..., ring_stroke=..., dot_gap=...)
#   value, ok = detail_test(min(feats.values()), mark_h, 45, 1.0)

import colorsys
def fit(colour, ground, target, darker=True):
    """Shift lightness, keep hue, until contrast(colour, ground) >= target."""
    if contrast(colour, ground) >= target: return colour
    h, l, s = colorsys.rgb_to_hls(*hex_to_rgb(colour)); step = -0.005 if darker else 0.005
    while 0 < l < 1:
        l += step; c = rgb_to_hex(colorsys.hls_to_rgb(h, l, s))
        if contrast(c, ground) >= target: return c
    return colour
```

A failure is fixed in the geometry of the right level (fewer teeth, thicker line, wider gap),
never by quietly lowering the threshold. Re-run the whole table after every fix.

## Perception Tests

Render each one and look once:

| Test | How | Fails when |
|---|---|---|
| Squint | Blur 3 px at 120 px | The silhouette no longer reads as the brand |
| Greyscale | Remove colour | Ink and accent merge into one grey |
| One colour | Mono ink on white, mono paper on the brand colour | Meaning depended on colour |
| Reversal | Reversed on the dark ground | Shapes look heavier or thinner than the positive |
| Small size | 16, 24, 32 px | Details fill in |

## Optical Corrections

Keep each under 3% and note it on the construction drawing:

- **Overshoot:** round and pointed shapes pass the baseline and cap height by 1–3%.
- **Optical centre:** a mark in a tile or badge sits slightly above the geometric centre.
- **Stroke compensation:** horizontal strokes slightly thinner than vertical ones.
- **Kerning:** hand-adjust awkward pairs in the wordmark (`Te`, `Lu`, `rn`, `G8`).

## Vendor Pack

| Item | Content |
|---|---|
| Vector masters | PDF and EPS with outlined fonts, plus SVG. Many local printers work in CorelDRAW (CDR) or Illustrator (AI); ask early and send PDF/EPS as the safe default. |
| Raster | Transparent PNG at 300 dpi at print size, for DTF/DTG |
| Spec sheet (1–2 pages) | Colour table, minimum sizes, clear space, which variant on which medium, placement guide, don'ts |
| Placement guide | Left chest 75–100 mm · full front 250–300 mm · upper back 200–250 mm · sleeve 50–70 mm · cap front 45–60 mm tall · lanyard repeat every 100–150 mm · mug 60–80 mm · tote 200–250 mm |
| Garment test | Show the logo on white, black, navy, heather grey and the brand colour. Heather lowers contrast; the brand colour takes mono paper. |

## Legal

- **Trademark (e.g. MyIPO):** software and services are classes 9, 35 and 42. Selling merch adds
  class 25 (clothing) and class 16 (printed matter).
- **Font licence:** SIL OFL fonts (most of fontsource) allow logos and merch. Many commercial
  fonts restrict logo or merchandise use; check before choosing.
- **Ownership:** the client owns the master files; freelancers and vendors hand over sources.
