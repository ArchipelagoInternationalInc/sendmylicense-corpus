# Task 8 — reminders and the calendar feed

**Date:** 2026-09-16
**Branch:** the contractor repositioning branch
**State:** built, verified, pushed. Task 9 is next.

## What the task was

A reminder schedule per document — 60, 30, 14, 7 days by default, adjustable. A
page showing what expires in the next 90 days. A private, revocable calendar
feed so expirations appear in Outlook or Google Calendar. Reminder emails to the
holder and, if allowed, the representative. And a constraint that shaped most of
the work: keep the existing reminder job's behaviour for existing accounts
unchanged.

## The instruction that had to be read carefully

The brief asks for a 60/30/14/7 default and, in the same paragraph, that
existing accounts see no change. The product's existing default is 90/60/30/7,
and it is not stored on most rows — it is what the code returns for a document
with no schedule of its own, which is every document in the product today.
Editing that one constant would have satisfied the first instruction by
breaking the second, silently, for everybody.

So there are two lists now, deliberately. The old one stays frozen and keeps
governing what already exists. The new one is written as an explicit schedule
when a document is added. Both were needed for both instructions to be true at
once, and a test asserts each of them rather than leaving the distinction to a
comment.

One trap inside that: the function which cleans a schedule drops any value it
does not recognise. Leaving 14 out of the recognised list would have stripped it
from every new schedule on the first round trip through the form — no error, no
log line, just a reminder that never arrives. That is now an assertion.

## What "per document" was taken to mean

In this product the thing that carries an expiration date is the tracked
document itself; the files attached to it share that one date. Reminding once
per file would put three identical emails in somebody's inbox on the same
morning, which is the exact failure the existing job was written to avoid. The
schedule is therefore attached to the tracked document, which is also where the
existing schedule already lived. Recorded here as an interpretation rather than
a certainty, for the owner to correct if it is wrong.

## The calendar feed

The attack file was written first, as ratified, and its threat model is ordered
by harm with revocation at the top rather than isolation. That ordering is
specific to this surface: a feed URL is pasted into a calendar client and then
fetched indefinitely, by servers the user does not control, long after they have
forgotten it exists. A token that cannot be killed is a permanent leak with a
friendly name.

Three consequences, all enforced by the database rather than by application code
that a future change might forget to call:

- Revoking marks the row, and the reader refuses a marked row. The URL dies at
  the database.
- Exactly one live feed per person, by a partial unique index. Two would make
  revoking a half-measure — one URL goes quiet while another keeps answering.
- The row is never deleted. There is no delete permission at all. A revoked feed
  is the answer to "what was I handing out in March".

The reader is a definer function with a pinned empty search path and execute
revoked from everyone but the job that serves the route, because a function that
resolves tokens is an oracle for which tokens are live. It returns a set rather
than a single value, so that "no such token", "revoked" and "live" are one shape
— a caller who forgets a null check leaks; a caller who forgets to read a row
gets nothing.

What the feed carries is labels and dates. No document leaves through it. Every
entry says the date is the one the user entered, because that line is read
months later, outside the product, by somebody who will not remember where the
number came from.

Proven able to fail: dropping the revocation clause from the reader — the
omission a hurried implementation makes — turns the revocation assertion red
rather than leaving it quietly passing.

## Two instrument errors of my own

Both found by running the attack file, and both worth recording because this
suite keeps relearning the distinction. Row-level security **raises** on an
insert, because the check is a constraint — but it **filters** on an update: a
statement whose condition matches nothing updates zero rows and succeeds. Two of
my assertions expected a refusal and would have passed against a policy that did
not exist at all. They assert the state of the row afterwards now. Separately, a
signed-out visitor holds no read permission on that table, so the attempt raises
rather than returning an empty count — a different instrument again.

## What the screenshots found

Four defects across the two rounds, none of them reachable by a test:

- **On the representatives screen**, the removed card said "Removed" and nothing
  else, while its whole reason for staying on the page is to record who had
  access between two dates. It prints the span now.
- **"Documents they can see"** was a present-tense claim on an invitation nobody
  had accepted. It now says plainly that nothing is handed over until they do.
- **The feed URL sat in a single-line field** that clipped it at the edge with
  nothing to say there was more. At phone width the user read a shortened link
  and had no way to check what they had copied. It wraps now.
- **Two cautions trailed two buttons**, with nothing saying which described
  which — and at phone width, where the buttons are on separate rows, the
  pairing was unrecoverable. Each sits under its own button now.

A fifth was caught by reading rather than looking: the per-document panel told
the user their reminders go to one address, which this task had quietly made
untrue.

## Still flagged, still not fixed

The shared checkbox style renders its ticked state as a ring rather than a mark.
This round found supporting evidence for reporting it rather than fixing it
here: another screen in the product already draws a proper checkmark, so the
ring is the inconsistent one and the fix is a decision about which style wins
across several approved screens. That is not a call to make inside a task the
owner has not reviewed. It goes to the critic round.

## Measured

- 627 unit tests pass; 46 new in this task.
- 236 lock assertions pass against a local throwaway database; 20 new.
- The verified-artifact checksums are clean; none was touched.
- Lint: 0 errors. Production build green, with both new routes present.
- Rendered hydrated in a real browser at 1280 and 390 and under reduced motion,
  through a full interaction cycle on each new screen. Zero console errors. No
  tap target under 44px. No text under 12px. No horizontal overflow.

## Not yet exercised

The feed route has not been fetched by a real calendar client, and the schedule
written at document creation has not been observed end to end against a running
stack. Both are part of the walk-through the owner scheduled for the critic
round, on a local copy of the stack. They are named here rather than left for
someone to notice as an absence.

## Next

Task 9: the single line at the bottom of each link-carrying email, whose wording
is the owner's to give.
