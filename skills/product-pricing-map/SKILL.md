---
name: product-pricing-map
metadata:
  compatible_agents: [claude-code, codex-cli, gemini-cli, cursor, chatgpt, gemini]
  tags: [pricing, monetisation, packaging, sales, simulator, feature-ledger, saas, on-premise, html]
description: >
  Reads a product's spec, feature matrix, cost model and the billing code itself, then produces
  one self-contained HTML pricing map: every feature placed on exactly one way of charging
  (included, subscription or seats, one-time unlock or pass, hosted, on-premise licence, per use,
  services, add-on), a growth ladder showing how a customer climbs without migrating, a column
  or node diagram of the pricing types with upgrade paths, plan cards that say why each price is
  what it is, per-use pricing only where our own cost grows with each use, a sales-call simulator
  that prices a customer for a year against our direct cost and margin, a filterable feature
  ledger with each feature's state in code today (live, built but off, not built, no gate), and a
  numbered list of decisions for the team. Every figure is marked decided or proposed, and every
  mismatch between spec and code becomes a decision. Vendor-neutral. Use this skill whenever the
  user asks to "price every feature", "build a pricing map", "how should we charge for this
  product", "which features go in which plan", "free vs paid features", "pricing strategy for
  X", "what would this customer pay", "pricing simulator", "monetisation plan", "petakan harga
  produk", "macam mana nak caj setiap feature", "feature mana free mana berbayar", "buat pricing
  map untuk X", or "strategi harga produk". Hands off to sales-planner for quotations and
  product-config.md once prices are decided.
---

# Product Pricing Map

Turns a product's documents and code into one page that answers, for every feature: how is it
charged, why that way, and is it even switched on today. The page is a proposal the team argues
over, not a price list for customers. It separates what is decided from what is proposed, prices
only the usage that actually costs us money, and ends with the decisions the team has to make.

The method is proven on three DevHub product maps (an error-monitoring platform, a deployment
platform and an event platform). `references/approved-examples.md` describes how each one was
shaped without repeating their internal figures.

## Command Reference

| Command | Description |
|---|---|
| `/pricing-map build` | Full run: study sources → classify features → price → simulator → page |
| `/pricing-map ledger` | Feature ledger only: every feature, pricing type, state in code today, reason |
| `/pricing-map simulate` | Simulator only, for a sales call: presets, inputs, year 1 / year 2+, cost, margin |
| `/pricing-map refresh` | Re-read the code and docs, update the "Today" column and anything that moved |
| `/pricing-map decide` | Record the team's answers: move items from proposed to decided, drop the rest |

## When to Use

- A product exists or is scoped and nobody has written down how each feature is charged
- Plans exist in code (seeders, `config/plans.php`, Stripe price IDs, feature flags) but the team
  is unsure what is gated, what is free by accident, and what costs us money per use
- A product is adding a new rung: a hosted tier, an on-premise licence, a one-time unlock
- Sales needs to answer "what would this customer pay us, and is it worth it for us?"

Does **not** cover: a client quotation or proposal (`sales-planner`), naming and brand
(`product-brand-kit`), requirements and roadmap (`product-blueprint`), or competitor research
beyond price anchors you can verify. It never publishes prices to customers.

---

## Step 1: Study Every Source

Read all of it before pricing anything. Pricing from the spec alone misses what code already
gates; pricing from code alone misses what was decided in meetings.

| Source | What to take from it |
|---|---|
| Spec / product docs | Decided prices, commercial principles, what the product promises never to charge for |
| Features matrix / roadmap | Every feature with its phase (MVP, P2, P3) |
| Plan config in code | Plan names, limits, prices, currency (`config/plans.php`, `PlanSeeder`, billing config) |
| Feature gates in code | Which features check a plan, a flag or nothing (`FeatureSeeder`, middleware, policies, `Feature::active`) |
| Env and flags | Billing driver, "pricing published" flags, default free seats, retention days |
| Cost model / deployment notes | Infra cost per tier, per GB, per event, per server; FX rate used |
| Commercial notes / meeting decisions | On-premise ranges, support percentages, pioneer offers, dates decided |

