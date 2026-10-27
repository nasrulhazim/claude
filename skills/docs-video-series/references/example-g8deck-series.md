# Example: G8Deck docs video series

An approved plan for [g8deck.com/docs](https://g8deck.com/docs/), 29 pages in 8 sidebar groups. It shows the expected depth: every page triaged, hooks taken from the pages' own warnings, and full sheets for wave 1 only. Video A1 was rendered with `docs-explainer-video`.

**Result:** 20 explainer videos, 6 shorts and 1 trailer in three waves.

## Triage (abridged to show each decision type)

| Docs page | Type | Decision | Video | Why |
|---|---|---|---|---|
| Your first deployment | Walkthrough | Video | A1 | Wizard, pipeline, live app |
| Operating what is running | Walkthrough | Video | A7 | Lifecycle, promotion gates, drift, rollback |
| When a deployment misbehaves | Troubleshooting | Video | A8 | Strongest warnings on the site |
| Caches + Object storage | Walkthrough ×2 | Merge | C2 | Same job (shared data services); each too thin alone |
| MCP overview + Connect an AI client + Customer server | Concept, walkthrough, reference | Merge | A9 | One story: connect an agent, deploy by conversation |
| Servers, connections and the fleet | Concept | Hold | B3 | A capability claim to confirm with the product owner first |
| Reference (statuses, 24 pipeline steps, limits) | Reference | Feeds | — | Pipeline rail and limits inside A1, A7, A8 |
| Design / Ops server tool lists | Reference | No video | — | Generated tool lists |
| Platform / Compliance servers | Reference | No video | — | Operator-only |
| Docs overview | Index | No video → trailer | T1 | Its guide order is the trailer's five beats |

## Series map (abridged)

| ID | Video | Length | Wave | Hook (from the page) |
|---|---|---|---|---|
| A1 | Your first deployment | 60s | done | Empty host to running app in three questions |
| A4 | Launch from git | 60s | 1 | One push becomes a signed, traceable release |
| A7 | Operate what's running | 60s | 1 | Day two: stop, promote, catch drift, roll back |
| A8 | When a deployment misbehaves | 60s | 1 | A green console is not proof |
| A9 | Drive G8Deck from an AI agent | 60s | 1 | Same permissions as you; values go in, never out |
| A5 | Domains and TLS | 60s | 2 | Saving routes nothing; the pipeline does |
| B1 | How G8Deck is organised | 45s | 2 | Five boxes, each answers one question |
| C2 | Caches and object storage | 60s | 3 | Prefixes separate keys, not data |
| D1 | Patching and upgrades | 45s | 3 | Canary first, then the fleet |

## One wave-1 sheet: A8 · When a deployment misbehaves

| # | Time | Scene | What moves | Pattern |
|---|---|---|---|---|
| 0 | 0–4 | Title | A pipeline bar fills to a green 100% | Title |
| 1 | 4–12 | A step failed | Run stops at **Check resource quota**; completed steps turn *rolled back*; **Re-run Pipeline** resumes at the failed step | Pipeline |
| 2 | 12–21 | Green but nothing built | The 100% bar expands: "18 steps ran in 4m 12s — 6 did no work"; amber rows with reasons | Before/after *(new)* |
| 3 | 21–28 | Nothing moves | Steps unclaimed on the `infra` queue; the worker box is missing; health panel shows **Queue worker**, **Scheduler**, **Infrastructure provider** | Topology |
| 4 | 28–35 | Simulated capability | **Test connection**: "nothing was contacted"; **Not yet real on this server** panel | Review + guards |
| 5 | 35–42 | Active but no answer | Domain → reverse proxy → app works; raw port on `127.0.0.1` dead-ends | Topology, split |
| 6 | 42–48 | Health and logs | Health empty until 2 readings cover half the window; secrets become `[redacted]` | Wizard form |
| 7 | 48–55 | Re-run, don't tear down | One run at a time; stopped → **Start**; then Incidents → Drift → Operations | Gates *(new)* |
| 8 | 55–60 | End card | g8deck.com → | End card |

## Later-wave outline row

| ID | Beats | Facts to keep | Signature visual |
|---|---|---|---|
| C2 | One cache, many apps in key-prefix lanes → `FLUSHALL` empties every lane → storage: one copy vs three → switching source leaves the old bucket full | Prefix separates keys, not data; redundancy is permanent | Lanes wiped at once |

## Shorts

| ID | Hook | Trap | Fix | Parent |
|---|---|---|---|---|
| S1 | Green is not done | 100%, 6 steps did no work | Read the amber rows | A8 |
| S2 | Published is not reachable | Port on `127.0.0.1` | Route via a domain and the proxy | A8 |
| S3 | The orange cloud breaks TLS | Proxied Cloudflare record | Grey cloud (DNS only) | A5 |
| S5 | Re-run is not Start | Stopped deployment shown active | Press **Start** | A7 |

## Engine gaps found

| Pattern | Needed by |
|---|---|
| State machine | A7, B2 |
| Gates | A7, A8, A9 |
| Before / after | A4, A7, A8, A9, every short |
| Branch flow | A4, A9 |
| Agent transcript | A9 |
| Nesting zoom | B1 |
| Fleet grid | D1, A2 |
| Matrix | D2, C1 |
| 9:16 layout | Shorts |

## Docs issues logged

| Issue | Effect |
|---|---|
| A capability described as working that the owner has not production-tested | B3 on Hold; A8 scene 4 names no driver |
| A tool listed as read-tier whose real run needs write | Tier label fixed before A3 |
| Request rate and latency always read 0 (not measured) | Filming rule: never show those tiles |

## Production order

Engine work → **Wave 1** (A8, A7, A4, A9, shorts S1 and S5) → gate: wave 1 approved, flagged docs fixed → **Wave 2** (A2, A5, A6, B1, B2, T1, shorts) → gate → **Wave 3** (A3, B4, C1–C4, D1–D3, B3 once cleared).
