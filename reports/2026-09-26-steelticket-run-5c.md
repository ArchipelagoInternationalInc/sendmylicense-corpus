# 2026-09-26 — Steelticket, run 5c: email fixes

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. Nothing merged. Nothing touched the live
site, the live database, Vercel or Doppler. Nothing in this run adds any
tracking.

## What changed

1. **Recipient disclaimer.** Everything a general contractor sees now uses the
   owner's wording, with the sender's company filled in: *"Steelticket stores
   the documents [company] uploads and shows the dates they entered.
   Steelticket does not check these documents or confirm that [company] is
   qualified for a job."*
   - **If there's no company name:** the person's name is used; if there's
     neither, "the sender". An email address is never used.
   - **Our own customer's screens and emails** keep the current disclaimer,
     unchanged.
2. **Email footer.** The footer now reads just *"Sent with Steelticket."*
3. **Times in the sender's time zone.** Every date and time in an email is
   written in the sender's own time zone, never UTC. For example: *"Friday,
   October 2, 2026 at 10:53 PM EDT"*.
   - **Where the zone comes from:** the sender's browser tells the app its
     time zone when they send or schedule a packet. It is saved with that
     send, because a scheduled send goes out later with no browser to ask.
   - **Older sends with no saved zone:** the email shows the day with no time,
     rather than a UTC time.

## Each place that changed (step 4)

- **The packet email, sent immediately** (one address at a time): recipient
  disclaimer, new footer, expiry in the sender's time zone.
- **The packet email, sent to the address book** (scheduled): recipient
  disclaimer, new footer, expiry in the sender's time zone.
- **The receiving page:** recipient disclaimer.
- **The cover sheet in the recipient's download:** recipient disclaimer. The
  account holder's own stored copy keeps their disclaimer.
- **The "not opened yet" email to the sender:** the "sent on" date and time
  are now in the sender's time zone, not UTC. It keeps the holder's
  disclaimer, because it goes to our own customer.

## Checked and left as they were

- **The reminder email** goes to our own customer. Its only date is the one
  they typed, shown as a plain day with no time and no "UTC".
- **The helper invitation** has no dates and goes to the account holder's
  helper, not a general contractor.
- **The "export ready" and "account deleted" email templates** exist, but no
  code sends them.
- **Sign-up and sign-in emails** come from the sign-in provider and are not in
  this repository.

## The test email — it went out

- **The send.** One new test packet email went to the studio address, from
  "Steelticket" at the no-reply address on mail.steelticket.com. The browser
  was set to US Eastern time.
- **The first attempt was refused, on purpose.** The run-5b test link to the
  same address was still working, so the app asked for it to be stopped
  first. It was stopped with "Stop that link", and the send then went through.
- **The result.** The mail service **accepted** the email: it answered with
  success and a message id. The receipt is marked sent, with the sender's time
  zone saved.
- **What I can't confirm.** Whether it has landed in the inbox. The mail key
  can only send, not read delivery status.
- **The key** stayed in the local test settings only.
- **In the email:**
  - the expiry is *"Saturday, October 3, 2026 at 12:06 PM EDT"*;
  - the disclaimer names the test account's company;
  - the footer is *"Sent with Steelticket."*

## Tests

- **Unit tests:** 991 pass, 0 fail. That includes 12 new ones for this run.
  Six existing tests were updated, because the old wording and the old UTC
  time were what they pinned.
- **Database checks:** all 339 pass, including 2 new ones for the saved time
  zone.
- **Other checks:** type check clean, lint clean, verified-file audit clean,
  production build passes. The two checksummed files that changed (the email
  templates and the cover sheet) had their master copies and checksums
  updated.

## Review pack

- **`emails.txt`:** the packet emails show the recipient disclaimer, the new
  footer and a US Eastern example time; the "not opened yet" email shows its
  time in the sender's zone.
- **`data-facts.txt`:** updated for this run.
- **`wording.txt`:** reprinted; none of its three sources changed. The new
  wording lives in the disclaimer and footer files and is printed in
  `emails.txt`.
- **`legal-pages.txt`:** unchanged.

## Screenshots

- `email-as-sent.png`: the test email, rebuilt from the saved send's own
  values.
- `test-email-sent-1280.png`: the send confirmation and its receipt.
- `receiving-disclaimer-*`: the receiving page's recipient disclaimer, at
  1280 px, 390 px, and 390 px with reduced motion.
- `recipient-cover-sheet.png`: the cover sheet from the recipient's download,
  with the recipient disclaimer at the foot.

## Left over

- **For the owner:** confirm the test email arrived.
- **The live From line** is still the old address. Changing it is the owner's
  step.
- **When the branch ships:** migrations 0019–0027 go to the live database, in
  order.
