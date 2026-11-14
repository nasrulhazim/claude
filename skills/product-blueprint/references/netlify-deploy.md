# Netlify Deploy

The prototype is a static folder. Every critique round gets a draft URL; the last round goes to
the site's production URL. No build runs on Netlify — the CSS is built before deploy.

## `_headers` (inside `prototype/`)

Use a `_headers` file in the published folder rather than `netlify.toml`: it travels with the
folder, so it applies on every route — MCP, CLI and manual drop alike.

```
/*
  X-Robots-Tag: noindex, nofollow
  X-Content-Type-Options: nosniff
  Referrer-Policy: strict-origin-when-cross-origin
  Content-Security-Policy: default-src 'self'; img-src 'self' data: https://images.unsplash.com; style-src 'self' 'unsafe-inline'; script-src 'self' 'unsafe-eval'; font-src 'self'; connect-src 'self'
```

No build runs on Netlify; the CSS is built before deploy, so no `netlify.toml` is needed.

Adjust the CSP when you add a source: Google Fonts needs `fonts.googleapis.com` in `style-src` and
`fonts.gstatic.com` in `font-src`; the Tailwind browser script fallback needs its CDN in
`script-src`. `'unsafe-eval'` is there for Alpine.js; drop it if Alpine is not used.

Site name: `<product-slug>-prototype` (lowercase, hyphens). Check it is free; on a clash add the
company slug, never a random suffix the user can't remember.

## Route 1 — Netlify MCP Connector

When the Netlify connector is connected in the session:

1. Load its context tool first (`get-netlify-coding-context`) and follow what it says about deploys.
2. Find or create the site with the project services tools; reuse the same site every round.
3. Deploy the `prototype/` folder with the deploy services tool as a draft for rounds, production
   for the final round.
4. Read the deploy state until it is `ready`, then record the URL.

## Route 2 — Netlify CLI

```bash
npm i -g netlify-cli                     # or npx netlify-cli
export NETLIFY_AUTH_TOKEN=…              # personal access token, never committed
netlify sites:create --name <product>-prototype          # first time only
netlify link --name <product>-prototype                  # writes .netlify/state.json (gitignore it)

netlify deploy --dir=prototype --message "Round 2: <one-line summary>"   # draft → unique URL
netlify deploy --dir=prototype --prod --message "Final: round 3"         # production URL
```

Use `--json` to capture `deploy_url` and `url` into the round entry. Never print or log the token.

## Route 3 — Manual Drop

No connector, no CLI or no token:

1. Zip the contents of `prototype/` (not the folder itself) and send the zip to the user.
2. Steps for them: open app.netlify.com/drop → drag the zip in → copy the URL → paste it back.
3. Later rounds: Site → Deploys → drag the new zip onto the deploys page (same site, new deploy).

## After Each Deploy

- Open the URL and confirm: tour page loads, sample banner visible, theme toggle works, one photo
  loads (CSP), response has `x-robots-tag: noindex` (`curl -sI <url> | grep -i robots`)
- Write the URL into the round entry in `CRITIQUE.md`
- Final round only: put the production URL in the blueprint masthead and Mockups tab

## Visibility

Netlify sites are public by URL. `noindex` keeps them out of search results, not out of reach.
If the BR doc is confidential or names a client, ask the user before the first deploy. Password
protection and Netlify's basic auth need a paid plan — say so rather than promising it.
