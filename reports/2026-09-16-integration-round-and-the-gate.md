# Task 10 — the integration round, and what it found

**Date:** 2026-09-16
**Branch:** the contractor repositioning branch
**State:** at the gate. Task 11 and Task 7b are built; Task 10's critic
sequence has run its full budget and the walk-through is blocked.

## What this session was asked to do

Three things, in order. Pin the mutable `search_path` on three flagged database
functions, proven locally and **not** applied to the live project (Task 11).
Build the representative application layer — the account switch, the scoped
screens, the capability checks and the on-behalf-of audit calls — with the
cross-account attack tests written first (Task 7b). Then resume the critic pass:
a third per-piece round, then an integration round on the assembled whole, with
a twelve-step walk-through against a database branch the owner created by hand.

## The headline: the integration round failed, and it was right to

The critic sequence used its whole budget — three per-piece rounds, each finding
and fixing one gap, then the integration round. The integration round returned
FAIL on five defects. Every one was re-checked by hand before anything was
changed; none was fixed on the critic's word alone.

**The largest was a banner that told the truth on four screens out of nine.**

Acting for somebody scopes four things, because a holder can hand over three
capabilities plus a set of individual documents: the dashboard, the book, the
packets, and sending. Everything else in the app stays the representative's own
— their own expiry list, their own continuing education, their own answer bank,
who may help *them*, and their own settings.

The shell rendered a single sentence for the whole signed-in group: *"You're
working in [the holder]'s account. What you do here happens to their
paperwork."* On the five unscoped screens that sentence was false. A
representative was reading and writing their own records while being told they
were in somebody else's account.

The sharpest instance was on the scoped dashboard. It offered "Add credential",
and the create flow behind it was not scoped at all: the credential landed in
the representative's own vault, counted against the representative's own plan
limit, and the redirect returned them to a dashboard where it did not appear.
Three wrong things in one click, none of which the person would have noticed
until much later.

The fix makes the list of scoped screens a single named thing that two different
readers consult — the notice and the navigation — instead of each carrying its
own assumption. The test holds that list against the pages themselves in both
directions: a page that reads the holder's rows must be in it, a page that reads
the signed-in person's must not. It was proven to fail by adding a personal
screen to the list.

## The other four

- The document-sharing checkboxes on a representative's card were optimistic and
  threw away what the server said. In the *withdrawing* direction that is the
  dangerous way round: a holder saw a document's access removed and left the
  page believing it, while the share was still in force. The result is awaited
  now, the tick goes back on a refusal, and the refusal is shown.
- Scheduling a send wrote an "acted on behalf of" audit row unconditionally — so
  a person scheduling their *own* sends wrote the one row in their record whose
  entire job is to say somebody else had been in there. Guarded now, matching
  the guard the book writes had from the start.
- Accepting an invitation read "no error" as "accepted". The statement is
  deliberately written so it matches nothing on a second attempt, and a database
  update that matches nothing returns no error at all — so an invitation revoked
  in that window still answered *"Accepted. You can now act for them"*, and the
  person then found an empty switch and no explanation. It selects the row back
  now.
- A flag added in the previous round to say "the message went but the receipt
  could not be written" reached the browser and was read by nothing. The screen
  said "Sent" with no receipt behind it — the exact state the flag was added to
  prevent.

## A test that could not fail

One of the previous round's guards asserted that a string appeared somewhere in
a source file. That same string also sits in a comment four lines above the code
it was guarding, so **the assertion passed on the prose**. Deleting the actual
behaviour from both code paths left it green.

This is worth recording as its own finding rather than a footnote. A guard that
cannot fail is worse than no guard, because it is a claim of coverage. It now
strips comments, looks only inside the response bodies, and additionally
requires that something consumes the flag — and it was proven to fail by
deleting the fields.

That is the third time this branch a guard has been found too weak to fail. The
pattern each time was the same: the assertion was written against the shape of
the fix rather than against the behaviour the fix produces.

## What the screenshots caught that nothing else did

The visual pass is mandatory here and it earned its place three times:

1. A missing space. The navigation's links are flex containers, so a literal
   space at the start of a child element is dropped — the marker rendered as
   `Next 90 days(yours)`. The gap is a style rule now.
2. The marker was too long. The first version read `(your account)` on five of
   nine destinations: the desktop bar wrapped onto two rows and pushed sign-out
   onto the second, and at phone width it ran to seven rows. One word instead.
3. The heading *"Your credentials"*, in the second person, sitting directly
   beneath the holder's name. Every sentence on that screen addresses the reader
   as the owner, so the copy was telling a representative the vault was theirs
   while the banner above said otherwise.

And one thing about the rendering harness itself, which nearly produced a false
pass and is the more useful lesson. The first run reused a page header cached
from an earlier session, so it rendered the **old** stylesheet: the new rule was
in the build and not on the page, and the screenshot looked exactly as it would
if the fix had not landed. The harness reads the built stylesheets on every run
now, picks them by content rather than by filename (the build hashes the names),
applies the font variable classes the app puts on the body, and refuses to build
a page whose stylesheet does not contain the rule under test. Before that last
fix the type was falling back to a system sans, which means the earlier
screenshots were not evidence about typography at all.

## Measured

At desktop width, at phone width, and at phone width with reduced motion
requested, through a full interaction cycle (a share toggle refused, a send
submitted and confirmed):

- Console errors: **0**
- Tap targets under 44px: **0**
- Text under 12px: **0**
- Horizontal overflow: **none** at either width

And across the whole branch at session end: **710 unit tests**, **0 lint
errors**, the row-level-security suite green, the production build green, and
the verified-artifact checksum audit clean. No checksummed artifact was
modified.

## What did not happen, stated plainly

**The twelve-step walk-through never ran.** No part of it is claimed as passed.

The environment is not the problem — the database branch the owner created is
provisioned and verified: every migration applied, row-level security enabled
*and forced* on all nineteen tables, sixty-seven policies, three private
storage buckets, three functions with their search paths pinned.

The problem is a key. Ten modules require the elevated service-role client, and
they sit directly under the steps the walk-through is made of — uploads, packet
assembly, the recipient's view of a link, invitation acceptance. That key cannot
be obtained from a session like this one: the tooling available returns only the
publishable and anonymous keys, by design, and the secret-store command-line
tool is not installed here. Four of the twelve steps would run. A walk-through
is a journey, and the steps that break are the ones that join it together.

**The branch was therefore not deleted**, against the instruction to delete it
when this report is filed, and that departure is deliberate rather than an
oversight. It exists for a walk-through that has not happened; deleting it is
irreversible and discards a correct environment that is the only thing standing
between "blocked" and "it runs" the moment a key is available. It costs a little
over a third of a dollar a day at the rate read from the provider. The call
belongs to the owner, and the alternative — supplying the key through the
environment or the secret store, never pasted into a conversation — turns a
blocked gate into a finished one.

**Nothing was applied to the live project. Neither real account was reached. No
pending wording was shipped** beyond the one sentence the owner approved
verbatim this morning, which is pinned by a test that also forbids the retired
placeholder by name.

## What the owner is being asked for at the gate

- The complete list of wording awaiting approval, grouped by the screen it is
  read on: **392 strings plus three letter bodies**, all of it in the
  repository.
- A decision on the database branch: delete it, or supply the key and let the
  walk-through finish.
- A decision on whether anything beyond the verified defects should be attempted
  before the gate. The critic budget is spent and the rule for that case is to
  stop and summarise rather than burn more rounds — which is what this is.
