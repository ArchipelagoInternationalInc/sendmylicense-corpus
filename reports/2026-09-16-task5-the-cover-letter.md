# 2026-09-16 — Task 5: the cover letter

**Session type:** build, fifth of a multi-session run. **Branch only. Nothing
applied to the live project. The two real accounts were not touched.**

## Account check

Unchanged and re-stated: publishing to the studio account, signing with the
studio address, both confirmed rather than assumed.

## What a letter is for

A qualification packet arriving with no covering note is a stack of certificates
from a stranger. The letter is the one place a subcontractor says which project
they are bidding, who referred them, and what they actually do — and it is the
reason a general contractor reads the rest. It is also the only text in this
product written by one user and read by somebody outside the account, which is
what every decision below is shaped around.

**It is still not a bid.** The migration calls the price guard on its last line
for the same reason the send migration does: a free-text field addressed to a
general contractor is the likeliest place in the whole product for a number to
appear. The guard catches a price-shaped column and the test scans the starter
templates. Neither polices what a user types into their own letter, and this
product does not read its users' words to police them.

## Two columns, because a draft and evidence are different things

The sender's draft keeps its placeholders and belongs to them; they may rewrite
it up until the packet goes, and the existing rule that stops at "already sent"
enforces that boundary rather than the form remembering it.

What the recipient was sent is a second column, written once by the dispatcher
at the moment the packet leaves, and never again. It is what the receiving page
renders and what the PDF carries, so the sender's record and the recipient's
copy are the same words.

Resolving the placeholders live instead would have been less code and a worse
product: a letter whose greeting quietly changed months after it was read,
because somebody renamed a contact in a book the reader has never seen.

For the same reason, **who the letter was addressed to is snapshotted onto the
receipt**. That also closes a hole rather than papering over one — a contact
removed from the book leaves the receipt standing by design, and without the
snapshot the dispatcher would have had to invent a name for a document going to
a general contractor.

## A correction, found while building this

The recipient's download used to hand out the archive built when the package was
assembled. The page above the download button tells the reader, in so many words,
that it shows the documents as they are now. **Those two statements could not
both be true**, and the one the product depends on is the promise: replace a
certificate of insurance and every link already sent starts showing the new one.
The record of what was served was wrong in the same direction — it wrote today's
version numbers for a download carrying older bytes.

So the recipient's packet is now assembled per request, from the current
documents, with their own letter in it. That is also the only way a letter
addressed to one person can be in a packet that several people received.

**What it costs, stated rather than discovered:** every download reads every
document out of storage and compresses them again. The route declares the Node
runtime and the sixty-second ceiling, exactly as the assembly route does.

## Where the letter sits, and why the order matters

The verified cover sheet is checksummed and untouched. The letter is composed
onto it, the way the continuing-education section already is — the verified pages
come through unaltered and only new pages are drawn, with the canonical
disclaimer imported from the verified file so the two can never drift into two
different disclaimers.

The letter's pages go after the cover sheet and before the courses. That is
decided by the order of two calls and nothing else; swap them and a letter
addressed to a person lands behind a list of continuing-education courses.

## The two starter templates

One for a first approach to a general contractor who has never heard of the
sender, one for replying to a general contractor who asked. They are starters,
not defaults: nothing is inserted on anybody's behalf, and what goes out is what
the sender wrote. A product that supplied a letter and sent it unread would have
written to a general contractor on a subcontractor's behalf.

A test asserts the templates claim nothing about the sender's standing — not
approved, not in good standing, not checked by anyone. A claim in a template is a
claim made over every signature that starts from it.

## The placeholders are a closed set

Five, and a letter naming anything else is refused at the save with the offending
word quoted back. The tempting alternative — fill what we recognise, leave the
rest — turns a typo into `{{project_number}}` printed in a letter to somebody the
sender is trying to win work from.

An empty value is filled as empty and the punctuation around the hole is tidied,
so a missing company name does not produce "Dear , of ." Nothing is substituted
for a value the product does not have.

## Two defects the screenshots found and the tests could not

1. **The letter's paragraphs rendered with the same spacing as its lines.** A
   five-paragraph letter reached the recipient as one undifferentiated block with
   the sender's structure gone. Spacing between paragraphs has to beat spacing
   within one, or there are no paragraphs.
2. **The shared note sat below the per-recipient override.** The override reads
   "just for this person" and offers a link back to "the shared note" — which the
   sender had not yet seen. They met the exception before the rule. The note now
   comes first, which is also the order in which a person writes a letter and
   then addresses it.

Both were rendered in a real browser from the real components, hydrated, clicked
through, and re-photographed after the fix.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **523 passed, 0 failed** (27 new) |
| Lock suite | **181 assertions, 0 failures** (was 162) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green** |
| Verified-artifact audit | **CLEAN**, 30 PASS |
| Earlier migrations | **byte-identical**, all thirteen |
| The existing single-send route | **byte-identical** |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px, horizontal overflow | **0 / none** |
| Console errors through a full interaction cycle | **0** |

Every state was photographed at 1280 and 390, and again at 390 with reduced
motion: the editor at rest, with a starter template chosen, with recipients
selected, with a per-recipient letter open, and with the shared note focused. The
receiving page was photographed with a filled letter on it.

## Pending the owner

Everything user-facing here is **[PENDING — owner]**: the editor's labels and
hints, the two starter templates in full, the heading a general contractor reads
above the letter, and the two refusal messages. Roughly twenty strings plus two
template bodies, to be listed as one block with the earlier tasks' in the gate
report.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. No price
anywhere. The letter is not in the sending email — that is Task 9, and adding it
here would have been the easy scope creep this run exists to avoid.

**The sender's logo is still not uploadable.** Unchanged from Task 4 and repeated
rather than allowed to go quiet: the column and the page's handling of it are in
place; the storage policy and the upload route are not.

**Task 6 needs a decision before it can start.** The brief says the answer bank
stores an EIN and insurance policy numbers "never in plain text in the database;
last four visible". Nothing in this system encrypts a column today — documents
are private objects behind short-lived signed URLs, which is a different
mechanism that does not transfer to a text field. That is a stop-and-ask, not a
choice to make at three in the morning.
