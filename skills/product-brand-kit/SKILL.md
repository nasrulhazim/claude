---
name: product-brand-kit
metadata:
  compatible_agents: [claude-code]
  tags: [branding, naming, logo, golden-ratio, design-system, landing-page, photography, marketing, print, social-media, export, whatsapp, mockup, merchandise, apparel, packaging]
description: >
  Takes a scoped product from name to launch-ready identity and launch material in one run: naming
  options that keep an internal code name apart from the public brand, a logo concept drawn from
  the product's own metaphor, constructed on the golden ratio (golden rectangle outer shape, φ-stepped
  proportions, φ lockup, clear space and icon padding) with the wordmark built from real font outlines
  (never SVG text), a
  brand kit with contrast-checked light and dark colour tokens, type, a brand book and
  product-specific components, domain advice that avoids paying for premium exact matches before
  revenue, a licensed and art-directed photo set from Unsplash, Pexels, Openverse or the user's own
  shoots with a credits file, a marketing landing page with no invented claims, a production check (full, simplified and micro
  marks, knockout and stacked variants, colour spec, minimum-detail tests for embroidery, pad print,
  engraving, deboss, screen print and icons), a mockup studio showing the logo on apparel, print,
  merchandise, packaging and the product on laptop, tablet and phone (real product photos when reachable), a logo export pack
  in every size and format (SVG, transparent PNGs, app icons, favicon.ico, Open Graph image,
  circle-safe WhatsApp/social avatars), and a marketing kit covering digital material (message
  house, social post set, captions, carousel, video script, email, WhatsApp broadcast, blog,
  press release, ads, deck) and print material (flyer, poster, roll-up banner, bunting, booth
  backdrop, brochure, stickers, merchandise) with bleed, QR and UTM rules. Use this skill whenever
  the user asks to "name this product", "design the logo and brand kit", "make a landing page",
  "find photos for the landing page", "which domain should I buy", "export the logo in all sizes",
  "logo for the WhatsApp group", "use the golden ratio for the logo", "is the logo ready for print and embroidery",
  "show the logo on t-shirts, mugs, packaging and the app", "create marketing materials", "social media posts for the launch",
  "design a flyer / bunting / roll-up banner", "cadangkan nama produk", "reka logo dan brand kit", "guna nisbah emas untuk logo",
  "buat landing page", "cari gambar untuk landing page", "domain mana patut beli", "bagi logo semua
  saiz", "logo boleh print dan sulam tak", "buat mockup baju, mug, kotak dan app", "buat bahan marketing", "buat post media sosial", or "reka flyer dan bunting". Runs after
  product-blueprint; use svg-logo-system or logo-designer to explore many logo concepts first, and
  business-card for name cards.
---

# Product Brand Kit

Goes from "this product needs a name" to a logo, a brand book, a photo set, a landing page, a zip
of every logo file, and the launch material the team will ask for in the first month, online and in
print. Each step builds on the ones before it, so run them in order unless the user asks for a
single piece.

## Command Reference

| Command | Description |
|---|---|
| `/brand name` | Naming options: code name vs public brand, with one pick |
| `/brand logo` | Logo concept on the golden ratio, wordmark from font outlines, all variants, construction drawing, contact sheet |
| `/brand kit` | Colour and type tokens, brand book, components, cover |
| `/brand domain` | Domain recommendation and the migration plan for moving domains later |
| `/brand photos` | Art-directed, licensed photo set per slot, with a credits file |
| `/brand landing` | Marketing landing page using the kit and photo set |
| `/brand production` | Mark levels, knockout and stacked variants, colour spec, size tests by medium, vendor pack |
| `/brand mockups` | Mockup studio: apparel, print, merchandise, packaging and digital (laptop, tablet, phone) |
| `/brand export` | Every logo size and format, zipped, with the WhatsApp/social avatar called out |
| `/brand marketing` | Message house, then digital and print marketing pieces and a launch calendar |

---

## When to Use

- A product is scoped (often by `product-blueprint`) and needs a public identity
- The user has a name and wants a logo, brand kit, landing page or photos built on it
- The team needs logo files or launch material now: a WhatsApp group icon, social posts, a flyer, a bunting for an event

**Do not** use this skill:
- to explore 25 logo concepts (use `svg-logo-system` or `logo-designer`)
- for name cards (use `business-card`)
- for pricing, quotations and revenue planning (use `sales-planner`)

---

## Step 1: Name

1. Separate two names:
   - **internal code name**: the product-family prefix plus one word, lowercase (e.g. `g8market`)
   - **public brand**: doesn't need the family prefix; endorsed as "<Brand> · part of <family>"
2. Offer about 10 options in a table: name · meaning · feel · fit. Mix family-prefixed, English
   standalone and local-language standalone names (`references/naming-patterns.md`).