Record for each feature its **state today**, read from code, not from the docs:

| State | Meaning |
|---|---|
| Live | Built, switched on, gated as the docs say |
| Built, off | Code exists but a flag, driver or missing key keeps it off |
| Not built | Planned only |
| No gate | Built and on, but every customer gets it whatever they pay |

Write down every place where spec and code disagree (free seats 1 in the spec, 3 in code; one
bundle in the spec, two Stripe prices in config). Each becomes a numbered decision in Step 7.

## Step 2: Choose the Pricing Types

Pick five to eight types from `references/pricing-types.md`, no more. Every feature lands on
exactly one. Each type gets a colour token and one sentence saying who it is for.

Apply the placement rules in this order:

1. **Our cost grows per use?** Per use (credits, per GB, per event, per message). Nothing else is
   metered. Ingest, alerts, reads and anything that runs on the customer's own machine stay in
   the plan.
2. **Safety, security basics or privacy?** Included. Never upsell PII scrubbing, passive scans or
   patching.
3. **Needed to run the product at all, and earns us a fee when used?** Included.
4. **A value metric (seats, servers, events, apps) that costs us little?** Subscription or seats.
5. **Needed for one event, one project or one deal?** One-time pass, pack or unlock.
6. **Only some customers need it (integration, own domain, SSO, priority support)?** Add-on.
7. **People time?** Services, never infra cost.

A one-time payment sells a capability, never perpetual compute. If the unlock implies hosting,
hosting is a separate monthly line or the customer's own servers.

## Step 3: Build the Ladder

Three to six rungs, cheapest first. Each rung states its price, its status (live, decided with
date, proposed) and one line on who it is for. The point of a ladder is that a customer climbs
by changing what they pay for, not by migrating data to another product. Say so, and say what
moves between rungs (whose server, which endpoint, which plan).

Add two or three callouts under the ladder: why the unusual rung exists (one-time unlock,
hosted tier, per-event packs), why per use exists at all, and what this product sells that
hosted competitors do not.

## Step 4: Price Each Line

For every priced line write the price, the basis, our cost, and a "why" of one or two
sentences. Use `references/cost-model.md` to get our cost per unit.

- **Per use**: price at roughly 2× our cost or more, with an included allowance sized so a
  fully used free tier still costs us only a few ringgit a month. State the allowance.
- **Hosting we run**: about 2× infra cost so backups and support are covered; quote above the
  largest standard size.
- **Plans**: name the break-even ("Pro pays for itself once monthly sales pass the plan price divided by the fee saved")
  and the next-plan trigger.
- **One-time packs**: dearer per event than the plan, cheaper than subscribing for a once-a-year
  customer, so regular customers still pick the plan.
- **Pioneer or launch prices**: always with a hard cutoff (count or date, whichever first).
- **Competitor anchors**: only when verified from the competitor's own page this session. If
  none were verified, say so in the decisions list rather than inventing one.

Mark every figure **decided** (with date or source) or **proposed**. State currency, FX rate and
whether tax (SST) is included, once, in the page header.

## Step 5: Build the Simulator

Pick the archetype that matches how the product earns, from `references/simulator-patterns.md`:

| Archetype | Use when | Shows |
|---|---|---|
| Year cost | Recurring lines plus a one-time unlock | Year 1, year 2+, our direct cost, gross margin |
| Best fit | Several rungs could serve the same customer | Every option, which fit, the cheapest that fits, our cost |
| Break-even | A fee per transaction competes with a plan | Every way to pay, cheapest for the client, what we earn after tax and gateway |

Five presets that span the range (worst or smallest, typical, agency or team, biggest, the
regulated or on-premise buyer) plus a "Your inputs" row in the side-by-side table. The verdict
line explains the result in one sentence a salesperson can say out loud.

## Step 6: Write the Feature Ledger

Every feature in one table: name, phase, pricing type pill, state today pill, plan or unit,
optionally where it runs (their server vs ours), and the why. Filter chips per type plus one
"not live yet" or "no gate today" chip, a search box and a live count. See
`references/page-anatomy.md` for the data shape.

