---
name: product-blueprint
metadata:
  compatible_agents: [claude-code]
  tags: [requirements, roadmap, architecture, mockups, prototype, html, product-planning, design-loop, netlify, unsplash]
description: >
  Turns a product's raw source material — a business requirements (BR) document, prototype
  screenshots, a Google Drive folder or a local docs folder — into one tabbed HTML blueprint:
  English requirements with phase, MoSCoW priority and prototype coverage, non-functional
  requirements the BR doc implies but never states, an architecture section with state machines,
  entities, the key contract and Pest tests for the riskiest behaviour, a phased roadmap with exit
  criteria, a sprint-level delivery plan with risks, and HTML mockups for the screens the prototype
  does not have yet. Then builds a clickable static prototype of the MVP walkthrough in a design
  loop — ui-ux-pro-max designs and revises, impeccable critiques each round, licensed Unsplash
  photos fill the slots that need real pictures — and deploys every round to Netlify so the team
  gets a live URL immediately. Use this skill whenever the user shares requirements or a prototype
  for a new product and asks for "requirements, roadmap, plan, mockups", "study this folder", "turn
  this BR into a plan", "what's missing from the prototype", "build a prototype from the
  blueprint", "deploy the prototype to Netlify", "kaji folder ni", "buat requirement dan roadmap",
  "rangka pelan projek dari BR ni", "buat mockup untuk skrin yang belum ada", "buat prototaip dari
  blueprint", or "deploy prototaip ke Netlify". Pairs with project-requirements for a formal SRS
  and project-roadmap for a standalone ROADMAP.md, and hands off to product-brand-kit once the
  product is scoped.
---

# Product Blueprint

Reads every source file a product has, then produces a single blueprint page a founder can
validate before anyone writes code. The blueprint argues for an order of work. It does not just
restate the requirements. Once the blueprint holds, it turns the MVP walkthrough into a clickable
prototype that is designed, critiqued and redesigned in rounds, and lives on a Netlify URL from
round one.

## Command Reference

| Command | Description |
|---|---|
| `/blueprint build` | Study the source folder and produce the full blueprint |
| `/blueprint requirements` | Requirements tab only: BRs in English, NFRs, constraints, open questions |
| `/blueprint architecture` | Architecture tab only: layers, state machines, entities, contract, tests |
| `/blueprint roadmap` | Roadmap and delivery plan tabs only |
| `/blueprint mockups` | Mockups for the screens the prototype is missing |
| `/blueprint prototype` | Build the clickable prototype and run the design → critique → revise loop (Steps 5–6) |
| `/blueprint critique` | Run one more critique round on the existing prototype and apply the fixes |
| `/blueprint deploy` | Deploy the current prototype to Netlify (draft URL; `--prod` for the stable URL) |

---

## When to Use

- A new product has a BR document (often in Bahasa Malaysia) and/or a clickable prototype
- The user wants to validate architecture and order of work before implementation
- A prototype exists and the user wants to know what it does not yet prove
- A pilot user, partner or client needs one page that explains scope, phases and risks
- The blueprint is agreed and the team needs something to click through and share by link

**Do not** use this skill for an SRS with traceability (use `project-requirements`), a roadmap
for an existing codebase (use `project-roadmap`), naming, logo and landing page
(use `product-brand-kit`), or production UI inside a Laravel app (use `ui-ux-sop` →
`livewire-flux`). The prototype is a throwaway static site, never the product's codebase.

---

## Step 1: Study Every Source File

1. List the folder recursively. On Google Drive, query `parentId = '<id>'` for each folder and
   follow every `nextPageToken`. Don't stop at the first level.
2. Read every requirements document in full, including comments. Keep the BR IDs exactly as written.
   Translate each requirement into plain English when the source is in another language.
3. Read any README or index file first. It often maps the screenshot filenames to screen names.
4. Read the screenshots. Desktop captures are enough; read the phone captures only for mobile-only
   behaviour.
5. Record the prototype's visual direction: surface and nav colours, accent, radius, tone of copy.
   The mockups must look like they belong to the same product.
6. Read the project's `CLAUDE.md` (if any) for the stack, conventions and the company identity: legal name,
   registration number, public contact. Never invent these.

---

## Step 2: Classify Each Requirement

For every BR, decide three things and keep the evidence:

| Field | Values | Rule |
|---|---|---|
| Phase | P1 … P4 | P1 is the riskiest integration that justifies the product's existence |
| Priority | Must / Should / Could | MoSCoW within its phase |
| Prototype status | Prototyped / Simulated / Partial / Not started | Simulated = screen exists, behaviour faked in the browser |