3. Give one pick with a one-line reason. Flag a real connotation risk once (a negative meaning, a
   collision). If the user picks it anyway, go with their choice.

## Step 2: Logo

1. Draw the concept from the product's own metaphor and make one element stand for something true
   about the product. Example: four shutter blades for four publishing platforms.
2. Construct the mark on the golden ratio (`references/golden-ratio-construction.md`):
   - **outer shape**: a square or a 1:φ golden rectangle
   - **proportions**: every radius, bar height and width-to-gap step comes from one φ scale
   - **lockup**: wordmark ink height = mark height ÷ φ, gap = mark height ÷ φ³
   - **clear space** = mark height ÷ φ²; **app icon** mark = tile ÷ φ
   Derive each dimension from φ in code, never by eye, and keep the ratio report.
3. Build the wordmark from real glyph outlines and generate the mark's geometry in code
   (`references/logo-build-pipeline.md`). Never ship `<text>` in a logo SVG. It falls back to
   whatever font the viewer has.
4. Produce the variants (`references/production-readiness.md`):
   - mark at three levels: **full** (48 px+), **simplified** (24–47 px, embroidery, deboss), **micro** (favicon)
   - wordmark lockup and **stacked** lockup: colour, reversed, mono-ink, mono-paper
   - mono variants as true **knockouts** (holes, not white ink)
   - app icon: dark tile and light tile, mark raised by tile ÷ φ⁸ for the optical centre
5. Render the construction drawing (golden rectangle and the mark's guides over a ghosted mark)
   and a contact sheet on light, dark and at 16–28 px. Look once. Fix strokes that poke out,
   blades that vanish and colour-on-colour clashes.
6. Present several concepts side by side when the user asks for options: one board per concept with
   the colour and reversed lockups, the construction drawing, the app icon at 112/32/16 px, and
   meaning · why it fits · watch out.

## Step 3: Brand Kit

1. Tokens: brand fill + `on-brand` text, a dark brand block + its text, ground, surface, sunk,
   line, line-strong, ink, ink-muted, focus, and four status colours. Light theme plus a dark theme
   named for the metaphor. Carry the prototype's colours over when one exists.
2. Contrast-check every text pair in both themes with a script: 4.5:1 for text, 3:1 for control
   borders and focus rings. Change any shade that fails.
3. Type: a display face, a text face and a data face, with real font files (fontsource).
4. Brand book and 2–3 product-specific components (`references/brand-book-outline.md`). The brand
   book includes a **photography** section: light, subjects, setting, people, palette fit, what to avoid.
5. Output:
   - **Design System artifact type available**: create the kit from it, uploading the logos as assets.
   - **Otherwise**: write `brand/<product>/` with `tokens.json`, `README.md`, `fonts/`, `logos/`.

## Step 4: Domain

- Advise against premium exact-match domains before revenue. Offer prefix/suffix patterns
  (`get-`, `use-`, `try-`, `-hq`, `-app`) on the local ccTLD and `.app`, with typical prices
  marked unverified.
- If the user already owns a company domain, a launch subdomain (`<brand>.<company-domain>`) is a
  valid first step. Record the domain rules in the brand book, including the migration hygiene in
  `references/naming-patterns.md`.

## Step 5: Photos

Follow `references/photo-sourcing.md`:

1. Write the art-direction brief from the brand book's photography section.
2. List the slots the landing page and marketing pieces need, with ratio and minimum size.
3. Search licensed sources in order: the user's own shoots, Unsplash, Pexels, Pixabay, Openverse,
   Wikimedia Commons. Pick one photo per slot.
4. Get the files by whichever route the environment allows:
   - **Download** via the API when the network allows it
   - **Network blocked**: give the user a shortlist with links and credits, and ask them to allowlist the photo domains or attach the files
   - **Inside an artifact**: upload each photo as an asset, because external image URLs are blocked
5. Write `photos/CREDITS.md`. Never use photos of real, identifiable people as fake customers.

## Step 6: Landing Page

Build it from the kit's tokens, logo and photo set (`references/landing-page-sections.md`).

- **Design artifact type available**: create the page on a fluid page artboard.
- **Otherwise**: write `brand/<product>/landing/index.html`.

Replace any claim the user hasn't confirmed (prices, hosting region, offers, dates, features outside
the requirements) with a `[PLACEHOLDER]`, and list those placeholders in the reply. Put the photo
credit line in the footer.

## Step 7: Production Readiness

Follow `references/production-readiness.md`:

1. Every mark function returns its features (smallest stroke, gap, tooth, counter). Run the size
   test for each medium: favicon, app icons, embroidery (cap, left chest), pad print, laser
   engraving, deboss, screen print. Fix failures in the geometry of the right mark level, never by
   lowering the threshold, and re-run the table.
