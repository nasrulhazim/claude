# Simulator Patterns

The simulator turns the map into an answer for one customer: what they would pay us in a year,
and what that customer costs us. Pick one archetype. All three share the same layout.

## Shared layout

```
[preset chips: 5 scenarios]            aria-pressed on the active one; editing an input clears it
[form: 4–8 inputs]  |  [3 KPI tiles]
                    |  [verdict sentence]
                    |  [line table or options table]
[scenarios side by side: every preset + "Your inputs"]
[assumptions paragraph: every constant the maths uses]
```

- Inputs: `type=number` with `inputmode`, selects for discrete choices, checkboxes for
  yes/no needs. Font size 16px so phones don't zoom.
- Constants live in two objects at the top of the script: `P` (prices) and `C` (our costs),
  plus `FX` and tax. The assumptions paragraph repeats them in words.
- Format money with one helper: `RM = n => 'RM' + Math.round(n).toLocaleString('en-MY')`.
- Never let a blank or negative input produce `NaN`: clamp in `read()`.

## Archetype A: Year cost

Use when revenue is recurring lines plus a one-time unlock and services.

| KPI | Meaning |
|---|---|
| Year 1 | Recurring + unlock + services |
| Year 2 onward | Recurring lines only |
| Our direct cost / year | Infra for this customer; RM0 if self-hosted |

```js
function price(i){
  const lines = [];               // [label, basis, year1, year2]
  lines.push(['Extra seats', `${i.seats-1} × RM${P.seat} × 12`, s, s]);
  lines.push(['Usage', basis, u, u]);
  lines.push(['Unlock', 'once', unlocked ? P.unlock : 0, 0]);
  lines.push(['Hosting', size, h, h]);
  lines.push(['Services', what, svc, 0]);
  const y1 = sum(lines, 2), y2 = sum(lines, 3);
  const cost = costUSD(i) * 12 * FX;
  return {lines, y1, y2, cost};
}
```

Verdict: if year 2 is zero, say the free tier is doing its job as a funnel and what it costs us;
otherwise "Recurring RMx a year against RMy of infra: gross margin about z% from year 2."

## Archetype B: Best fit

Use when several rungs could serve the same customer (hosted, own servers, on-premise).

- Build every option as `{key, name, planTxt, plan, use, useTxt, cost, na}`; `na` is the reason
  it doesn't fit ("Control plane must be on their network", "Starter has no overage").
- `pays = plan + use`; best = cheapest option with no `na`.
- Options table: every option, fits rows first, the best highlighted, unfit rows muted with
  their reason, a bar showing price relative to the dearest option.
- If nothing fits: "Enterprise — quote" and say how to build the quote.
- Verdict names the rung and the next move up the ladder ("past about five web apps, own
  servers become cheaper per app").

## Archetype C: Break-even

Use when a fee per transaction competes with a plan (ticket fees, payment cuts).

- Options: stay free (highest fee %), per-event pass, plan monthly, plan yearly, top plan.
- `clientPays = plan + usage + (buyerPaysFee ? 0 : fees)`; `weEarn = plan + fees + usage`.
- Show "after tax and gateway": `weEarn / (1 + tax) − gatewayPerTicket × tickets − supplierCost`.
- Mark free as `na` when the client needs a paid feature.
- Verdict states the break-even: the plan pays for itself from `planPrice / feeDifference` in
  yearly sales; below that, pitch the one-time pass.

## Presets

Five that span the range and land on different answers. If two presets give the same option,
change one. Typical set:

| Preset | Purpose |
|---|---|
| Smallest / worst case | Shows the free tier's cost to us, or that free is cheapest |
| Typical | The customer the plan was designed for |
| Team / agency | Seats, many apps or projects, often the highlighted plan |
| Biggest / busiest | Usage lines and hosting dominate |
| Regulated / on-premise | Licence, required support, services in year 1 |

## Check the maths

Before delivering, work two presets by hand (or in a short script) and compare with what the
page shows. Off-by-one on the owner seat, an allowance subtracted twice, and monthly vs yearly
mix-ups are the usual errors.
