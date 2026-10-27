# Toolkit: render scripts

Write `unpack.py` first (the first block below, copied verbatim), then run it over all `toolkit-*.md` references to get every file:

```bash
python3 toolkit/unpack.py toolkit references/toolkit-*.md
```

| File | Role |
|---|---|
| `unpack.py` | Writes every marked block in these references to disk |
| `scripts/setup.sh` | One-time install: Playwright + Chromium, numpy, scipy, pillow, soundfile, kokoro-onnx, and the voice model (~350 MB; `SKIP_VOICE=1` skips it) |
| `scripts/render.sh` | `render.sh <project> 720\|1080\|2160 [--stills-only] [--no-voice]` → timeline, music + SFX, narration (when `narration.json` exists), frames, MP4 |
| `scripts/capture.py` | `--timeline` (also exports `BRAND.voice`), `--stills`, `--frames --scale` against a project's `scene.html` |
| `scripts/narrate.py` | Synthesises `narration.json` with Kokoro, applies the `say` pronunciation map, stops on any line that overruns its slot, ducks music under the voice |
| `scripts/fetch_fonts.sh` | Fontsource → `.woff2` + licence into a folder |
| `scripts/contact_sheet.py` | Tiles stills into one review image |

<!-- file: unpack.py -->
````python
import pathlib, re, sys
# Unpack every <!-- file: path --> block from the given reference files into a toolkit folder.
# Usage: python3 unpack.py <out_dir> references/*.md
out = pathlib.Path(sys.argv[1]); n = 0
for ref in sys.argv[2:]:
    text = pathlib.Path(ref).read_text()
    for path, body in re.findall(r"<!-- file: (.+?) -->\n````[a-z]*\n(.*?)````\n", text, re.S):
        f = out / path; f.parent.mkdir(parents=True, exist_ok=True); f.write_text(body)
        if f.suffix == ".sh": f.chmod(0o755)
        n += 1
print(f"unpacked {n} files -> {out}")
````

<!-- file: scripts/setup.sh -->
````bash
#!/usr/bin/env bash
# One-time setup for rendering. Safe to rerun.
set -euo pipefail
command -v python3 >/dev/null || { echo "install python3 first"; exit 1; }
command -v ffmpeg  >/dev/null || { echo "install ffmpeg first (brew install ffmpeg / apt install ffmpeg)"; exit 1; }
PKGS="playwright numpy scipy pillow soundfile kokoro-onnx"
python3 -m pip install --quiet $PKGS 2>/dev/null \
  || python3 -m pip install --quiet --break-system-packages $PKGS
python3 -m playwright install chromium
# narration voice model (Kokoro, ~350 MB, one time) — skip with SKIP_VOICE=1
if [[ "${SKIP_VOICE:-0}" != 1 ]]; then
  python3 -c "import sys;sys.path.insert(0,'$(dirname "$0")');import narrate;narrate.model_dir();print('voice model ready')"
fi
echo "✓ ready — next: ./render.sh <project_dir> 1080"
````

<!-- file: scripts/render.sh -->
````bash
#!/usr/bin/env bash
# Render an explainer-video project to MP4 with synced music, SFX and (optional) narration.
#   ./render.sh <project_dir> [720|1080|2160] [--stills-only] [--no-voice]
# Narration runs automatically when <project_dir>/narration.json exists.
# Needs: python3, ffmpeg, and the packages from setup.sh.
set -euo pipefail
HERE="$(cd "$(dirname "$0")" && pwd)"
PROJ="$(cd "${1:?usage: render.sh <project_dir> [720|1080|2160] [--stills-only] [--no-voice]}" && pwd)"
RES="${2:-1080}"
STILLS=0; VOICE=1
for a in "${@:3}"; do
  case "$a" in --stills-only) STILLS=1;; --no-voice) VOICE=0;; *) echo "unknown flag $a"; exit 1;; esac
done
case "$RES" in 720) SCALE=1;; 1080) SCALE=1.5;; 2160|4k|4K) SCALE=3; RES=2160;; *) echo "resolution must be 720, 1080 or 2160"; exit 1;; esac

echo "▸ timeline + sound cues"
python3 "$HERE/capture.py" "$PROJ" --timeline

