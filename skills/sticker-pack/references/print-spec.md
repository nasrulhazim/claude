# Print Spec

The defaults a sticker shop accepts without questions. Units are millimetres unless stated.

## Geometry

| Item | Value | Note |
|---|---|---|
| Bleed | 2 mm on every side | Art runs to the bleed; the PNG and PDF page = trim + 4 mm |
| Safe area | 3 mm inside the cut line | All text and logos inside it; checked against each glyph's real ink box |
| Resolution | 300 dpi | Store the dpi in the PNG (`Image.save(..., dpi=(300, 300))`) |
| Minimum text | 6 pt = 2.117 mm cap-to-baseline font size | A floor, not a target. Labels 6.5–8 pt, headlines 12 pt and up |
| Minimum line | 0.3 mm printed lines; 0.45 mm for anything that has to read at arm's length | Hairlines vanish or break on vinyl |
| Cut path stroke | 0.25 mm, magenta `#FF00FF` in the SVG | Spot colour `CutContour` in the PDF |
| Contrast | ≥ 4.5:1 for every text and background pair | Check against the real background colour, not the page colour |

## Sizes that work

| Use | Typical trim |
|---|---|
| Mark, die-cut or tile | 45–58 mm |
| Round | 58–64 mm diameter |
| Laptop rectangle with QR | 90–100 × 50–60 mm |
| Strip, pill or bumper | 80–110 × 26–42 mm |
| Tall card | 46–56 × 64–78 mm |
| Helmet | ≤ 54 mm, simple contour |
| Tank or side panel | 80–110 mm wide, cast vinyl |

## Cut rules

| Rule | Why |
|---|---|
| One outer contour, **no inner holes** | Weeding islands at a print run costs time and they tear |
| Close notches and necks narrower than 3 mm (buffer +3 then −3) | Plotter blades tear narrow necks |
| Contour cuts get a 2.5–3.2 mm rim around the art | Survives cut drift and gives the sticker a clean edge |
| No ring, frame or keyline parallel to the cut within about 3–4 mm | Cut drift makes a parallel line look crooked; move it in to 4 mm or more, or let colour run to the bleed |
| Round any point sharper than about 60° | Points lift off a laptop after a week |
| Concave curves under about 1 mm radius: ask the shop | Some plotters can't turn that tight |

## File layers

| File | Layers |
|---|---|
| SVG | `Artwork` (inkscape layer), `SafeArea` (hidden guide), `CutContour` (magenta stroke). Text outlined: zero `<text>` and zero `<image>` elements |
| PNG | Artwork only, transparent outside the bleed |
| PDF | One page. `Artwork` OCG plus a `CutContour` OCG whose stroke uses `[/Separation /CutContour /DeviceCMYK …]`. 0 fonts, 0 images (pure vector) |

## Vinyl and finish

| Choice | When |
|---|---|
| White vinyl, matte laminate | Default for laptops; easy to read under event lighting |
| Gloss laminate | Saturated colours (neubrutalist, Memphis) |
| Cast vinyl + UV laminate | Helmets, tanks and outdoor use |
| Transparent vinyl | Only with a white underlay plate under the art |

## Colour

- Files are RGB. Bright greens, cyans, fuchsias, limes and violets may fall **outside CMYK**,
  so ask for a printed proof.
- A rich black on large dark grounds: give the shop a recipe (for example C60 M40 Y40 K100),
  never registration black.
- A brand gradient prints as flat stops (one per shape) or as a solid. Say so in the README.
- Near-white or cream grounds shift on white vinyl, and a white sticker disappears on a silver
  lid. Add an ink keyline about 1.6 mm inside the cut, or give it a dark ground.

## README must list

Each sticker's file, trim size, shape, copy, suggested quantity, finish, and the open checks:
phone scan of a printed proof, CMYK proof, and plotter test of the irregular cuts.
