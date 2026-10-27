# Narration guide

The voice tells viewers why each scene matters; the picture shows what happens. Narration is optional per video: it runs whenever the project has a `narration.json`.

## How it works

| Piece | Where | What it does |
|---|---|---|
| `narration.json` | project folder, beside `scenes.js` | The lines and their start times; optional voice overrides |
| `BRAND.voice` | `brand.js` | Default voice, speed, language and `say` pronunciation map for the product |
| `scripts/narrate.py` | run by `render.sh` | Synthesises each line (Kokoro, local, no API key), checks timing, ducks the music under the voice, normalises to -16 LUFS |
| `render.sh … --no-voice` | flag | Renders without narration even when `narration.json` exists |

```json
{
 "lines": [
  {"t": 1.0,  "text": "Your first deployment, with G8Deck."},
  {"t": 4.8,  "text": "Connect a server you own: its address and an SSH key."},
  {"t": 56.6, "text": "G8Deck. Deploy on infrastructure you own."}
 ]
}
```

Write product names as they are spelled; `say` in `brand.js` turns them into speech (`"G8Deck": "Gate Deck"`). Never write the phonetic spelling into the lines themselves, so the script stays readable and correct when a pronunciation changes.

Overrides for one video go at the top level of `narration.json`: `"voice"`, `"speed"`, `"lang"`, `"say"`.

## Timing rules

| Rule | Value |
|---|---|
| Speaking rate | about 2.5 words per second at speed 1.0 |
| First line of a scene | 0.3 s after the scene starts |
| Gap before the next line | at least 0.15 s (enforced: an overrun stops the render) |
| Beat-by-beat scenes (a list revealing) | one short line per reveal, starting when that item appears |
| Title scene | the video's title only |
| End card | product name + tagline, nothing else |

Find scene start times in `timeline.json` (`scenes[].s`) after `capture.py --timeline`, or by adding up `SPEC[].d`.

## Writing rules

- **Say why, not what.** The screen already shows "Test connection ✓"; the voice says what that buys you.
- **One idea per line.** Short sentences, present tense, second person ("you").
- **Facts from the docs only**, like everything else in the video. Keep the page's warnings ("skipped is not success").
- **Don't read on-screen text word for word.** Exception: the title and the tagline.
- **Numbers as words** when spoken ("twenty-four pipeline steps"), digits on screen.
- **Abbreviations** the voice may misread: write them as spoken in `say`, e.g. `"DR": "D R"`.
- **Over the limit?** Cut words before you slow the picture. Speed above 1.1 sounds rushed.

## Voices

| Voice | Accent | Character |
|---|---|---|
| `af_heart` (default) | American, female | Warm, clear |
| `af_bella` | American, female | Brighter |
| `am_michael` | American, male | Calm |
| `bf_emma` | British, female | Crisp |
| `bm_george` | British, male | Deeper |

Kokoro speaks English (US and UK), Spanish, French, Hindi, Italian, Japanese, Portuguese and Mandarin. It has **no Malay voice**: for Bahasa Melayu narration, use a cloud TTS (Google, Azure, ElevenLabs) with the user's API key, and keep the same `narration.json` timing.

## Review

The assistant cannot hear the result. Before calling a narrated video done:

- every line fits (no overrun);
- loudness is checked (`ffmpeg -i <project>/audio_vo.wav -af ebur128 -f null -` → about -16 LUFS);
- a person has listened, at least to the product-name pronunciation.

A wording change needs no frame re-render: edit `narration.json` and run `render.sh` again (frames are reused; only the audio and the encode rerun).