if [[ "$STILLS" == 1 ]]; then
  TS=$(python3 -c "import json;d=json.load(open('$PROJ/timeline.json'));print(','.join(str(round(s['s']+min(3,(s['e']-s['s'])*.7),2)) for s in d['scenes']))")
  python3 "$HERE/capture.py" "$PROJ" --stills "$TS"
  python3 "$HERE/contact_sheet.py" "$PROJ/stills" "$PROJ/contact_sheet.png"
  echo "✓ $PROJ/contact_sheet.png"; exit 0
fi

echo "▸ music + sfx"
python3 "$HERE/audio.py" "$PROJ"
AUDIO="$PROJ/audio.wav"
if [[ "$VOICE" == 1 && -f "$PROJ/narration.json" ]]; then
  echo "▸ narration"
  python3 "$HERE/narrate.py" "$PROJ"     # exits non-zero if a line overruns its scene
  AUDIO="$PROJ/audio_vo.wav"
fi

echo "▸ frames @ ${RES}p (resumable — rerun if interrupted)"
python3 "$HERE/capture.py" "$PROJ" --frames --scale "$SCALE"

NAME="$(basename "$PROJ")"
OUT="$PROJ/${NAME}-${RES}p.mp4"
CRF=17; [[ "$RES" == 2160 ]] && CRF=18
echo "▸ encode"
ffmpeg -loglevel error -y -framerate 30 -i "$PROJ/frames_${RES}p/f_%05d.jpg" -i "$AUDIO" \
  -c:v libx264 -preset veryfast -crf $CRF -pix_fmt yuv420p \
  -c:a aac -b:a 192k -af loudnorm=I=-16:TP=-1.5:LRA=11 -shortest -movflags +faststart "$OUT"
echo "✓ $OUT"
````

<!-- file: scripts/capture.py -->
````python
"""Deterministic frame capture for an explainer-video project.

A project folder contains: scene.html, engine.js, brand.js, scenes.js, fonts/
Every frame is drawn by calling render(t) in the page, then screenshotted.

Usage (run from anywhere):
  python3 capture.py <project_dir> --timeline            # writes timeline.json (scenes + sound cues)
  python3 capture.py <project_dir> --stills 3,12.5,40    # writes stills/still_<t>.png
  python3 capture.py <project_dir> --frames [--scale 1.5] [--fps 30] [--start N --end M]
        scale: 1 = 1280x720, 1.5 = 1920x1080, 3 = 3840x2160
Frames go to <project_dir>/frames_<height>p/ and are resumable (existing frames are skipped).
"""
import argparse, json, pathlib, sys
from playwright.sync_api import sync_playwright


def main():
    ap = argparse.ArgumentParser()
    ap.add_argument("project")
    ap.add_argument("--timeline", action="store_true")
    ap.add_argument("--stills")
    ap.add_argument("--frames", action="store_true")
    ap.add_argument("--scale", type=float, default=1.0)
    ap.add_argument("--fps", type=int, default=30)
    ap.add_argument("--start", type=int, default=0)
    ap.add_argument("--end", type=int)
    a = ap.parse_args()

    proj = pathlib.Path(a.project).resolve()
    page_url = (proj / "scene.html").as_uri()
    if not (proj / "scene.html").exists():
        sys.exit(f"no scene.html in {proj}")

    with sync_playwright() as p:
        b = p.chromium.launch()
        pg = b.new_page(viewport={"width": 1280, "height": 720}, device_scale_factor=a.scale)
        errors = []
        pg.on("pageerror", lambda e: errors.append(str(e)))
        pg.goto(page_url)
        pg.evaluate("document.fonts.ready")
        pg.evaluate("Promise.all([...document.fonts].map(f=>f.load()))")
        if errors:
            sys.exit("page error: " + errors[0])

        if a.timeline:
            data = pg.evaluate(f"""({{dur: DUR,
                scenes: SC.map(s=>({{s:s.s,e:s.e,cap:s.cap?s.cap[1]:null,lift:!!s.lift}})),
                cues: collectCues({a.fps}),
                voice: (typeof BRAND!=='undefined' && BRAND.voice) || null}})""")
            (proj / "timeline.json").write_text(json.dumps(data))
            print(f"{len(data['cues'])} cues · {data['dur']}s · {len(data['scenes'])} scenes")
            return

        if a.stills:
            out = proj / "stills"; out.mkdir(exist_ok=True)
            for t in a.stills.split(","):
                pg.evaluate(f"render({float(t)})")
                pg.screenshot(path=str(out / f"still_{t}.png"))
            print("stills ->", out)
            return

        if a.frames:
            dur = pg.evaluate("DUR")
            total = round(dur * a.fps)
            end = min(a.end or total, total)
            out = proj / f"frames_{round(720 * a.scale)}p"; out.mkdir(exist_ok=True)
            for i in range(a.start, end):
                f = out / f"f_{i:05d}.jpg"
                if f.exists():
                    continue
                pg.evaluate(f"render({i / a.fps})")
                pg.screenshot(path=str(f), type="jpeg", quality=94)
            print(f"frames {a.start}-{end} -> {out}")
            return
        b.close()
    ap.print_help()


