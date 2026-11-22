# Approved Examples

Three internal DevHub pricing maps (November 2026) set the standard for this skill. Their
prices and costs are internal and are not repeated here; this file records how each was
shaped, so a new map makes the same kinds of choices.

## Error-monitoring platform (shared → dedicated → on-premise)

| Aspect | Choice |
|---|---|
| Pricing types | Included, Seats, One-time unlock, Dedicated hosting, Per use, Services, Add-ons (7) |
| Ladder | Free shared tier → paid seats → one-time capability unlock (with pioneer price and cutoff) → dedicated nodes we run, or self-hosted for RM0 to us → government on-premise with required yearly support |
| Per use | Only stored events above an allowance and retention above the default, because the database is the one store and storage drives every cost tier |
| Included on purpose | PII scrubbing, alerts, read-only AI-agent (MCP) tools, unlimited projects, SDKs |
| Diagram | Column map, items flagged not built / built, off |
| Simulator | Year cost: year 1, year 2+, our direct cost, margin; presets solo, startup, agency, busy SaaS, government on-premise |
| Decisions raised | Free seats spec vs code, retention number, event allowance and meter, one unlock vs two Stripe prices, pioneer cutoff, turning billing on, hosting prices, on-premise support price, run through sales-planner before launch |

## Deployment platform (hosted → your servers → on-premise)

| Aspect | Choice |
|---|---|
| Pricing types | Included, Subscription (your servers), Hosted, On-premise, Per use, Services, Add-ons (7) |
| Ladder | Proposed hosted tier (push code, get a URL) → live subscription list on the customer's own servers → on-premise licence by server range with required support → services on every rung |
| Per use | Backups kept on our storage, servers and members above plan, hosted bandwidth and build minutes; everything that runs on the customer's servers stays in the plan |
| Included on purpose | Passive security scans, patching, audit trail; "security is not upsold" |
| Diagram | React Flow node diagram with upgrade paths |
| Simulator | Best fit: every option with fit reasons, cheapest that fits, our cost; presets hobbyist, solo, agency, SME with security needs, university on-premise |
| Decisions raised | Turn billing on, publish the list price flag, sync on-premise ranges into plan config and fix expiry so it never stops workloads, build the hosted tier or not, overage vs hard caps, price our storage, yearly plans, security pack price vs the top plan |

## Event platform (free + fee → plans → packs → credits)

| Aspect | Choice |
|---|---|
| Pricing types | Free, Subscription, One-time, Per use, Yearly add-on (5) |
| Ladder | Free with the highest ticket fee → plans that lower the fee → per-event packs for once-a-year organisers → credits for usage that costs us money → yearly add-ons |
| Per use | Platform fee per ticket (core income), SMS, WhatsApp API, email and certificate credits above allowance, AI generations, storage |
| Gap found | Many modules shipped with no gate at all; the ledger has a "No gate today" filter and the first decision is how to lock them with grandfathering |
| Diagram | React Flow node diagram with upgrade paths (free → pass → plan → top plan) |
| Simulator | Break-even: every way to pay for a year, cheapest for the client, what we earn after SST and gateway; presets worst, typical, best, expo, free events only |
| Decisions raised | Lock no-gate modules, pass vs themed packs, build credit top-ups first, yearly plans, education plan and own-gateway add-ons, confirm supplier costs |

## What all three share

- Header names every source with its date; "proposed" defined once
- Every price has a why; every per-use line has our cost beside it
- The "Today" column comes from code, and its gaps drive the decisions list
- Footer marks the page internal and states which figures are estimates
