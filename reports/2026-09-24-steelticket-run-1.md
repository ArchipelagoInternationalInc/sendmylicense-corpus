# 2026-09-24 — Steelticket, run 1: answers, two fixes, and the new look

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me (stated at the start of
the run, and every commit carries it)
**Branch:** the contractor branch. **Nothing merged. Nothing reached the live
site or the live database.**
**Run type:** overnight, nobody watching. Blocked steps were written down and
skipped, not worked around.

## In one paragraph

Six of the seven steps finished. The one that did not — sending a real test
email — was blocked by this environment's network policy, which refuses the
mail provider's address, and it was skipped. The app now has the Steelticket
look on its own screens and on the page a general contractor opens; the two
decisions recorded on 17 September are built and tested; W-9s and other
undated documents that never expire say "On file"; the question of what counts
as proof a packet arrived is answered; the old name is inventoried; and the
wording-approval list is in the repository as a text file — with 356 items, not
377, for a reason explained below.

## How it was tested without touching anything live

A complete, throwaway copy of the database stack was started inside the
container, from public images, with every migration applied. The app was built
and run against it, signed into through the real sign-in form with a fixture
account on an example.com address, and photographed. Nothing in the run spoke
to the live project. The mail key was withheld from the app process during the
screenshots, so nothing a screenshot touched could send mail.

## Step by step

### 1. What counts as proof a packet arrived — FINISHED (answer only)

Written up in full, with the file and line for each claim, at
`docs/PROOF_OF_ARRIVAL.md` in the build repository. Nothing was built.

- **The time it was sent — YES.** Stamped only after the mail provider accepts
  the message. On both ways of sending.
- **A "delivered" event from the mail service — NO.**
  - *Wired up:* partly. The endpoint exists, and the provider has one webhook
    pointed at the production site — but it is subscribed to **bounces only**.
    The "delivered" event is never sent to the app.
  - *Verified:* yes, in code — signatures are checked, and a missing secret
    refuses everything. Whether the secret is actually set on the live
    deployment could not be established from here.
  - *Stored:* no. The code deliberately drops "delivered". Only bounces are
    stored, and — a gap found this run — only for the older single-send path.
    The main path, sending to your book, never keeps the provider's message id,
    so even a bounce cannot be matched to it.
- **Whether the recipient opened the link, and when — YES**, for packets sent
  to your book: the first time the page showed them their packet. Not for the
  older single send, which is a direct file link.
- **Other evidence:** when they downloaded it and exactly which version of each
  document they received; anything they asked for, and when it was fulfilled.
  One gap: the main send path writes no row in the audit log for the send
  itself. Reported, not fixed.

Open and click tracking are off at the provider and refused in the code. Not
changed. **What the home page can truthfully promise:** when it was sent, when
it was first opened, when it was downloaded and which versions. Not that it
reached an inbox.

### 2. The two recorded decisions — FINISHED

- **The receiving page's third state.** When the database cannot be read, the
  page now says, exactly: *"We can't check this link right now. Please try
  again in a few minutes."* It is its own state; the existing "This link isn't
  available" is unchanged and still covers only spent, wrong and expired links.
  Proven for real by stopping the local database gateway and opening a good
  link — and a wrong link during the outage gets the same sentence, so nothing
  is disclosed. Its tests fail on the old code and pass on the new.
- One related fix: the "opened" record used to be written before the page had
  finished reading, so a reader shown an error could still be recorded as
  having opened the packet. It is now written only when the packet is actually
  shown.
- **Company name in Settings.** A real field, saved through the real form in
  the screenshots; the receiving page then leads with the company name.

### 3. "On file" — FINISHED

A document with no date, of a type that never carries one (a W-9, a reference
letter, a capability statement), now shows "On file" — on the dashboard, on the
document's own page, and on the receiving page. Two notes:

- Today's labels were not quite what the brief assumed. A W-9 used to read "No
  date entered", not "Current", and dated documents read "Current", "Renews
  soon" and "Date passed" rather than "Current" and "Expired". All of those are
  kept exactly as they were.
