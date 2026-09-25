# 2026-09-25 — Steelticket, run 3: wording, account type, bounces, a real test email, and the review pack

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me (stated at the start of
the run; every commit carries it)
**Branch:** the contractor branch. **Nothing merged. Nothing reached the live
site or the live database.**
**Run type:** overnight, nobody watching. Blocked steps were written down and
skipped, not worked around.

## In one paragraph

Everything finished except the logo, which was blocked: the five logo files
never reached this session, and they are final artwork that must not be
redrawn, so nothing was done with them. The approved wording is in, word for
word. The send screen now shows placeholders as plain words in brackets. New
accounts are contractor accounts and no longer see "Continuing education",
while existing accounts keep it. A bounced send now says so on its receipt. The
mail key works now, and one real test packet reached the owner's inbox. The
review pack for outside reviewers is filed. Along the way, this run found and
fixed a defect that would have stopped some people deleting their accounts.

## How it was tested without touching anything live

The same throwaway local copy of the database as runs 1 and 2, with this run's
database changes applied to it. The app was built and run against it. The one
real email went through the mail service to the owner's own address. Its
download link points at the local copy, so it does not open.

## Step by step

### The logo — SKIPPED (blocked)

The request said five files were attached. None reached this session. I looked
in the container, on every branch of the build repository, and in this
notebook. Because the files are final and may not be redrawn or recolored,
nothing was substituted. Still waiting on them:

- the header logo
- the logo on light surfaces (the receiving page, cover sheet, export and email)
- the PNG version for email
- the browser-tab icons at every size
- storing the two S watermarks

The missing-icon console error is therefore still there. **To unblock:** attach
the five files again, or commit them to the branch.

### 1. The approved wording — FINISHED

- **The "Getting on a bid list" letter** now has the new body, exactly as
  written. That includes its line breaks: the greeting and the body are one
  paragraph, and the name and company sit on their own lines at the end. If
  paragraph breaks were meant, that is a one-line change.
- **The disclaimer** is the new text everywhere it appears on the app: every
  screen, the short version in the footer (now the same text), the cover
  sheet, the export's read-me, and the quoted version in the product brief.
  The cover sheet is checksummed: its master copy changed with it, and the
  checksum audit passes.
- **The second line** is now "An independent service — not affiliated with any
  general contractor, insurer, or licensing agency."
- **Item 178** is now "We told you it hadn't been opened." **Item 336** is now
  "Steelticket can't fill {names}."
- **American spelling.** "licence" is now "license" in all wording, the helper
  invitation email, and the code. Old session notes were left as written.
- **Placeholders on the send screen** now read [their name], [their company],
  [your name], [your company] and [this packet's name]. When the sender saves,
  they are turned back into the old form, so everything after that works
  exactly as before. A letter saved the old way still fills in correctly, and
  there are tests for both.
- **Not changed:** the marketing site's own disclaimer and second line. That
  site still carries the old name and its clinician wording until its own run,
  and putting a sentence starting "Steelticket…" on it would mix the two names.
  The terms' section 1 was also left alone, because it is the terms' own clause
  rather than the disclaimer.

### 2. Account type — FINISHED

- **New accounts are contractor accounts.** A database change sets the default
  for every account created from now on. It rewrites no existing account, and
  sign-up asks no new question.
- **"Continuing education"** is left out of the menu only for contractor
  accounts. If an account's type can't be read, it keeps the menu item, so a
  clinician can never lose it by mistake. The page itself was not deleted.
- **Proven on the local copy.** A brand-new account came out as a contractor
  account and had no "Continuing education" in its menu. The existing account
  still showed it. There are tests for both, including one proving an existing
  clinician account still sees it.
- **One thing to decide.** The Profession line in Settings shows the stored
  word as it is, "contractor". It needs a proper label.

### 3. Showing a bounce — FINISHED, with a receipt list built for it

**There was no receipt screen.** The task that added sending to your book
wrote the wording for one (a heading and column names) but never showed it on
any page. So the package page now lists its sends to the book, one card per
person. Each card shows when the send was scheduled, when it was sent (or
"Could not send"), when the link was opened, when it was downloaded, and when
the link expires.

A bounced send shows the approved sentence: "Bounced — their email system
refused it. Check the address and send again." A bounce outranks "Sent",
because a bounced message was accepted by the mail service first. This was
proven end to end on the local copy: a correctly signed test bounce was posted
to the real webhook, which matched the send, marked it bounced, and wrote an
audit row.

The fixture send in the screenshot also shows a "link opened" time. That is
left over from earlier test data and is not something a real bounced send
would show.

### 4. The test email — FINISHED: sent and delivered

The key worked this time. One packet was scheduled through the real screens
to the owner's address from run 1, with nothing else due, and the send job was
run once.

- **What the app recorded:** when it was sent; the link's expiry (seven days
  later); the mail service's message number; and an audit row for the send.
