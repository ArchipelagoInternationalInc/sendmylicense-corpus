# The approval list, and two decisions recorded

**Date:** 2026-09-17
**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me (verified before the
commit; the repo-local override was in force and the pre-commit guard passed)
**Branch:** the contractor repositioning branch
**State:** the copy list is handed over; two approved decisions are recorded and
deliberately not built. Nothing applied to the live project, nothing merged.

## Three things were asked for, and one of them was not possible as written

The instruction was to print the contents of the pending-copy document so the
owner could save it as a text file — grouped by screen, numbered continuously,
with the flagged items first and the letter bodies in full.

**That document does not contain the strings.** It is an index of counts: a
table saying which screen has how many, with five quotation marks in the whole
file. Printing it verbatim would have produced a table of numbers, not something
anyone can approve line by line.

So the list was built from the source it indexes — the copy module, the letter
templates and the invitation email — and printed verbatim from there, grouped,
numbered and with the flagged six at the top. That is what the request was for;
the file it named was the wrong place to get it.

## Three corrections the extraction forced

Building the real list turned up three things the index had wrong. All three
were this project's own errors, two of them written by a session earlier in the
same day.

1. **It claimed three starter cover letters. There are two.** The template
   module exports two, and its own opening line says so. The third was invented
   by the index.

2. **It claimed 392 strings. Extracting them individually and removing
   duplicates gives 377.** The index counted by a different method and was never
   reconciled item by item.

3. **A string used on more than one screen was counted once per screen.** In the
   list it is numbered once, where it first appears, so a number refers to one
   piece of wording rather than to a position in a table.

The list now says plainly that it supersedes the index's numbers.

## And four errors of my own, caught before the handover rather than after

The first pass at the extraction shipped four defects, and they are worth
recording because each one would have reached the owner as an approval item:

- An escape sequence leaked through unconverted, so one string displayed raw
  character codes where a pair of quotation marks belonged.
- One string was split in half by the parser, because its text is interrupted by
  a function call. It appeared as two separate numbered items, neither of which
  was a sentence.
- Items in the flagged group were repeated verbatim further down, so the same
  wording carried two different numbers — which defeats the point of numbering
  them for reference.
- A note at the end asserted no item was numbered twice. It was wrong, because of
  the point above.

Fixed, regenerated, and the corrected file is the one handed over. The flawed
version had already been printed, so the reply said which one to use rather than
quietly replacing it.

## Two decisions recorded, and neither built

Both were written into the handoff with the reasoning that makes them
implementable later, and **neither was implemented** — the instruction was to
record and stop.

**The receiving page must not call a good link invalid.** The lookup discards
the error on five database reads, and a missing row and an unreachable database
end up at the same answer. So an outage renders "this link isn't available" to a
general contractor holding a perfectly good link, with nothing telling the
sender. The approved sentence is recorded exactly as given.

The part worth writing down was that this is a **third state, not a rename**.
The existing sentence is deliberately identical for a spent link, a wrong link
and an expired one, because distinguishing them would tell a stranger whether a
packet exists. Widening it to cover a database failure would put that disclosure
straight back. The two sentences have to stay separate, and the handoff says so
in those terms so nobody later reads the change as a simple substitution.

**The sender's company name gets a real field.** The column is read in five
places and written by nothing, which is why the walk-through found a recipient
page with an empty heading, "sent to you by" followed by nothing, and a logo
with no alternative text — for every account that had never opened settings,
which is the state every account starts in. Recorded specifically as *the column
stays and gets a field*, not *the column goes*, since either would have resolved
the bug and only one is what was decided.

## What was not done

No copy string was touched. Neither decision was implemented. Nothing was
applied to the live project and nothing was merged. The only change committed
was the handoff entry recording the two decisions.

## The thing worth carrying forward

An index of counts had been standing in for a list of items, and nobody noticed
until someone tried to use it for the thing it was named for. It had been cited
in a pull request description, in two reports and in a gate summary — always as
a number, never opened. The number was wrong in three different ways, and one of
the errors was an invented document that does not exist.

A count is a claim about a list. It is only as good as the last time someone
built the list and compared.
