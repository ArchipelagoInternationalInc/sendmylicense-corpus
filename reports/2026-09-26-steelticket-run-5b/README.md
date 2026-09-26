# 2026-09-26 — Steelticket, run 5b

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. Nothing merged. Nothing touched the live
site, the live database, Vercel or Doppler.

## The test email — it went out

- **The key.** The owner's new mail key was put in the local test settings
  only: a private file on the test machine that only its owner can read. It was
  not committed, not printed, and is not in this notebook.
- **The send.** One test packet email went to the studio address, from
  "Steelticket" at the no-reply address on mail.steelticket.com, through the
  app's own send form.
- **The result.** The mail service **accepted** it: it answered with success
  and a message id, and the app marked the receipt as sent
  (`test-email-sent-1280.png`).
- **What I can't confirm.** Whether it has landed in the inbox. The new key can
  only send; when asked for the message's delivery status, the mail service
  refused ("This API key is restricted to only send emails"). The owner should
  check the inbox.
- **The link inside it** points at the local test copy of the app, so it will
  not open.

## Also done

- **Wording approved.** The PM approved all of run 5's new wording as written,
  and the approval markers are removed from the code.
- **Deleting a package** stays the account holder's alone. Helpers are refused,
  and the button is not shown to them.
- **Long receipt lists.** A package page now shows its 10 newest receipts, with
  a "Show all receipts" button under them. The button is an ordinary link, so
  it works without scripts. After the click all of them show, and the page
  lands on the 11th so you carry on reading. The Delete section stays where it
  was, below the receipts.
  - Measured at 1280 px, 390 px, and 390 px with reduced motion: 10 receipts
    before the click and all 20 after, with Delete still below.
  - No console errors, and no send was made while taking the screenshots.

## Tests

- **Unit tests:** 978 pass, 0 fail. That includes 4 new ones for this run: the
  paging, the button wording, the markers being gone, and helpers staying
  refused.
- **Database checks:** all 337 pass.
- **Other checks:** type check clean, lint clean, verified-file audit clean,
  production build passes.

## Review pack

- **Reprinted:** the review pack has been printed again.
- **`wording.txt`:** 383 items, with no approval markers left from run 5.
- **`emails.txt`:** the From-line section now records the accepted test send.
- **`data-facts.txt`:** notes the receipts paging.
- **`legal-pages.txt`:** unchanged.

## Left over

- **For the owner:** confirm that the test email arrived.
- **The live From line** still uses the old address. Changing it is the
  owner's step, in the live settings, which this run did not touch.

## Screenshots

- `receipts-first-ten-*`: the 10 newest receipts with "Show all receipts",
  and Delete below.
- `receipts-all-*`: after the click, landed on the 11th receipt.
- `test-email-sent-1280.png`: the send confirmation and its receipt.
