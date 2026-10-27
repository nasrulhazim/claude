---
name: docs-explainer-video
metadata:
  compatible_agents: [claude-code, codex-cli, gemini-cli, cursor, chatgpt, gemini]
  tags: [video, explainer, documentation, tutorial, motion-graphics, playwright, ffmpeg, audio, branding, marketing]
description: >
  Turns a product documentation page into a branded ~60-second explainer video with music and
  sound effects synced to the picture, rendered deterministically to 720p, 1080p or 4K MP4. It
  maps doc sections to nine scene patterns (title, wizard form, single-path form, word-by-word
  sentence builder, topology diagram, review with guards, pipeline, "it's live" browser scene,
  end card), takes the real logo, colours and fonts from the product's live site, draws every frame
  as a pure function of time in a headless browser, synthesises a licence-free music bed and SFX from
  the same timeline, and ships an editable source project. Vendor-neutral: works in any assistant;
  ones without a shell produce the project and the user renders it with one command. Use this skill
  whenever the user asks to "make a video from this doc", "explainer video for this page",
  "tutorial video for the getting-started guide", "turn the docs into a video", "product walkthrough
  video", "render it in 1080p / 4K", "buat video dari dokumentasi ni", "buat video explainer untuk
  page ni", "jadikan docs ni video", "buat video tutorial produk", or "render video resolusi tinggi".
---

# Docs Explainer Video

Turns one documentation page into a ~60-second explainer that walks a viewer through the guide in the product's real brand, with music and sound effects synced to the picture. Output is an MP4 plus an editable source project.

The skill is plain Markdown. Its code (engine, render and audio scripts, starter brand and a complete example) lives in `references/toolkit-*.md` as marked blocks, and one unpack command writes them to disk. That makes it work in any assistant: those with a shell render end to end, and chat-only ones hand the user a project to render locally.

## Command Reference

| Command | Description |
|---|---|
| `/video plan <docs-url>` | Read the page and fill the brief: scene table, facts to keep, liberties, fictional data |
| `/video brand <site-url>` | Extract logo, colour variables and fonts into `brand.js` |
| `/video build` | Unpack the toolkit, create the project, write `scenes.js` |
| `/video stills` | One still per scene in a contact sheet; review before rendering |
| `/video render [720\|1080\|2160]` | Timeline → audio → frames → MP4 (720p preview first) |
| `/video deliver` | MP4 + source zip + summary of scenes, fictional data and next step |

## When to Use

- A docs, guide, tutorial or getting-started page should become a short video for a launch, onboarding, a social post or a training session.
- The user asks for a higher-resolution re-render of a project made with this skill.

**Not for:**
- Live-action or screen-recorded video.
- Talking heads or AI-generated footage.
- Videos longer than about 90s; split those into a series of one page each.
- A brand identity from scratch; use `product-brand-kit` first.

## 1. Read the source

Fetch the page and list every heading, step, table, warning and gotcha in order. If you can't fetch URLs, ask the user to paste the page text.

## 2. Plan the scenes

Fill `references/brief-template.md`, mapping one doc section to one pattern from `references/scene-patterns.md`. The default 60s arc:

