# 2026-09-25 — Steelticket, run 4: the letter, helper access, stopping links, deletion

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me (stated at the start of
the run; every commit carries it)
**Branch:** the contractor branch. **Nothing merged. Nothing reached the live
site or the live database.** The Terms of Service and the Privacy Policy were
not touched.
**Run type:** overnight, nobody watching. A blocked step was written down and
skipped, not worked around.

## In one paragraph

All eight steps are finished. The logo was blocked for most of the night,
because the five files had not reached the session. Late in the run the owner
committed them to the branch, and the logo is now in place everywhere it was
asked for. The bid-list letter now has proper
paragraphs. A helper now sees only what the invitation promises. Every link
sent from now on can be stopped, and a stopped link shows the recipient the
same "This link isn't available" page they would already see for an expired
one. Deleting an account, a credential or a course now removes the stored
files too. Receipt times show in the reader's own time zone. Settings says
"Trade contractor" for contractor accounts. The receiving page now checks the
sender's account on every document it reads.

## How it was tested without touching anything live

The same throwaway local copy of the database as runs 1 to 3, with this run's
two database changes applied to it. The app was built and run against that
copy, and the screenshots come from it. No email was sent in this run.

## Step by step

### 1. Logo — DONE (late in the run)

For most of the night this step was blocked, because the five files had not
reached the session. Late in the run the owner committed them to the branch and
asked for them to be used. They now live in `public/brand/`, with the icon
renamed to `steelticket-icon.svg`. **Nothing was redrawn or recoloured.** Every
copy made from them is checked by a test against the file it came from.

- **App header:** the on-dark logo. A screen reader hears its name as
  "Steelticket.".
- **Sign-in and first-run screens:** these sit on the light paper background,
  so they use the on-light logo.
- **Receiving page:** the on-light logo sits above the independence line, in
  every state of the page, including "This link isn't available".
- **Cover sheet:** the on-light logo, drawn as sharp vector lines taken
  straight from the file's own shapes and colours. The cover sheet is a
  checksummed file, so its master copy and both checksum lists were updated.
- **Email:** the on-light logo as a PNG at the top of every email. It travels
  *inside* the message as an attached image, not as a picture loaded from our
  website. A picture loaded from our website would tell us when someone
  opened the email, and the product promises no tracking beyond the
  recipient following their own link. The existing test that guards that
  promise now allows exactly this one attached image and still refuses any
  image that would be loaded from elsewhere. **This has not yet been tried
  with the real mail service.** The first real email should be checked in an
  inbox.
- **Browser tab icons:** 16, 32, 48, 180, 192 and 512 pixels, plus a
  favicon.ico holding the three small sizes, all made from the icon file. They
  apply to the app, sign-in, first-run and receiving pages. The marketing site
  keeps its own until its run. **The missing-icon console error is gone:** zero
  console errors and zero failed requests across all the screenshot passes.
- **The two S marks** are stored for the marketing site. Nothing in the app
  uses them.
- **Export:** the export is a ZIP of data files and a plain-text read-me. There
  is no page in it where a logo could go, so it has none.

**One thing for the owner to look at:** the icon's dark background shows as
solid black. In the file, those shapes have no colour set, and a missing colour
draws as black. It was left exactly as drawn.

### 2. The "Getting on a bid list" letter — DONE

The letter now reads as a greeting, three body paragraphs and a sign-off, with a
blank line between each. The name and the company are on their own lines under
"Thank you for your time,". The wording itself did not change. A test pins the
five blocks and the blank lines.

### 3. Helper access matches the invitation — DONE

The invitation says a helper sees a document only if it is ticked for them, one
at a time. Before this run, that was true for documents but not for five other
things. It is now true for all five:

- **Package download.** A helper can download one of the holder's packages only
  if they are allowed to assemble or send, **and** every document in that
  package was shared with them. Otherwise it is refused.
- **Credential notes.** Notes moved into their own table that only the account
  holder can read. A helper who can see a credential no longer sees its notes.
  Notes that already existed were moved over automatically.
- **Delivery log and recipient details.** A helper sees these only if they have
  permission to send.
- **Reminder copies.** A helper's copy of a reminder now covers only credentials
  that have a document shared with them. Before this run, they got a copy for
  every credential.
- **Invitations expire 14 days after they are sent.** An older invitation gets
  the same answer as one that was already used. The app checks this, and so
  does the database.

Ten new tests drive the real download route, reminder loader and acceptance
step. Six of them fail on the old code. Twelve new database checks cover the
notes, the log and the expiry, and each denial comes with a control case that
must be allowed.

### 4. Every sent link can be stopped — DONE, with one limit

- **Single send** now uses the same receiving-page link as a book send. The
  form still takes one email address. It now gets the same receipt: sent,
  opened, downloaded, bounced, stopped.
- **Every receipt** has a "Stop this link" button for anyone allowed to send.
  Clicking it asks, word for word: *"Stop this link? They won't be able to open
  it again. This can't be undone."*
- **After stopping,** the receipt shows "Link stopped" with the date and time,
  and the button is gone.
