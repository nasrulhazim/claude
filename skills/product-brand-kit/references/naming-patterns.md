# Naming Patterns

## Two Names, Two Jobs

| Name | Audience | Rule | Example |
|---|---|---|---|
| Code name | The team, the repo, the product family | Family prefix + one word, lowercase | `g8market` |
| Public brand | Customers | Standalone, memorable, searchable | Expose |
| Endorsement | Listings, proposals | "<Brand> · part of <Family> by <Company>" | Expose · part of G8Suite by Developers Hub |

## Option Families

Offer a mix, about 10 in total:

| Family | Pattern | Strength | Watch for |
|---|---|---|---|
| Prefixed | `<Family><Word>` (G8Cast, G8Relay) | Instant family fit | Sounds like a module, not a product |
| English standalone | A verb or image (Chorus, Ripple, Beacon) | Works regionally | Crowded trademarks, common words hard to search |
| Local-language standalone | Malay words with a story (Canang, Hebah, Tebar) | Memorable, ownable locally | Pronunciation for regional buyers |
| Metaphor | From the product's mechanism (shutter, relay, echo) | Gives the logo a concept | Don't let the metaphor leak into UI labels |

## Connotation Check

Before recommending a name, check:
- negative meanings in English and Malay, and close lookalikes (expose → exposé = scandal, "exposed" = data leak)
- the main domain and the social handles
- search noise (single common words)

Flag a real risk once, plainly. The user decides.

## Domain Advice

| Situation | Recommend |
|---|---|
| Exact match is premium priced | Modifier domain: `get<brand>.my`, `use<brand>.app`, `<brand>hq.com`, `try<brand>.com` |
| Company already owns a domain | Launch on `<brand>.<company-domain>`, move later |
| Local-first market | Prefer the ccTLD (`.my`) for trust with local SMEs and government |
| Avoid | Hyphens, creative misspellings, accented characters |

Prices are typical registry prices for standard names. Always mark them unverified.

## Migration Hygiene (subdomain → own domain later)

| Do now | Why |
|---|---|
| Base URL only from config (`APP_URL`) | The move is an env change plus a redirect |
| OAuth callbacks on a stable path (`/auth/{provider}/callback`) | Add the new domain to each provider; no new review |
| QR codes on long-lived print | Banners outlive domains |
| Tracking links through one redirect path | Old campaign links keep working |
| 301 old → new for at least a year | Keeps search ranking and shared links |
| Move in a quiet week | Providers may re-review apps when the domain or privacy URL changes |
