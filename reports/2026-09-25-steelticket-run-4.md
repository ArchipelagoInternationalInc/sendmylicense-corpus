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

Seven of the eight steps are finished. The logo was blocked again: the five
logo files did not reach this session, and they are final artwork that must not
be redrawn, so nothing was done with them. The bid-list letter now has proper
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

### 1. Logo — SKIPPED (blocked)

**Why:** the five files were said to be attached, but none reached the
session. They were not in the working folder, not on any branch, and not in
this notebook. The instructions say not to redraw or recolor anything, so there
was no honest way to go on without them.

**Still waiting on the files:** the header logo with its accessible name, the
light-surface logo (receiving page, cover sheet, export, email), the email PNG,
the tab icons in six sizes, and the two stored S marks for the marketing site.
The cover sheet was not changed, so its checksum and master copy were not
changed either.

**Screenshots not possible without the files:** the browser tab icon and the
cover sheet with the logo. The header screenshots in the folder show the
current text wordmark, for reference. The one console error left on every page
is the missing tab icon. It goes away when the icon files land.

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

- Unit tests: **935 pass**, 0 fail.
- Database suite (local copy only): **passes**, including this run's new
  checks.
- Type check: clean. Lint: 0 errors. There is one old warning in the checksummed
  cover sheet, which was left alone as a verified file.
- Verified-file audit: **clean**. Production build: **passes**.
- Console errors across the screenshot passes: **one**, the missing tab icon
  (step 1).

## Screenshots

In `2026-09-25-steelticket-run-4/`, each at 1280 px, 390 px, and 390 px with
reduced motion:

- `receipt-stop-button-*`: receipts with "Stop this link".
- `receipt-link-stopped-*`: a receipt reading "Link stopped" with its local time.
- `recipient-after-stop-*`: what the recipient sees afterwards (the existing
  page).
- `cover-letter-*`: the corrected bid-list letter on the send screen.
- `settings-trade-contractor-*`: the profession line on a contractor account.
- `header-*`: the current header, for reference. The logo is blocked.

**Not taken, because they are blocked by step 1:** the header with the logo, the
browser tab icon, and the cover sheet with the logo.

One thing in the receipt screenshots comes from the test setup, not the app.
The receipts that were stopped were created directly in the local copy, so the
"Sent from this package" list above them says nothing has been sent.

## What the owner needs to decide or supply

1. The five logo files, attached where the session can reach them.
2. Wording for a repeated single send to the same address (step 4), if the
   existing "already scheduled" wording is not right.
3. Whether a package, and its ZIP, should be deletable on its own (step 5).
4. When this branch ships: this run's two database changes go to the live
   project with the earlier unapplied ones, in order.
