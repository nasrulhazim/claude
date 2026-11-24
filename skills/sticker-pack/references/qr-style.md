# Styled QR

A QR that looks designed and still scans. The same geometry works for a deck slide (SVG/JS)
and a sticker (Python).

## Before you draw

1. **Data = a live URL.** Run `curl -sI <url>` and get 200. Prefer the product's public site or a
   campaign short link (e.g. `/r/<code>` that sets a referral and lands on sign-up). No staging
   hosts and no IPs.
2. **Level H** whenever a logo sits in the middle (about 30% recovery). Level Q is enough
   without a logo.
3. **Module size ≥ 0.8 mm printed** (0.75 mm is the bare minimum). Longer URLs raise the
   version and shrink the modules, so make the panel bigger or the URL shorter.

## Geometry (in module units, n = module count)

| Part | Draw |
|---|---|
| Data modules | A circle of radius 0.42 centred in each dark cell (`M c+.5 r+.08 a.42 .42 0 1 1 0 .84 a.42 .42 0 1 1 0-.84z`) |
| Finder eyes (3) | Don't draw them as modules. Each is a **solid ring**: an evenodd path, outer 7×7 rounded r=2, hole 5×5 offset 1 rounded r=1.3. Centre: a 3×3 square at offset 2, rx 0.9, filled in the brand accent |
| Centre logo | Clear k = round(n × 0.24) \| 1 modules (odd). Draw a white rounded rect (k + 0.6, rx 1.4), a dark rounded rect inside it (k − 0.4, rx 1.2), then the mark at 60% of k |
| Panel | White, with a quiet zone of at least 2 modules (4 is the spec; 2 is fine on a light panel) |

Rules learned the hard way:

- **Draw the eye rings as filled evenodd paths, not stroked rects.** Some PDF renderers print a
  stroked SVG rect as a hairline.
- **Embed the logo** (inline path, or a data: URI), never a relative `<image href>`. A relative
  path breaks the moment the page is opened from another folder, and the QR then shows a
  broken-image icon.
- **Generate the QR offline.** Vendor the JS library next to the deck, or build the QR at build
  time. A CDN-only QR disappears when the venue Wi-Fi drops.
- **A spinning conic-gradient border** around a QR window prints as a solid wedge across the
  code. Hide it in `@media print`.

## Python (segno)

```python
q = segno.make(url, error="h", boost_error=False, micro=False)
m = [list(r) for r in q.matrix]; n = len(m)
mod = panel_mm / (n + 2 * quiet)          # report this: the module size in mm
```

## JS (qrcode-generator, vendored)

```js
var q = qrcode(0, 'H'); q.addData(url); q.make();
var n = q.getModuleCount();               // then q.isDark(r, c) per cell, same geometry as above
```

## Decode gate

```python
for scale in (1.0, 0.75, 0.5, 0.33, 0.25):
    im = Image.open(png).convert("L"); im = im.resize((int(im.width*scale), int(im.height*scale)), Image.LANCZOS)
    out = subprocess.run(["zbarimg", "-q", "--raw", p], capture_output=True, text=True).stdout.strip()
    if scale in (1.0, 0.25): assert out == url       # hard gate
```

- Use **zbarimg**, not OpenCV. OpenCV's `QRCodeDetector` routinely fails on dot-style modules
  and rounded eyes that phones and zbar read fine.
- zbar is also erratic on dot codes at **some** sizes. Report the 0.75, 0.5 and 0.33 sweep for
  information, and gate on 1.0 and 0.25. If 1.0 fails, change the panel size by 1–2 mm rather
  than switching styles.
- On a deck slide, test the PDF render (`pdftoppm -r 200`, crop the QR, then `zbarimg`) **and**
  the screen render (screenshot with reduced motion so the floating window holds still).
- A library decode is not a phone scan. Always list "phone scan of a printed proof" as still
  open.
