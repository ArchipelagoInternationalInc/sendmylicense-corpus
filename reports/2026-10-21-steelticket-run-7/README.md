# 2026-10-21 — Steelticket, run 7: status badges and small fixes

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commit:** a9fce3a.
Nothing merged. Nothing touched the live site, the live database, Vercel or
Doppler. Nothing in this run adds any tracking, and no email was sent.

## Account check — one thing to know

This session started on a freshly rebuilt machine.

- **What happened:** the repository's own commit identity had been lost, so
  git was falling back to the machine's default identity. Nothing had been
  committed with it.
- **The fix:** before any commit, I put back the studio identity the way
  CLAUDE.md prescribes for a fresh copy. I also confirmed the GitHub account
  is ArchipelagoInternationalInc.
- **Result:** the run-7 commit is authored by the studio address.
- **Also rebuilt from scratch:** the local test database.

## 1. Status badges

Every status word on receipts, the deliveries page and the packet page is now
a solid badge: white capital letters on a block of color. Each badge has the
same 6 px ticket notch as the app's chips.

| Color | Badges | Fill | White text contrast |
|---|---|---|---|
| Deep green | SENT, OPENED, DOWNLOADED | `#1C6B3A` | **6.53 : 1** |
| Deep red | COULD NOT SEND, BOUNCED | `#9E1D22` | **7.92 : 1** |
| Gray | LINK STOPPED, EXPIRED, SCHEDULED | `#5A5F66` | **6.43 : 1** |

**How the colors were chosen:**

- All three fills are the app's existing status colors, so nothing new was
  introduced.
- All three clear the 4.5 : 1 standard.
- Each badge also stands out from the page by at least 5.7 : 1.

**How the numbers are checked:**

- They are pinned in the contrast tests to two decimals.
- They were measured again in the browser from the rendered page, and matched.

**The word is always there.** The badge is the word itself; color is never the
only signal.

- The capitals come from the styling, so a screen reader reads "Link stopped"
  rather than spelling out letters.
- On a receipt the badges are a list named "Status".
- In Windows high-contrast mode, each badge keeps an outline.

**Where they appear:**

- **Receipts**, under the address.
  - **The rule:** what happened to the email, then what the recipient did,
    then the state of the link. So a receipt can read *SENT · OPENED ·
    DOWNLOADED* or *SENT · OPENED · LINK STOPPED*.
  - **Bounces:** BOUNCED replaces SENT, because showing SENT beside BOUNCED
    would claim the one thing that turned out false.
  - **Stopped and expired:** LINK STOPPED outranks EXPIRED.
  - **Times:** the rows under the badges still give the times. The old "Link
    stopped · time" note became a "Link stopped" row with its time.
- **The deliveries page.**
  - **The badges:** SENT, OPENED, COULD NOT SEND and BOUNCED.
  - **What changed:** that page used to say "Couldn't be sent" both for a send
    that failed on the way out and for one refused later. Now it tells them
    apart.
  - **The sentence under each badge** is unchanged.
- **The packet page.** The confirmation after a send now shows a SENT badge as
  its heading. The receipts on the packet page are the same as above.

**The bounce message is kept.** The red "Bounced — their email system refused
it. Check the address and send again." box (wording approved in run 3) is
still under the BOUNCED badge, because it tells the sender what to do.

## 2. "Continuing education" for trade contractors

- **The test account in the run-6 screenshots was not a contractor account.**
  The menu rule was already right: it hides the tab for a contractor account.
  That account carried the old clinician value, so the tab showed. That local
  database no longer exists.
- **This run's test account** was created the ordinary way, with its type
  never set. It came out as **contractor**.
- **Every new account is a contractor account.** The column default is
  "contractor", and sign-up never sets the account type.
- **A contractor now never sees continuing education.** Each item below was
  checked in a browser.
  - **Menu:** no tab, at 1280 and 390 wide.
  - **Dashboard:** no mention.
  - **Settings:** shows "Trade contractor" and no mention of continuing
    education.
  - **New-packet page:** no mention.
  - **By web address:** every continuing-education page now sends a
    contractor to the dashboard. This is new.
  - **Recording a course:** the server refuses it for a contractor, even from
    an old tab. This is new.
  - **Export:** a contractor with no courses gets no continuing-education file
    and no README line about one. This is new. An account that does have
    courses still gets them, because it is their data.
  - **Emails:** none of the email templates mention continuing education. A
    test now keeps it that way.
