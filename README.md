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

## Plan tomorrow

Tap the moon icon (☾) in the top bar to open an empty disc for tomorrow (or any day,
via Week view). Drag a chip from the tray onto the disc — where you drop it sets the
start time — and the block editor opens pre-filled so you can adjust the end time,
category, and repeat. Tap an already-placed block to edit or delete it.

Nothing special happens at midnight: a planned block is just stored with tomorrow's
date, and Day Pie's existing "is this block active today" check naturally picks it up
the moment that date becomes today — so it's live on the Glance disc from wake time
onward with no extra step.

## Week view

Tap the grid icon (▦) to see today plus the next six days as small discs side by side.
Each mini disc shows only block structure (today's also shows elapsed progress) — a
full week makes a busy stretch or a wide-open one visible as a shape, not a list. Tap
any day to open it in the Plan view.

## Meeting shadow (optional)

Enable "Meeting shadow" in Settings to hatch the 15 minutes before every block with a
diagonal pattern — a visual reminder that transitions between commitments quietly eat
time. The shadow clips at wake time and at the end of a preceding block, so back-to-back
blocks don't show a false gap.

## Day Card status drawer

If you also run [Day Card](#) (a companion daily-bingo PWA) **hosted at the same origin**
as Day Pie, a slim drawer under the disc shows today's squares marked, bingo status, and
current streak, pulled live from `localStorage`. Browsers isolate storage per-origin, so
this only works if both apps are served from the same domain — e.g. Day Pie at `/` and
Day Card at `/day-card/` in one repo. If Day Card isn't present (or is on a different
origin), the drawer just says so.

Data contract Day Pie reads (written by Day Card):
- `daycard_day_YYYY-MM-DD` → `{ marked: bool[25], dayWon, reward, rewardUnlocked, ... }`
- `daycard_stats_v1` → `{ currentStreak, bestStreak, ... }`

## Anchor Mode tie-in

If you use [Anchor Mode](https://activecatalyst.github.io/anchor-mode) — the daily
job-search schedule app — **hosted at the same origin as Day Pie**, Settings has an
"Import Anchor Mode day" button. It reads today's chosen template (Standard / Minimal /
High-performance), respects any items you've dragged into a different order or given
custom times, and turns each into a Day Pie block for today: Gym → training, lunch/
shutdown → rest, skill/creative blocks → life, everything else (deep work, networking,
admin) → work.

Re-importing replaces the previous import rather than duplicating it, so it's safe to
run again after you tweak something in Anchor Mode. Like the Day Card drawer, this only
works same-origin — e.g. Day Pie at `/` and Anchor Mode at `/anchor-mode/` on
`activecatalyst.github.io` — since it reads Anchor Mode's `anchor_mode_v2` localStorage
key directly rather than over a network request.

One thing worth knowing: the High-performance template starts at 6:00 AM, earlier than
Day Pie's default 7:00 AM wake time. Its early blocks will still be created, but
anything before your set wake time renders clipped to the start of the disc — adjust
your wake time in Settings if you use that template.

## Golden hour (optional)

Add your latitude/longitude in Settings to show a subtle band around sunset, pulled
from [Open-Meteo](https://open-meteo.com/) (no API key needed) and cached once per day
in `localStorage`.

## Data & privacy

Everything — settings, blocks, sunset cache — lives in `localStorage` on your device.
Nothing is sent anywhere except the optional Open-Meteo sunset lookup, which receives
only the coordinates you provide. The Day Card drawer only *reads* localStorage already
on the same origin — it never makes a network request.

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