- **What arrived:** the mail service reports the message **delivered**. It
  carries the new disclaimer and the Steelticket wording.
- **Two things the owner will notice:**
  - It came from the old sender name and address, which stay until a new
    sending domain is verified.
  - Its download link points at the local test copy, so it will not open.
- The subject still says "credential package". That is wording from the
  verified email template, and nobody has changed it yet.

### 5. The review pack — FINISHED

`review-pack/` in this notebook holds four plain-text files. They contain
facts only, and every name and address in them is an example.

- `legal-pages.txt` — the terms and the privacy policy, verbatim, after this
  run's changes.
- `emails.txt` — every email the app sends, verbatim: subject, plain text and
  HTML text, including the line at the bottom of packet emails. Two email
  templates exist that no code sends (export ready, account deleted). They are
  printed and marked as such. The sign-up and sign-in emails come from the
  sign-in provider, whose wording is not in the repository, so they are marked
  unknown.
- `wording.txt` — the wording list, reprinted after this run: 364 items.
- `data-facts.txt` — the fact sheet, with the file each fact comes from, and
  "unknown" wherever the repository does not say. The unknowns include:
  - whether stored files are encrypted, beyond what a planning document says
  - how long backups and outside services' logs are kept
  - the sign-in provider's email wording
  - whether visitors' browsers contact the font service

## Found and fixed along the way

**Deleting an account had been broken on this branch.** An earlier database
change (made to tighten a setting) had accidentally undone the one exception
that lets "delete my account" remove a person's send history. On the local
copy, deleting any account that had ever used the single send failed.

A new database change restores the exception. A new test fails without it and
passes with it. Outside a deletion, the history still can't be changed. None of
this is on the live database.

## Things the fact sheet surfaced, for the owner to know

These are stated as facts, with sources, in `data-facts.txt`. None was changed.

- Uploaded logos stay in storage after an account is deleted.
- Deleting a credential leaves its files in storage until the account is
  deleted.
- A helper with any permission can read the account's send log, including
  recipient addresses.
- The single send's link length has no upper limit in the code.

## Checks

889 tests pass, including new ones for everything above. Type check clean, and
lint shows no errors. The checksum audit is clean, the database security suite
passes on its own throwaway database, and the production build passes.

## Screenshots

Filed beside this report in `reports/2026-09-25-steelticket-run-3/`, each at
desktop and phone width, plus phone-width captures with reduced motion:

- the send screen with bracket placeholders
- the receiving page with the new disclaimer and second line
- a receipt with a bounce
- the menu for an existing account (with "Continuing education")
- the menu for a new contractor account (without it)

The only browser console error was the missing site icon, which the blocked
logo work would have fixed. The header-logo, browser-tab-icon and cover-sheet
logo screenshots could not be taken, because the logo files never arrived.

## Not done, and why

- The logo, icons and watermarks: the files never reached this session.
- The marketing site's disclaimer: that site is its own run.
- No merge, no deployment to the live site, nothing applied to the live
  database.