- **For the owner:** accounts created on the live site before the contractor
  default existed still carry the old clinician value. When this branch ships,
  they will still see continuing education. Changing them is a live-database
  step, and I didn't take it.

## 3. Deleted packets show when

- **What each line shows:** the deleted-packets list now shows *"Deleted Oct
  19, 2026, 10:48 AM EDT"* under each name, in the reader's own time zone. That
  is the same format the receipts use.
- **Why the time is included:** two packets with the same name deleted on the
  same day would still look identical with the date alone.
- **If you want only the date:** that is a one-line change.
- **Screen readers** hear the moment too, in the "Receipts" link for that line.

## 4. Nurse and clinician wording — every place found

**How the search was done.** I read every piece of text in the app's code with
a parser, not by guessing, and searched the legal drafts separately. The
search covered:

- **Ordinary words:** employer, assignment, clinician, clinical, nurse,
  travel, facility, state board, patient, hospital, healthcare, medical,
  respiratory, therapist, recruiter, staffing, practitioner, onboarding, shift
  and licensed professional.
- **Credential and course names:** CE, CEU, NBRC, AARC, BLS, ACLS, PALS, NRP,
  RRT, CRT, malpractice and immunization.

### Changed (in-app, where the contractor meaning is obvious)

| Where | Was | Now |
|---|---|---|
| Credential form, "Credential name" hint | “RRT license — Texas” | “Contractor license — Texas” |
| New-packet form, "Packet name" hint | “Travel assignment — Denver” | “Prequalification — Harbor Street project” |

### Not changed: public, for the owner

**Site description,** the one search engines read on every page:

- *"Store your credentials, track the dates you enter, and send an
  assignment-ready packet to an employer."*

**The marketing site** (its own run) still has the old name, and clinician
wording throughout. 47 places in the marketing copy, plus 2 in its pages:

- **Hero and title:** "Built for respiratory therapists" and "…respiratory
  therapists and travel clinicians who work across state lines".
- **Credential examples:** BLS, ACLS, PALS and malpractice examples, and the
  BLS and ACLS card images. "RT License — Texas" and "RT License — New
  Mexico", plus an "NBRC Respiratory Care Practitioner" example.
- **Assignment, employer and recruiter wording:**
  - "New assignment. The recruiter needs your licenses…"
  - "Every assignment. Every renewal. Every time."
  - "…between assignments"
  - "Employer-ready packages…"
  - "Assignment paperwork, in about a minute."
  - "Your next assignment package…"
  - "Same recruiter. Same documents."
  - "Select the credentials the employer needs…"
  - "…the recruiter's email…"
  - "Can an employer see my vault?" and "Never. Employers see…"
- **Onboarding examples:** "Onboarding package". The example send line also
  shows a sample hospital HR address.
- **Traveler wording:** "Built for the traveling clinician's life.",
  "…for the traveling clinician's life", "For the working traveler.", "…one
  assignment's worth of saved scramble…" and "If your day is patients and
  travel…".
- **Patient wording:** "…not patient information. We adopt healthcare-grade
  technical safeguards…".
- **Board and profession wording:**
  - "Does SendMyLicense keep me compliant with my state boards?"
  - "…built first with traveling and telehealth respiratory therapists."
  - "Board-reporting systems like CE Broker…"
  - "I'm a PT / OT / nurse / other licensed professional — can I use it?"
  - "…RT is simply where we started…"
- **Pages:** the How-it-works page description says "Build and send an
  employer-ready package in three clicks."
- **Footer:** "An independent service — not affiliated with any state board
  or agency." This is the standing footer line, and it still reads correctly
  for contractors, since contractor licensing boards are state boards.

**Legal drafts** (under outside review, not touched):

- **Privacy:** "…we don't collect patient information…"
- **Privacy:** "Steelticket is for licensed professionals and is not directed
  to anyone under 18."
- **Terms:** one use of "patient" in the acceptable-use paragraph.

