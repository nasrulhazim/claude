---
name: docs-video-series
metadata:
  compatible_agents: [claude-code, codex-cli, gemini-cli, cursor, chatgpt, gemini]
  tags: [video, documentation, content-plan, series, triage, explainer, shorts, marketing, roadmap]
description: >
  Studies every page of a product's documentation site and turns it into a production-ready
  explainer video series plan: per-page notes in a fixed shape, a triage of each page (own
  video, merge, feeds another video, no video, hold), a series map grouped into walkthrough,
  concept, data and admin videos with lengths, hooks taken from the docs' own warnings and a
  status column, nine-scene sheets for the first wave, beat outlines for later waves, 9:16
  gotcha shorts, a trailer, the scene patterns the video engine still lacks, a log of docs
  claims to fix before filming, and a gated wave roadmap. Plans what docs-explainer-video then
  renders. Vendor-neutral. Use this skill whenever the user asks to "plan videos for all our
  docs", "study the documentation and plan a video series", "which docs pages deserve a video",
  "turn the whole docs site into videos", "content plan for product tutorials", "kaji semua
  dokumentasi dan rancang video", "rancang siri video untuk docs", "page mana patut jadi video",
  or "buat pelan video tutorial produk".
---

# Docs Video Series

Turns a whole documentation site into a video series plan the team can work through: which pages become videos, in what format, in what order, with scene sheets for the first wave and a tracker for the rest. It plans; `docs-explainer-video` renders each video.

The plan is a document people keep and update, not a chat answer. The approved reference is the G8Deck plan in `references/example-g8deck-series.md`: 29 pages became 20 videos, 6 shorts and a trailer in three waves.

## Command Reference

| Command | Description |
|---|---|
| `/video series <docs-url>` | Full run: inventory → notes → triage → plan document |
| `/video series inventory <docs-url>` | List every docs page from the sidebar or index, grouped as the site groups them |
| `/video series triage` | Decide video / merge / feeds / none / hold for each page |
| `/video series wave <n>` | Write full scene sheets for wave *n* |
| `/video series update` | Re-read changed pages and refresh the plan's rows |

## When to Use

- A docs site, or a large section of one (10+ pages), should become a planned set of videos.
- A plan exists and pages have changed since it was written.

**Not for:**
- One page to one video: use `docs-explainer-video` directly.
- Marketing campaigns not grounded in docs: use `product-brand-kit`.
- Writing or fixing the docs themselves: use `project-docs`.

## 1. Inventory

Fetch the docs index and read its sidebar or navigation. List every page with its URL and sidebar group, in the site's order. Count them, and say the count to the user before going on.

If the site can't be fetched, ask for the sitemap or the page list.

## 2. Study every page

Read each page in full and record notes in the fixed shape from `references/page-notes-template.md`:
- type;
- purpose;
- steps with exact UI names;
- key concepts;
- warnings;
- screenshot captions;
- links;
- visual potential.

Some pages are very long (generated tool lists, for example). Read them in passes, and flag anything you could not see.

When the environment can run parallel sub-agents, fan the pages out in batches and keep only their notes. Otherwise read sequentially. Never skip a page because its title looks like reference: decide that in triage.

## 3. Triage

Apply `references/triage-rubric.md` to every page. Each page gets exactly one decision:

| Decision | When |
|---|---|
| **Video** | It has a UI flow or a model that moves |
| **Merge** | It is too thin alone, and a neighbour tells the same story |
| **Feeds** | A reference list whose content appears inside other videos |
| **No video** | A tool or parameter list, operator-only material, or an index page |
| **Hold** | It makes a claim to verify with the product owner first |

Put every page in the triage table: page, sidebar group, type, decision, video ID. Nothing is silently dropped.

## 4. Build the series map

Group the videos into series:

| Series | Type | Length |
|---|---|---|
| A | Task walkthroughs | 60s |
| B | Concepts (diagram-led, no "it's live" scene) | 45s |
| C | Data and config | 45–60s |
| D | Fleet / organisation admin | 45s |

Then add 9:16 gotcha shorts of 15–20s, and one 30s trailer built from finished scenes.

Each video gets:
- an ID;
- its source page(s);
- a length;
- a wave;
- a **hook**: the one idea a viewer leaves with, taken from that page's own warnings, never invented;
- a status (Planned / Scripting / Rendering / Review / Done / On hold).

