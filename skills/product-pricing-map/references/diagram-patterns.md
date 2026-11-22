# Diagram Patterns

The map shows every pricing type and what sits under it at a glance. Two proven forms.

## Form 1: Column map (no library)

One column per pricing type in a CSS grid, scrolling sideways on narrow screens. Simplest,
prints well, no CDN.

```html
<div class="mapx"><div class="map">           <!-- grid-template-columns: repeat(N, minmax(200px,1fr)); min-width: N×210px -->
  <div class="mcol c-inc">
    <div class="mhead"><b>Included</b><span>Every account, RM0</span></div>
    <div class="leaf"><b>Capture</b><span class="lp">Shared ingest</span>
      <ul><li>PII scrub</li><li class="nb">Breadcrumbs</li><li class="off">Stripe seat price</li></ul>
    </div>
  </div>
</div></div>
```

- `.mhead` has a 4px top border in the type colour; leaves are white cards with coloured dots.
- `li.nb::after{content:" · not built"}`, `li.off::after{content:" · built, off"}`,
  `li.ng::after{content:" · no gate"}` in the warn colour.

Use it when there are no upgrade paths worth drawing, or the product is simple.

## Form 2: Node diagram with upgrade paths (React Flow 11 UMD)

A root node ("Customer pays <Product>"), one type node per column, leaf cards under each, and
dashed animated edges for upgrade paths ("needs a backend", "auditors", "2+ events a year").
Clicking a node fills a detail panel below with the price, the why and its items.

Load from CDN (React 18 UMD, ReactDOM 18 UMD, `reactflow@11.11.4/dist/umd/index.js`) and
**inline the React Flow base CSS** — artifact sandboxes often block CDN stylesheets.

```js
const COLS = [{t:"inc", title:"Included", price:"…", why:"…", leaves:[
  {id:"core", title:"Core", price:"All plans", why:"…", items:[["Ticketing"],["Discount codes",1]]}
]}];
const UPGRADES = [["pass","pro","2+ events a year"], ["pro","business","teams, expos"]];
// layout: x = colIndex × 320; type row y = 170; leaves from y = 320,
// each leaf's height estimated from its item count so cards don't overlap
```

- Nodes not draggable or connectable; `fitView`; Background, Controls (no lock), MiniMap
  coloured by type.
- Item flags: `[text, 1]` = not built / no gate, `[text, 2]` = built, off.
- A checkbox toggles upgrade-path edges.
- Theme the canvas with the page tokens (`--surface`, `--line`, a `--mask` for the minimap).
- Keep a `<p>Loading the diagram…</p>` fallback inside the root so the page reads without JS.

Use it when the ladder has real moves between types worth showing, or there are more than
about 25 leaves.

## Either form

- Same type keys, labels and colours as the ledger and pills.
- Every leaf price matches the section tables; if a price changes, change it in both.