- A document that normally has a date but has none entered — an insurance
  certificate, say — still reads "No date entered", because "On file" would
  hide a gap the holder needs to see. That is a judgement call; it is one line
  to change if the owner wants it wider.

### 4. A real test send to the owner — SKIPPED (blocked)

The mail key and the sender address are now present in the environment, but
the environment's network policy refuses connections to the mail provider (the
request is rejected at the proxy). There is also no database for the app's own
send path to record into, other than the throwaway local one. So there is
nothing to report about what the app recorded or what arrived. **Nothing was
sent.** To unblock: allow the mail provider's address in the environment's
network settings.

### 5. The new look — FINISHED

Applied to the app's screens and the receiving page. The marketing site is
untouched and keeps its look until its own run. No wording changed.

- Brass for accents, buttons and borders; gunmetal for the header; paper for
  every reading area; dark text on every light background.
- Main headings in Special Gothic Expanded One, labels and status chips in
  Karantina, body text unchanged.
- A small notched corner on buttons, document cards and status labels.
- **No colour needed adjusting.** The owner's three values pass as given. The
  arithmetic did set two rules: brass is too light to be read as text on paper
  (2.04:1), so it is never used as text there, never as a focus ring, and brass
  buttons carry a slightly darker brass edge. Every pairing is checked by the
  build.
- The 44-pixel tap-target floor holds, measured on the rendered pages, not just
  in the stylesheet. That check found one old problem — standalone "back" and
  "cancel" links were 17 pixels tall — and fixed it.
- Two calls flagged for the owner: delete buttons were red, and are now marked
  by a heavy dark edge instead, because red is for status only; error messages
  are still red.

**Screenshots** are filed beside this report, in
`reports/2026-09-24-steelticket-run-1/`: before and after of the dashboard, a
document's page (the W-9), the send screen and the receiving page, each at
desktop and phone width; plus the Company name field saved, the receiving page
with a company name, the new "can't check" state, sign-in, and reduced-motion
captures. The only browser console error through the whole cycle is a missing
site icon, which was already on the recorded-only list.

### 6. Every place the old name appears — FINISHED (listed, not changed)

`docs/OLD_NAME_INVENTORY.md` in the build repository. **104 places a person
reads the name**, in source, grouped by where they meet it — 30 on the
marketing site, 24 browser-tab titles on app screens, 18 in the legal pages, 12
in files people download (the cover sheet, the export, the calendar feed), 10
in emails, 3 in the app header, 3 elsewhere on app screens, 2 in the standing
disclaimer, 1 on the receiving page, 1 in the security-incident email
template. Outside the source: the sender address and the verified sending
domain both carry the old name, so a new sending domain will need verifying
before any mail can come from the new one. One thing to plan for: the email
templates and the cover sheet are checksummed, verified files, so the rename
has to update their master copies in the same change.

### 7. The wording-approval list — FINISHED, with a different count

**It is at `docs/WORDING_APPROVAL_LIST.txt` on the contractor branch of the
build repository.** Plain text, flagged items first, cover letters in full.

It has **356 items, not 377.** The 17 September list was built by hand in
another session and handed over in the chat; no copy of it was ever saved, so
it could not simply be reprinted. It was regenerated from the same point in
the branch by a script that is now in the repository, so it can be printed
again at any time. Without the old copy the two cannot be compared line by
line, so the file ends with an appendix that prints, word for word, every piece
of text the branch added that the new list does not count as wording — route
addresses, internal labels, email markup, punctuation. Anything on the old list
and not the new one will be there. The four strings added by this run are
listed separately at the end; two of them are the owner's own wording.

## Checks

789 tests pass, including new ones for every change. Type check clean, lint
clean. The verified-files audit is clean: no checksummed file was touched, the
database security suite passes on its own throwaway database, and the
production build passes.

## Not done, and why

- The real test send (step 4) — blocked by the network policy, as above.
- The audit-row gap and the message-id gap found in step 1 — reported only,
  because step 1 was answer-only.
- No merge, no deployment, nothing applied to the live database.
