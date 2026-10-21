# 2026-10-21 — Steelticket, run 9: website, second pass

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commit:** 65193ef.
Nothing merged. Nothing touched the live site, the live database, Vercel or
Doppler. There is no tracking and no outside script.

## The one thing that did not happen: the photographs

**The four images could not be downloaded.** This session's network policy
refused the image host: the connection was refused before any file was sent.
The proxy's rule is to report a refusal, not retry it or work around it, so
the photos are **not on the page yet**.

**Everything else is built to take them with no code change.**

**To bring them in:**

1. Allow the image host in this environment's network settings, or put the
   four PNGs in a folder.
2. Run `node scripts/marketing-photos.mjs` (or
   `node scripts/marketing-photos.mjs <folder>`). It:
   - converts each image to WebP, plus a smaller copy for phones;
   - measures each photo's **lightest point**;
   - keeps the four under a 1.05 MB budget, so the home page stays under the
     owner's 1.5 MB.
3. Commit what the script wrote. The tests check that the recorded
   measurements match the files.

**How the page uses the photos:**

- **The overlay is solved, not styled.** The page computes each photo's
  gunmetal overlay from that photo's measured lightest point: the lightest
  overlay at which all text clears 4.5:1. It is never lighter than the
  owner's 70%.
- **Worst case:** if the lightest point were pure white, 70% already gives
  4.63:1 for the light text color.
- **Brass is never over a photo:** the hero label sits on its own small
  gunmetal plate.
- **Until a photo lands,** each photo section keeps its brushed-steel texture
  and shows, in small letters, the file it is waiting for and its export
  size. That is the studio's rule for image slots.
- **The folder** keeps its code-drawn stand-in until its photo arrives.
- **Test run:** I ran the pipeline once with plain test images to prove it
  works end to end, then removed them. Nothing synthetic was committed or
  photographed for this report.

## Part A — the words (DRAFT 2)

- **Every word** on home, How it works and Features is now the owner's second
  draft, exactly as given.
- **The words file** is marked **"DRAFT 2 — awaiting owner approval"**.
- **The exact-text test** pins DRAFT 2 character for character.
- **The inner pages** show each step's or feature's title with its long
  paragraph.
- **The reminders line** ("Reminders are a convenience and are not
  guaranteed") is gone from the marketing pages. It stays in the app's
  disclaimer.
- **Search-result description:** the new wording, on every marketing page.

**One thing for the owner to check.** Step 03 on How it works says the GC can
"download the complete packet as a single PDF with a cover sheet". The app's
own receiving page describes the download as "One PDF packet with a cover
sheet, plus every document". Both are the owner's to choose; worth a glance.

**Still marked [PENDING — OWNER]** (from run 8, unchanged): "Skip to content"
and "Menu", words only screen readers hear.

## Part B — the look

1. **Hero.**
   - The headline fills the left half, one size larger.
   - The label sits on a gunmetal plate.
   - On the right, the laptop with the Packets page. The folder with the
     brass tag stands in front of the laptop's lower-left corner, with its own
     soft shadow.
   - The phone has moved down. It now hangs over the hero's bottom edge into
     the paper section and **does not cover the laptop's buttons**.
   - The loop is unchanged: about 8 seconds, and fully off for reduced
     motion.
   - The crane photo slot is behind it.
2. **Scale.**
   - Section headlines are one size up.
   - The step numbers are 96 px Karantina figures in brass-hue `#8C6811`.
     That is the brightest shade of the brass hue that still reads as text on
     paper: 4.54 : 1 on paper and 4.86 : 1 on the cards.
   - True brass `#D9A21B` would be 2.19 : 1 there, and `#B8860B` 3.09 : 1, so
     neither can be text on paper.
   - Paper sections now have 144 px between them.
3. **"They open a link."** The concrete photo slot is behind it, and the
   receiving-page frame now takes about 55% of the width.
4. **Closing.** The steel photo slot is behind it, with the headline centered
   and larger.
5. **Textures and watermark.** The textures stay wherever there is no photo.
   The S watermark is now on the packet section only.
6. **DOWNLOADED badge:** `#2A873D`, in the app and on the site. White letters
   on it are **4.53 : 1**, and that is the brightest green that clears 4.5.
   - The suggested `#28803A` is 4.95 : 1, a little darker.
   - SENT and OPENED stay `#1C6B3A` (6.53 : 1).
7. **Newsletter.**
   - The footer sign-up is back, with the live site's wording and the product
     name changed to Steelticket. It has the unticked consent checkbox and
     uses the same Brevo list.
   - **What it sends:** the address, the list, and the time of sign-up
     (`OPT_IN_AT`). Nothing else about the visitor is sent or logged.
   - **Consent:** the server refuses a sign-up without the ticked box. The
     consent itself isn't stored separately; the sign-up time stands for when
     it was given.
   - **Tested end to end locally:**
     - without the box, the form asks for it;
     - with the box ticked, it reaches the server, which (with no Brevo key
       locally) shows "We couldn't sign you up just now".
8. **Old addresses:** `/pricing`, `/security` and `/faq` now send visitors to
   the home page (permanent redirect).
9. **Phones.**
   - The photos stay.
   - The folder and laptop are hidden.
   - The phone frame sits below the hero text.

## Checks

**Lighthouse, phone settings, without the photos:**

| Page | Performance | Accessibility | Best practices |
|---|---|---|---|
| Home (three runs) | 98, 98, 99 | 100, 100, 100 | 100 |
| How it works | 99 | 100 | 100 |
| Features | 96 | 100 | 100 |

- **Page weight:** home is 299 KB without the photos.
- **With the photos:** the four photos are capped at 1.05 MB together, so the
  page stays under 1.5 MB.
- **What must be re-measured:** Lighthouse once the photos are in.
- **SEO** reads 63 only because the pages still ask not to be indexed
  (DECISION-007).

**Contrast:** every text pairing is measured in tests at 4.5 : 1 or better.
That covers:

- the new sign-up form: the placeholder at 5.40 : 1 and the error text at
  8.05 : 1;
- both badge greens;
- the brass figures;
- the photo overlay at its worst case.

**Keyboard:** the skip link comes first, and every link, field and button
shows a focus ring, including the new form. The phone menu opens with Enter.

**Console and network:** zero errors on all five pages, at 1280, and at 390
both with and without reduced motion. Every request stayed on the site
itself.

**Tests:**

- Unit tests: 1081 pass, 0 fail. That includes 16 new run-9 tests and the
  DRAFT 2 word pins.
- Database checks (local only): all 339 pass.
- Type check, lint, verified-file audit and production build are all clean.

## Screenshots

**What they show.** The photo sections show their texture and file-name slot,
because the photos are not in yet.

- **Full pages:**
  - `home-1280.png` and `home-390.png`;
  - `how-it-works-1280.png` and `how-it-works-390.png`;
  - `features-1280.png` and `features-390.png`.
- **Reduced motion:** `hero-390-reduced-motion.png` shows the hero at 390
  wide, still and finished.
- **The loop:** `hero-loop.gif`, one 8-second loop at 10 frames a second,
  framed around the laptop, folder and phone.
- **The app:** `app-receipt-downloaded-1280.png` is an app receipt with the
  new DOWNLOADED green.

## Left over

- **The photos:** allow the image host, or supply the files, then run the
  script. After that, re-run Lighthouse and the screenshots with the photos
  in place.
- **The words:** the owner reads DRAFT 2 on the preview and approves it, or
  sends changes.
- **Two screen-reader words:** "Skip to content" and "Menu", still pending.