2. Check contrast on both grounds: text 4.5:1, graphic parts 3:1, ink vs accent told apart by
   lightness. Shift lightness, keep hue, when a colour fails, and tell the user which colours changed.
3. Run the perception tests once: squint (blur), greyscale, one colour, reversal, small size.
4. Write the colour spec (HEX, CMYK start, Pantone and thread left to physical guides), the
   placement guide and the vendor pack list.

## Step 8: Mockup Studio

Follow `references/mockup-studio.md`: one page with a concept switcher and tabs for **Production
check, Apparel, Print, Merchandise and Digital**.

- **Apparel:** tees (light and dark), polo left chest, cap, lanyard and ID card
- **Print:** name card with foil back, A5 flyer, roll-up, feather flag, stickers
- **Merchandise:** packaging box, mug, tote, notebook, pen, tumbler
- **Digital:** laptop and tablet showing the web app, phone showing the mobile app (realistic
  screens with sample data, not grey boxes); in drawn mode also favicon, home-screen icon, splash
  and laptop lid

Use real blank product photos when the environment can fetch them (photo mode); otherwise build
the SVG items and say so. Position every logo by `references/logo-placement.md`: pick front-facing
photos, measure reference points (centre front, 2nd button, crown seam, card corners), place at
the standard position and real size, and add a zoomed inset for small placements such as the left chest. Each card names the technique, variant and real size, with its size-test
result. Render each tab once and fix what spills or clips before publishing.

## Step 9: Export Pack

Render every file in `references/export-matrix.md`, zip it with a README, and send the files. Name the
exact file to use for the most urgent need first, e.g. "use `<brand>-avatar-dark-640.jpg` for the
WhatsApp group icon".

## Step 10: Marketing Kit

Follow `references/marketing-kit.md`:

1. Write the **message house** first: tagline, one-liner, three pillars with proof points from the
   requirements, audiences, CTA, words to use and avoid. Write it in EN and BM when the market is local.
2. Ask which pieces the user needs now. With nobody to ask, default to:
   - **digital**: launch social set, captions per platform, WhatsApp broadcast, announcement email, launch article
   - **print**: A5 flyer and an 850×2000 mm roll-up banner
3. Build the digital pieces at exact pixel sizes and the print pieces at their final size in mm, with
   bleed and a safe area. All of them use the kit, the logo masters and the photo set.
4. QR codes point to a redirect path you control, with a UTM tag per piece.
5. Finish with a two-week launch calendar: week · channel · piece · owner · status.

