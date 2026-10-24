# Photo Sourcing

Photos make a landing page and marketing material believable. The rules here keep them licensed, on-brand and
traceable.

## Sources (in order of preference)

| Source | Licence | Attribution | API | Notes |
|---|---|---|---|---|
| Unsplash | Unsplash License: free for commercial use, no permission needed | Not required, but credit "Photo by <name> on Unsplash" | `api.unsplash.com` (free access key) | Can't be sold unmodified or used to build a competing photo service. Best for workplace, city, people at work |
| Pexels | Pexels License: free commercial use | Not required, credit appreciated | `api.pexels.com` (free key) | Good for Asian and Malaysian people and settings, plus video |
| Pixabay | Pixabay Content License | Not required | `pixabay.com/api` (free key) | Check for logos or trademarks in the shot |
| Openverse | Per item (CC0, CC BY, CC BY-SA …) | **Required** for BY licences | `api.openverse.org` (no key) | Always record the exact licence and author |
| Wikimedia Commons | Per item | Usually required | MediaWiki API | Landmarks, Malaysian places, historic images |
| The user's own photos / brand shoots | Owned | n/a | n/a | **Always preferred** for team, office, product and customer shots |

Never use:
- Google Images results
- Pinterest
- competitor sites
- stock sites with watermarks
- AI-generated photos of real people or real places presented as real
- any photo of a real, identifiable person in a way that implies they endorse the product

## Art Direction

Write the brief from the brand book before searching:

| Field | Example (shutter / "bring your work to light" brand) |
|---|---|
| Light | Strong directional light, window light, hard shadows |
| Subjects | Small teams at work, hands on laptops and phones, planning walls, screens |
| Setting | Malaysian offices, co-working spaces, kedai, campus, city at golden hour |
| People | Diverse Malaysian faces (Malay, Chinese, Indian, East Malaysian), real working clothes |
| Palette fit | Neutral grounds with one warm highlight that echoes the brand colour |
| Avoid | Handshakes, people pointing at blank screens, staged laughter, visible third-party logos, phones showing another app's UI |

Search terms: the local setting plus the activity plus the light, e.g. "malaysian team office window light",
"kuala lumpur coworking laptop", "hands planning sticky notes". Run 2–3 queries per slot.

## Slots

Decide where photos go before searching, and pick one photo per slot:

| Slot | Ratio | Min size | Use |
|---|---|---|---|
| Hero support | 4:5 or 3:2 | 1600 px long edge | Beside or behind the product visual. Never cover the product UI |
| Local fit band | 16:9 | 1920 × 1080 | One wide scene of a Malaysian workplace |
| Testimonial / team | 1:1 | 800 × 800 | Only real customers or the real team, never stock pretending to be a customer |
| Social posts | 1:1, 4:5, 9:16 | 1080 wide | Background under a text overlay; leave a quiet area |
| Print (flyer, banner) | Per format | 300 dpi at final size | Check the pixel count: an A5 flyer needs about 1748 × 2480 px |

## Getting the Files

1. **Network allowed** (local Claude Code, or the domains are allowlisted): call the API, download the
   chosen size, and save it under `brand/<product>/photos/<slot>-<source>-<id>.jpg`. For Unsplash, use the
   API's `download_location` endpoint when a photo is used, as their API terms require.
2. **Network blocked** (a cloud sandbox without those domains): find candidates with WebSearch/WebFetch,
   then give the user a shortlist table (slot · photo page URL · photographer · licence). Either:
   - ask them to allowlist `images.unsplash.com`, `api.unsplash.com`, `images.pexels.com`, `api.pexels.com` and `api.openverse.org`
     (claude.ai → Settings → Capabilities; an admin does this on Team/Enterprise plans), or
   - ask them to download and attach the files.

   Until then, build with labelled placeholders sized to the slot.
3. **In an artifact**: external image URLs are blocked by the page's CSP. Upload each photo as an asset
   and reference the returned URL. Never hotlink.

## Treatment

- Crop to the slot ratio. Keep faces and the subject off the crop line.
- For text overlays: add a dark-block gradient on one side only, or use a solid brand block next to the photo.
  Text never sits directly on a busy area.
- Apply one consistent grade across the set (slight warmth, lifted shadows) rather than per-photo filters.
- Compress for web: WebP or JPEG at quality 80, under 300 KB for a hero. Print files stay full resolution.

## Credits File

Keep `brand/<product>/photos/CREDITS.md` with one row per photo: file · source · photographer ·
photo page URL · licence · where it's used. Add a small "Photos: Unsplash / Pexels" credit line in the landing
page footer, even when the licence doesn't require one.
