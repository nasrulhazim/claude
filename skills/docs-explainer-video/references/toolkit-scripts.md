# Toolkit: render scripts

Write `unpack.py` first (the first block below, copied verbatim), then run it over all `toolkit-*.md` references to get every file:

```bash
python3 toolkit/unpack.py toolkit references/toolkit-*.md
```

| File | Role |
|---|---|
| `unpack.py` | Writes every marked block in these references to disk |
| `scripts/setup.sh` | One-time install: Playwright + Chromium, numpy, scipy, pillow |
| `scripts/render.sh` | `render.sh <project> 720\|1080\|2160 [--stills-only]` → timeline, audio, frames, MP4 |
| `scripts/capture.py` | `--timeline`, `--stills`, `--frames --scale` against a project's `scene.html` |
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
python3 -m pip install --quiet playwright numpy scipy pillow 2>/dev/null \
  || python3 -m pip install --quiet --break-system-packages playwright numpy scipy pillow
python3 -m playwright install chromium
echo "✓ ready — next: ./render.sh <project_dir> 1080"
````

<!-- file: scripts/render.sh -->
````bash
#!/usr/bin/env bash
# Render an explainer-video project to MP4 with synced music + SFX.
#   ./render.sh <project_dir> [720|1080|2160] [--stills-only]
# Needs: python3, ffmpeg, and the packages from setup.sh.
set -euo pipefail
HERE="$(cd "$(dirname "$0")" && pwd)"
PROJ="$(cd "${1:?usage: render.sh <project_dir> [720|1080|2160]}" && pwd)"
RES="${2:-1080}"
case "$RES" in 720) SCALE=1;; 1080) SCALE=1.5;; 2160|4k|4K) SCALE=3; RES=2160;; *) echo "resolution must be 720, 1080 or 2160"; exit 1;; esac

echo "▸ timeline + sound cues"
python3 "$HERE/capture.py" "$PROJ" --timeline

if [[ "${3:-}" == "--stills-only" ]]; then
  DUR=$(python3 -c "import json;print(json.load(open('$PROJ/timeline.json'))['dur'])")
  TS=$(python3 -c "import json;d=json.load(open('$PROJ/timeline.json'));print(','.join(str(round(s['s']+min(3,(s['e']-s['s'])*.7),2)) for s in d['scenes']))")
  python3 "$HERE/capture.py" "$PROJ" --stills "$TS"
  python3 "$HERE/contact_sheet.py" "$PROJ/stills" "$PROJ/contact_sheet.png"
  echo "✓ $PROJ/contact_sheet.png"; exit 0
fi

echo "▸ audio"
python3 "$HERE/audio.py" "$PROJ"

echo "▸ frames @ ${RES}p (resumable — rerun if interrupted)"
python3 "$HERE/capture.py" "$PROJ" --frames --scale "$SCALE"

NAME="$(basename "$PROJ")"
OUT="$PROJ/${NAME}-${RES}p.mp4"
CRF=17; [[ "$RES" == 2160 ]] && CRF=18
echo "▸ encode"
ffmpeg -loglevel error -y -framerate 30 -i "$PROJ/frames_${RES}p/f_%05d.jpg" -i "$PROJ/audio.wav" \
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
                cues: collectCues({a.fps})}})""")
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
