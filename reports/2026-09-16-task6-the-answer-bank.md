# 2026-09-16 — Task 6: the answer bank

**Session type:** build. **Branch only. Nothing applied to the live project. The
two real accounts were not touched.**

## Account check

Unchanged and re-stated: publishing to the studio account, signing with the
studio address, both read rather than assumed.

## What it is

One page holding the answers a general contractor asks for on every
prequalification form, with a copy button beside each. A subcontractor retyping
the same eleven answers for the ninth time is the person this exists for, and
every one of those buttons is one fewer transcription error in a document that
decides whether they get onto a bid list.

## The order is the feature

The groups follow a questionnaire's sequence — who are you, where are you, who do
we call, what are you licensed for, who stands behind you, what does your
insurance pay, what is your safety record. The person reading the page has that
form open beside it. A list in the database's order would make them hunt for
every answer, which is the work this page exists to remove.

## Three things it deliberately does not store

**An EIN, and insurance policy numbers.** This was the owner's ruling, and it
removed work rather than adding it. Both groups exist on the page and hold **zero
fields**: each offers a button that opens the stored document instead — the W-9,
or the certificate of insurance. The numbers already live inside documents the
vault protects, and a text column would have been a second and weaker home for
the most sensitive strings in the product, plus a key-management question this
system does not otherwise have. Two tests assert the absence, so a later change
that quietly adds such a column fails rather than passing a review nobody runs.

**Licence numbers.** They are already stored against the credential they belong
to. A second copy is a second thing to keep current, and the copy people forget
is always the one they paste. The page reads them from the vault.

**A count of years in business.** The form asks for a count; a stored count is
wrong every January, because nobody edits this page on New Year's Day. The year
founded is the durable fact and the count is derived from it.

## Two judgement calls worth reading

**Bonding capacity and insurance limits are text, not numbers.** These fields
exist to be *pasted into somebody else's form*, and those forms ask in shapes
that differ. "Five million single, ten million aggregate" is one answer, not two
numbers plus a convention for joining them. Forcing a structure here would make
the user reassemble it at the other end.

**Neither engages the price ban, and the migration says why.** The ban is on a
price for work — a bid, a quote, what a job would cost. A bonding capacity is
what a surety will stand behind and an insurance limit is what a policy pays;
both are qualification facts a general contractor asks for *before* they will
accept a bid at all. The guard is called on the migration's last line and passes.

## The test most worth having

One test reads the migration and asserts that every length bound in the
application validator is literally the bound in the database. When those two
drift, the user meets a generic failure after a form that looked valid — the
exact two-layer disagreement an earlier migration's quarter-hour rule was written
to avoid.

A second asserts every stored field appears in exactly one group on the page. A
column that is saved but never rendered is invisible in the way that takes months
to notice.

## What the screenshots found

**The EIN sentence rendered twice**, once on either side of the button it
explains. The form already draws every group's hint, and the button was drawing
it again.

## A measurement corrected rather than a result

The visual audit counted every field label as a tap target and reported twenty
failures. They were captions sitting above visible inputs that already clear the
floor. A label counts as a target only when it **is** the control — as in the
logo panel, where its input is hidden — so the rule was made precise rather than
the floor loosened. Recorded here because the change moved a number in the
executor's favour, and that is exactly the kind of change that should be stated
rather than absorbed.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **558 passed, 0 failed** (19 new) |
| Lock suite | **209 assertions, 0 failures** (was 190) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green** |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **untouched** |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px, horizontal overflow | **0 / none** |
| Console errors through a full interaction cycle | **0** |

The page was rendered and photographed at both widths and again with reduced
motion, including the copy button's confirmed state.

The grants guard caught the new table on its first run and required its table
counts raised, as it has for every migration in this run. That is the guard
working, not an obstacle.

## Pending the owner

Everything user-facing is **[PENDING — owner]**: the page title and lede, eight
group headings with their hints, twenty-one field labels, seven entity-type
labels, and eight refusal messages.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. No price
anywhere. Nothing was built to *fill* the bank automatically from stored
documents — parsing a certificate to extract a limit is document auto-extraction,
which the packet excludes, and it would put a number this product inferred into
a form a general contractor relies on.
