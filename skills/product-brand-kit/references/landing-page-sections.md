# Landing Page Sections

A fluid page, ~1200 px content width, that works at 390 px. Colours, type and logo all come from
the brand kit.

| # | Section | Content | Notes |
|---|---|---|---|
| 1 | Nav | Wordmark, 4 anchors, Sign in, primary CTA | Anchors hide on phones; the CTA stays |
| 2 | Hero | Eyebrow pill, tagline as H1, one-sentence promise, two CTAs, endorsement line | Product visual built in markup: the signature screen in its most telling state (e.g. 3 of 4 published, one "Retry") |
| 3 | Integration strip | "Works with" + official names | Third-party glyphs only as small badges |
| 4 | How it works | The real sequence, numbered | Number only if it's a true order |
| 5 | Features | ~8 features with line icons | Features not built yet get "Coming <year>", matched to the roadmap |
| 6 | Trust band | Dark brand block: the safety or AI promise, as an allowed/never list | One message only |
| 7 | Local fit | One wide photo of a local workplace + 4 cards: timezone, privacy law, multi-tenant, fast lanes | Only claims grounded in the requirements; photo from the `local fit band` slot |
| 8 | Pricing | 2 self-serve plans + enterprise "Quotation" | `RM [PRICE]` until the user gives prices |
| 9 | FAQ | 4 `<details>` | Answer the hardest questions first: failures, accounts needed, data location |
| 10 | CTA band | Brand-colour band, short line, real form with `<label>`s | No fake success message |
| 11 | Footer | Reversed wordmark, link columns, legal name, website, registration number, public contact, photo credit line | All from project context |

## Photos

Use the photo set from `photo-sourcing.md`:
- one hero-support photo that sits beside the product visual and never covers its UI
- one wide local-fit photo
- testimonial photos only of real customers

Upload each photo as an asset inside artifacts. Use `loading="lazy"` below the fold, and write alt text that describes the scene.

## Claim Check (before publishing)

Turn each of these into a `[PLACEHOLDER]` unless the user stated it:

- prices, discounts, early-access offers
- hosting region, certifications, uptime figures
- launch dates and customer counts
- features outside the requirements (no "BM and English captions side by side" unless a BR says so)
- personal contact details; use the company's public contact only

List every placeholder back to the user in the reply.