Count the statuses. The coverage tiles on the Summary tab come from these counts, never estimates.

---

## Step 3: Write the Blueprint

One self-contained HTML file with six tabs, in this order. Section contents and layout are in
`references/blueprint-sections.md`.

1. **Summary**:
   - a TL;DR of 4–6 bullets
   - what's in the folder
   - prototype coverage tiles
   - five "decisions to validate first", as a table: decision, options, recommendation, why
2. **Requirements**:
   - every BR in English, filterable by phase and prototype status
   - non-functional requirements (`references/nfr-checklist.md`)
   - external constraints (third-party APIs, review processes)
   - open questions for the team
3. **Architecture**:
   - module layers
   - separate state machines for planning and for execution
   - entity table (UUID public IDs, internal bigint)
   - the key contract, enum and invokable action
   - Pest tests for the riskiest behaviour
   - MCP surface, if AI is in scope
   - package-worthy abstractions (`references/contract-patterns.md`)
4. **Roadmap**:
   - a Gantt grid plus one card per phase: dates, BR chips, a measurable exit criterion
   - Phase 0 always validates access and decisions, and starts slow external approvals in parallel
5. **Delivery plan**:
   - roles
   - two-week sprints in detail for Phase 0–1, epics after that
   - the MVP acceptance walkthrough
   - a risks table
6. **Mockups**:
   - only the screens the prototype lacks, rendered inside the prototype's own app chrome
   - a picker, with the BR IDs each mockup covers
   - sample data visibly labelled as sample

Output target:
- **Artifact tool available** (claude.ai / Cowork): publish the page as an artifact.
- **Otherwise**: write `docs/blueprint/<product>-blueprint.html` and tell the user the path.

Once the prototype is live (Step 6), add its Netlify URL to the blueprint masthead and the
Mockups tab, so the static mockups point to the clickable version.

---

## Step 4: Hold the Line on Judgement

- Lead the MVP with the riskiest integration (the third-party API, the payment flow, the hardware). Planning screens,
  analytics and AI come after, because a prototype usually already proves them.
- When AI acts in the product, a human gate is the default. The MCP surface may draft, suggest and
  read. It never approves, publishes, pays or deletes.
- Any external limit you didn't verify this session (API caps, review timelines, caption lengths) is marked
  "re-check against live docs in Phase 0".
- Recommend where the product lives (standalone vs inside an existing app) and say why in one line.

---

## Step 5: Build the Prototype in a Design Loop

The prototype makes the **MVP acceptance walkthrough** (Delivery plan tab) clickable end to end,
including its one failure and recovery. Screens outside that path are out of scope unless the
user names them. Full procedure, folder layout and round log: `references/prototype-loop.md`.

This is a fixed two-skill route that `ui-ux-sop` recognises as "Blueprint prototype": the
**designer** is `ui-ux-pro-max`, the **critic** is `impeccable`. No third design skill is loaded.

| Round step | Who | Does | Writes |
|---|---|---|---|
| 0. Seed | ui-ux-pro-max | `--design-system` for the product type, reconciled with the prototype's visual direction from Step 1 (the existing prototype wins) | `prototype/DESIGN.md`, `css/tokens.css` (light + dark) |
| 1. Build | you | Static HTML + Tailwind + Alpine.js, one file per screen, sample data in `data/*.json`, every screen tagged with its BR IDs | `prototype/` |
| 2. Deploy | you | Draft deploy to Netlify (Step 6) | Round URL |
| 3. Critique | impeccable | `critique` (design heuristics, scored) and `audit` (a11y, responsive CSS, performance) on the rendered round, at the user's real viewport | `prototype/CRITIQUE.md` round entry |
| 4. Revise | ui-ux-pro-max | Turns each finding into a targeted `--domain` query (ux, color, typography, style, chart), updates `DESIGN.md` and tokens first, then the screens | Updated design + changelog line per finding |

Repeat 1–4. **Exit** when a round has no P0/P1 findings, the audit has no AA failure in either
theme, and the critique score did not drop — or after **3 rounds**, whichever comes first. Unresolved
findings stay in `CRITIQUE.md` as open items; they are never silently dropped.

Photos: only for slots that need a real picture (listing images, venues, products, onboarding
or marketing panels). Never for avatars of "customers" or team members — use initials. Sourcing,
hotlinking and credit rules: `references/unsplash-photos.md`.

---

## Step 6: Deploy to Netlify

Every round gets its own draft URL so the critique runs on the deployed page and the user can
watch the design change. The final round goes to the stable production URL. Details, the
`_headers` file and the three deploy routes: `references/netlify-deploy.md`.

