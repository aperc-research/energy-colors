# APERC Energy Colors

A reference page for the data visualization colors used across the APEC EBT dashboard (fuels, sectors, regions, economies), so the palette is documented in one place instead of scattered across the dashboard's source files.

## Files

- `colors.json` — the color data. Single source of truth.
- `index.html` — the page. Fetches `colors.json` at load time and renders it. No build step.

Open `index.html` in a browser (served over HTTP, not `file://`, so `fetch()` can read the JSON) to view the page.

## Updating a color

Edit `colors.json` and refresh the page. `index.html` has no colors, labels, or scheme names written into it: the sidebar and every card are built from whatever is in the JSON. This covers editing an existing value, adding an entry to an existing list, and adding a whole new fuel-grouping scheme.

Adding a new top-level category (something beyond fuels/sectors/regions/economies) also works without touching the HTML, as long as it follows one of the two shapes the page already understands:

- `{ standard: [...], aggregate: {...} }` — like `fuels`. Gets a "Standard" link plus one link per scheme in `aggregate`.
- `{ list: [...] }` — like `sectors`, `regions`, `economies`. Gets a single link.

A genuinely different shape would need a small change in `index.html`'s `resolvePath`/`buildSidebar`.

## `colors.json` structure

- **`meta`** — title, description, `sources` (where the colors came from in the dashboard codebase), `notes` (callouts about intentional or coincidental color reuse).
- **`fuels.standard`** — every raw EGEDA fuel code with its own color. The ground truth.
- **`fuels.aggregate`** — the named "Fuel grouping" schemes offered in the dashboard (Major Fuels, Supply (Overview), Consumption + Major Oil, etc). Each entry has a `label`, a `regex` (which raw codes it clusters), and `inherits_from` — a code pointing back into `fuels.standard` instead of repeating the hex.
- **`sectors.list`** — the 6 TFC/TFEC end-use sector colors.
- **`regions.list`** — the 7 regional rollup colors. USA, China, and Russia use `inherits_from` pointing at their own economy code in `economies.list`; the other four (SEA, NEA, OAM, OCE) are genuine clusters with their own color and a `members` list of the economies they roll up.
- **`economies.list`** — all 21 APEC economies. Most are addressed by `family` + `lightness` (a hue family shared by several economies, distinguished only by lightness). Six are `fixed: true` with a manually chosen `display` label instead (e.g. Australia is `brown`, not a computed hue).

## How the page resolves a color

A card's color comes from either its own `hex`, or its `inherits_from` code looked up against a base list (`fuels.standard` for aggregate fuel entries, `economies.list` for regions). This keeps every color defined exactly once.

## Known color reuse

A few colors are intentionally or incidentally shared between labels — see `meta.notes` in `colors.json`, also shown on the page's Overview tab.
