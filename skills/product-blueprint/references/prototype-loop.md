# Prototype Loop

How the blueprint's MVP walkthrough becomes a clickable prototype, and how `ui-ux-pro-max`
(designer) and `impeccable` (critic) take turns on it until it passes or three rounds are spent.

## Scope

| In | Out |
|---|---|
| Every step of the MVP acceptance walkthrough, in order | Screens no walkthrough step visits |
| The walkthrough's failure and its recovery screen | Real auth, real APIs, real payments |
| Empty, loading and error states of each walkthrough screen | Admin back-office, settings pages nobody clicks in the demo |
| A tour page (`index.html`) listing the steps with links | Marketing site and logo (that is `product-brand-kit`) |

If the blueprint has no acceptance walkthrough yet, write it first (Delivery plan tab), then build.

## Stack and Folder Layout

Static, build-optional, deployable as-is. No framework, no backend.

```
prototype/
├── index.html              # Tour: product name, the walkthrough steps as links, "Reset demo" button
├── screens/
│   ├── 01-<step>.html      # One file per walkthrough step, numbered in walkthrough order
│   └── 0n-<step>-error.html
├── partials/shell.html     # App chrome (sidebar/topbar) injected by js/shell.js
├── css/
│   ├── tokens.css          # Semantic tokens, :root + [data-theme=dark] — generated from DESIGN.md
│   ├── app.css             # Tailwind entry (@import "tailwindcss"; @theme maps to tokens)
│   └── app.build.css       # Built output (committed, served)
├── js/
│   ├── shell.js            # Injects shell, marks active nav, theme toggle, BR overlay toggle
│   ├── store.js            # localStorage-backed demo state (try/catch; in-memory fallback)
│   └── vendor/alpine.min.js
├── data/*.json             # Sample data from the product's own world, each record "sample": true
├── DESIGN.md               # Design memory: direction, tokens, type, decisions per round
├── CRITIQUE.md             # Round log (format below)
├── CREDITS.md              # Photo credits (see unsplash-photos.md)
└── _headers                # noindex + CSP, see netlify-deploy.md
```

Build steps:

```bash
npm i -D @tailwindcss/cli alpinejs
cp node_modules/alpinejs/dist/cdn.min.js prototype/js/vendor/alpine.min.js
npx @tailwindcss/cli -i prototype/css/app.css -o prototype/css/app.build.css --minify
```

No Node available: use the `@tailwindcss/browser` script tag instead of the build, and say so in
`DESIGN.md` — it is slower and the CSP in `_headers` must allow it.

Rules every screen follows:

- `<body data-screen="03-review" data-br="BR-04 BR-07">` — the BR overlay (key `b`) shows these as chips
- A fixed banner: "Prototype · sample data · not the real product". It stays in every round
- Colour, radius, spacing and shadow come from `tokens.css` only — no raw hex, no `bg-blue-500`
- Light and dark both defined; theme toggle in the shell; respect `prefers-color-scheme` on first load
- Every interactive element reachable by keyboard with a visible focus ring
- Simulated behaviour (a fake API call, a fake approval) shows a realistic delay and a toast, and is
  marked `data-simulated` so the critic does not grade it as a real integration

## Round 0: Seed the Design

1. Locate the CLI: `~/.claude/skills/ui-ux-pro-max/scripts/search.py` (or the project's
   `.claude/skills/...`). Needs Python 3.
2. Generate the starting system for the product type:

   ```bash
   python3 <path>/search.py "<product type> <audience> <feel>" --design-system -p "<Product>"
   python3 <path>/search.py "<product type>" --stack html-tailwind
   ```

   Do **not** pass `--persist` — the house rule is one `DESIGN.md`.
3. Reconcile with the precedence order from `ui-ux-sop`: the user's brief → the existing
   prototype's visual direction recorded in Step 1 → accessibility floor → ui-ux-pro-max output.
   A recommended style that the existing prototype contradicts (glass, gradients, neumorphism)
   loses.
4. Write `prototype/DESIGN.md`:

   | Section | Content |
   |---|---|
   | Direction | One paragraph: who uses it, the verb, the feel, what was rejected and why |
   | Tokens | Colour roles (surface, surface-raised, border, text, text-muted, accent, success, warning, danger) for light and dark, radius, spacing scale, shadow/elevation |
   | Type | Families, scale, weights, line heights |
   | Components | Button, input, table, card, chip, toast, empty state — one line each |
   | Decision log | `R<n> · finding id · change · reason` — appended every round |