### Not changed: in-app, needs an owner decision

These are not label fixes; changing them changes what a contractor can pick.

- **The credential-type picker** shows a contractor a group called "Clinical
  credentials" containing BLS, ACLS, PALS, NRP, NBRC Credential, AARC
  Membership, Malpractice and Immunization. Hiding that group for contractor
  accounts, as continuing education now is, would take one line. It needs
  your approval.
- **NBRC and AARC field labels and hints**, for example "Which NBRC
  credential?". They only appear once one of those clinical types is chosen.

### Checked and left as they are

- **Continuing-education wording** (CEUs, "General Respiratory Care", the
  continuing-education cover-sheet section). A contractor can no longer reach
  any of it. Clinician accounts still use it.
- **"Respiratory Therapist" as an account-type name** in Settings. Only an
  account of that type sees it.
- **"healthcare fit-out, tenant improvement"** in the trade-specialty hint. It
  is a construction term, so it is correct for contractors.
- **Code comments** mentioning recruiters or clinicians. No one reads them on
  screen.
- **Emails, the receiving page and the cover sheet.** Nothing was found.

## Tests

- **Unit tests:** 1042 pass, 0 fail (1001 after run 6).
  - **New:** 34 tests in the run-7 file, 7 badge contrast tests, and 1
    automatic check for the new white token.
  - **Updated:** one older test, which pinned the old deliveries-page words.
  - **Replaced:** two tests that checked the removed chip styles became one
    test that checks the badge styles.
- **Database checks (local only):** all 339 pass.
- **Other checks:** type check clean, lint clean, verified-file audit clean,
  production build passes.

## Review pack

- **`wording.txt`:** reprinted (421 → 428 items). The header lists what run 7
  added and changed.
- **`data-facts.txt`:** the account-type fact now covers the new
  continuing-education rules.
- **`emails.txt` and `legal-pages.txt`:** unchanged. No email wording changed
  in this run.

## Screenshots

**How they were made.** All screenshots come from the local test stack, with
the browser set to US Eastern time.

- **The test data:** the receipts are test data for one packet, made to cover
  every badge.
- **The "Sent" panel:** to show it, the app's one send went to a local stand-in
  for the mail service. No email left the machine.
- **Extra receipts:** that send also added a receipt and a deliveries line for
  a `stub-…@local.example.com` address.

**Browser console:** zero errors across the whole run.

**The files:**

- `packet-receipts-1280.png`, `packet-receipts-390.png` and
  `packet-receipts-390-reduced-motion.png`: every receipt badge — SCHEDULED,
  SENT, OPENED, DOWNLOADED, COULD NOT SEND, BOUNCED, LINK STOPPED and EXPIRED.
- `deliveries-1280.png`, `deliveries-390.png` and
  `deliveries-390-reduced-motion.png`: SENT, OPENED, COULD NOT SEND and
  BOUNCED.
- `sent-panel-1280.png` and `sent-panel-390.png`: the SENT badge after a send.
- `packets-page-1280.png`, `packets-page-390.png` and
  `packets-page-390-reduced-motion.png`: two deleted packets with the same
  name, each showing when it was deleted.
- `dashboard-menu-1280.png`: the contractor menu, with no continuing
  education.

## Noticed, not changed

- **Deliveries times are in UTC.** The deliveries page prints its "Sent" times
  in UTC, while receipts use the reader's own zone. I can match them on
  request.
- **The deliveries page knows less than receipts.** It lists what the send log
  knows, so it cannot show LINK STOPPED, EXPIRED or DOWNLOADED. Those are on
  the packet's receipts.
- **Expired links still have a stop button.** An expired link's receipt still
  offers "Stop this link". This was already the case before this run.

## Left over for the owner

- **Clinical credential types:** decide whether to hide the "Clinical
  credentials" picker group for contractors.
- **Public wording:** rewrite the public site description and the marketing
  site. The marketing site is its own run.
- **Existing live accounts:** set them to the contractor type when the branch
  ships. This is a live-database step.
- **Still open from run 6:**
  - confirm the run-6 test email arrived;
  - keep or remove the "Message from [Company]" line;
  - change the live From line;
  - apply migrations 0019–0027 live when the branch ships.
