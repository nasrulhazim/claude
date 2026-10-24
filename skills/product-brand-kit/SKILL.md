---
name: product-brand-kit
metadata:
  compatible_agents: [claude-code]
  tags: [branding, naming, logo, design-system, landing-page, domain, export, whatsapp]
description: >
  Takes a scoped product from name to launch-ready identity in one run: naming options that keep an
  internal code name apart from the public brand, a logo concept drawn from the product's own
  metaphor with the wordmark built from real font outlines (never SVG text), a brand kit with
  contrast-checked light and dark colour tokens, type, a brand book and product-specific
  components, domain advice that avoids paying for premium exact matches before revenue, a
  marketing landing page with no invented claims, and a logo export pack in every size and format
  (SVG, transparent PNGs, app icons, favicon.ico, Open Graph image and circle-safe WhatsApp/social
  avatars). Use this skill whenever the user asks to "name this product", "propose a brand name",
  "design the logo and brand kit", "make a landing page for it", "which domain should I buy",
  "export the logo in all sizes", "logo for the WhatsApp group", "cadangkan nama produk", "reka
  logo dan brand kit", "buat landing page", "domain mana patut beli", or "bagi logo semua saiz".
  Runs after product-blueprint; use svg-logo-system or logo-designer instead when the user wants
  to explore many logo concepts before committing to one.
---

# Product Brand Kit

Goes from "this product needs a name" to a logo, a brand book, a landing page and a zip of every
logo file the team will ask for in the first month. Each step uses what the steps before it
decided, so run them in order unless the user asks for one of them alone.

## Command Reference

| Command | Description |
|---|---|
| `/brand name` | Naming options: code name vs public brand, with one pick |
| `/brand logo` | Logo concept, wordmark from font outlines, all variants, contact sheet |
| `/brand kit` | Colour and type tokens, brand book, components, cover |
| `/brand domain` | Domain recommendation and the migration plan for moving domains later |
| `/brand landing` | Marketing landing page using the kit |
| `/brand export` | Every size and format, zipped, with the WhatsApp/social avatar called out |

---

## When to Use

- A product is scoped (often by `product-blueprint`) and needs a public identity
- The user has a name and wants a logo, a brand kit or a landing page built on it
- The team needs logo files right now (a WhatsApp group icon, a favicon, a slide)

**Do not** use this skill to explore 25 logo concepts (use `svg-logo-system` or `logo-designer`),
for a business card (use `business-card`), or for pricing and marketing copy strategy (use
`sales-planner`).

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
2. Build the wordmark from real glyph outlines and generate the mark's geometry in code
   (`references/logo-build-pipeline.md`). Never ship `<text>` in a logo SVG. It falls back to
   whatever font the viewer has.
3. Produce the variants:
   - wordmark: colour, reversed, mono-ink
   - mark: colour, mono-ink, mono-paper
   - app icon: dark tile and light tile
4. Render a contact sheet on light, dark and at 16–28 px. Look once. Fix strokes that poke out,
   blades that vanish and colour-on-colour clashes.

## Step 3: Brand Kit

1. Tokens: brand fill + `on-brand` text, a dark brand block + its text, ground, surface, sunk,
   line, line-strong, ink, ink-muted, focus, and four status colours. Light theme plus a dark theme
   named for the metaphor. Carry the prototype's colours over when one exists.
2. Contrast-check every text pair in both themes with a script: 4.5:1 for text, 3:1 for control
   borders and focus rings. Change any shade that fails.
3. Type: a display face, a text face and a data face, with real font files (fontsource).
4. Brand book and 2–3 product-specific components (`references/brand-book-outline.md`).
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

## Step 5: Landing Page

Build it from the kit's tokens and logo (`references/landing-page-sections.md`).

- **Design artifact type available**: create the page on a fluid page artboard.
- **Otherwise**: write `brand/<product>/landing/index.html`.

Replace any claim the user hasn't confirmed (prices, hosting region, offers, dates, features outside
the requirements) with a `[PLACEHOLDER]`, and list those placeholders in the reply.

## Step 6: Export Pack

Render every file in `references/export-matrix.md`, zip it with a README, and send the files. Name the
exact file to use for the most urgent need first, e.g. "use `<brand>-avatar-dark-640.jpg` for the
WhatsApp group icon".

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "SVG `<text>` with the brand font is fine" | Inside `<img>`, on another machine, or in a PNG export, the font isn't there and the wordmark changes shape. Outline the glyphs. |
| "Yellow on yellow works if we add an outline" | An outline is a patch for the wrong file. Use the mono-ink variant on brand-colour grounds. |
| "The colours look readable" | Contrast is a number. A muted grey that looks fine on a laptop fails 4.5:1 on a phone in sunlight. Run the check. |
| "Just put the full logo in the WhatsApp icon" | WhatsApp crops to a circle and shows the icon at ~48 px. A wordmark becomes a smudge; the mark at 58% of the square survives. |
| "Hosted in Malaysia sounds right, I'll add it" | An unconfirmed claim on a public page is a liability. It becomes a placeholder until the user confirms it. |
| "Buy the exact-match domain now so nobody takes it" | Paying a premium price before revenue rarely pays back. A modifier domain or a subdomain launches the same day. |

---

## Red Flags

- A logo SVG contains `<text>` or references a web font
- A mark's strokes extend past its disc or tile at large sizes
- The colour mark is used on the brand-colour background in any export
- A token's usage note doesn't name the grounds it was checked on
- The landing page states prices, hosting, certifications or dates nobody gave you
- The export pack has no favicon or no square avatar, or the avatar's mark crosses the circle crop
- The brand book uses darkroom/metaphor words as UI status labels

---

## Verification

- [ ] Code name and public brand are recorded separately
- [ ] Wordmark glyphs are paths; the contact sheet was rendered and looked at once
- [ ] Every text token pair passes 4.5:1 in both themes (script output kept)
- [ ] Brand book covers story, voice, logo rules, colour, type, layout, iconography, motion
- [ ] Landing page placeholders are listed back to the user
- [ ] Export zip holds SVG masters, transparent PNGs, app icons 16–1024, favicon.ico, OG image,
      circle-safe avatars at 640 and 1080, and a README naming which file goes where
- [ ] The user was told which artifacts are private until shared

---

## Reference Files

| File | Read When |
|---|---|
| `references/naming-patterns.md` | Proposing names, judging connotations, recommending a domain and its migration plan |
| `references/logo-build-pipeline.md` | Building the wordmark from font outlines and generating the mark geometry |
| `references/brand-book-outline.md` | Writing tokens, the brand book sections and the component set |
| `references/landing-page-sections.md` | Building the landing page and checking its claims |
| `references/export-matrix.md` | Rendering the export pack: every size, format and the render script |