Put the strongest "why this product" stories in wave 1. Use these as signals:
- a "don't trust X" warning;
- day-two operations;
- automation;
- the core deploy path.

## 5. Write the waves

- **Wave 1:** full nine-scene sheets on the `docs-explainer-video` arc. Each sheet has #, time, scene, what moves (exact UI names and numbers from the notes) and pattern. Mark every pattern the engine lacks as *(new)*.
- **Later waves:** one row each, with beats, facts to keep on screen and a signature visual. Full sheets are written when a video moves to Scripting.
- **Shorts:** hook (2s) → trap (6s) → fix (6s) → logo + docs URL (3s), each tied to a parent video so its scenes are reused.

## 6. Engine gaps and docs issues

- **Engine gaps:** list each *(new)* pattern once, with what it draws, which videos need it, and its wave. Add the 9:16 layout if there are shorts, and a shared `SAMPLE` data constant so the series reads as one story.
- **Docs issues:** a video repeats a docs claim to a wider audience, so log what to fix before filming:
  - contradictions between pages;
  - internal inconsistencies (e.g. a tool tier versus its parameters);
  - claims that may run ahead of the product.

  **Ask the user** about capability claims that look aspirational. Don't assume they are true or false. Put any video that depends on an unverified claim on **Hold**.

## 7. Write the plan document

Use the skeleton in `references/plan-template.md`, in this order:

1. TLDR with counts.
2. Triage table.
3. Series map with a status column.
4. Wave 1 scene sheets.
5. Later-wave outlines.
6. Shorts and trailer.
7. Engine work.
8. Docs issues.
9. Production order.

The production order is a drawn roadmap of engine work → waves with review gates between them, followed by the per-video workflow. No dates unless the user gives them.

Deliver it as a shareable, editable document: the environment's document tool if it has one, otherwise a Markdown file. Reply with one line, the link, and the one decision the user must make first (usually a docs claim to confirm).

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "The reference pages are boring, skip reading them" | Reference pages hold the step lists and limits that make scenes accurate. Read everything, then decide. |
| "Every page deserves its own video" | Thin neighbours told twice bore viewers. Merge them, and let lists feed other videos. |
| "A punchy hook will sell it" | An invented hook is marketing the docs don't back. Take it from the page's own warnings. |
| "The docs say it's supported, so the video can say so" | Docs can run ahead of the product. Log doubtful claims and ask; put dependent videos on Hold. |
| "Write all twenty scene sheets now" | Pages change and later waves get re-scoped. Sheets for wave 1 only; outlines for the rest. |
| "Put dates on the roadmap" | Dates nobody agreed to become commitments. Gates and order only, unless the user sets dates. |

## Red Flags

- The triage table has fewer rows than the inventory count.
- A hook or scene fact that appears nowhere in that page's notes.
- A wave-1 sheet that uses a pattern not in the engine and not marked *(new)*.
- Operator-only or internal pages planned as customer videos.
- A video depending on a claim in the docs-issues log that isn't on Hold.
- The plan delivered as a long chat message instead of an editable document.

## Verification

- [ ] Inventory count stated; every page appears once in the triage table
- [ ] Every page has notes in the fixed shape; truncated pages flagged
- [ ] Each video has an ID, source page(s), length, wave, hook from the docs and a status
- [ ] Wave 1 has full nine-scene sheets with exact UI names and numbers
- [ ] Every *(new)* pattern appears in the engine-work table
- [ ] Docs issues are logged, the doubtful claims were put to the user, and dependent videos are on Hold
- [ ] Roadmap shows engine work, waves and gates; no invented dates
- [ ] The plan is an editable, shareable document; the reply names the first decision needed

---

## Reference Files

| File | Read When |
|---|---|
| `references/page-notes-template.md` | Recording notes for each page (step 2); briefing sub-agents |
| `references/triage-rubric.md` | Deciding video / merge / feeds / no video / hold, and grouping into series (steps 3–4) |
| `references/plan-template.md` | Writing the plan document: section order, table headers, scene-sheet and shorts formats |
| `references/example-g8deck-series.md` | Calibrating: a complete approved plan for a 29-page docs site |
