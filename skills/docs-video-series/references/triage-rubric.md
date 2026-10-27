# Triage rubric

Every page gets exactly one decision. Work top-down: the first row that matches wins.

| # | Test | Decision |
|---|---|---|
| 1 | The page states a capability the product owner hasn't confirmed, and the video would repeat it | **Hold** (log under docs issues) |
| 2 | It's an index or landing page | **No video**; it can become the trailer's structure |
| 3 | It's a generated tool, API or parameter list | **No video**; any user-facing flow on it goes into a task video |
| 4 | Its audience is operators or internal staff only | **No video** for the customer series |
| 5 | It's a fixed-list reference (statuses, steps, limits) | **Feeds**: the lists appear inside other videos (e.g. a pipeline rail) |
| 6 | It has under ~3 beats of its own, and a sibling covers the same job | **Merge** with that sibling (one video, both pages as sources) |
| 7 | It has a UI flow, or a model with states, layers or branches | **Video** |
| 8 | None of the above | **No video**; say why in one phrase |

## Series and format

| Page type | Series | Length | Arc |
|---|---|---|---|
| Task walkthrough | A | 60s | Full nine scenes, ending with "it's live" |
| Concept | B | 45s | Title, 4–5 diagram scenes, end card; no "it's live" |
| Data / config walkthrough | C | 45–60s | Walkthrough arc, with a diagram scene for sharing or replication |
| Admin (fleet, roles, org) | D | 45s | Matrix, grid or slide visuals |
| A single sharp warning | Short | 15–20s, 9:16 | Hook → trap → fix → logo |
| The index | Trailer | 30s | One beat per stage of the docs' order of work |

## Hooks

The hook is the one idea a viewer leaves with. Find it in this order:

1. A "not" statement on the page ("skipped is not success", "a published port is not a reachable port").
2. A warning box or gotcha.
3. The page's own purpose line, shortened.

Never write a hook the page doesn't support.

## Waves

| Wave | Pick |
|---|---|
| 1 | 3–4 videos that carry the product's strongest differentiators: the core path's hardest moment, day-two operations, troubleshooting honesty, automation. They also force the new engine patterns first. |
| 2 | The remaining core walkthroughs, the first concepts, the trailer (it needs finished scenes) |
| 3 | Data, config and admin; anything that was on Hold once it clears |

Put a review gate between waves. Wave *n+1* starts after wave *n* is approved and any docs fix it depends on has landed.

## Shorts

A good short has a trap a viewer has probably fallen into, and a one-action fix.

- Take shorts only from warnings, never from features.
- Tie each short to a parent video so it reuses that video's scenes.
- Six is a good first batch.
