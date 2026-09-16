# 2026-09-16 — Task 4: the receiving page

**Session type:** build, fourth of a multi-session run. **Branch only. Nothing
applied to the live project. The two real accounts were not touched.**

## Account check

Unchanged and re-stated: publishing to the studio account, signing with the
studio address, both confirmed rather than assumed.

## What this screen is

The only screen in the product read by somebody **outside the account**, and it
has no login. A general contractor opens a link and sees the paperwork. Every
decision below follows from that one fact, and this is the page where a mistake
reaches a stranger rather than a user.

## Access control is the token and nothing else

So it lives in one small module with its own tests, and says three things: the
token must resolve to a receipt, the packet must actually have been sent, and the
link must not have expired.

**Every failure gives the visitor the same answer.** Expired, never sent, and no
such token all render one sentence. "This link expired" would confirm to a
stranger that a packet existed and went to this address — a fact about the
sender's business relationships that they never authorised anyone to disclose.
The reasons stay distinct in the server log, so an operator can tell a typo from
an expiry without the page telling anyone.

There is a test whose entire job is to assert that the three failures are
indistinguishable from outside.

## The page is deliberately unguarded, and that is now pinned

It lives outside the signed-in route group so that no session guard applies. The
existing test that reads route directories off disk and demands each one be
guarded is exactly why it lives where it does.

A new assertion pins the opposite: the recipient's page must **not** be in the
guarded list. The obvious "safety" change — adding it — would break every link the
product sends while looking like tightening security. That is the kind of change
somebody makes in good faith six months from now.

## What a visitor with no session can read: nothing

Asserted four ways against the real database, including that **link tokens cannot
be enumerated**. The token is the capability, so that last one is the whole model.

Everything the recipient sees is fetched server-side after the token is checked,
and the fetch returns exactly what the page renders — no account identifiers, no
storage keys, no other recipients of the same packet, and no other packets from
the same sender.

## The one write a person without an account can cause

A document request. Three things stop it being a hole:

- **The token is re-checked here from scratch**, not trusted because the page
  already checked it.
- **The owner comes from the receipt, never from the form.** Nothing a visitor
  types can aim a task at somebody else's dashboard.
- **There is no insert policy for signed-in users either**, so a compromised
  session cannot fabricate the appearance that a general contractor asked for
  something.

Every request is logged with who asked and which kind — **never the free text**,
which is the recipient's own words about their project and belongs in the
sender's dashboard, not in an operator log.

**Free text is half the feature, not a fallback.** A general contractor asks for
things this product has never heard of: a particular endorsement form, a
jobsite-specific safety plan for one project. A picker alone turns "what they
need" into "the nearest thing on our list", which is how a subcontractor sends
the wrong document and loses the bid.

## Always current, and a record of what that meant

The page serves the **current** version of every document. Replace a certificate
of insurance in the vault and every link already sent starts showing the new one
— which is the reason a link is worth more than an attachment.

That makes "what did they actually get?" a real question, so the receipt records
the version of each document served at download time. And the sender cannot
rewrite it: the send has already gone, and a sent receipt is evidence.

## Two things found by the tooling rather than by review

1. **The date formatter would have thrown on the recipient's page.** The
   platform refuses to combine its two shorthand style options with a time-zone
   name and raises an error. The first version did exactly that — and it would
   have failed in front of a general contractor rather than in any path a
   developer walks. Its own test caught it before it left the branch.
2. **The forbidden-language check reads this file's comments as well as its
   copy** and flagged a word in mine. For the one screen a stranger reads, that
   strictness is right, so the comment moved and the guard did not. This is the
   second session running in which that guard has earned its keep.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **496 passed, 0 failed** (12 new) |
| Lock suite | **162 assertions, 0 failures** (was 140) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green**, two new routes |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **byte-identical**, all twelve |
| The existing single-send route | **byte-identical** |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px, horizontal overflow | **0 / none** |
| Console errors | **0** |

The page was rendered and photographed at both widths. It carries the canonical
disclaimer, imported and never retyped, and the standing independence line —
required on every screen showing credential status, and this is the only one a
person outside the account ever reads. One change came from looking rather than
from a test: the document picker's placeholder repeated its own label.

## Pending the owner

Everything user-facing here is **[PENDING — owner]**, including the sentence a
general contractor reads about what the product does and does not do. Roughly
thirty strings, to be listed as one block with the earlier tasks' in the gate
report.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. No price
anywhere — and this migration calls the guard too, because a company asking for
paperwork is one small step from a company asking for a number.

**The sender's logo is not uploadable yet.** The column and the page's handling of
it are in place; the upload itself needs a storage policy and an upload route,
and is stated here rather than left to be discovered. The cover letter the page
is meant to show is Task 5 and is not built.