5. Generate `css/tokens.css` from the Tokens section. Check every text/surface pair for AA in
   both themes before building screens.

## Round n

### Build

Build or update only what the previous round's findings touched, plus any walkthrough step not
built yet. Re-run the Tailwind build. Click through the whole walkthrough once yourself.

### Deploy

Draft deploy (`references/netlify-deploy.md`). Record the URL in the round entry.

### Critique (impeccable)

Run against the deployed round URL (or the local file if deploy failed), at the user's real
viewport — never resize the browser or emulate devices unless the user asked for a mobile check;
check responsiveness by reading the CSS.

| Pass | Command | Looks at |
|---|---|---|
| Design critique | `impeccable critique` on the tour page and each walkthrough screen | Hierarchy, focal point, consistency, clarity of the next action, copy, signature vs generic look — scored |
| Technical audit | `impeccable audit` | Contrast in both themes, keyboard path, focus, labels, touch targets, CSS responsiveness, image weight |
| Edge pass | by hand | 60-char names, empty lists, 1,000+ rows, Malay and Chinese text, slow simulated call |

Classify every finding:

| Severity | Meaning | Must fix this round |
|---|---|---|
| P0 | Walkthrough cannot be completed, or an AA failure on a primary action | Yes |
| P1 | A user would hesitate or misread the next step; inconsistent component; missing state | Yes |
| P2 | Polish: spacing rhythm, weaker hierarchy, copy tone | If cheap |
| P3 | Taste, nice to have | No — log it |

### Revise (ui-ux-pro-max)

Turn each P0–P2 finding into a targeted query instead of guessing:

| Finding is about | Query |
|---|---|
| Layout, flow, feedback, forms, navigation | `search.py "<issue>" --domain ux` |
| Contrast, palette harmony, status colours | `search.py "<product type> <issue>" --domain color` |
| Hierarchy, readability, font pairing | `search.py "<issue>" --domain typography` |
| Overall look feels generic or off-brand | `search.py "<product type> <feel>" --domain style` |
| A chart reads badly | `search.py "<data shape>" --domain chart` |

Apply in this order: `DESIGN.md` (decision log line) → `tokens.css` → shared partials/components →
individual screens. A fix that only touches one screen's HTML when the cause is a token is wrong.

## Round Log — `CRITIQUE.md`

```markdown
## Round 2 — 2026-11-15 — https://<deploy-id>--<site>.netlify.app
Score: 31/40 (R1: 26/40) · Audit: 0 AA failures (light), 1 (dark)

| ID | Sev | Screen | Finding | Fix | Status |
|---|---|---|---|---|---|
| R2-01 | P1 | 03-review | Approve and Reject have equal weight | Primary/secondary roles in DESIGN.md; Reject → ghost | Fixed R2 |
| R2-02 | P0 | all (dark) | text-muted on surface 3.9:1 | text-muted dark → #A3A9B5 (5.1:1) | Fixed R2 |
| R2-03 | P3 | 01-start | Hero copy could be shorter | — | Open |

Exit check: P0/P1 open = 0 · AA failures = 1 → continue to Round 3
```

## Exit Rule

Stop when **all** hold, or after Round 3 regardless:

- No P0 or P1 open
- Audit: zero AA failures in light **and** dark
- Critique score is not lower than the previous round

After the last round: deploy `--prod`, write a "Final" section in `CRITIQUE.md` (final score,
rounds run, open P2/P3 items as the backlog), and link the production URL from the blueprint.

## Fallbacks

| Missing | Do instead |
|---|---|
| `impeccable` skill or its binary | Say so in one line. Score by hand on the rubric below, run the audit checks by reading the CSS and checking contrast with a script |
| `ui-ux-pro-max` or Python | Say so in one line. Seed tokens from the existing prototype's colours; apply the `ui-ux-pro-max` fallback rules in `ui-ux-sop/references/design-skills.md` |
| Netlify | Keep building; deliver the zip and the manual drop steps; critique the local file |

Hand rubric (0–4 each, total /40): visibility of state · next action obvious · consistency ·
error prevention and recovery · recognition over recall · hierarchy and focal point · density fit
for the user · copy clarity · accessibility basics · looks like this product, not a template.
