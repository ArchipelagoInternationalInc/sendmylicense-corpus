# 2026-09-26 — Steelticket, run 5: deleting packages, cleaning up uploads, sending again

**GitHub account:** ArchipelagoInternationalInc (checked before starting)
**Signing identity:** ArchipelagoInternational@proton.me (checked before starting;
every commit carries it)
**Branch:** the contractor branch. **Nothing merged.** Nothing touched the live
site, the live database, Vercel or Doppler. Email open and click tracking stay
off. Nothing in this run adds any tracking.

Screenshots are in `2026-09-26-steelticket-run-5/`, next to this file.

## In one paragraph

Four of the five building steps are done. Packages can now be deleted on their
own. Unfinished uploads are removed automatically after 24 hours. A second send
to the same address now offers "Stop that link" instead of simply refusing.
The "Sent from this package" list is gone, and everything it showed is now on
the receipts. The test email from the new sending address could not be sent:
the mail service refused the key for that domain. As instructed, the email step
stopped there. The exact error is below.

## What changed

### 1. Packages can be deleted on their own — DONE

- **The button.** A package's page has a "Delete package" button. It first
  asks, word for word: *"Delete this package? Anyone you sent it to will lose
  access. This can't be undone."* The two buttons are "Delete package" and
  "Keep it". The question uses the browser's own dialog box, so the keyboard
  and screen readers handle it properly. The Escape key means "Keep it".
- **What deleting does.** It removes the package's ZIP from storage and stops
  every link that was sent for it. Anyone who opens one of those links sees the
  normal "This link isn't available" page, and their download is refused.
- **What stays.** The record that the package was sent stays in the receipts,
  exactly as asked. The package disappears from the main list. A small
  "Deleted packages" section under the list leads to each one's receipts, and
  nothing else.
- **How it was built.** The package's record is marked as deleted rather than
  erased. That is the only way the receipts can stay: the send history can
  never be erased, and the receipts are tied to the package's record.
- **Order and safety.** An audit entry is written first; if it can't be
  written, nothing happens. If the ZIP can't be removed at that moment, the
  hourly cleanup (step 2) removes it later.
- **Who can delete.** Only the account holder. A helper is refused, whatever
  permissions they were given, because deleting takes access away from people
  the holder chose to send to. The owner may want this changed.
- **What is not deleted.** The documents in the vault. They belong to
  credentials, and other packages may use them. A package's own copy of those
  documents is its ZIP, and that is what goes.
- **Measured on the local test copy of the database:**
  - "Keep it" left the package untouched.
  - "Delete package" stopped the link, removed the ZIP from storage, wrote the
    audit entry and kept the receipt.
  - The recipient's link then showed "This link isn't available", and its
    download was refused.

### 2. Unfinished uploads are removed after 24 hours — DONE

- **What gets removed.** Once an hour, a job finds stored files that are more
  than 24 hours old and that nothing in the app points to:
  - a document upload that was started but never finished;
  - a logo that was uploaded but never saved;
  - the ZIP of a deleted package.

  The files are deleted from storage itself, not just from its index.
- **Where it runs.** The cleanup runs inside the existing hourly sending job,
  after the sends are done. If the cleanup fails, the sends are not affected.
  No new schedule was needed, so nothing about the hosting set-up changes.
- **Measured on the local test copy, using real stored files.** Three test
  files were planted: an unfinished document upload and an unsaved logo, both
  made to look more than 24 hours old, and one fresh upload still in progress.
  - Both old files were removed.
  - The fresh upload was kept.
  - All 6 real documents were untouched.

### 3. Sending again to the same address — DONE

- **The message.** If the address already has a working link to this packet,
  the send form shows, word for word: *"That address already has a working link
  to this packet. Stop it first if you want to send a new one."* A
  "Stop that link" button sits right under it. That button asks the same
  confirmation question as every other stop button.
- **After stopping.** Once that link is stopped, or has expired, sending to the
  address works normally. Before this run, a second send to the same address
  was refused forever.
- **What counts as a working link.** A link that is not stopped, not expired,
  and not a send that failed before anything went out. A send that is scheduled
  but hasn't gone out yet counts, because it will go out.
