# 2026-09-24 — Steelticket, run 2: the preview question, two fixes, and the new name

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me (stated at the start of
the run; both commits carry it)
**Branch:** the contractor branch. **Nothing merged. Nothing reached the live
site or the live database.**
**Run type:** overnight, nobody watching. Blocked steps were written down and
skipped, not worked around.

## In one paragraph

Three of the five steps finished and two were stopped on purpose. The two gaps
found in run 1 are closed: every send to the book now keeps the mail service's
message number, so a bounce can be matched to the send it came from, and every
send writes a row to the audit log. The app now says Steelticket everywhere a
user or a general contractor meets it, apart from the marketing site and the
sending address, as instructed. "Continuing education" was **not** hidden: the
app has no reliable way to tell a contractor account from a clinician account,
so the menu was left alone. The test email was **not** sent: the mail service
could be reached this time, but it refused the message. On the preview copy's
database: almost certainly the live one, but the check that would prove it was
refused by this session's permissions, so it was not attempted another way.

## How it was tested without touching anything live

The same throwaway local copy of the database as run 1, started inside the
container, with the new migration applied to it. The app was built and run
against it and signed into through the real form with a fixture account on an
example.com address. Nothing spoke to the live project.

## Step by step

### 1. Which database the preview copy uses — ANSWERED, not proven

**Very probably the live one.** No other database exists for it to use. The
database provider's preview check on this branch's pull request was *skipped*,
so no preview database was made for it. When the walk-through copy was deleted
on 17 September, the provider showed the live database as the only one left.

What could not be done: the hosting tools available here don't list a
project's settings for environment variables. The direct check was to compare
the database address built into the preview's public page code with the live
site's. The live-site half of that was refused by this session's permission
system as a read of production, and it was not attempted another way.

**It matters less than it sounds, for two reasons.** The preview could not
actually work against the live database: this branch's new screens need twelve
database changes that are not on the live database, and none may be applied.
And scheduled jobs only run on the live site, so scheduled sends never go out
from a preview.

**Proposed only, nothing set up.** For the owner to click through the new
screens safely:

1. Make a throwaway copy of the database, as for the 17 September walk-through
   (about $0.013 an hour).
2. Apply the branch's database changes to it.
3. Point this branch's preview settings at that copy (the hosting service
   allows per-branch preview settings), and redeploy the preview.
4. Sign up there with a throwaway account and click through.
5. Delete the copy afterwards.

No real account is touched at any point.

### 2. The two gaps from run 1 — FINISHED

- **The message number is kept.** A new database change stores the mail
  service's message number on each send to the book, in the same write that
  marks it sent. A user cannot write it or change it. When the mail service
  reports a bounce, the app now finds the matching send and records the bounce
  time on it, once, with an audit row. The bounce is **recorded but not yet
  shown on any screen**, because showing it needs wording the owner hasn't
  approved.
- **Every send writes an audit row**, before the message goes. If that row
  can't be written, the packet isn't sent and waits for the next run, the same
  rule the older single send follows. The row never holds the recipient's
  address or their link.
- One safety rule: the mail has already left by the time the message number is
  saved. If the database ever refuses the number, the send is recorded without
  it. Losing one bounce match is better than the app thinking the packet never
  went, and sending it twice.
- Reminder emails and invitations to helpers also write no audit row. They are
  not packet sends, so they are listed here and left unchanged.

New tests: 19 in the app's test suite, 12 of which fail on the old code (the
other 7 guard things that were already right), and 7 database checks.

### 3. Hide "Continuing education" for contractor accounts — SKIPPED, by instruction

The only field that could tell the two kinds of account apart is the
"profession" on each profile. It is set to respiratory therapist for **every**
account, contractors included, and nothing in the app ever changes it. Every
other clue (having contractor documents, a company name, a book of contacts)
would be a guess. **The menu is unchanged**, so the clinician on the live site
keeps it. To make this possible, an account needs a real account-type field,
chosen at sign-up or in Settings. That is a decision about stored data and
wording, left for the owner.

### 4. A real test send to the owner — SKIPPED (blocked)

The address in the brief was a blank placeholder, so the owner address from
run 1 was used. This time the mail service could be reached. The one packet was
scheduled through the real screens and the send job was run once, with exactly
one packet due and nothing else able to go out. **The mail service refused it,
and nothing was sent.**

Why, as far as could be seen without guessing: the key this environment holds
can only send. The mail account the connector can read has the sending domain
verified, and has no record of the refused request at all. So the key most
likely belongs to a different mail account, or is limited to a different
domain. **To unblock:** put the sending key from the account that owns the
verified domain into this environment.

What the app recorded was right in every part: the send is marked as failed,
with the reason "rejected"; there is no message number, because none was
issued; and the new audit row for the attempt is there. No retry happened, by
design.

### 5. The new name — FINISHED

**84 replacements**: every app screen and its browser-tab title, the header,
the page a general contractor opens, the standing disclaimer, the privacy and
terms pages, the email wording, and the cover sheet, the export and the
calendar feed. A packet downloaded from the receiving page was opened, and its
cover sheet says Steelticket with no trace of the old name.

- **Checksummed files.** The email templates and the cover sheet are
  checksummed. Their master copies were changed in the same commit, each copy
  still matches its master exactly, and the checksum lists were updated. The
  audit passes.
- **The product brief.** The disclaimer's wording is fixed by the product brief,
  and the tests hold the app to it word for word, so the name was changed in
  that one quoted paragraph of the brief too.
- **Not changed, as instructed:** the marketing site, and the sending address
  and domain.

**Things I wasn't sure about, left as they were rather than guessed:**

- **The calendar feed's hidden event IDs** still end in the old domain. A
  calendar recognises an event by that ID, so changing it would put a duplicate
  of every expiry date into every subscribed calendar. Nobody sees it.
- **The header.** The old name had "My" picked out in a second colour. The new
  name has no agreed accent, so it is plain.
- **The privacy and terms pages** now say Steelticket, but they sit inside the
  marketing site's header and footer, which still carry the old name until that
  site's own run.
- **Internal names nobody sees** (a database setting, the project's package
  name, the incident runbook's own title) are unchanged.
- **The wording-approval list from run 1** was not reprinted. Its entries now
  differ only by the name.

**Screenshots** are filed beside this report in
`reports/2026-09-24-steelticket-run-2/`: the dashboard, the send screen and the
receiving page with the new name, each at desktop and phone width, plus
phone-width captures with reduced motion. Through the whole cycle, the only
browser console error is the missing site icon, already on the list. The
44-pixel tap floor still holds on every control shown.

## Checks

856 tests pass, including the new ones. The type check is clean, and lint shows
no errors. The checksum audit is clean, the database security suite passes on
its own throwaway database, and the production build passes.

## Not done, and why

- Hiding "Continuing education" (step 3): there is no reliable way to tell the
  account types apart.
- The test email (step 4): the mail service refused the key.
- Proving which database the preview uses (step 1): the deciding check was
  refused by this session's permissions.
- No merge, no deployment to the live site, nothing applied to the live
  database.
