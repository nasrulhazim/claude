# Page notes template

Record one block per docs page, in this exact shape. Use the same shape when briefing sub-agents, so their notes merge cleanly.

```
### <Page title> — <url>
- Sidebar group:
- Type: task walkthrough (UI steps) | concept (explains a model) | reference (lists/tables) | troubleshooting | index
- Updated date (if shown):
- Purpose (one line):
- Steps / flow in order — UI names in **bold** exactly as written (buttons, menus, wizard steps,
  fields, statuses); every number (step counts, limits, ports, timings, retention)
- Key concepts and how they relate (entities, states, lifecycles, state machines, hierarchies)
- Warnings / gotchas / "X is not Y" statements (paraphrased faithfully)
- Screenshots: alt text or caption of each image
- Links to other docs pages it depends on
- Visual potential: what could move (wizard, pipeline, lifecycle, topology, before/after,
  matrix, fan-out) — or "low" for mostly reference text
- Not visible: anything truncated or unreadable on this page
```

## Rules

| Rule | Why |
|---|---|
| Paraphrase; quotes under 15 words | Notes feed scripts, not copies of the docs |
| Keep exact UI labels and numbers | Scene sheets put them on screen; a wrong label is a wrong video |
| Never invent a feature or a number | A gap stays a gap; flag it under "Not visible" |
| Note screenshots even when you can't download them | Captions say what the real screen shows, so the redrawn UI can match it |
| Long generated pages: read in passes | Tool lists can exceed 150k characters; say what was cut |

## Sub-agent brief (paste and fill)

> Read every page listed below with your web-fetch tool. For each page return notes in the shape in `page-notes-template.md`. Be faithful: paraphrase, invent nothing, keep any quote under 15 words. If a page 404s or redirects, say so. At the end, list any docs pages you found that were not in the list, and the sidebar structure as shown on the site.