Output:
- **claude.ai**: Design artboards for visual pieces, Slides for decks, Docs for articles and press releases.
- **Claude Code**: `brand/<product>/marketing/{digital,print}/`, with PNGs and PDFs rendered by Playwright, plus `copy.md`.

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The proportions look balanced, no need for φ" | "Looks balanced" can't be checked or repeated. Derive the box, radii, bar steps, lockup and padding from φ and keep the ratio report, so the next revision lands on the same grid. |
| "It looks fine on screen, it'll print fine" | Embroidery, pad print and favicons each have a minimum detail size. Measure the smallest feature at the real size; a 0.9 px gap or a 0.6 mm tooth fills in. |
| "White ink on the dark tee is the same as a knockout" | It isn't: white ink adds a screen, cracks and covers the fabric. Knockouts let the garment show through. |
| "A flat SVG t-shirt is enough to sell the logo" | Clients judge the logo on things they recognise. Use real product photos when they can be fetched; otherwise say the mockups are drawn. |
| "SVG `<text>` with the brand font is fine" | Inside `<img>`, on another machine, or in a PNG export, the font isn't there and the wordmark changes shape. Outline the glyphs. |
| "Yellow on yellow works if we add an outline" | An outline is a patch for the wrong file. Use the mono-ink variant on brand-colour grounds. |
| "The colours look readable" | Contrast is a number. A muted grey that looks fine on a laptop fails 4.5:1 on a phone in sunlight. Run the check. |
| "Just put the full logo in the WhatsApp icon" | WhatsApp crops to a circle and shows the icon at ~48 px. A wordmark becomes a smudge; the mark at 58% of the square survives. |
| "Hosted in Malaysia sounds right, I'll add it" | An unconfirmed claim on a public page is a liability. It becomes a placeholder until the user confirms it. |
| "Buy the exact-match domain now so nobody takes it" | Paying a premium price before revenue rarely pays back. A modifier domain or a subdomain launches the same day. |
| "It's on Google Images, it's fine" | Search results aren't a licence. Use a licensed source and record it in CREDITS.md. |
| "A stock smiling face works as a testimonial" | A stock model presented as a customer is a fake endorsement. Testimonials use real customers or none. |
| "The web hero photo will do for the bunting" | A 1600 px photo on an 850×2000 mm banner prints as mush. Check the pixel count at 300 dpi (or the printer's minimum for large format). |
| "We'll write the BM version later" | Later means two drifting messages. Write both from the same message house now. |

---

## Red Flags

- A mark's aspect ratio is neither 1.000 nor 1.618, or the lockup's ink/gap/clear ratios aren't 0.618/0.236/0.382
- No construction drawing ships with the chosen logo
- A logo SVG contains `<text>` or references a web font
- A mark's strokes extend past its disc or tile at large sizes
- The colour mark is used on the brand-colour background in any export
- A token's usage note doesn't name the grounds it was checked on
- The landing page states prices, hosting, certifications or dates nobody gave you
- The export pack has no favicon or no square avatar, or the avatar's mark crosses the circle crop
- The brand book uses the brand metaphor's words as UI status labels
- A photo has no row in CREDITS.md, or is hotlinked from the source site
- A photo shows a third-party logo, a competitor's UI or a recognisable person used as an endorser
- A print file has no bleed, text sits outside the safe area, or a QR is smaller than 20 mm
- A size test was passed by lowering its threshold, or a medium has no test
- The colour mark appears on the brand-colour ground in a mockup (cap, flag, box side)
- A logo floats on a crumpled or angled garment, sits off the left-chest or cap-front position, or
  ignores the item's real-world size
- A mockup texture or logo spills outside the item, or an item is drawn at the wrong proportions
- A device mockup shows white slivers at the screen edge, covers the bezel, or fills the screen with
  grey placeholder boxes instead of a realistic app view
- A Pantone or thread code appears that nobody picked from a physical guide
- Marketing copy contradicts the message house or makes a claim the landing page doesn't

---

## Verification

- [ ] Code name and public brand are recorded separately
- [ ] Mark box is 1:1 or 1:φ; radii, bar steps, lockup, clear space and icon padding come from φ (ratio report kept)
- [ ] A construction drawing ships with the logo and the brand book has a Construction subsection
- [ ] Wordmark glyphs are paths; the contact sheet was rendered and looked at once
- [ ] Every text token pair passes 4.5:1 in both themes (script output kept)
- [ ] Brand book covers story, voice, logo rules, colour, type, layout, photography, iconography, motion
- [ ] Every photo is licensed, sized for its slot, credited in CREDITS.md and uploaded (never hotlinked)
- [ ] Landing page placeholders are listed back to the user
- [ ] Full, simplified and micro marks exist; every size test by medium passes (table kept)
- [ ] Mono variants are true knockouts; a stacked lockup exists
- [ ] Colour spec lists HEX and CMYK start, with Pantone and thread marked for physical matching
- [ ] Mockup studio covers apparel, print, merchandise, packaging and digital, and states photo or drawn mode
- [ ] Every mockup passed the placement review in `references/logo-placement.md` (position, size, axis, no other brands)
- [ ] Export zip holds SVG masters, transparent PNGs, app icons 16–1024, favicon.ico, OG image,
      circle-safe avatars at 640 and 1080, and a README naming which file goes where
- [ ] Message house exists, in EN and BM when local; each marketing piece traces to it
- [ ] Print files are at final size with bleed and safe area, photos are 300 dpi, and each QR has a UTM tag
- [ ] A launch calendar lists every piece with a channel, an owner and a week
- [ ] The user was told which artifacts are private until shared

---

## Reference Files

| File | Read When |
|---|---|
| `references/naming-patterns.md` | Proposing names, judging connotations, recommending a domain and its migration plan |
| `references/golden-ratio-construction.md` | Constructing the mark, lockup, clear space and icon on φ; construction drawing; ratio check |
| `references/production-readiness.md` | Mark levels, knockout and stacked variants, colour spec, size tests by medium, perception tests, vendor pack, legal |
| `references/logo-placement.md` | Where the logo goes on each apparel, print, merch and digital item, at what size, in which variant; the placement review |
| `references/mockup-studio.md` | Building the mockup page: apparel, print, merchandise, packaging, digital; photo mode and SVG fallback; material filters |
| `references/logo-build-pipeline.md` | Building the wordmark from font outlines and generating the mark geometry |
| `references/brand-book-outline.md` | Writing tokens, the brand book sections and the component set |
| `references/photo-sourcing.md` | Choosing photo sources, the art-direction brief, slots, downloading, treatment and credits |
| `references/landing-page-sections.md` | Building the landing page and checking its claims |
| `references/export-matrix.md` | Rendering the export pack: every size, format and the render script |
| `references/marketing-kit.md` | Writing the message house and building each digital and print piece, plus the launch calendar |