| Route | When | Command |
|---|---|---|
| Netlify MCP connector | Connected in this session | Deploy services tool, site `<product>-prototype` |
| Netlify CLI | `netlify` installed and `NETLIFY_AUTH_TOKEN` set | `netlify deploy --dir=prototype` (round) · `--prod` (final) |
| Manual | Neither available | Zip `prototype/`, user drags it onto app.netlify.com/drop; give the exact steps |

Prototype sites ship with `X-Robots-Tag: noindex` and a visible "Prototype — sample data" banner.
Ask before making a prototype public if the BR doc is marked confidential; Netlify password
protection needs a paid plan, so say so instead of promising it.

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The BR doc is the requirements, I'll just translate it" | A BR doc never states tenancy, idempotency, secrets, audit retention or privacy law. Those NFRs are where production incidents come from. |
| "The prototype covers most of it" | A browser-local prototype proves the workflow, not the integration. Count Simulated separately from Prototyped. |
| "I'll mock up every screen" | Mocking screens the prototype already has duplicates work and hides what's actually new. |
| "Phase 1 should be the planning screens, they're easiest" | Easy-first defers the risk that can kill the product. Lead with the integration nobody has proven yet. |
| "The API limits are well known" | Platform limits change. Unverified numbers go into the blueprint flagged for Phase 0. |
| "AI can publish if the user trusts it" | One bad auto-publish costs more than every draft saved. Keep approve and publish out of the AI surface. |
| "Round 1 looks fine, skip the critique" | The designer can't grade its own work. One impeccable round always runs before calling the prototype done. |
| "I'll fix the critique findings straight in the HTML" | Fixes that skip `DESIGN.md` and the tokens drift back next round. Design first, screens second. |
| "Keep looping until it's perfect" | Three rounds, then stop and log what's open. A prototype that never ships proves nothing. |
| "Stock photos make the dashboard feel real" | Stock faces as customers are fake testimonials. Initials for people, photos only where the product itself shows pictures. |
| "I'll deploy once it's finished" | A URL from round one is the point — the user reviews progress and redirects early. |

---

## Red Flags

- A BR ID is missing from the requirements table, or two BRs were merged
- The coverage tiles don't add up to the BR count
- A phase card has no measurable exit criterion
- Phase 0 doesn't start the slow external approvals (app review, API access, procurement)
- The mockups use a different visual language from the prototype
- Prices, hosting regions, dates or company details appear without a source
- The architecture has one state machine for both planning and execution
- The prototype has screens outside the MVP walkthrough but skips the failure-and-recovery step
- A critique round has findings but `DESIGN.md` / `tokens.css` did not change
- More than two design skills loaded for the prototype loop, or a fourth round started
- Unsplash photos downloaded and re-hosted without credit, or used as people the product claims as users
- A prototype deployed without the sample-data banner or the `noindex` header

---

## Verification

- [ ] Every source file in the folder (including subfolders) was read, and the summary names them
- [ ] Every BR appears once, in English, with phase, priority and prototype status
- [ ] Coverage tile counts match the table
- [ ] NFRs cover access, isolation, timing, idempotency, secrets, audit, media, privacy, observability, mobile
- [ ] Each phase has dates, BR chips and an exit criterion someone could test
- [ ] Mockups exist only for screens the prototype lacks, each listing the BRs it covers
- [ ] Unverified external limits are flagged; nothing in the page is invented
- [ ] The page works at phone width and in light and dark themes
- [ ] (Prototype) The MVP walkthrough clicks through end to end, including the failure and its recovery
- [ ] (Prototype) `CRITIQUE.md` has one entry per round with score, findings, fixes and open items; the loop met its exit rule
- [ ] (Prototype) Every photo has a row in `CREDITS.md` and an on-page credit; no photo stands in for a real person
- [ ] (Prototype) The production Netlify URL loads, shows the sample banner, sends `noindex`, and is linked from the blueprint

---

## Reference Files

| File | Read When |
|---|---|
| `references/blueprint-sections.md` | Building any tab: what goes in it, the tables and their columns, layout notes |
| `references/nfr-checklist.md` | Writing the non-functional requirements and the risks table |
| `references/contract-patterns.md` | Writing the architecture tab's contract, enum, action, Pest tests and MCP surface |
| `references/prototype-loop.md` | Building the prototype: folder layout, seed, ui-ux-pro-max queries, impeccable critique/audit, severity scale, round log, exit rule, fallbacks |
| `references/unsplash-photos.md` | Any prototype screen that needs a real photo: when to use one, search and art direction, hotlink vs download, credits |
| `references/netlify-deploy.md` | Deploying a round or the final prototype: `_headers` (noindex + CSP), MCP / CLI / drop routes, URL handling |
