# Scene patterns

Reusable scene archetypes. Every one is implemented in the example `scenes.js` (`references/example-g8deck.md`, unpacked to `examples/g8deck-first-deployment/scenes.js`). Copy the function, then change the text and data. Times are local seconds within the scene.

| Pattern | Use it for | Example fn | Typical length |
|---|---|---|---|
| Title | Logo draws, title types, "before → after" cards, a time/effort chip | `s0` | 4–5s |
| Wizard form | A UI step where the user fills fields and presses a button | `s1` | 6–8s |
| Single-path form | A choice screen: show **only** the option being demonstrated | `s2` | 6s |
| Sentence builder | A configurable sentence or form, revealed word by word with an in-place picker per choice | `s3` | 9–11s |
| Topology diagram | How the configured thing actually runs: separate nodes, wires draw, packets flow | `s4` | 7–8s |
| Review + guards | Summary table, validations that pass, cursor clicks the primary button | `s5` | 6s |
| Pipeline / progress | Many steps ticking, warning states, status flips to success | `s6` | 7s |
| It's live | Browser opens the real URL with a padlock, the app renders with data, post-checks tick | `s7` | 7s |
| End card | Logo draws, tagline, CTA button with the product site | `s8` | 4s |

## Building blocks (engine.js)

| Helper | What it does | Sound |
|---|---|---|
| `pop(u, start, dy)` | Overshoot pop-in from below | `pop` |
| `popq(u, start, dy)` | Same pop, silent (use for dense groups) | — |
| `typed(text, u, start, cps)` | Types text with a caret | `key` per char |
| `count(u, s, e, a, b)` | Number counts up a→b | — |
| `cue(type, at, vol)` | Emits a sound cue inline, returns '' | any |
| `ic(name, size)` | Line icon: server app key lock git check shield shieldok alert pause refresh globe file pulse db bolt bucket entry users | — |
| `logo(h, draw, iconK)` | Brand logo; `draw` 0→1 wipes/draws the wordmark | — |
| `bz(p0,p1,p2,p3,t)` | Point on a cubic bézier (moves packets along wires) | — |

Sound cue types: `pop key word open step select tick warn success chime stamp click whoosh draw blip logo final`. A `whoosh` plays automatically at every scene start that has a caption.

## Timeline (`SPEC` in scenes.js)

```js
{d:7, k:.9, cap:['4','Watch the pipeline','24 idempotent steps'], rail:3, reveal:true, lift:true}
```

| Key | Meaning |
|---|---|
| `d` | Scene duration in seconds |
| `k` | Speed factor for the scene's choreography (0.9 = 10% slower), so you can retime without rewriting offsets |
| `cap` | Caption pill: [step number, title, where in the UI]; `null` for title and end scenes |
| `rail` | Index into `RAIL` to highlight (-1 hides the rail, ≥ RAIL.length hides it on the end card) |
| `reveal` | Camera pull-back (1.3→1.0) as the scene opens: use for diagrams and big reveals |
| `lift` | Music lifts from this scene on (one per video, usually the climax) |

## Rules that make it look good

- Something always moves: the camera drifts on its own, so holds are fine.
- One idea per scene. The caption names it in five words or fewer.
- Show the outcome: a success state is followed by the real thing running.
- Dense groups (grids, rows) use `popq` plus explicit `cue('tick', …)` so the audio doesn't rattle.
- Use `white-space:nowrap` on numbers and keep cards inside 1280×720 with 80px+ side margins.
