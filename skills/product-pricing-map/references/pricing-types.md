# Pricing Types

Pick five to eight of these per product. Every feature lands on exactly one. Rename a type to
match the product's language ("Your servers" instead of "Subscription", "Unlock" instead of
"One-time") but keep the meaning.

## Catalogue

| Key | Type | Charged as | Who it is for | Typical lines |
|---|---|---|---|---|
| `inc` | Included / Free | RM0, in every plan | Everyone; the funnel | Core workflow, safety and privacy basics, SDKs, public pages, basic reports |
| `sub` | Subscription | Monthly or yearly plan | Customers who use it every month | Plan tiers stepping on a value metric |
| `seat` | Seats | Per member per month | Teams | Extra members, roles |
| `once` | One-time pass / pack | Per event, per project | Once-a-year customers | Event pass, conference pack, print pack |
| `unlock` | One-time unlock | Once, permanent, account-level | Customers moving off the shared tier | Dedicated endpoint capability, longer retention right, isolation |
| `host` | Hosted | Monthly, usage-capped | Customers with no server | Free / hobby / pro hosted tiers, dedicated nodes we run |
| `prem` | On-premise licence | Licence once + yearly subscription + required support | Banks, ministries, universities | Server or user ranges, support percentage |
| `use` | Per use | Credits, per GB, per event, per message | Only where our cost grows | SMS, email above quota, AI generations, storage, events above allowance, bandwidth, build minutes |
| `svc` | Services | One-time, people time | Customers who want it done for them | Onboarding, migration, install, assessment, training, done-for-you setup |
| `addon` | Add-on | Monthly or yearly on any plan | Some customers only | Own payment gateway, own email domain, custom domain, SSO, priority support, a feature pack for a lower plan |

## Colour tokens

One token pair (`--x` text, `--x-bg` tint) per type, with a dark-theme pair. Keep the same
hue per type across products so the team reads maps the same way.

| Key | Light text / tint | Dark text / tint |
|---|---|---|
| `inc` | `#047857` / `#d8f1e7` | `#34d399` / `#0f2e24` |
| `sub`, `seat` | `#4f46e5` / `#e6e5fb` | `#a5b4fc` / `#1e1f3d` |
| `once`, `unlock` | `#a15c07` / `#fbeed3` | `#fbbf24` / `#33260b` |
| `host` | `#0e7490` / `#d6eff5` | `#67e8f9` / `#0d2f38` |
| `prem`, `svc` | `#7c2d12` / `#f6e2d6` | `#fdba74` / `#35200f` |
| `use` | `#be123c` / `#fbe1e7` | `#fda4af` / `#3a141d` |
| `addon` | `#1d4ed8` / `#e0e8fc` | `#93c5fd` / `#14223d` |
| `warn` (state pills) | `#9a3412` / `#fdebd8` | `#fdba74` / `#35200f` |

Check every pair for 4.5:1 contrast on its tint in both themes when you change a hue.

## Placement rules

Apply in order; the first rule that matches wins.

| # | Question | Type | Why |
|---|---|---|---|
| 1 | Does our own bill grow with each use (SMS, AI tokens, stored GB, stored events, bandwidth, hosted CPU)? | `use` | A flat price can't cover a customer who uses 100× the average |
| 2 | Is it safety, security basics or privacy (PII scrub, passive scan, patching, audit trail)? | `inc` | Upselling safety costs the trust the product sells on |
| 3 | Is it needed to run the product at all, and earns us a fee when used (ticketing, ingest, deploy)? | `inc` | Charging to use the core cuts off the main income |
| 4 | Is it a value metric that costs us little (seats, servers, events, apps, projects)? | `sub` / `seat` | Price on value, not cost |
| 5 | Is it needed for one event, one project or one deal? | `once` / `unlock` | Turns once-a-year customers into paying ones |
| 6 | Does only a minority need it (integration, own domain, SSO, priority support)? | `addon` | Doesn't force a whole plan jump |
| 7 | Is it people time? | `svc` | Priced by days, never counted as infra cost |

## Rules that hold across products

- Work that runs on the customer's own machine (builds, scans, load tests, patching on their
  servers) costs us nothing: it stays in a plan, never per use.
- A one-time unlock sells a capability, never perpetual compute or hosting.
- On-premise always carries required yearly support: it is what carries patches and upgrades.
- Free tiers that only cost us cents (a static page, a QR, one email) can be generous; anything
  that costs us dollars a year each (a running web app) is never free.
- A feature with **no gate today** that moves into a paid type needs a notice period or
  grandfathering for customers already using it. Put it in the decisions list.
- If two types both seem to fit, prefer the one with less friction for the customer and note the
  alternative in the decisions list.
