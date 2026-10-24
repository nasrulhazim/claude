# Blueprint Sections

What each tab of the blueprint contains, in order. One self-contained HTML file. A sticky tab bar uses
plain `#anchor` tokens (`#summary`, `#requirements`, …) so each tab can be linked.

## Masthead

- Product wordmark or name, the company name, blueprint version and date
- One headline that states what the product does in a sentence
- A lede naming the sources ("Built from the N business requirements in <doc> and the M-screen
  prototype captured on <date>")
- 3–4 fact pills: BR count, platforms or integrations, phase span, number of new mockups

## 1. Summary

| Block | Content |
|---|---|
| TL;DR | 4–6 bullets: core job, what the prototype proves and doesn't, biggest schedule risk, where the product lives, AI stance |
| Source material | One card per document or folder, saying what it holds |
| Coverage | Four tiles (Prototyped / Simulated / Partial / Not started), each with count, bar and one line |
| Decisions to validate first | Table: `#`, Decision, Options, Recommendation (chip), Why — five rows (D1–D5) |

Typical D1–D5: where it lives (module vs standalone), what an approval binds to (record vs immutable
version), unit of execution (one job per record vs per destination), retry safety (blind vs
verify-then-retry), tenancy (DB per tenant vs shared DB with scope).

## 2. Requirements

- Filter segments: by phase, by prototype status, plus a live "N of total" count
- Table columns: ID · Module · Requirement (English) · Phase · Priority chip · Prototype chip
- NFR table: ID · Area · Requirement · Traces to (BR IDs)
- External constraints table: one row per third-party system (account type needed, approval gate,
  limits, watch-outs). Add a line saying the table needs re-checking in Phase 0.
- Open questions: two cards (Scope, Rules), 3 bullets each

## 3. Architecture

- Layers: Clients → domain modules → execution/publishing → measurement → platform services. Mark
  the core modules.
- State machines: one per lifecycle, drawn as chip flows. Planning stages never imply approval.
- Entities table: Entity · Key fields · Notes. UUID public IDs, internal bigint, `workspace_id` on
  tenant tables.
- The key contract as a code block, then the Pest tests (see `contract-patterns.md`)
- Two cards: MCP surface (read / write / not exposed) and package-worthy pieces

## 4. Roadmap

- Gantt grid: one column per month, rows for each phase plus tracks such as external reviews and pilot feedback
- One card per phase: number tile, title, date chip, paragraph, BR chips, **Exit** line

Phase pattern:

| Phase | Theme | Notes |
|---|---|---|
| 0 | Validate access | 2 weeks. Start external reviews, audit accounts, sign off D1–D5, set up CI |
| 1 | The riskiest core (MVP) | Foundation + the integration that justifies the product. Internal launch |
| 2 | Plan / organise | Turn the prototype's planning screens into real ones. First external pilot |
| 3 | Measure | Attribution, leads, reports, escalation |
| 4 | Intelligence | MCP, AI drafting, trend or recommendation features |

## 5. Delivery Plan

- Role cards: Product owner, Pilot user (the domain owner), Build team
- Sprint list: sprint id + dates, title, 2–3 bullets, phase chip, with the launch marked ◆
- Acceptance walkthrough: a chip flow of the MVP happy path, including one failure and its recovery
- Risks table: Risk · Likelihood chip · Impact · Mitigation

## 6. Mockups

- Picker buttons (numbered, since the order matches the doc), a description line, BR chips
- A device frame holding the prototype's own sidebar and chrome; only the screen area changes
- Use believable sample data from the product's own world, and label it as sample
- Typical missing screens: composer with validation, connected accounts and token health,
  content/asset bank, lead inbox, report, AI activity and caps, any discovery or recommendation screen

## Layout Rules

- Colours as CSS tokens on `:root`, a dark-theme override, and an explicit body background
- Status shown by chip shape and word, not colour alone
- Tables and the Gantt sit in their own `overflow-x: auto` boxes, so the page never scrolls sideways
- Footer: the company's legal name, website and registration number, taken from project context and never guessed
