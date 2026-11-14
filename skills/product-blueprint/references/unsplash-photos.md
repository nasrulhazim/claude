# Unsplash Photos for Prototypes

A prototype uses real photos only where the product itself would show a picture. Everything else
is type, colour, data and icons.

## When a Photo Earns a Slot

| Use a photo | Don't |
|---|---|
| Content the product displays: listings, venues, products, events, courses | Avatars of users, customers or staff — use initials on a token colour |
| An onboarding or sign-in side panel | Testimonials or "trusted by" faces |
| An empty state where the product invites the user to add pictures (show one sample) | Decorative backgrounds behind dashboards and tables |
| A marketing panel inside the app (upgrade, announcement) | Anything with a visible third-party logo or another app's UI |

Each photo slot is listed in `DESIGN.md` with its ratio before searching.

## Search and Art Direction

1. Write a one-line brief per slot from `DESIGN.md`: subject, setting, light, palette fit.
   For Malaysian products, put the local setting in the query ("kuala lumpur", "malaysian",
   "kedai", "campus").
2. Run 2–3 queries per slot and pick one photo per slot. Keep one consistent light and tone across
   the set.
3. Reject: staged handshakes, people pointing at blank screens, watermarks, recognisable brands.

## Getting Photos — Three Routes

| Route | When | How |
|---|---|---|
| Unsplash MCP connector | Connected in the session | `search_photos` with the query, orientation and colour; record id, photographer, profile URL and photo page URL |
| Unsplash API | Network allowed and `UNSPLASH_ACCESS_KEY` set | `GET https://api.unsplash.com/search/photos?query=…&orientation=landscape` with `Authorization: Client-ID $UNSPLASH_ACCESS_KEY` |
| Shortlist | Neither | WebSearch on unsplash.com, give the user a table (slot · photo page URL · photographer); use labelled placeholders sized to the slot until they confirm |

## Hotlink, Don't Re-host (Netlify)

The Unsplash API guidelines ask apps to **hotlink** the returned `urls.*` and to call the photo's
`links.download_location` once when the photo is used. For a Netlify prototype:

- Use `urls.raw` with sizing params: `…&w=1200&q=80&fm=webp&fit=crop&crop=entropy`
  and add `width`/`height` attributes so the layout doesn't shift
- `loading="lazy"` except the first visible image
- Call `download_location` (with the same `Client-ID`) for each photo that ships
- the `_headers` CSP allows `img-src 'self' https://images.unsplash.com`

Exception: a Claude artifact blocks external images by CSP — upload the file as an artifact asset
there instead. Photos from Pexels or Openverse are downloaded into `prototype/img/` (their terms
allow it; Openverse BY licences need attribution).

## Credits

Two places, both required:

1. On the page that shows the photo, a small caption or footer line:
   `Photo by <a href="https://unsplash.com/@user?utm_source=<app>&utm_medium=referral">Name</a> on
   <a href="https://unsplash.com/?utm_source=<app>&utm_medium=referral">Unsplash</a>`
2. `prototype/CREDITS.md`, one row per photo:

| File / URL | Slot | Source | Photographer | Photo page | Licence | Screens |
|---|---|---|---|---|---|---|

Licence note for the user: the Unsplash License allows commercial use without permission, but not
selling photos unmodified or building a competing image service, and it gives no model or property
release — fine for a prototype, re-check before a photo goes on paid advertising.
