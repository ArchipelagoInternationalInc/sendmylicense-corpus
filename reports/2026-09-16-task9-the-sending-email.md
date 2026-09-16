# Task 9 — the line at the bottom of the sending email

**Date:** 2026-09-16
**Branch:** the contractor repositioning branch
**State:** built, verified, pushed. Task 10 — the critic pass — is next.

## What the task was

One line at the bottom of each link-carrying email addressed to the recipient, a
general contractor. The wording is the owner's to give; a placeholder was
supplied with the brief. Transactional mail only, with no tracking beyond the
link-open record the product already keeps.

## Reading the scope carefully

"Each link-carrying email, addressed to the recipient" is two conditions rather
than one, and the difference decides three of the five messages this product
sends. The nudge carries a link but goes to the sender. The expiration reminder
and the export notice carry links and go to the account holder. Only the packet
delivery email meets both conditions — and it reaches a general contractor by two
separate paths, the scheduled send and the immediate one, so the line is added at
both. A recipient getting a different email depending on how the sender happened
to press send would be a defect nobody would ever report.

A test asserts the line's **absence** from the other three. Putting it there
would be an advertisement inside mail somebody receives about their own
documents, which is not what was asked and is not something this product should
do unasked.

## Composed, not edited

The delivery template is one of the eight checksummed artifacts. It was not
touched; the line is composed onto its output, the same discipline already used
for the nudge and for the continuing-education section of the cover sheet. All
eight checksums are intact.

## No tracking, asserted rather than intended

The delivered message contains no image of any kind, exactly one link and it is
the recipient's own packet, no query string on that link, no campaign parameter
and no redirect. Every one of those is an absence, which is precisely what
nobody notices going missing — a tracking pixel added by a later change would
look like one more image tag. They are assertions now.

One thing this code cannot reach: open and click tracking at the email provider
is a setting on the sending domain, not a per-message flag. It has to be
confirmed off where that domain is configured. Named here rather than assumed,
and it is not yet set up at all — the sending credentials do not exist yet.

## Two notes on the placeholder, for the owner

The supplied line is carried verbatim and marked as pending. Two things about it
are worth an eye before it ships, neither of which is a decision to make inside
a task:

1. It offers a general-contractor-facing view of subcontractor paperwork. The
   same brief's exclusion list forbids building that view, so the line currently
   offers something that does not exist.
2. It states that something is free, in mail that reaches a general contractor.
   It is not a price for the subcontractor's work — which is what the ban exists
   to keep out of a packet, and why the automated guard does not fire on it — but
   it is close enough to that line to name rather than quietly ship.

## Three things found by attacking my own work

- **The automated language check refused the first version.** A comment I wrote
  used one of the words the guard greps for across the email directory. The
  guard cannot tell a comment from a subject line, and in a directory whose
  strings land in a contractor's inbox that strictness is correct — so the
  comment moved, not the guard.
- **One guard was too weak to fail.** It checked that a name appeared somewhere
  in a file. Renaming the import left the name in the source and the test went
  on passing while the line itself was gone. It now matches the composer
  actually wrapping the template call, and that version does fail when broken. A
  mention in a comment is not a call.
- **A phone screenshot caught the line rendering outside the email's padded
  container**, running flush to the edge of the screen while every other line
  sat inside a gutter. It read as something bolted on after the fact — which is
  the last impression a message carrying somebody's paperwork should give. The
  insertion point is now tried innermost-first and always ends somewhere that
  works rather than dropping the line.

## Measured

- 645 unit tests pass; 18 new in this task.
- 236 lock assertions pass against a local throwaway database.
- All eight checksummed artifacts intact; the automated review script is clean.
- Lint: 0 errors. Production build green.
- The delivered email rendered and read at two widths, both the text part and
  the HTML part.

## Next

Task 10: a fresh blind critic pass, with a full walk-through against a local
copy of the stack.
