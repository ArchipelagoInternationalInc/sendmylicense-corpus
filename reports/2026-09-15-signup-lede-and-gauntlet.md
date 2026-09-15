# 2026-09-15 — Sign-up sentence shipped; the build put through a blind critic gauntlet

**Session type:** build + verification. **One copy change reached production. The
feature branch is unchanged and still at the gate.**

## Account check — out loud, both halves

- **Studio account:** the remote is the studio organisation's repository. Correct.
- **Studio signing address:** read from what git will actually record as the
  author, not from a configuration file — the studio address. Correct.
- **The identity guard is armed in this checkout:** the hooks path points at the
  tracked hook directory and the pre-commit hook is present and executable, so
  the refusal is live rather than merely sitting on disk.

No mismatch. Nothing needed fixing.

## Task 1 — the sign-up sentence is live

The closing call-to-action was fixed last session. That was not the same as
fixing the claim: the sign-up page carried its **own wording** of the same
retired cap, and it is the first sentence a new beta user reads. It had been
live and false since the day the beta was uncapped.

Fetched from the live page **before** starting, and again after deploying:

| | Before | After |
|---|---|---|
| Retired wording | present | **0 occurrences** |
| Approved sentence | absent | **present** (twice in the served document: the rendered markup and the hydration payload) |

Shipped alone — two files, its own pull request, its own deploy, so the release
had exactly one reason to be inspected. Merged and deployed with the correct
commit author, so nothing was blocked.

### The guard is the real fix

The previous guard knew a **single** phrasing of the untruth, which is precisely
why a second copy of it survived one page away. It now blocks both retired
wordings, pins the approved sentence, and asserts the page names no credential
count at all.

**Proven to refuse, not assumed:** reintroducing the retired sentence fails three
separate assertions; restoring the approved one passes all six.

The two remaining cap messages were deliberately left alone. They describe the
cap **correctly** for the day it returns and can only appear when a limit is
reached, which cannot happen while the beta is uncapped. They are unreachable,
not wrong. A pattern broad enough to catch every mention of the old number would
fail the build on copy that is correct, and the usual next step after that is
loosening the guard until it catches nothing.

## Task 3 — every user-facing string written in this build that is not yet approved

Listed verbatim, one per line, for approval as a block. **None of it has reached
a user:** all of it sits behind the gate on the feature branch. The approved
field labels, picker labels, specialty areas and panel names are excluded — those
were approved as written and are unchanged.

A note on the two certification bodies named in these strings. They appear
because the strings are quoted verbatim and the owner cannot approve copy they
cannot read. They are public professional bodies whose names are already part of
the product's approved, user-visible vocabulary — not a workplace, not a portal
login, not a piece of infrastructure. The prose in this report names neither
portal.

### Continuing education — area and navigation

```
Courses you have completed. Recorded exactly as you enter them — no expiration, no status, no reminders.
Add a course
No courses recorded yet.
Record a course you have completed. You can attach the certificate next.
Edit course
Changes saved.
Delete this course
This removes the course and the certificate filed under it. It cannot be undone.
Delete course
Deleting…
Save
Saving…
Cancel
```

### Continuing education — field hints and the blank option

```
However it appears on the certificate.
Optional. Who ran the course.
Optional. Who accredited it, if anyone.
Not specialty-specific
Quarter hours, from 0.25 to 100.
The date on the certificate. It cannot be in the future.
```

### Continuing education — validation messages

```
Give the course a title so you can find it later.
That title is too long — keep it under 120 characters.
Choose what kind of course this was.
That provider name is too long — keep it under 120 characters.
That accreditor name is too long — keep it under 120 characters.
Choose a specialty area from the list, or leave it blank.
Enter the CEUs for this course.
CEUs must be between 0.25 and 100.
CEUs are recorded in quarter hours — try 0.25, 0.5, 0.75 or 1.
Enter the date you completed the course.
Enter the date as YYYY-MM-DD.
Check that year — it looks like a typo.
That date is in the future. Record a course once you have completed it.
That course isn't in your record.
Something went wrong on our side. Try again.
```

### The copy sheet — surrounding copy and controls

```
Copy sheet
The same course in each portal's order. Tap a field to copy it.
Copy
Copied
—
No certificate attached.
```

### The credential form — the two new conditional fields

```
Which NBRC credential?
Required for an NBRC credential — pick the award you hold.
Choose one
Optional. Whatever appears on your AARC membership.
```

### The credential form — validation messages for those fields

```
Choose which NBRC credential this is.
Choose an NBRC credential from the list.
An NBRC credential can only be set on an NBRC Credential.
That membership number is too long — keep it under 40 characters.
A membership number can only be set on an AARC Membership.
```

### Printed on the cover sheet itself

```
Course titles, hours and completion dates shown are as entered by the user.
Provider:
Accreditor:
NBRC specialty area:
Completed (as entered by the user):
```

The last group matters most, because it is printed on a document that goes to an
employer. Each line states what the user typed and attributes it to them. Note
what is deliberately absent: no expiry, no status, and no word about whether any
of it counts for anything.

## Task 2 — the blind critic gauntlet

Four rounds. A fresh critic each time, spawned with **only** the bar and the
artifact — no build notes, no summaries, no earlier verdicts. The bar had both
halves the method requires: the packet's acceptance criteria, and a named
external reference (the two portals the first user retypes every course into,
with their field orders fixed in advance).

| Round | Verdict | Largest gap |
|---|---|---|
| 1 — pieces | PASS | none against the product |
| 2 — pieces, adversarial | PASS | none |
| 3 — integration | **FAIL** | the test suite had stopped testing what ships |
| 4 — integration, re-run | PASS | a residual coverage hole, since closed |

### Round 1

