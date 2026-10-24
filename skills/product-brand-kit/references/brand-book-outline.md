# Brand Book Outline

## Tokens

Write tokens as lists of `{name, value, usage}` objects (`value` per theme for colours), never as a name→value map.
Every usage note names where the token is used and the grounds it was checked on.

| Token | Role |
|---|---|
| `brand` (e.g. `flash`), `brand-strong`, `brand-soft` | Fill, hover, tinted ground |
| `on-brand` | Text on the brand fill; never white on yellow |
| `block` (e.g. `darkroom`), `on-block`, `on-block-muted` | Sidebar, app-icon tile, dark bands |
| `paper`, `surface`, `surface-sunk` | Page, cards, wells |
| `line`, `line-strong` | Hairlines; control borders (3:1) |
| `ink`, `ink-muted` | Text; secondary text (≥ 4.5:1) |
| `focus` | 2 px solid ring, 2 px offset; ≥ 3:1 on every surface |
| `success`, `danger`, `warning`, `info`, `status-tint` | Status, each paired with a word or icon |

Contrast script:

```python
def lum(h):
    r, g, b = [int(h.lstrip('#')[i:i+2], 16)/255 for i in (0, 2, 4)]
    f = lambda c: c/12.92 if c <= 0.03928 else ((c+0.055)/1.055)**2.4
    return 0.2126*f(r) + 0.7152*f(g) + 0.0722*f(b)
ratio = lambda a, b: (max(lum(a), lum(b)) + .05) / (min(lum(a), lum(b)) + .05)
```

Type: display (800/600), text (400/500/600/700), data/mono (400/600), all with real font files.
Styles to define: `display-xl`, `display`, `title`, `heading`, `body`, `body-strong`, `small`, `label` (uppercase,
tracked), `mono`, `metric`.

Spacing 4/8/12/16/24/32/48. Radius sm 8, md 12, lg 16, pill 999, and the icon tile at 22%.

## Brand Book Sections

1. **Opening**: what the product is, its code name, the metaphor, the tagline and a short line for bios
2. **The idea behind the mark**: what each element stands for
3. **Content fundamentals**:
   - voice, person ("you"), sentence case
   - product-name casing
   - "the metaphor stays in the brand, not the interface"
   - buttons that say exactly what they do
   - errors that name the target, the cause and the fix
   - date and timezone format
   - no emoji
4. **Logo usage**:
   - clear space = the mark's diameter
   - minimum sizes; below 24 px use the app icon
   - which file goes on which ground
   - the don'ts
   - the endorsement line
5. **Colour**: how much brand colour per screen, text-on-fill rules, status rules, third-party brand colours kept inside their badges
6. **Typography**: roles and line length
7. **Layout and shape**: spacing, radii, borders over shadows, app frame, focus ring
8. **Photography**:
   - light, subjects, setting, people, palette fit, what to avoid
   - the art-direction brief that `photo-sourcing.md` searches with
9. **Iconography**: one line-icon set, official third-party glyphs only, no mascots
10. **Motion**: durations and the one brand animation, with respect for reduced motion
11. **Web**: the domain, how to write it, and the plan for moving to an own domain later

## Components

2–3 product-specific components with static previews, each with a README:
- what it shows
- variants
- what the consumer provides
- do / don't

Always include Button. Then add the product's signature row or chip, e.g. a status chip and a
per-target result row showing a partial failure with "Retry <target>".

## Cover

Name in the display face at bottom-left with the tagline, brand blocks on the right, and one pattern
taken from the metaphor (e.g. quarter-discs pinwheeling like shutter blades). No words on the blocks.
