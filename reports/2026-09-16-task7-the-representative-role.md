# Task 7 — the representative role

**Date:** 2026-09-16
**Branch:** the contractor repositioning branch
**State:** built, verified, pushed. Task 8 is next.

## What the task was

A holder invites a representative by email. The representative gets their own
login and sees only what the holder shares, by checkbox: view documents (per
document), assemble packets, send packets, manage the book. Every action logged
as "by [representative] on behalf of [holder]". The holder can revoke instantly;
a revoked representative sees nothing — proved with attack tests written before
the policies. Reminders go to the representative if the holder allows.

## The order the work was done in, and why it matters

**The attack file was written first, before a single policy existed.** Its
threat model is ordered by harm: a representative reading a document that was
never shared with them sits above one who can see a contact's name. Writing the
assertions first is what stops the test being shaped to fit whatever the
implementation happened to do.

The file was committed with the schema, as the first of two commits. The screens
came second. That split is deliberate: the half that decides who may read what
was finished and proven before anything rendered it.

## The shape that was chosen

Viewing is **per document**, in its own table, and is deliberately **not** one of
the three capabilities. "Can see my documents" and "can see this document" are
different promises, and the brief asks for the second. A capability flag would
have quietly turned one shared insurance certificate into the whole vault. The
unit suite asserts the absence rather than leaving it to a comment.

Four predicates decide access. They are SECURITY DEFINER with an empty search
path, because a policy on one table has to ask a question about another and a
plain policy doing that recurses. Execute is revoked from public and anon — a
definer function that anyone may call is an existence oracle even when it
returns false.

State is derived from two timestamps, never stored. A status column would be a
third source of truth beside them, and the copy that goes stale is always the
one the policies do not read.

## Proving the locks can fail

The per-document policy was temporarily replaced with the mistake a hurried
implementation makes — "this representative acts for this holder" instead of
"this document was shared with this representative". The isolation assertion
went from PASS to a hard failure naming the row that leaked. The policy was then
restored and the assertion passed again.

Two application guards were proven the same way. Breaking the state check in the
capability resolver, and renaming one capability string, each turned the suite
red. The rename matters more than it looks: the capability names cross into SQL
as text, so a rename on one side alone fails **silently** — the CASE falls
through to "false" and the representative simply cannot do the thing, with no
error anywhere. A guard that reads the migration turns that into a red test.

## What the screenshots found that the tests could not

Two real defects, both fixed before this was reported:

- **The removed card said "Removed" and nothing else.** Its entire reason for
  staying on the page is that it is the holder's own record of who had access
  between two dates — and it carried neither date. It now prints the span, using
  the delivery log's existing formatter rather than a second one that would
  eventually disagree with it about which midnight a row sits beside.
- **"Documents they can see" was a present-tense claim on an invitation nobody
  had accepted.** The boxes still work, because deciding in advance is
  reasonable, but the page now says plainly that nothing is handed over until
  they accept. This product does not make statements that are not yet true.

## One thing flagged rather than fixed

The shared checkbox style renders its ticked state as a gold ring around a dark
centre rather than as a mark. It is legible, and it is also not what a checkmark
looks like. It is already shipped, already approved, and used by three other
screens, so restyling it inside this task would widen the work into something
the owner has not seen. It is recorded for the critic round to judge without the
author's bias.

## Measured

- 581 unit tests pass; 23 of them are new here.
- 216 lock assertions pass against a local throwaway database.
- The verified-artifact checksums are clean; none was touched.
- Lint: 0 errors (one pre-existing unused-import warning, unrelated).
- Production build green.
- Rendered hydrated in a real browser at 1280 and 390 and under reduced motion,
  through a full interaction cycle — tick a capability, share a document,
  unshare it, press revoke. Zero console errors. No tap target under 44px. No
  text under 12px. No horizontal overflow at either width.

## Next

Task 8: per-document reminder schedules, a 90-day view, and a private revocable
calendar feed. The existing reminder job's behaviour for existing accounts is to
stay unchanged.