if __name__ == "__main__":
    main()
````

<!-- file: scripts/narrate.py -->
````python
"""Narration: synthesise <project>/narration.json with a local TTS voice and mix it over <project>/audio.wav.

narration.json — either a list of lines, or an object with settings + lines:
  {"voice": "af_heart", "speed": 1.0, "lang": "en-us", "say": {"G8Deck": "Gate Deck"},
   "lines": [{"t": 1.0, "text": "Your first deployment, with G8Deck."}, ...]}
Each line starts at t seconds. Defaults come from BRAND.voice in brand.js (exported into timeline.json
by capture.py --timeline); narration.json overrides them. `say` maps written words to how they are spoken.

Checks: every line must end 0.15 s before the next line starts (or before the video ends).
An overrun exits with code 2 and prints which line to shorten — nothing is mixed.

Writes <project>/voice.wav and <project>/audio_vo.wav (music + SFX ducked under the voice, -16 LUFS).
Usage: python3 narrate.py <project_dir> [--allow-overrun]
Voice model: Kokoro (Apache-2.0), downloaded once to $EXPLAINER_VOICE_DIR (default ~/.cache/explainer-voice).
"""
import json, os, pathlib, re, subprocess, sys, urllib.request

MODEL_URL = "https://github.com/thewh1teagle/kokoro-onnx/releases/download/model-files-v1.0/"
FILES = ["kokoro-v1.0.onnx", "voices-v1.0.bin"]
GAP = 0.15


def model_dir():
    d = pathlib.Path(os.environ.get("EXPLAINER_VOICE_DIR", pathlib.Path.home() / ".cache" / "explainer-voice"))
    d.mkdir(parents=True, exist_ok=True)
    for f in FILES:
        if not (d / f).exists():
            print(f"downloading voice model {f} (one time) ...")
            urllib.request.urlretrieve(MODEL_URL + f, d / f)
    return d


def apply_say(text, say):
    for written, spoken in sorted(say.items(), key=lambda kv: -len(kv[0])):
        text = re.sub(rf"(?<![\w-]){re.escape(written)}(?![\w-])", spoken, text)
    return text


