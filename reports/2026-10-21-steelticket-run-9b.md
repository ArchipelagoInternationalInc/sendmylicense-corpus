# 2026-10-21 — Steelticket, run 9B: the photos

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commits:**

- 45e2492: the photos, plus one fix.
- 628bfa5: the handoff.

Nothing merged. Nothing touched the live site, the live database, Vercel or
Doppler. There is no tracking and no outside script.

The run began late on 21 October by the owner's clock. It is filed under that
date, as asked.

## How the photos got here

- **The image host was still refused** by the session's network policy. The
  owner's photo folder is on the owner's own computer, which a cloud session
  cannot see.
- **So the owner uploaded the four PNGs** to the branch on GitHub, under the
  studio identity.
- **They are the same four images** the script lists. Each file name's ending
  matches one of the script's four image addresses, so each was given its slot
  name: hero-folder, site-crane, site-concrete, site-steel.

## What the script wrote

`node scripts/marketing-photos.mjs <folder>`, run once and committed as is.

| Photo | File written | Bytes (desktop) | Lightest point | Overlay | Light text at that point |
|---|---|---|---|---|---|
| Crane (hero) | 2400 × 1350, plus 1200 wide | 68,848 | rgb(222, 215, 202) | 70% | **5.53 : 1** |
| Concrete ("They open a link") | 2400 × 1350, plus 1200 wide | 131,006 | rgb(235, 236, 238) | 70% | **5.04 : 1** |
| Steel (closing) | 2400 × 1350, plus 1200 wide | 58,228 | rgb(252, 250, 247) | 70% | **4.73 : 1** |
| Folder (hero object) | 720 × 542, transparent, plus 360 wide | 28,344 | none (no text over it) | none | — |

- **Size:** quality 74 on the first pass. The four desktop files total
  **286 KB**, against the 1.05 MB budget.
- **Overlay:** every photo's overlay solved to the owner's 70% floor, and
  every text pairing clears 4.5 : 1 at the photo's brightest point.
- **Clean-up:** the uploaded PNGs were deleted from the branch in the same
  commit. Only the WebPs and their recorded measurements stay.
- **Picture:** `photos-as-converted.png` shows all four as converted. The
  folder sits on a test color there, so the transparency shows.

## One fix the photos exposed

The photo records file was read with a plain type cast. That only passed the
type check while every entry was empty, so with real entries the type check
failed.

- **The fix:** each entry is now checked field by field. A malformed entry
  counts as missing, so the page shows the slot instead of breaking.
- **New test:** every entry the script writes must be well formed.
- **Updated run-8 test:** it used to require that the folder photo was
  *absent*. It now checks both states: the photo when it is in place, the
  drawing when it is not.

## Checks, with the photos in place

**Lighthouse, phone settings:**

| Page | Performance | Accessibility | Best practices |
|---|---|---|---|
| Home (three runs) | 93, 95, 95 | 100, 100, 100 | 100 |
| How it works | 95 | 100 | 100 |
| Features | 96 | 100 | 100 |

- **Largest paint:** the crane photo itself, at about 2.9 to 3.1 seconds on
  Lighthouse's slow-phone setting.
- **Layout shift:** 0.
- **SEO:** reads 63 only because the pages still ask not to be indexed, as
  before.

**Page weight, measured in a browser after scrolling the whole page:**

| Page | 1280 | 1280, retina | 390 phone |
|---|---|---|---|
| Home | 585 KB | 603 KB | 457 KB |
| How it works | 581 KB | 598 KB | 452 KB |
| Features | 451 KB | 469 KB | 393 KB |

All well under the owner's 1.5 MB. Phones get the 1200-wide copies; desktops
get the 2400-wide ones.

**Other checks:**

- **Console and network:** zero errors on all five marketing pages, at 1280
  and at 390, with and without reduced motion. Every request stayed on the
  site itself.
- **Reduced motion:** nothing runs (0 running animations). The hero shows its
  finished state.
- **Keyboard:** the skip link is first, and every link, field and button shows
  a focus ring. The phone menu opens with Enter.
- **Tests:**
  - unit tests: 1082 pass, 0 fail;
  - database checks (local only): 339 pass;
  - type check, lint, production build and the verified-file audit are all
    clean.

## Screenshots

**What they show.** These are all the run-9 screenshots again, now with the
photos in place:

- **Full pages:** `home-1280.png`, `home-390.png`, `how-it-works-1280.png`,
  `how-it-works-390.png`, `features-1280.png`, `features-390.png`.
- **Reduced motion:** `hero-390-reduced-motion.png`.
- **The loop:** `hero-loop.gif`, one 8-second loop at 10 frames a second. It
  is framed around the laptop, the folder photo and the phone.
- **The app:** `app-receipt-downloaded-1280.png` is an app receipt with the
  DOWNLOADED green. The app was not changed in this run. The test account's
  recipient line is cropped off.

**One correction on the way.** The first full-page captures showed the
concrete and steel sections without their photos. Those two photos load only
as the visitor scrolls to them. The capture script scrolled too fast for the
browser to start loading them. In a real browser they appear as soon as the
section comes into view; this was checked by scrolling to each one. The
captures above wait for every image before they are taken.

## For the owner to know

- **The backgrounds are smaller than their slots.** The three arrived at
  1344 × 752, and the script scales them up to the 2400 × 1350 slot. They look
  right at 1280. On a large or retina screen a sharper original would show
  more detail. If larger originals exist, re-running the script on them is the
  whole change.
- **Small extra downloads, left alone:**
  - The inner pages also fetch the crane photo and the folder (about 55 KB),
    though neither appears on them. The likely cause is the site prefetching
    the home page; I did not confirm it.
  - Phones download the folder photo (28 KB) even though it is hidden there.
  - Both are well inside the budget, so I left them. Each is a one-line
    change if wanted.

## Left over

- **The words:** the owner reads DRAFT 2 on the preview and approves it, or
  sends changes.
- **Two screen-reader words:** "Skip to content" and "Menu", still pending.
- **The download wording:** the "single PDF" line on How it works against the
  app's own download hint, from run 9.
