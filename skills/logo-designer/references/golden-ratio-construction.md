# Golden Ratio Construction

Every logo is constructed on the golden ratio, φ = (1 + √5) / 2 ≈ 1.618. The ratio sets the mark's
outer shape, its internal proportions, the lockup with the wordmark, the clear space and the app
icon. Because each dimension comes from one constant, the rules can be checked with a script, so a
logo either passes or it doesn't.

## The φ Scale

All dimensions are drawn from one scale, built from a base unit `u` (usually the mark's height):

| Step | Factor | Value | Typical use |
|---|---|---|---|
| φ² | 2.618 | u × 2.618 | Large radii, long proportions |
| φ | 1.618 | u × 1.618 | Golden rectangle long side |
| 1 | 1.000 | u | Mark height |
| 1/φ | 0.618 | u × 0.618 | Wordmark ink height, inner circle, second bar |
| 1/φ² | 0.382 | u × 0.382 | Clear space, third bar, small radius |
| 1/φ³ | 0.236 | u × 0.236 | Mark-to-wordmark gap, stroke weight |
| 1/φ⁴ | 0.146 | u × 0.146 | Notches, fine detail |

## The Rules

| # | Rule | Check |
|---|---|---|
| R1 | **Outer shape.** The mark's bounding box is a square (1:1) or a golden rectangle (1:φ, either orientation). Circles, seals and squircles count as 1:1. | `max(w,h) / min(w,h)` is 1.000 or 1.618 (±2%) |
| R2 | **Curves.** Every radius in the mark comes from one φ scale: r, r/φ, r/φ², r/φ³. Concentric rings step by φ; a hook's outer radius is the stroke × φ and its inner radius is the stroke ÷ φ. | All radii divide by φⁿ |
| R3 | **Rhythm.** Repeated elements step by φ. Rising bars are h/φ², h/φ, h. Widths beat gaps by φ (width : gap = φ : 1), so three bars plus two gaps = width × φ³. | Neighbour ratios ≈ 1.618 |
| R4 | **Lockup.** In a horizontal lockup the wordmark's ink height above the baseline (its tallest ascender, cap or i-dot) = mark height ÷ φ, centred on the mark. Gap between mark and wordmark = mark height ÷ φ³. | 0.618 and 0.236 of mark height |
| R5 | **Clear space and icons.** Clear space on every side = mark height ÷ φ² (0.382). In an app icon the mark's longest side = tile ÷ φ (61.8%), centred. Circle-crop avatars keep their own safe zone (the mark at 58% of the square), since a circle crop wins over R5. | 0.382 and 0.618 |
| R6 | **Show the construction.** Ship a construction drawing for each chosen logo: the mark ghosted, the golden rectangle (square + remainder + quarter arc) on the mark height, and the mark's own guides (rings, bar-top lines). | `<brand>-construction.svg` exists |

The rules decide proportions. They don't replace the eye. When optical correction is needed (a
circle overshooting the baseline, a pointed tip extending past the line), keep it under 3% and
note it in the construction drawing.

## Building With φ (Python)

```python
import math
PHI = (1 + 5 ** 0.5) / 2

def bars(x0, base, w, h3, fills):
    """Three rising bars: heights h/phi^2, h/phi, h; gap = w/phi (R3)."""
    g, out = w / PHI, []
    for i, (h, fill) in enumerate(zip([h3 / PHI**2, h3 / PHI, h3], fills)):
        out.append(f'<rect x="{x0 + i*(w+g):.2f}" y="{base-h:.2f}" width="{w:.2f}" height="{h:.2f}" fill="{fill}"/>')
    return "".join(out), 3*w + 2*g          # total width = w * phi^3

def lockup(mark_bbox, font_ink_ymax):
    """R4: wordmark scale and position from the mark's box."""
    x0, y0, x1, y1 = mark_bbox
    mh = y1 - y0
    ink = mh / PHI                            # wordmark ink height
    gap = mh / PHI**3                         # mark-to-text gap
    scale = ink / font_ink_ymax               # font units -> px
    baseline = (y0 + y1) / 2 + ink / 2        # ink centred on the mark
    return dict(scale=scale, x=x1 + gap, baseline=baseline, clear=mh / PHI**2)

def icon_scale(mark_bbox, tile=512):
    """R5: mark's longest side = tile / phi."""
    x0, y0, x1, y1 = mark_bbox
    return (tile / PHI) / max(x1 - x0, y1 - y0)
```

Measure the wordmark's ink height with fontTools `BoundsPen` over every glyph in the word (take the
highest `yMax`), so "jelas" uses the i-dot or `l` ascender and "PLUMB" uses the cap height.

## Construction Drawing

Render the mark in a light grey and overlay:

1. The golden rectangle on the mark height (blue): outer box mh × mh·φ, the line closing the square,
   and the quarter arc of radius mh.
2. The mark's own guides (red, 0.5 px): every ring at its true radius, a dashed line at each bar top,
   the bounding box when it is a golden rectangle.
3. A one-line caption naming the ratios used, e.g. `rings R, R/φ · stroke R/φ³ · box 1:φ`.

## Ratio Check

Keep a ratio report with the logo files and run it on every revision:

```python
report = dict(
    aspect=round(max(w, h) / min(w, h), 3),     # R1: 1.0 or 1.618
    ink_over_mark=round(ink / mh, 3),           # R4: 0.618
    gap_over_mark=round(gap / mh, 3),           # R4: 0.236
    clear_over_mark=round(clear / mh, 3),       # R5: 0.382
)
ok = (abs(report["aspect"] - 1) < 0.02 or abs(report["aspect"] - PHI) < 0.03) \
     and abs(report["ink_over_mark"] - 1/PHI) < 0.01 \
     and abs(report["gap_over_mark"] - 1/PHI**3) < 0.01 \
     and abs(report["clear_over_mark"] - 1/PHI**2) < 0.01
```

A mark whose aspect lands between 1.0 and 1.618 (e.g. 1.45) fails R1. Fix it by solving the free
dimension for the target ratio rather than stretching. Example: a j-shaped mark whose width is fixed
by its bars gets its stem height from `height = width × φ`.

## In the Brand Book

Add a **Construction** subsection under Logo: the construction drawing, the φ table above trimmed to
the steps the mark uses, and the lockup, clear-space and icon rules with their numbers.
