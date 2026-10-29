# Mockup Studio

One HTML page that shows a logo (or several candidate logos) on real-world items: apparel, print
and merchandise. Every item is drawn in SVG, and the logo variants are embedded as data URIs, so
the page works offline and inside an artifact. A switcher swaps the concept; tabs hold
**Production check · Apparel · Print · Merchandise · Digital**.

## Page Structure

| Tab | Content |
|---|---|
| Production check | Rules applied, size tests by medium (pass/fail), contrast table, colour spec table, variant sheet with Normal / Squint (blur 3 px) / Greyscale toggles, icon at 64/32/16 px |
| Apparel | White tee (screen print), black tee (knockout on dark), polo left chest (embroidery, with a zoomed detail circle), cap front (stacked mono paper, embroidered), lanyard + ID card |
| Print | Name card front + gold-foil back, A5 flyer (crop marks, bleed note, sample QR), roll-up banner 850 × 2000, feather flag, die-cut stickers |
| Merchandise | Packaging box in 3/4 view (front, side, lid), mug, tote (1 spot colour on natural canvas), notebook (blind deboss), pen (pad print, zoomed detail), tumbler (laser engraving through powder coat) |
| Digital | Web app in a browser (favicon in the tab, lockup in the sidebar, sample dashboard), mobile app (home-screen icon among others + splash screen), laptop showing the web app, laptop lid with die-cut stickers |

Each card caption states the **technique · variant · real size**, and shows the matching size-test
pill from `production-readiness.md` when one exists.

## Which Variant Goes Where

| Item | Variant | Never |
|---|---|---|
| Light garment, mug, flyer header on white | Colour lockup | — |
| Dark garment | Reversed lockup (knockout) | White ink filling the counters |
| Brand-colour cap, flag, bunting | Stacked or lockup in **mono paper** | The colour mark on the brand colour |
| Embroidery | Simplified mark | The full mark under 45 mm |
| Pen, tumbler, deboss | Mono (one tone) | Two colours |
| Tote on natural canvas | Stacked mono, recoloured to the brand ink | CMYK tints |
| Foil | Mark used as a mask over a gold gradient | Foil on fine wordmark text |
| Packaging box | Stacked colour on the front; mono paper on the dark side and lid | Flat art pasted without the box's perspective |
| Browser tab | Micro mark favicon | The full mark at 16 px |
| Web app sidebar | Reversed lockup on the dark brand ground | The colour lockup on a dark sidebar |
| Phone home screen | App icon at tile ÷ φ, shown among other icons at real size | The icon shown alone and oversized |
| Splash screen | Stacked reversed lockup, centred on the dark ground, endorsement line at the bottom | Full-bleed photography behind the mark |

## Building Blocks

```js
const uri = svg => 'data:image/svg+xml;charset=utf-8,' + encodeURIComponent(svg);
const ratio = svg => { const v = svg.match(/viewBox="([-\d. ]+)"/)[1].trim().split(/\s+/).map(Number); return v[2] / v[3]; };
// Fit a logo variant into a box centred on (cx, cy)
function img(svg, cx, cy, maxW, maxH, attrs = '') {
  const r = ratio(svg); let w = maxW, h = w / r; if (h > maxH) { h = maxH; w = h * r; }
  return `<image href="${uri(svg)}" x="${cx - w/2}" y="${cy - h/2}" width="${w}" height="${h}" ${attrs}/>`;
}
const recolour = (svg, col) => svg.split('#111111').join(col);   // mono ink -> one spot colour
// A 400 x 400 tee; clip every texture to the garment so nothing spills onto the backdrop
const TEE = 'M120,62 L162,48 Q200,74 238,48 L280,62 L352,104 L326,166 L290,150 L292,362 Q200,374 108,362 L110,150 L74,166 L48,104 Z';
```

Each mockup is a `viewBox="0 0 400 400"` SVG on a soft radial "studio backdrop", with a floor
shadow ellipse under the item.

- **Box faces:** draw the three faces as parallelograms and place each face's art with an SVG
  `matrix()` that matches the face's skew (front `matrix(1 0.338 0 1 x y)`, side
  `matrix(1 -0.338 0 1 x y)`, lid `matrix(0.71 -0.24 0.71 0.24 x y)` for a 30° view).
- **Screens:** build the app view from simple rectangles in the brand tokens (sidebar, search,
  chart, cards). It shows where the logo lives in the product, so keep it schematic rather than a
  fake real UI.
- **Phones:** a rounded frame with a clipped screen; home screen as a 4 × 4 icon grid with the
  brand icon at real size and its label under it.

## Material Filters

Put one hidden `<svg>` with these `<defs>` on the page (positioned off-screen, **not**
`display:none`, or some browsers drop the filters):

