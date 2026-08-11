# סופ״ש של שנה — רשימה

A packing and shopping checklist for a two-night anniversary trip, 13–15 August 2026.
Hebrew, RTL, mobile-first, and usable with no signal.

**Live:** https://nadav-source.github.io/anniversary-weekend/

## What it does

- **156 items across 17 sections** — shopping, packing, and timed tasks.
- **Three filters** — `לקנות` / `לארוז` / `משימות`. In a supermarket, tap *לקנות*
  and the list collapses to just the 41 things you actually need to buy.
- **Hide-checked toggle**, so the list shrinks as you work through it.
- **Owner pills** — tap the circle at the end of a row to cycle
  both → נדב → טוני. Clothes sections come pre-assigned.
- **Add your own items** to any section.
- **Progress is saved on the device**, and the *לשלוח את ההתקדמות* button encodes
  the whole state — ticks, owners, custom items and notes — into a link. Send it
  and the other phone picks up exactly where you left off, with an undo.
- **Works offline.** Fonts are bundled and a service worker caches the shell,
  because reception at a tzimmer is not a given.
- **Prints** to a clean sheet if you'd rather carry paper.

## Structure

```
index.html              the entire app — markup, styles, logic, data
fonts/                  Heebo + Playfair Display, subset from the anniversary deck
sw.js                   offline shell: network-first HTML, cache-first assets
manifest.webmanifest    installable to a phone home screen
icon.svg
```

Edit the `SECTIONS` array near the top of the `<script>` block to change items.
Item ids are positional, so **reordering sections invalidates previously shared
links** — the decoder length-checks and refuses a stale link rather than
applying it to the wrong rows.

Design tokens (palette, type ramp) are inherited from the *year in review*
anniversary deck so the two pieces look like one project.