def main():
    args = [a for a in sys.argv[1:] if not a.startswith("--")]
    allow = "--allow-overrun" in sys.argv
    proj = pathlib.Path(args[0]).resolve()
    tl = json.loads((proj / "timeline.json").read_text())
    raw = json.loads((proj / "narration.json").read_text())
    cfg = {"voice": "af_heart", "speed": 1.0, "lang": "en-us", "say": {}}
    cfg.update({k: v for k, v in (tl.get("voice") or {}).items() if v is not None})
    lines = raw if isinstance(raw, list) else raw.get("lines", [])
    if isinstance(raw, dict):
        say = {**cfg.get("say", {}), **raw.get("say", {})}
        cfg.update({k: v for k, v in raw.items() if k not in ("lines", "say")})
        cfg["say"] = say
    lines = sorted(lines, key=lambda l: l["t"])
    dur = float(tl["dur"])

    import numpy as np, soundfile as sf
    from kokoro_onnx import Kokoro
    d = model_dir()
    k = Kokoro(str(d / FILES[0]), str(d / FILES[1]))

    track, sr, problems = None, None, []
    print(f"voice {cfg['voice']} · speed {cfg['speed']} · {cfg['lang']} · {len(lines)} lines")
    for i, ln in enumerate(lines):
        spoken = apply_say(ln["text"], cfg["say"])
        samples, sr = k.create(spoken, voice=cfg["voice"], speed=float(cfg["speed"]), lang=cfg["lang"])
        if track is None:
            track = np.zeros(int(sr * (dur + 2)), dtype=np.float32)
        took = len(samples) / sr
        limit = lines[i + 1]["t"] if i + 1 < len(lines) else dur
        over = ln["t"] + took > limit - GAP
        if over:
            problems.append((ln, took, limit - ln["t"]))
        print(f"{ln['t']:6.2f}s  {took:5.2f}s of {limit - ln['t']:5.2f}s{'  <-- OVERRUNS' if over else ''}  {spoken[:64]}")
        s = int(ln["t"] * sr)
        track[s:s + len(samples)] += samples[: len(track) - s]

    if problems and not allow:
        print("\nOVERRUN — shorten these lines (about 2.5 spoken words per second), or move the next line later:")
        for ln, took, room in problems:
            print(f"  t={ln['t']}: needs {took:.2f}s, has {room - GAP:.2f}s — \"{ln['text']}\"")
        sys.exit(2)

    track = track[: int(sr * dur)]
    sf.write(proj / "voice.wav", track, sr)
    subprocess.run(["ffmpeg", "-loglevel", "error", "-y", "-i", str(proj / "audio.wav"), "-i", str(proj / "voice.wav"),
        "-filter_complex",
        "[1:a]aresample=44100,pan=stereo|c0=c0|c1=c0,volume=1.6,asplit[v1][v2];"
        "[0:a][v1]sidechaincompress=threshold=0.015:ratio=10:attack=15:release=350:makeup=1[duck];"
        "[duck][v2]amix=inputs=2:normalize=0,loudnorm=I=-16:TP=-1.5:LRA=11[out]",
        "-map", "[out]", "-ar", "44100", str(proj / "audio_vo.wav")], check=True)
    print(f"voice -> {proj / 'audio_vo.wav'}")


if __name__ == "__main__":
    main()
````

<!-- file: scripts/fetch_fonts.sh -->
````bash
#!/usr/bin/env bash
# Download Google/Fontsource fonts as latin .woff2 files into a folder.
#   ./fetch_fonts.sh <out_dir> archivo:400,500,600,700,800 ibm-plex-mono:400,500,600
#   e.g. ./fetch_fonts.sh my-video/fonts archivo:400,700
# Font slug = Fontsource package name (https://fontsource.org). Needs node + npm.
set -euo pipefail
mkdir -p "$1"; OUTD="$(cd "$1" && pwd)"; shift
TMP="$(mktemp -d)"
( cd "$TMP" && npm init -y >/dev/null )
for spec in "$@"; do
  slug="${spec%%:*}"; weights="${spec#*:}"
  ( cd "$TMP" && npm i --silent "@fontsource/$slug" )
  IFS=',' read -ra W <<< "$weights"
  for w in "${W[@]}"; do
    cp "$TMP/node_modules/@fontsource/$slug/files/$slug-latin-$w-normal.woff2" "$OUTD/"
  done
  cp "$TMP/node_modules/@fontsource/$slug/LICENSE" "$OUTD/LICENSE-$slug" 2>/dev/null || true
  echo "✓ $slug ($weights)"
done
rm -rf "$TMP"
````

<!-- file: scripts/contact_sheet.py -->
````python
"""Tile every still in a folder into one contact sheet for review.
Usage: python3 contact_sheet.py <stills_dir> <out.png> [columns]
"""
import sys, pathlib
from PIL import Image

src = pathlib.Path(sys.argv[1]); out = sys.argv[2]; cols = int(sys.argv[3]) if len(sys.argv) > 3 else 3
files = sorted(src.glob("still_*.png"), key=lambda p: float(p.stem.split("_", 1)[1]))
if not files:
    sys.exit("no stills found")
w, h = 640, 360
rows = (len(files) + cols - 1) // cols
sheet = Image.new("RGB", (cols * w, rows * h), "black")
for i, f in enumerate(files):
    sheet.paste(Image.open(f).convert("RGB").resize((w, h)), ((i % cols) * w, (i // cols) * h))
sheet.save(out)
print(out)
````