Passed. The critic ran rather than read: it mounted the copy sheet with real
clipboard permissions and read every value back, unzipped a real package and
inflated the document's content streams to read the printed text, and measured
every screen in a browser at both required widths.

**One finding in it was mine, not the product's.** The audit reported one
failure — the build step — and the cause was the isolated checkout I had
prepared for the critic: I had symlinked its dependency directory, which the
build tool refuses. The critic diagnosed that itself, rebuilt the identical tree
with a real directory, and got a clean build. I repaired the checkout and re-ran:
**clean, no failures.** Round 2 ran on the repaired environment so that item was
measured rather than inferred.

### Round 2

Passed, and pushed considerably harder. Every value the critic could think to
break the clipboard with came back byte-identical — a 400-character string,
smart quotes and an em dash, embedded newlines and tabs, whitespace runs at both
edges. A stress build of forty courses and ten credentials produced nine pages
with the standing disclaimer on all nine and the item numbers running unbroken
against the archive's own entry order. The conditional fields on the credential
form were verified **live**, by driving the picker in a browser, and shown to be
genuinely removed rather than merely hidden for every type they do not belong to.

**It also caught a discipline failure of mine and said so unprompted:** I edited
two project records inside that checkout while it was measuring. It checked that
no source, test, component, migration or content file had changed and concluded
its measurements stood — which is right — but a tree under a critic should be
frozen and this one was not. The later rounds ran with no concurrent edits.

### Round 3 — the integration round, and why it was worth running

**FAILED.** Both piece rounds had passed, and it still found this:

The row-level-security suite had quietly stopped being evidence about this build.
Its harness applied only the first three migrations. That was sound until the new
migration, which **drops and recreates the policy that stops one user filing a
document against another user's record**. From that moment the suite was
asserting an expression that is no longer deployed, against a database that did
not contain the new table at all. A green run certified none of the new work.

The critic established that the **shipped policy is correct** — it dumped both
expressions and exercised the real one against a database with the full chain
applied — so this was an assurance gap rather than a live hole. But the next edit
to that policy would have shipped unnoticed, and "the tests are green" would have
kept saying so.

Fixed, one gap as the method requires: the harness now applies the new
migrations, and a **new, separate** assertion file carries the checks. Separate
because the original suite file is a checksummed verified artifact that must stay
byte-identical, so assertions for a later migration cannot be added inside it.
The new file covers the branch that nothing covered — a second user cannot attach
a document to the first user's course — with a **negative control** so that a
policy which simply refused everything could not pass as one that works.

A detail worth keeping: the suite's table counts moved on their own, and that
turned out to be the interesting part. An earlier migration grants the service
role through default privileges, so the new table inherited both its access grant
and its restriction without anyone writing them down. The suite could not observe
that property before and now does.

### Round 4 — the re-run, and an error of mine inside it

**PASSED.** The critic confirmed by dumping the live policy catalog that the
suite now tests the deployed expression, then went further than the bar: a
forty-two document stress case with numbering checked against the archive order;
hostile file names including directory-traversal attempts, all sanitised; every
picker value round-tripped into a real database and accepted; route protection
checked both by running the predicate and against a live server, twelve of twelve
redirecting when signed out.

**And it caught a comment I had written an hour earlier that was false.** I had
justified leaving two further migrations out of the harness by saying the suite
asserts nothing about them. That was wrong on its own terms: the suite asserts at
length about a function that one of those migrations **rewrites**, so those
assertions were running against a stricter version than the one that ships — the
same defect as the one just fixed, in a different object. The critic proved by
hand that account erasure works and carries courses away; nothing automated
covered it.

Fixed: the harness applies those migrations too, and the new assertion file
gained erasure coverage — the course and its certificate going with the account,
a **control** that the other user's records survive, and a check that the
append-only protection resumes immediately once the erasure call returns.

**That last fix landed after the round, so it was not itself put in front of a
blind critic.** The verification log says so in as many words. One migration
remains outside automated coverage for a stated reason: it configures file
storage against a schema the harness does not simulate.

### Where the numbers ended up

| Check | At the start | Now |
|---|---|---|
| Row-level-security assertions | 55 | **75**, 0 failures |
| Unit tests | 368 | 368, 0 failures |
| Lint and type checking | 0 errors | 0 errors |
| Build | green | green |
| Artifact audit | clean | **clean, 8 of 8 checksums unmodified** |

The checksummed cover-sheet generator is byte-identical throughout. That is the
whole reason the new section is composed onto the finished document by a separate
module instead of being added to it.

### Findings left deliberately unfixed

Three, all real, all recorded for the owner rather than quietly patched:

1. A package containing **only** continuing-education documents still prints the
   verified sheet's unconditional "enclosed documents" heading and its
   expiration-date caveat above an empty list. Awkward to fix precisely because
   that generator is a checksummed artifact.
2. The copy button on the unresolved live-category prompt is **enabled**, so the
   instruction sentence itself can be pasted into a portal field.
3. The copy sheet overflows horizontally at 280 pixels — below the required
   widths and below any shipping device; clean from 320 up.

None was fixed because this branch is parked at a gate. Changing it further would
mean the thing the owner approves is not the thing that was verified.

## The gate, and the cost of holding it

**Nothing has been applied to the live project.** That remains a hard stop for the
owner regardless of the verification result, and the branch has been held there
throughout.

The development branch on the database project bills at **$0.01344 per hour**,
read from the platform rather than remembered. It has not been deleted, as
instructed.

## Deliberately not done

No migration applied to the live project. The two real accounts untouched. No
verified artifact altered — all eight checksums unmodified. The database branch
not deleted. The two dormant capacity messages left exactly as they are. The
three critic findings above recorded rather than fixed. No user-facing string
beyond the two approved sentences has reached anyone.