## Step 7: Decisions for the Team

A numbered list, most blocking first. Always include, when they apply:

- Turning billing on (driver, keys, price IDs, local payment and tax handling)
- Each spec-vs-code mismatch from Step 1
- Locking features that have no gate today, with a notice period or grandfathering
- Every proposed price that needs a yes or no
- Supplier costs that must be confirmed before per-use prices are fixed

## Step 8: Build and Deliver the Page

One self-contained HTML file following `references/page-anatomy.md`: sticky section nav, ladder,
pricing map diagram (`references/diagram-patterns.md`), simulator, one section per rung, per use,
services and add-ons, feature ledger, decisions, footer. Light and dark themes, phone width,
tabular numerals, no external data.

Deliver it as a private page (an artifact or a local HTML file). It carries cost assumptions and
unannounced prices, so the footer says internal and the page is never published publicly unless
the user asks. Once the team decides, hand the decided prices to `sales-planner` to update
`product-config.md`.

---

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "Put everything in the plans, it's simpler" | A flat plan can't cover a customer who sends 100× the average. Heavy users wipe out a tier's margin unless what costs us per use is metered. |
| "Meter everything, it's fairer" | Metering ingest, alerts or reads adds friction for costs near zero. Meter only where our bill grows. |
| "Charge for the security pack's basics too" | Upselling safety loses the trust the product sells on. Basics stay included; only heavy active work is gated. |
| "The docs say it's gated, that's enough" | Docs drift. Many features ship with no gate at all. Read the code for the Today column. |
| "One-time unlock can include hosting" | A one-time fee for perpetual compute is an open-ended liability. Hosting is its own line. |
| "Use the competitor's price we remember" | Prices change. Unverified anchors mislead the team. Verify this session or leave it out. |
| "Pioneer price, no end date for now" | Without a cutoff it becomes the real price. |
| "Skip our cost, the price looks right" | A price with no cost behind it can't be defended and can lose money at scale. |
| "Decided and proposed look the same in a table" | The team will treat a suggestion as settled. Label every figure. |

## Red Flags

- A feature appears under two pricing types, or not at all
- A per-use line with no "our cost" column, or a per-use line for something that runs on the customer's machine
- Safety or privacy features gated behind a paid tier
- A one-time price that implies we run servers forever
- Prices with no decided/proposed label, or a currency or tax basis that isn't stated
- The "Today" column copied from the docs, with no code reference
- A spec-vs-code mismatch that isn't in the decisions list
- A simulator with no "our cost" or margin, or presets that all land on the same option
- A competitor price with no source
- Internal cost figures in a page marked or shared as public

## Verification

- [ ] Every source in Step 1 was read; the header names them with dates
- [ ] Every feature from the matrix appears once in the ledger, with type, state today and reason
- [ ] Each per-use line has our cost per unit and a price at or above about 2× it
- [ ] Every figure is labelled decided (with source) or proposed
- [ ] Currency, FX rate and tax basis stated once in the header
- [ ] Simulator presets give different answers; the "Your inputs" row updates live
- [ ] Simulator arithmetic checked by hand for at least two presets
- [ ] Every spec-vs-code mismatch and every no-gate feature appears in the decisions list
- [ ] The page works at phone width and in light and dark themes; filters and search work
- [ ] Footer marks the page internal; it was not published publicly

## Reference Files

| File | Read When |
|---|---|
| `references/pricing-types.md` | Step 2: the pricing type catalogue, colour tokens and placement rules |
| `references/cost-model.md` | Step 4: deriving our cost per unit, allowances, margins, FX and tax |
| `references/simulator-patterns.md` | Step 5: the three simulator archetypes, presets and the JS shape |
| `references/page-anatomy.md` | Steps 6 and 8: section order, layout, tokens, ledger data shape, footer |
| `references/diagram-patterns.md` | Step 8: the column map or the node diagram with upgrade paths |
| `references/approved-examples.md` | Before starting: how the three approved maps were shaped and why |
