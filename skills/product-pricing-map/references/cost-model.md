# Cost Model

Every priced line needs our cost behind it, even a rough one. A price with no cost can't be
defended and can lose money at scale. Figures below are illustrative shapes, not prices.

## Where our cost comes from

| Cost driver | Unit | How to estimate |
|---|---|---|
| Stored data (events, logs, rows) | per million stored units, per retention window | bytes per unit × units × retention days ÷ 30 → GiB-month × blended storage price (volume + replica share + backups) |
| Object storage (backups, photos, files) | per GB-month | provider list price; add restore egress if restores are common |
| Shared compute | per million requests or events | node cost ÷ throughput the node sustains at target utilisation |
| Dedicated nodes we run | per month | sum of the droplets/VMs in the smallest sensible size, from the deployment model |
| Control plane per managed thing | per server, per app, per tenant | control-plane cost ÷ managed count at today's load |
| Messages (SMS, WhatsApp API, email) | per message | supplier rate card; confirm before fixing a price |
| AI | per generation | average tokens × model rate; budget for retries |
| Payment gateway | % + fixed per transaction | gateway contract; it reduces what a fee actually earns us |
| People time (support, services) | per day | day rate; shown as services, never as infra cost |

## Worked shape: stored events

```
bytes_per_event   = 2.8 KB          # measured from the real table, indexes included
gib_per_million   = 2.8e6 / 1024^3 ≈ 2.6 GiB
monthly_GiB       = events_millions × gib_per_million × retention_days / 30
storage_cost_usd  = monthly_GiB × blended_usd_per_GiB_month
compute_cost_usd  = events_millions × usd_per_million_events
cost_per_million  = (storage + compute) / events_millions
```

Longer retention multiplies stored bytes, so it is usually the most expensive lever: price
30 and 90 days as separate, higher per-unit rates rather than a free toggle.

## Pricing from cost

| Line | Rule of thumb |
|---|---|
| Per use | Price ≥ about 2× our cost per unit; round to a number a customer can do in their head |
| Included allowance | Size it so a fully used free allowance costs us only a few RM a month |
| Hosting we run | About 2× node cost so backups, monitoring and support are covered |
| Value metric (seats, servers) | Not priced from cost (cost ≈ 0); price from value and the next-plan trigger |
| On-premise subscription | Priced from support and update effort plus a margin target; licence once on top |
| Services | Day rate × days; year 1 of an on-premise deal is often mostly services |

## Currency, FX and tax

- Pick one display currency (RM for Malaysian buyers) and state the FX rate used for any
  supplier cost billed in USD, once, in the page header. Use the rate the business already
  plans with (for example the on-premise buffer rate), not today's spot rate.
- State once whether prices include SST. If they do, revenue after tax is `price ÷ 1.08` (8%).
- Gateway cost reduces what a ticket or transaction fee earns: show "after tax and gateway" in
  the simulator when fees are the main income.

## Margin

```
gross_margin = 1 − our_direct_cost / recurring_revenue
```

Show margin on recurring revenue (year 2 onward), not year 1, so one-time unlocks and services
don't flatter it. When our cost is people time (on-premise support), show "people time"
instead of a percentage.

## Label everything

Every cost is an estimate unless it comes from an invoice or a supplier contract. Mark
estimates as estimates in the table and footer, and put "confirm supplier costs" in the
decisions list before per-use prices are fixed.
