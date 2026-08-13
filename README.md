# סופ״ש של שנה — רשימה

A packing and shopping checklist for two nights in Kamun, 13–15 August 2026.
Hebrew, RTL, mobile-first, and usable with no signal.

**Live:** https://nadav-source.github.io/anniversary-weekend/

## What it does

- **Sections for shopping, packing, and timed tasks**, with a day-by-day plan
  and a menu card for each meal being cooked.
- **Three filters** — `לקנות` / `לארוז` / `משימות`. In a supermarket, tap
  *לקנות* and the list collapses to only what still needs buying.
- **Hide-checked toggle**, so the list shrinks as you work through it.
- **Owner pills** — tap the circle at the end of a row to cycle
  both → נדב → טוני. Clothes sections come pre-assigned.
- **Add, rename, or delete any row**, built-in ones included. Deleting shows an
  undo; *לאפס הכל* restores everything.
- **Shared across devices on one permanent link.** Both phones read and write a
  single anonymous JSON blob; the dot next to the progress bar is the sync lamp
  (grey idle, amber working, green clean, red offline) and is tappable to force
  a sync.
- **Works offline.** Fonts are bundled, a service worker caches the shell, and
  localStorage is the local source of truth — edits made with no signal are
  held and pushed when the connection returns.
- *עותק גיבוי* still encodes the entire state into a URL, as a restore point.
- **Prints** to a clean sheet if you'd rather carry paper.

## How syncing works, and what it costs

State lives in one anonymous record on textdb.dev — no account, no API key.
The trade is that **anyone who reads this page's source can also read or
overwrite the list.** Acceptable for a packing list, not for anything else.
If it ever gets clobbered, *עותק גיבוי* restores from a link.

**The key is ours and fixed, and that is not incidental.** This first ran on
jsonblob, which issues the id itself; it deleted the record two days later and
every write then 404'd against an id no client could recreate — the list simply
stopped syncing while each device quietly held its own edits. With a key we
choose, a purge reads back empty and the next write restores it at the same
address. `readRemote()` therefore treats 404, empty and unparseable responses
as "nothing shared yet" rather than as errors. Do not move to a store that
assigns the id.

Merging is **per row, newest write wins** — not per document — so two phones
editing different rows never clobber each other. Every row carries a timestamp
in `st.ts`; `mergeDoc` only adopts a remote row whose timestamp beats the local
one, and a row with no local timestamp is always adopted.

Two failure modes are handled explicitly, both learned the hard way:

- The host answers **rate limiting as HTTP 200 with an `error` body**. Taken at
  face value that looks like a successful write, so `guard()` rejects any body
  carrying `.error`, and `dirty` is cleared only after a confirmed write.
- A **failed push must not be dropped.** `dirty` stays set through failures and
  any later sync flushes it; rate limiting sets `backoffUntil`, which defers
  work rather than discarding it.

Under heavy use sync can lag by up to a minute while backing off. Nothing is
lost — it just arrives late. Moving to a Cloudflare Worker or Apps Script
endpoint would remove that ceiling.

## Structure

```
index.html              the entire app — markup, styles, logic, data
fonts/                  Heebo + Playfair Display, subset to what's used
sw.js                   offline shell: network-first HTML, cache-first assets
manifest.webmanifest    installable to a phone home screen
icon.svg
```

Edit the `SECTIONS` array near the top of the `<script>` block to change items.

## Two things to know before editing

**Item ids are positional** (`s{n}i{n}`). Reordering or inserting sections
invalidates previously shared links. The decoder length-checks and refuses a
stale link rather than applying it to the wrong rows — keep that guard.

**The share format is versioned** (`#v2`). Each built-in row is one base64 char
packing `deleted(6) + checked(3) + owner(0..2)`; renames, added items and notes
ride along as JSON after a dot. Changing that packing means bumping the version
so old links are refused instead of misread.
