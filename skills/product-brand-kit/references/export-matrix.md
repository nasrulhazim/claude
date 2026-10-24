# Export Matrix

Every file the team asks for in the first month, rendered from the SVG masters.

| Folder | Files | Background | Notes |
|---|---|---|---|
| `svg/` | All 8 masters | As designed | For print and designers |
| `mark/` | Colour, mono-ink, mono-paper × 64/128/256/512/1024 | Transparent | |
| `wordmark/` | Colour, reversed, mono-ink × h60/h120/h240/h480 | Transparent | Width follows the aspect ratio |
| `wordmark/` | `-on-paper`, `-on-dark`, `-on-yellow` 1600×600 | Solid | The brand-colour ground uses the mono-ink wordmark |
| `app-icon/` | Dark tile 16/32/48/64/128/180/192/256/512/1024; light tile 512/1024; `favicon.ico` (16/32/48) | Tile | 180 = Apple touch, 192/512 = Android/PWA |
| `whatsapp-social/` | Avatar dark/brand/paper × 640 and 1080 (PNG) + 640 JPG copies | Solid, full bleed | Mark at 58% of the square, centred: safe inside the circle crop |
| `whatsapp-social/` | Banner 1280×720 | Brand colour | Pinned post or the group's first message |
| `social/` | Open Graph 1200×630 | Dark block | Reversed wordmark, tagline, URL, large mark at right |

Rules:
- Never put the colour mark on the brand-colour ground. Use mono-ink there.
- Below 24 px, use the app icon, not the bare mark.
- Text in the OG image and banner: embed the text font as a base64 `@font-face` before rendering.

## Render Script (Playwright)

```js
const { chromium } = require('playwright'); const fs = require('fs'); const path = require('path');
(async () => {
  const jobs = JSON.parse(fs.readFileSync('jobs.json'));   // [[outPath, W, H], …] + svgtmp/<i>.svg
  const b = await chromium.launch(); const p = await b.newPage();
  for (let i = 0; i < jobs.length; i++) {
    const [out, W, H] = jobs[i];
    await p.setViewportSize({ width: W, height: H });
    await p.setContent(`<html><head><style>${FONT_FACE_CSS}html,body{margin:0;background:transparent}svg{display:block}</style></head><body>${fs.readFileSync(`svgtmp/${i}.svg`, 'utf8')}</body></html>`);
    await p.evaluate(() => document.fonts.ready);
    fs.mkdirSync(path.dirname('export/' + out), { recursive: true });
    await p.screenshot({ path: 'export/' + out, omitBackground: true, clip: { x: 0, y: 0, width: W, height: H } });
  }
  await b.close();
})();
```

Compose each job by inlining a master SVG's inner markup into a `<g transform="translate scale">` on an
outer SVG of the target size (prefix ids per placement). Then:

```python
from PIL import Image
icons = [Image.open(f'export/app-icon/<brand>-app-icon-{s}.png') for s in (16, 32, 48)]
icons[2].save('export/app-icon/favicon.ico', sizes=[(16, 16), (32, 32), (48, 48)])
Image.open('export/whatsapp-social/<brand>-avatar-dark-640.png').convert('RGB').save('…-640.jpg', quality=95)
```

## Pack README

Put a `README.txt` at the zip root: the file to use for the WhatsApp group first, then one line per folder,
then the two rules above. Check the avatars once inside a circle mask at 200 px and 48 px before zipping.