| # | Pattern | Seconds |
|---|---|---|
| 0 | Title | 4.5 |
| 1–2 | Wizard form / single-path form (the doc's steps) | 6–8 each |
| 3 | Sentence builder (configuration) | 10.5 |
| 4 | Topology diagram (`reveal`) | 7.5 |
| 5 | Review + guards | 6 |
| 6 | Pipeline / progress (`lift`) | 7 |
| 7 | It's live | 7 |
| 8 | End card | 4 |

- Keep the page's own facts and warnings, and invent no capabilities. Any simplification (for example, a component drawn as its own node) is told to the user.
- If the request is thin, show the brief and build after a yes. If the user says "just try", state the brief in three lines and build.

## 3. Get the real brand

Follow `references/brand-template.md`:

1. Read the `:root` colour variables and header logo SVG from the live site with page JavaScript.
2. Map them to `brand.js`.
3. Fetch the fonts with `scripts/fetch_fonts.sh`.

No browser? Ask for the logo SVG, hex colours and font names. Never invent a logo or a palette.

## 4. Build the project

```bash
# 1. write toolkit/unpack.py from the first block of references/toolkit-scripts.md, then:
python3 toolkit/unpack.py toolkit references/toolkit-*.md references/brand-template.md references/example-g8deck.md
bash toolkit/scripts/setup.sh                    # once per machine (python3 + ffmpeg required)

# 2. new project = shell + engine + brand + scenes
mkdir -p my-video/fonts
cp toolkit/template/scene.html toolkit/template/engine.js my-video/
cp toolkit/template/brand.js my-video/            # then fill it (step 3)
cp toolkit/examples/g8deck-first-deployment/scenes.js my-video/   # starting point
```

Rewrite `scenes.js`:
- `SPEC` sets the timeline: `d`, `k` speed factor, `cap`, `rail`, `reveal`, `lift`.
- `RAIL` sets the doc's numbered steps.
- `FN` holds one function per scene.

Copy pattern functions from the example and change text and data. Every frame must be a pure function of time: no timers, no wall-clock, no unseeded randomness. Sound cues come from the same calls (`pop`, `typed`, `cue()`), so audio stays in sync on its own. `scene.html?t=12.5` previews any moment in a browser.

## 5. Review, then render

```bash
bash toolkit/scripts/render.sh my-video 720 --stills-only   # -> my-video/contact_sheet.png
bash toolkit/scripts/render.sh my-video 720                  # preview, ~2-3 min
bash toolkit/scripts/render.sh my-video 1080                 # final, ~6 min
bash toolkit/scripts/render.sh my-video 2160                 # 4K on request, ~20 min — run in the background
```

Read the contact sheet before any full render. Fix:
- wrapped numbers (`white-space:nowrap`);
- overlaps;
- a cursor missing its button;
- leftover hardcoded colours.

The assistant can't hear audio, so check loudness with `ffmpeg -i my-video/audio.wav -af ebur128 -f null -` (target about -16 LUFS after mux) and ask the user to listen.

## 6. Deliver

Send:
- the MP4;
- a zip of the project without `frames_*`.

Reply with:
- a table of what each scene shows;
- what's fictional (sample data, mock UI), with an offer to use real screenshots;
- a note that MP4s can't hold links, so the URL goes in the upload description;
- one next step (9:16 cut, voiceover, a second page).

## Other assistants

| Environment | What happens |
|---|---|
| Shell + network + Chromium (Claude Code, Codex CLI, Gemini CLI, Cursor agents) | Full workflow, end to end |
| Sandbox without browser or network | Build the project, zip it, give the user `setup.sh` + `render.sh` |
| Chat only (ChatGPT, Gemini apps) | Output `brand.js` and `scenes.js` as code blocks; the user unpacks the toolkit and renders |

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| "An emoji is a quick icon" | Emoji read as childish next to product UI. Use `ic()` line icons in the brand accent. |
| "Show all five source options, it's more complete" | A wall of alternatives hides the path. Show only the option being demonstrated. |
| "Spin every choice at once, it's faster" | Simultaneous slot reels read as chaos. Words appear in order; each choice opens, scrolls, selects, closes. |
| "The success badge is enough of an ending" | Viewers trust what they see running. Show the app live in a browser before the end card. |
| "I'll recreate the logo from the screenshot" | A redrawn logo is a fake logo. Take the SVG from the site, or ask. |
| "A setTimeout here is harmless" | Wall-clock timing makes frames depend on render speed. Everything is a function of `t`. |
| "Skip the stills, render the whole thing" | A full render takes minutes; a contact sheet takes seconds and catches layout bugs. |
| "Audio sounds fine" | You can't hear it. Measure loudness and ask the user to listen. |

## Red Flags

- Any emoji in `scenes.js`.
- Colours as hex literals inside `scenes.js` instead of `var(--…)` tokens.
- A scene with no caption or no motion for more than a second.
- The pipeline ends and the video cuts straight to the end card.
- Audio cues land in the wrong scene. Usually a `cue()` time is beyond the scene's `d * k`.
- Text wraps inside stat cards or pills.
- The end card shows a deep docs URL instead of the product site.

## Verification

- [ ] Every scene traces to a section of the source page; liberties were listed to the user
- [ ] Logo, colours and fonts come from the live site (or were supplied by the user)
- [ ] No emoji; line icons only
- [ ] Contact sheet reviewed; no wraps, overlaps or off-target cursors
- [ ] Topology scene shows each component as its own node with flowing packets
- [ ] An "it's live" scene shows the outcome before the end card; the end card is held ≥ 3s
- [ ] `timeline.json` regenerated after the last edit, so the audio matches the picture
- [ ] MP4 has video and audio streams of equal duration; loudness checked
- [ ] Source zip excludes `frames_*`; fictional data and the link-in-description note were stated

---

## Reference Files

| File | Read When |
|---|---|
| `references/scene-patterns.md` | Planning scenes; choosing helpers, sound cues and timeline keys |
| `references/brief-template.md` | Writing the scene sheet before building |
| `references/brand-template.md` | Extracting the brand from a site and filling `brand.js` |
| `references/toolkit-scripts.md` | Unpacking the toolkit; setup, render, capture, fonts and contact-sheet scripts |
| `references/toolkit-engine.md` | Changing motion, camera or HUD behaviour (rare; normally untouched) |
| `references/toolkit-audio.md` | Adjusting the music sections or SFX palette |
| `references/example-g8deck.md` | Starting `scenes.js`: the complete approved example to copy from |