| Filter | Recipe | Use |
|---|---|---|
| `fx-fabric` | `feTurbulence fractalNoise 0.85` → alpha-only colour matrix; draw as a rect clipped to the garment at 5% (light) to 18% (dark) opacity | Cotton, canvas, cap twill |
| `fx-ink` | Fine noise → `feDisplacementMap` scale 1.6, then knock out a few specks | Screen-print ink sinking into fabric; add `mix-blend-mode:multiply` on light fabric |
| `fx-embroider` | Anisotropic noise (`baseFrequency="1.6 0.12"`) multiplied into the art + `feSpecularLighting` on the blurred alpha + small drop shadow | Thread with raised stitches |
| `fx-deboss` | Shape minus offset shape for a dark inner edge (+1.3, +1.8) and a light one (−1, −1.2); art at ~55% opacity | Blind deboss on leather or board |
| `fx-engrave` | Streaky noise tinted steel, composited inside the art | Laser through powder coat |
| `fx-sticker` | `feMorphology dilate` radius 7 → white border → soft drop shadow | Die-cut vinyl stickers |
| `g-gold` gradient + `<mask>` of the mono-paper mark | Foil reflecting light | Foil on name cards and packaging |
| `g-cyl` gradient | Dark edges, highlight at 25% | Mugs, tumblers, pens |

## Photo Mockups (preferred when photos are reachable)

Real product photos look more convincing than drawn items. Use them when the environment can
fetch images; fall back to the SVG items above when it can't, and say which mode was used.

1. **Source** blank, front-facing items with no existing branding, in this order: the user's own
   product shoots, Unsplash, Pexels, Openverse/Wikimedia (check each photo's licence). Search one
   item per query: "blank white t-shirt", "blank black t-shirt", "blank white mug", "blank tote
   bag", "blank cap front", "blank business card", "blank notebook", "blank tumbler", "blank
   product box", "laptop mockup screen", "phone mockup".
2. **Get the files.** Download through the source's API (Unsplash: trigger the download endpoint).
   Inside an artifact, upload each photo as an asset, since external image URLs are blocked.
   Record photographer, source and licence in `photos/CREDITS.md`.
3. **Map the print area once per photo.** View the photo, note the print area's four corners in
   pixels, and store them with the photo: `{"tee-white": {"src": "...", "quad": [[x,y],…], "blend": "multiply"}}`.
4. **Place the logo:**
   - Flat, front-facing area: an SVG `<image>` in the quad's bounding box.
   - Angled area (mug, cap, card at an angle): a CSS `matrix3d` transform solved from the four
     corners, or a canvas homography.
   - Light material: `mix-blend-mode: multiply` so the photo's folds and texture show through
     the ink.
   - Dark material: `screen` with the reversed or mono-paper variant, or normal with the
     knockout variant.
   - Embroidery and deboss: add the `fx-embroider` / `fx-deboss` filter on top of the blend.
5. **Check:** the logo never floats above a fold, never crosses a seam or handle, and matches the
   photo's lighting direction.

Blocked environments: if image hosts aren't reachable (sandbox allowlist, unconfirmed API
account), give the user a shortlist of search terms and ask them to (a) allow the photo domains,
(b) confirm the stock account, or (c) attach their own blank product photos. Meanwhile, build
the SVG studio.

## Rules

- Mark every person's name, role, QR code and price on a mockup as a sample. Never show a real
  person as staff or a customer.
- Draw items at their real proportions: name card 85 × 55, A5 148 × 210, roll-up 850 × 2000.
- Mockups are illustrations, not proofs. Say so in the footer.
- Fonts load from Google Fonts with a system fallback; the logos themselves are outlined paths,
  so they render the same even if the web font fails.
- Render each tab once in Playwright before publishing and fix what spills or clips (textures
  outside a shape, text running past a card edge, wrong item proportions).

## Review Checklist (per item, per concept)

Screenshot every card at about 600 px, for every concept, and check:

| Check | Typical fault |
|---|---|
| The whole object is inside the stage with a margin (handle, brim, base, phone top) | A mug handle or lanyard cut off at the card edge |
| Each face of a 3D object maps the art on that face's own axes | Box lid art rotated 90°: build the lid matrix from its two edges (front edge = x, back-to-front edge = y), e.g. `matrix(0.78 0.264 -0.78 0.264 cx cy)` |
| The object reads as itself at a glance | A cap brim drawn as a dark band under the crown looks like a bucket hat; draw the bill as a lit crescent with a stitch line |
| Light garments keep folds and texture subtle | Strong side shading on a white tee looks like coloured panels; keep folds at about a quarter of the dark-garment strength |
| Print effects don't wash the logo out | Ink "holes" filter so strong that a navy logo prints grey; keep speckle light |
| The longest name and the tallest mark both fit | A long wordmark overflowing a flyer header; wrap the tagline onto two lines |
| No colour mark on the brand-colour ground | Cap, flag, box side |
