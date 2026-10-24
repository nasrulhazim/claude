# Marketing Kit

Launch material built from the brand kit: digital content first, then print. Every piece uses the
brand tokens, logo files and photo set. Every claim passes the claim check in `landing-page-sections.md`.

## Message House (write first, one page)

Everything else reuses this wording.

| Block | Content |
|---|---|
| Tagline | The brand book's tagline |
| One-liner | What it does, for whom, in under 20 words |
| Three pillars | The 3 benefits, each with one proof point taken from the requirements |
| Audiences | 2–3 segments, each with its pain and its hook |
| CTA | One primary action and the URL or QR target |
| Words to use / avoid | From the brand book's content fundamentals |

Write it in English and Bahasa Malaysia when the audience is local. Keep both versions side by side so they stay in sync.

## Digital

| Piece | Format and size | Notes |
|---|---|---|
| Launch social set | 1080×1080 and 1080×1350 (FB/IG/LinkedIn/Threads), 1080×1920 (stories) | 6–9 posts: teaser, launch, one per pillar, how it works carousel, early-access CTA, FAQ |
| Per-platform captions | Text | LinkedIn longer and professional; IG short with hashtags (≤ 30); Threads ≤ 500 chars; FB with link |
| Carousel | 1080×1350 × 5–8 slides | Hook → problem → 3 steps → proof → CTA |
| Short video script | 15s, 30s, 60s (9:16) | Scene list, on-screen text and voice-over. Pair with a promo-video skill if one is available |
| Email announcement | 600 px wide HTML + plain text | Subject + preview text, hero, 3 pillars, one CTA |
| Email nurture | 3–5 emails | Welcome → setup → first win → feature → upgrade. Pair with an email-sequence skill if one is available |
| WhatsApp broadcast / group message | Text ≤ 700 chars + 1 image | Plain, friendly, one link, no hashtag spam. Separate BM and EN versions |
| WhatsApp group kit | Avatar, banner, pinned rules message, description ≤ 512 chars | Uses the export pack's circle-safe avatar |
| Launch blog / article | 800–1200 words | Problem → approach → how it works → who it's for → CTA |
| Press release | 1 page | Headline, dateline, lede, quote placeholder `[SPOKESPERSON QUOTE]`, boilerplate, contact |
| Ads | Meta 1080×1080, 1200×628; Google responsive text | 3 headline + 3 description variants per audience |
| Slide deck | 16:9, 10–12 slides | Pitch or tech-update session. Use the Slides artifact type when available |
| Email signature banner | 600×150 | Logo, tagline, URL |

## Physical / Print

| Piece | Final size | Bleed | Notes |
|---|---|---|---|
| Flyer | A5 148×210 mm (or A4) | 3 mm | Front: hook + visual + QR; back: 3 pillars, pricing pointer, contact |
| Poster | A3 297×420 mm / A2 420×594 mm | 3 mm | One message, big type, QR bottom right |
| Roll-up banner | 850×2000 mm (common in Malaysia) | Per printer, usually 5 mm | Logo top, message upper third, nothing important in the bottom 300 mm |
| Bunting / X-banner | 600×1600 mm | 5 mm | Same rule as the roll-up |
| Booth backdrop | e.g. 3×2.4 m | Per vendor | Logo at eye level, one line, giant QR |
| Tri-fold brochure | A4 folded to DL | 3 mm | Panel order: cover, back, flap, then the inside spread |
| Business card | 90×54 mm (MY standard) | 3 mm | Use the `business-card` skill |
| Stickers | 50 mm die-cut mark, 60×20 mm wordmark | 2 mm | Mono-ink on brand colour, colour mark on white |
| Merchandise | T-shirt front 250 mm wide, lanyard 20 mm | Vendor template | Mono marks only. Never the colour mark on the brand-colour fabric |

Print rules:
- Build at the final size in mm with the bleed and a 5 mm safe area. Text and logos stay inside the safe area.
- Photos at 300 dpi at the final size (`photo-sourcing.md`). Logos always from the SVG masters.
- Supply a PDF with bleed. Note CMYK: the screen brand colour may shift in print, so put the hex value and
  "match on proof" on the spec sheet. Ask the printer for their profile.
- QR codes: at least 20 mm on print, pointing at a redirect path you control (survives domain moves),
  with a UTM tag per piece (`?utm_source=flyer&utm_medium=print&utm_campaign=launch`).
- Use the short URL on print, written in lowercase.

## How to Build Each Piece

| Environment | Digital pieces | Print pieces |
|---|---|---|
| claude.ai / Cowork with artifact types | Design type canvas: one artboard per post or size; Slides type for decks; Docs type for the article and press release | Design type artboards at the final size, exported as PDF |
| Claude Code | `brand/<product>/marketing/digital/*.html`, rendered to PNG with Playwright at exact pixel sizes | `brand/<product>/marketing/print/*.html` with CSS `@page { size: 148mm 210mm; margin: 0 }`, rendered with Playwright `page.pdf({ width, height, printBackground: true })` including bleed |

Copy lives in `brand/<product>/marketing/copy.md`: the message house, then each piece's text, in EN and BM.

## Calendar

End with a launch calendar table: week · channel · piece · owner · status. The first two weeks are
teaser → launch → pillar posts → early-access push. Hand the schedule to the product itself if it
publishes social posts.
