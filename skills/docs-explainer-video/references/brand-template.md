# Brand template

Fill one `brand.js` per product from the live site. Never invent a logo.

## Read the brand from the site

Run this in a browser on the product site (any tool that can execute page JavaScript):

```js
const names=new Set();const walk=r=>{for(const x of r){if(x.style)for(const p of x.style)if(p.startsWith('--'))names.add(p);if(x.cssRules)walk(x.cssRules)}};
for(const s of document.styleSheets){try{walk(s.cssRules)}catch(e){}}
const cs=getComputedStyle(document.documentElement);const v={};names.forEach(n=>v[n]=cs.getPropertyValue(n).trim());
const svg=document.querySelector('header a svg, header svg');({vars:v,len:svg?.outerHTML.length,font:getComputedStyle(document.body).fontFamily})
```

Then read the logo in slices (tools truncate long strings): `document.querySelector('header a svg').outerHTML.slice(0,950)`, then `.slice(950)`.

No browser? Ask the user for the logo SVG, 4–6 hex colours and the font names.

## Token map

| `colors` key | Role | Typical site variable |
|---|---|---|
| `bg` | Page background | `--color-ink`, `--background` |
| `panel`, `panel2` | Cards, raised surfaces | `--color-surface`, `--color-raised` |
| `field` | Input background | slightly darker than `panel` |
| `line`, `strong` | Borders | `--color-line`, `--color-line-strong` |
| `text`, `muted`, `idle` | Text levels | `--color-fg`, `--color-muted` |
| `acc`, `accdim`, `onacc` | Brand accent, its dark tint, text on accent | `--color-accent`, `--primary` |
| `acc2` | In-progress / info | `--color-run`, `--info` |
| `amber`, `red` | Warning, failure | `--color-warn`, `--color-fail` |
| `grid`, `grid-major` | Background grid lines | `--color-grid` |

## Logo modes

| Mode | When | Fields |
|---|---|---|
| A | The wordmark is stroke paths | `viewBox`, `iconOrigin`, `strokeWidth`, `icon` (filled mark using `var(--acc)`), `word[]` (one path per letter group) |
| B | Any logo | `svg` (complete `<svg>`), which pops and wipes in |

## Starter file

<!-- file: template/brand.js -->
````js
/* Brand tokens for one product — replace every value with the real brand.
   Get them from the product's live site: header logo SVG, :root CSS colour variables, font families.
   Never invent a logo. If you cannot reach the site, ask the user for the logo SVG and hex colours. */
const BRAND={
 name:'Acme Cloud',
 site:'https://example.com',
 tagline:'Ship it on servers you own.',
 cta:'example.com →',
 footer:'Acme Cloud',
 logo:{
  // Mode B (works with any logo): paste the complete <svg>. It pops in and wipes left-to-right.
  svg:'<svg viewBox="0 0 200 48" xmlns="http://www.w3.org/2000/svg"><rect x="4" y="8" width="32" height="32" rx="8" fill="#3b82f6"/><text x="48" y="34" font-family="system-ui,sans-serif" font-size="26" font-weight="700" fill="#e8edf2">Acme Cloud</text></svg>',
  // Mode A (nicer, when the wordmark is stroke paths): viewBox, iconOrigin, strokeWidth, icon:'<path fill="var(--acc)" .../>', word:['M..','M..']
 },
 colors:{
  bg:'#0b0f14', panel:'#111821', panel2:'#18212b', field:'#0d131a', line:'#222d38', strong:'#33424f',
  text:'#e8edf2', muted:'#94a3b0', idle:'#64748b',
  acc:'#3b82f6', accdim:'#1e3a8a', onacc:'#020617', acc2:'#38bdf8', amber:'#f59e0b', red:'#fb7185',
  grid:'#121a22', 'grid-major':'#1a2530'
 },
 fontStacks:{ sans:'system-ui,sans-serif', mono:'ui-monospace,monospace', label:'ui-monospace,monospace' },
 fonts:[ /* {family:'Inter',weight:400,file:'fonts/inter-latin-400-normal.woff2'} — use fetch_fonts.sh */ ],
};
````
