# 2026-10-21 — Steelticket, run 8: the marketing site

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commit:** f8ce38e.
Nothing merged. Nothing touched the live site, the live database, Vercel or
Doppler. There is no tracking, no analytics and no outside script.

- **One host:** the only address any page contacted in testing was the site's
  own.

## What was built

The old marketing site is gone. The new one has three pages:

- **Pages:** home, How it works, and Features.
- **Shared parts:** the top bar, the footer, the browser tab title and the
  search-result description.
- **Words:** everything uses the owner's words in the owner's order, with
  two kinds of exception:
  - the expanded paragraphs listed below;
  - two words that only screen readers hear.
- **Exact text:** a test pins every approved sentence character for
  character, curly apostrophes included.
- **Terms and Privacy:** their wording is unchanged. They now sit on the
  site's paper style.
- **Sign in and Get started:** they go to the existing sign-in and sign-up
  pages.

**What was removed with the old site:**

- **Pages:** the Pricing, Security and FAQ pages.
- **Motion:** the old motion code and its animation library. The new site's
  only motion is the hero loop, written in plain CSS.
- **Clinician wording:** a test now fails if any of the run-7 clinician words
  appear, or the old name, a price, or "free".
- **The newsletter sign-up form:** it is not among the approved words, so it
  is not on the new site.
  - Its code is kept but unused, so it can come back by decision.
  - The Privacy Policy still describes the newsletter, because Privacy wasn't
    to change.

## The look

- **Colors.**
  - Gunmetal `#2A2E33` for dark sections and paper `#F4F1EA` for reading
    sections.
  - Brass `#D9A21B` appears only on dark.
  - On paper, text is gunmetal. The darker brass `#B8860B` is only a line
    accent there, never text.
  - Green `#1C6B3A` and red `#9E1D22` appear only as status badges.
- **Fonts.**
  - Special Gothic Expanded One in capitals for headlines, Karantina for the
    small labels, and Hanken Grotesk for body text.
  - The font files are served from the site's own address; a visitor's
    browser never contacts Google. The test run showed no request to any other
    address.
- **Shapes.** The 6 px ticket notch on buttons, cards and badges.
- **Textures.** Brushed steel on the dark sections and paper grain on the
  paper ones. Both are drawn in code from fine lines and generated noise, and
  are kept faint.
- **Watermark.** The big S, cropped off the edge at 5.5% opacity, on three
  sections: the hero, the packet list and the closing.
- **Logo.** The dark wordmark in the top bar. In the footer it is oversized
  and cropped off the bottom edge.
- **Icons.** Thin single-weight line icons, drawn in code:
  - a document;
  - people;
  - a paper plane;
  - a stamp for "Keep the record";
  - a group for "Let someone else handle it".
- **Hero.**
  - A laptop showing the Packets page, with a phone over its lower corner
    showing a receipt. Both are the real app, rebuilt as page elements, with
    the app's own words.
  - Where the app would show someone's data (an address, a company name, a
    date), the picture shows a plain gray bar instead of inventing one.
  - The loop: the first packet is picked, a paper plane carries it to the
    phone, then SENT, OPENED and DOWNLOADED arrive one by one, and it resets.
    One loop takes about 8 seconds.
  - Reduced motion: no animation at all. The receipt is shown finished.
- **The folder.** The spot for the owner's photographed folder with a brass
  tag is reserved beside the laptop. A flat folder and tag is drawn in code
  for now.
  - To swap in the photo, save it as `public/marketing/hero-folder.webp` at
    720 × 600 with a transparent background.
  - The next build uses it with no code change.
- **"They open a link".** The receiving page is rebuilt the same way, in a
  browser frame.
- **Phone layout.**
  - The menu folds into a button; the wordmark and Get started stay in view.
  - The hero shows the label, headline, sub and button, then the phone frame
    alone. The laptop and folder are hidden.
  - Everything else is one column with 20 px side margins and full-width
    buttons.

## Checks

**Lighthouse, phone settings:**

| Page | Performance | Accessibility | Best practices |
|---|---|---|---|
| Home (three runs) | 96, 95, 98 | 100, 100, 100 | 100 |
| How it works | 96 | 100 | 100 |
| Features | 96 | 100 | 100 |

- **Home timings:** first paint 0.9 s, largest paint 2.1–2.8 s, layout shift
  0.
- **SEO reads 63 on every page, for one reason only:** the pages still tell
  search engines not to index them. That setting stays until the owner lifts
  it (DECISION-007).
- **A fix during the run.** Lighthouse once scored the home page 96 for
  accessibility. It had caught a badge halfway through fading in, when its
  text was briefly faint. The badges now appear at full strength instead of
  fading, so their text clears 4.5 : 1 in every frame. Three runs after the
  fix all scored 100.

