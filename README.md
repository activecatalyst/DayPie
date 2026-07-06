# Day Pie

A time-blindness clock. Your waking day, rendered as a live disc — how much of it is
spent, and how much is actually still free.

No build step, no dependencies, no backend. Vanilla HTML/CSS/JS + SVG, installable as
a PWA, works offline once loaded.

## What it shows

The disc represents only your waking hours (default 7:00–23:00, set in Settings) —
midnight-to-midnight is irrelevant here. It fills clockwise:

- **Elapsed** time is near-black charcoal — spent, gone.
- **Now** is a thin glowing orange line, sweeping in real time (updates every 30s).
- **Remaining** time is a warm dim gradient the dark is still consuming.
- In the **final 90 minutes** before your sleep time, the remaining wedge shifts to a
  calm ember tone — a wind-down signal, not an alarm.
- At **wake time**, a soft bloom animation plays once for the day.

The center number is the honest figure: waking hours left *minus* time already
committed to blocks. Below it: current time, and what's coming up next.

## Blocks

Add commitments or intentions as arcs on the disc: label, start/end time, a category
color, and an optional repeat (daily / weekdays) for standing structure like training
or meals.

- **Category colors**: work = orange, training = crimson, life = slate, rest = navy.
- Tap a block to edit or delete it.
- Blocks that have already happened fade into the dark with just a faint outline.
- Long-press the block currently in progress to open **Focus** mode — its own ring,
  showing elapsed/remaining within just that block.

## Modes

- **Glance** — the default view described above.
- **Focus** — one block, isolated. Triggered by long-pressing the current block.
- **Ambient** — strips all text except the disc and the remaining-hours number, dims
  slightly. Meant to be left open on a desk display all day.

Switch between Glance and Ambient with the bar at the bottom of the screen.

## Golden hour (optional)

Add your latitude/longitude in Settings to show a subtle band around sunset, pulled
from [Open-Meteo](https://open-meteo.com/) (no API key needed) and cached once per day
in `localStorage`.

## Data & privacy

Everything — settings, blocks, sunset cache — lives in `localStorage` on your device.
Nothing is sent anywhere except the optional Open-Meteo sunset lookup, which receives
only the coordinates you provide.

## Running it

No build step. Just serve the folder statically:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Or open `index.html` directly in a browser (the service worker requires being served
over `http://` or `https://`, not `file://`).

## Deploying to GitHub Pages

1. Push this folder to a repository.
2. In the repo's **Settings → Pages**, set the source to the branch/folder containing
   these files (root, or `/docs` if you move them there).
3. Visit the published URL. On mobile, use "Add to Home Screen" to install it as a
   standalone app; on desktop Chrome/Edge, use the install icon in the address bar.

## Files

| File           | Purpose                                              |
|----------------|-------------------------------------------------------|
| `index.html`   | The entire app — markup, styles, and logic            |
| `manifest.json`| PWA metadata (name, icons, colors, display mode)       |
| `sw.js`        | Service worker — caches assets for offline use         |
| `icon.svg`     | App icon / favicon                                     |

## Customizing colors

Category colors live in one place — the `CATEGORY_COLORS` object near the top of the
`<script>` block in `index.html` — and are mirrored in the settings modal's category
swatches (`#categoryPick`). Change both to re-theme.

Current palette:

- `work` — `#F26419` (orange)
- `training` — `#9E2B25` (crimson)
- `life` — `#7C8591` (slate)
- `rest` — `#1F2A44` (navy)