- **The recipient** sees the existing "This link isn't available" page. Nothing
  new is said to them. Downloads and the request form refuse a stopped link in
  the same way.
- **An audit row** (`packet.link_stopped`) is written before the link is stopped.
  If that row cannot be written, the link is not stopped.
- **A stopped link stays stopped.** The database refuses to un-stop one, even
  for the server's own key. A link stopped before it went out is never sent.

This was measured end to end on the local copy, at desktop width, at phone
width, and at phone width with reduced motion. In each pass the recipient's
link opened first. Then the real button and the real confirmation stopped it.
The receipt then read "Link stopped". The recipient then got the
not-available page. One audit row was written each time.

**The limit (as instructed, not attempted):** single-send links that were
already sent before this change **cannot be stopped**. They were direct,
time-limited download addresses, not receiving-page links, so the app has
nothing to switch off. They run out on their own when they expire.

**One change to flag for the owner:** sending the same package to the same
address twice by single send is now refused, with the existing "already
scheduled" wording. Before this run it went through. The owner may want
different wording for this case.

### 5. Deletion removes what it says it removes — DONE

- **Deleting an account** now also removes the logo from storage. Before this
  run the logo stayed behind.
- **Deleting a credential or a course** now also removes its stored files. The
  app reads where the files are before deleting, deletes the record, and only
  then removes the files. If the delete is refused, no file is lost. It never
  touches anything outside the account's own folder.

Seven new tests drive the real deletion route and actions. Three of them fail
on the old code.

**What still stays behind after a deletion (reported, not changed):**

- **Package ZIPs.** A package made before a credential was deleted still holds a
  copy of that credential's files in its ZIP. There is no way to delete a single
  package in the app. The ZIP goes only when the whole account is deleted.
- **Uploads that were never finished.** If someone starts an upload and never
  completes it, that file stays in storage until the account is deleted.
- **Send history and the audit log.** These are kept by design and cannot be
  edited. Send history goes with the account. The audit log keeps one row that
  records the deletion itself, holding counts only.
- **Outside this app:** the mail service's own records of messages sent, and any
  copy a recipient already downloaded.

### 6. Receipt times in local time — DONE

Receipt times now show in the reader's own time zone, labelled with it, for
example "Sep 25, 2026, 10:35 AM CDT". Before the browser has worked out the
zone, or with scripts turned off, the time shows in UTC and says UTC, so it is
never unlabelled. The screenshots were taken with the browser set to US
Central time.

### 7. Settings profession line — DONE

Contractor accounts now show **"Trade contractor"** (the PM-approved wording).
Clinician accounts still show "Respiratory Therapist". Before this run, the line
showed the raw stored word "contractor".

### 8. Receiving page lookup — DONE

When the receiving page reads the packet, its documents and their credentials,
it now also checks that each one belongs to the sender's account, the same way
the download already did. This is an extra layer of protection: nothing was
known to be leaking. Three new tests fail on the old code.

## Checks at the end of the run

- Unit tests: **949 pass**, 0 fail. That includes 14 new logo tests.
- Database suite (local copy only): **passes**, including this run's new
  checks.
- Type check: clean. Lint: clean.
- Verified-file audit: **clean**. Production build: **passes**.
- Console errors across the screenshot passes: **zero**, now that the tab icon
  is in (step 1).

## Screenshots

In `2026-09-25-steelticket-run-4/`, each at 1280 px, 390 px, and 390 px with
reduced motion:

- `receipt-stop-button-*`: receipts with "Stop this link".
- `receipt-link-stopped-*`: a receipt reading "Link stopped" with its local time.
- `recipient-after-stop-*`: what the recipient sees afterwards (the existing
  page).
- `cover-letter-*`: the corrected bid-list letter on the send screen.
- `settings-trade-contractor-*`: the profession line on a contractor account.

The logo, in files starting `logo-`:

- `logo-header-*`: the app header, on desktop and phone.
- `logo-sign-in-*`: the sign-in screen.
- `logo-receiving-*` and `logo-receiving-unavailable-*`: the receiving page,
  both working and unavailable.
- `logo-cover-sheet.png`: a cover sheet made by the real code, then drawn as
  an image.
- `logo-email-delivery.png`: the email a general contractor receives. For the
  preview only, the attached logo is shown from the same bytes, because a
  browser cannot read an email attachment.
- `logo-tab-icon.png`: the tab icons. A headless browser has no tab bar to
  photograph, so this is a drawn stand-in tab bar using the real icon files
  the app serves, with each size shown enlarged beneath it.

One thing in the receipt screenshots comes from the test setup, not the app.
The receipts that were stopped were created directly in the local copy, so the
"Sent from this package" list above them says nothing has been sent.

## What the owner needs to decide or supply

1. Look at the first real email in an inbox, to confirm the attached logo
   shows. Also decide whether the icon's black background is intended.
2. Wording for a repeated single send to the same address (step 4), if the
   existing "already scheduled" wording is not right.
3. Whether a package, and its ZIP, should be deletable on its own (step 5).
4. When this branch ships: this run's two database changes go to the live
   project with the earlier unapplied ones, in order.