**Contrast.** Every text color pairing clears 4.5 : 1, and a test measures
each one. The badges are white on the app's deep green 6.53 : 1, deep red
7.92 : 1, and gray 6.43 : 1.

**Keyboard.**

- The skip link comes first.
- Every link and button shows a visible focus ring.
- The phone menu opens with Enter.
- The device pictures are hidden from screen readers and hold nothing
  focusable.

**Console:** zero errors and zero warnings, across all five pages at 1280 and
390, with and without reduced motion.

**Tests:**

- Unit tests: 1058 pass, 0 fail. That includes 22 new run-8 tests, covering:
  - the words;
  - nothing from the old site;
  - the colors;
  - scripts and fonts;
  - reduced motion.
- **Retired tests**, for code and copy that no longer exist:
  - two tests for the old closing banner;
  - four tests for the old motion code.
- Database checks (local only): all 339 pass.
- Type check, lint, verified-file audit and production build are all clean.

## [PENDING — OWNER] — words not in the approved list

Please approve or change these in one go.

**The seven expanded paragraphs.** Each is written only from facts in the app.

- **How it works — 01 Load your documents:** Upload each document as a PDF, PNG or JPG and file it under the credential it belongs to: a license, an insurance certificate, a safety program. Enter the expiration date if it has one. A document without one is kept on file. Steelticket shows the dates you entered; it does not check the documents.

- **How it works — 02 Pick who’s asking:** Your book holds the general contractors you work with and the people at each one, added by hand or imported from a spreadsheet. Pick one person, or several at once. Send now, or let it go out on each person’s next business morning, in their time zone.

- **How it works — 03 Send the packet:** Each person gets their own link, with your cover letter. The link opens a page with your logo, your letter and your current documents. They can download everything, one PDF packet with a cover sheet plus every document, or ask you for a document that isn’t there. Your receipt for each person shows when it was sent, when the link was opened, when the documents were downloaded, and when the link expires. You can stop a link at any time.

- **Features — Keep it current:** Each credential carries the expiration date you entered. Choose how many days ahead your email reminders go out. The Next 90 days view lists what runs out soon, and a private calendar link puts your dates in Outlook or Google Calendar. Reminders are a convenience and are not guaranteed.

- **Features — Send it right:** Your book keeps each general contractor with the people you deal with there. Send a packet to one person, or to a whole list at once. Each person gets their own link, their own cover letter and their own receipt. Choose how long the link works: 7, 30 or 90 days.

- **Features — Keep the record:** Every send leaves a receipt: who it went to, when it was sent, when the link was opened, when the documents were downloaded, and when the link expires. If their email system refuses it, the receipt says so. If a link hasn’t been opened after 7 days, Steelticket tells you. Stop a link and it stops working; the receipt stays.

- **Features — Let someone else handle it:** Invite a helper and choose what they can do: put packets together, send packets to contractors, or add and edit contractors in your book. They see a document only if you tick it for them. Only you can delete a packet. Your answers are kept in one place, ready to copy into the next form.

**Two words only screen readers hear:**

- **"Skip to content":** the skip link, the first thing a keyboard user
  reaches.
- **"Menu":** the name of the phone menu button.

**Put together from approved words, flagged for completeness:**

- **Browser tab titles of the two inner pages:** "How it works — Steelticket"
  and "Features — Steelticket". Both pages carry the owner's search-result
  description.
- **Page labels:** each inner page shows its nav word ("How it works",
  "Features") as a small label above the section heading. The section heading
  is that page's main heading.
- **Inside the device pictures:**
  - all words are the app's own;
  - "10 documents" counts the owner's ten document types;
  - nothing else is invented.

## Screenshots

- **Full pages:** `home-1280.png`, `home-390.png`, `how-it-works-1280.png`,
  `how-it-works-390.png`, `features-1280.png` and `features-390.png`. In these
  the hero loop is held on its finished frame.
- **Reduced motion:** `hero-390-reduced-motion.png` shows the hero at 390 wide
  with reduced motion, still and finished.
- **The loop:** `hero-loop.gif` is the hero devices at 10 frames a second for
  one full 8-second loop.

## Left over

- **Words to approve:** the [PENDING — OWNER] list above.
- **The folder photo:** it drops in by filename, as described above.
- **Pages that were removed:** decide whether the old addresses `/pricing`,
  `/security` and `/faq` should redirect to the home page. They now show
  "not found".
- **The newsletter:** decide whether its form should come back.
- **Indexing:** lift the "don't index" setting when the site should appear in
  search.