- **The same rule for book sends.** The same rule now applies when sending to
  contacts from the book, because both kinds of send use the same record.
- **Measured on the local test copy:**
  - The second send was refused with the message.
  - "Stop that link" asked the confirmation question, then stopped the link.
  - A new send record for the same address and packet was then accepted.

  No email was sent during this check.

### 4. Receipts cleanup — DONE

- **The old list is gone.** "Sent from this package" has been removed from the
  package page. The receipts show who, when, opened, downloaded and stopped.
- **Older single sends.** Single sends made before run 4 had no receipt of
  their own. Only the old list showed them. They now appear as receipts: who,
  when, and any bounce the mail service reported. They have no "Stop this link"
  button, because those links can't be stopped. A short note on each says why.
- **Found and fixed in the screenshots.** A send that failed before anything
  went out ("Could not send") was offering "Stop this link", but there was no
  link to stop. It no longer does. `test-email-refused-1280.png` was taken
  before this fix, so it still shows the button on the two refused test sends.

### 5. New sending address, local test only — COULD NOT BE DONE

- **What was set.** The From line was set to
  "Steelticket" at the no-reply address on mail.steelticket.com in the local test settings
  only. The live settings were not touched.
- **What happened.** One test packet email to the studio address was attempted
  through the app's own send form. The mail service refused it.
- **The exact error:** HTTP 403 — **"This API key is not authorized to send
  emails from mail.steelticket.com"**.
- **What I did next.** Nothing was sent. As instructed, I stopped there and did
  not create or change any keys. The first attempt only recorded the status
  code, so the app now also records the mail service's own reason (with any
  email address masked). The identical send was repeated once to capture that
  text. It was refused the same way, and nothing was sent either time.
- **The screenshot.** `test-email-refused-1280.png` shows the refused send.
  Both refused attempts appear as "Could not send" receipts.

### Also fixed, found in this run's screenshots

- **Button hover.** Outline buttons across the app turned dark text on purple
  on mouse-over, which is hard to read. They now stay light, with a firmer
  edge.
- **Dialog position.** The confirmation box was stuck in the top-left corner,
  because the page-wide style reset removed its centring. It is now centred.
- **Deletion time.** The deleted-package note now shows its time in the
  reader's own time zone, like the receipts do.

## Test count

- **Unit tests: 974 pass, 0 fail.** That is 25 new ones in this run.
- **Database checks: all pass, 337 in the full suite.** That includes 11 new
  ones for this run:
  - a deleted package can't be un-deleted, by anyone;
  - one working link per address and packet;
  - sending is allowed again after a stop, an expiry, or a send that failed
    before going out;
  - only the server can read the list of unfinished uploads.
- **Other checks:** type check clean, lint clean, verified-file audit clean,
  production build passes.

## Review pack

The review pack in `review-pack/` was reprinted to match this run:

- **`wording.txt`:** now 382 items, up from 364. New wording written for this
  run is marked `[PENDING — PM]` in the code and still needs approval: the
  deleted-package note and list, the helper line under the delete button, the
  after-stop line, and the note on older single sends.
- **`emails.txt`:** every email now shows the logo as an image carried inside
  the message. The From-line section records the refused test send.
- **`data-facts.txt`:** facts from runs 4 and 5 were updated in place: deletion,
  cleanup, stopping links, helper access, and the 20th table.
- **`legal-pages.txt`:** reprinted and unchanged. The Terms of Service and the
  Privacy Policy were not edited.

## What I couldn't do

- **The test email.** It is blocked by the key's permissions, as described
  above.

## Left over

1. **For the owner:** give the mail key permission to send from the new
   sending address. The one test send can then be re-run.
2. **For the PM:** approve the new wording marked `[PENDING — PM]`.
3. **For the owner:** decide whether a helper should be allowed to delete
   packages. Right now only the account holder can.
4. **When the branch ships:** the database changes that are not yet live go
   out in order, now including this run's.
5. **The console, for the record:** the only console entry in the screenshot
   passes is the browser noting the deliberate refusal of a second send (a 409
   response). It is expected, not a fault.
