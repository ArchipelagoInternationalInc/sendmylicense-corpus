# 2026-09-14 — CTA shipped live; continuing education built on a branch, at the gate

**Session type:** build. **One change reached production. Everything else stops
at the gate.**

## Account check — stated out loud, both halves

- **Studio account:** the remote is the studio organisation's repository. Correct.
- **Studio signing address:** read from what git will actually record as the
  author, not from a config file — the studio address. Correct.
- **The identity guard is armed in this checkout:** the hooks path points at the
  tracked hook directory, so the pre-commit refusal is live rather than merely
  present on disk.

No mismatch. Nothing needed fixing.

## Why the reports stopped after 2026-09-03

The watch ran on its three-hourly cadence through 2026-09-03 and the next
check-in did not fire. The session went quiet rather than being stopped; it woke
on 2026-09-14 and the cadence resumed. There are no reports for 2026-09-04
through 2026-09-13 because **no session ran on those days**, and back-filling ten
entries describing checks that never happened would put fiction in a public
record. The gap is recorded as a gap, in a report filed on the day the session
came back.

The cost was bounded: the watch is a notification path, not a safeguard. Nothing
in it could change the review branch, and on resuming the full branch record was
re-read rather than only its check results — head, file count, diff size and
last-updated timestamp were all unchanged. Eleven unobserved days produced
nothing to observe.

## Correction accepted

Deployment protection was re-enabled by the owner on 2026-08-29 and verified
from outside. It has been removed from the open list in the handoff, where this
seat had been carrying it as outstanding for two weeks.

## Task 1 — the approved sentence is live

Fetched from the apex **before** starting: the retired sentence was being served,
the approved one absent. The copy had been committed since 2026-08-29 but sat on
a review branch behind gated database work, so it never reached production.

Shipped alone, in its own pull request: three files, +59 / −2 — the content
module the pages render from, the copy bank, and the guard test that pins the
approved sentence verbatim and fails the build if the retired one returns. One
change, one test, nothing riding along, so the deploy it triggered had exactly
one reason to be inspected.

Checks green, merged, deployed, and then **the apex was fetched again**:

- approved sentence: **1 occurrence**
- retired sentence: **0 occurrences**
- the no-index directive, the standing independence line and the absence of the
  retired product name: all still intact

That is the whole of what reached production this session.

## ⛔ The same false claim survives in three more places

Finding the CTA was not the same as finishing the job. A search for the retired
cap across live copy turns up three more:

- **The sign-up page lede** — "Free to start — three credentials, the dashboard,
  and reminders. No card required." **This is live right now and it is false**,
  in exactly the way the closing band was. It is also the first sentence a new
  beta user reads.
- Two cap messages shown when the limit is reached. These are dormant rather
  than wrong: they cannot fire while the beta is uncapped, and they become
  correct again if the cap returns.

**Not changed.** Public copy belongs to the owner, and inventing a replacement
would be the thing this seat is not allowed to do. A proposed sentence is in the
build repository's task list marked as needing owner approval. The sign-up lede
is the urgent one.

## Task 2 — "Name it"

A case-insensitive search of the whole repository found **one** user-facing
occurrence: the label on the credential form's name field, defined in the content
module and rendered at one call site. The other five hits are ordinary prose
inside code comments and packet documents ("appends the name it is handed") and
are not interface strings.

Replaced with the approved wording. No location needed to be left alone, so
there is nothing held for a decision here. Before and after were rendered from
the real component at both widths.

## Task 3 — the full build, on a branch, stopped at the gate

A development branch was created on the database project and both migrations
were applied **there and nowhere else**. The live project was not touched and
neither were the two real accounts on it.

### What the database now enforces

The widened credential vocabulary with the approved picker; the NBRC award,
required on an NBRC row and refused on any other; the continuing-education
table with the approved four-item type list and the approved six specialty
areas; quarter-hour CEUs between 0.25 and 100; a completion date that cannot be
in the future; an optional membership number allowed only on an AARC membership
row; and a document that must hang off exactly one parent.

Two of those are worth explaining, because both were nearly written the wrong way:

- **"Not in the future" is a trigger, not a CHECK constraint.** The database
  requires CHECK expressions to be immutable, and "today" is not. A trigger is
  the only way to make this a guarantee of the database rather than a promise of
  the form, and it is worth making — a completion date after today is not a typo
  worth keeping, it is a claim about something that has not happened.
- **The quarter-hour rule is an exact test, not a remainder.** In binary floating
  point, 1.3 divided into quarters leaves 0.04999999999999993, so the obvious
  version would accept a value the database then rejects — the worst kind of
  disagreement between two layers, because the person at the form sees a generic
  failure after filling in something that looked fine.

### Measured on the branch — 50 assertions, 0 failures

| Group | Assertions | Failures |
|---|---:|---:|
| Widened credential types | 9 | 0 |
| Continuing-education constraints | 20 | 0 |
| Document parent rules and membership-number scope | 9 | 0 |
| Owner-only access, including a negative control | 7 | 0 |
| Account erasure | 5 | 0 |

The access tests were run by **trying to break them**: a second account
attempting to read, update, delete and insert against the first account's
courses, and to attach a file to one. All refused. The negative control — the
second account's own insert being accepted — is there so that a policy which
simply denied everything could not pass as a policy that works.

The erasure test includes its own control: erasing one account leaves the other
account's rows intact.

### The cover sheet, and the artifact that was not touched

The cover-sheet generator is a checksummed verified artifact. It renders one
flat list and has no idea what a second headed section is.

The tempting shortcut was to feed courses through the existing list. It would
have compiled, and it would have printed "no expiration date entered" under
every course — a statement about a record that has no expiration by design, on a
document going to an employer. A false line on a cover sheet is worse than an
extra module.

So the section is composed onto the finished document instead: the verified
pages pass through untouched, only new pages are drawn, and the rule that every
page carries the standing disclaimer is honoured by importing the same constant
rather than retyping it. **All eight checksums pass.**

This is asserted nowhere and measured instead: a package carrying one credential
document and one certificate is built, and the text is read back out of the
produced document. The course appears under the approved heading with its title,
its hours and its completion date labelled as entered by the user; the phrase
about a missing expiry appears nowhere; the disclaimer appears on the added page
as well as the original.

### Getting that test to tell the truth took three attempts

Worth recording, because all three failures looked like passes:

1. The first scanned the archive for the document. Archive entries are
   compressed, so nothing was found — and every "must not contain" assertion
   passed for that reason. **A negative assertion over an empty string is not
   evidence.**
2. The second looked for text written one way; the library writes it another.
   Again nothing was found, and again the negatives "passed".
3. The third advanced its scanner one byte past the start of a keyword, which
   left it inside the word "endstream", where the next search matched that
   keyword's own tail. Everything after the first block was read at the wrong
   offset — and the added page was precisely what went missing.

Each test now proves the extraction produced something before asserting what it
does not contain.

### The copy sheet

The first user retypes every course into two portals whose field orders
disagree, reading off a screen that is in neither order. The product cannot
submit on her behalf and should not pretend to, but it can stop her hunting for
the next value: the same course laid out twice, in each portal's own order, with
a copy button per field and one button for the certificate.

It is presentation only — no new table, nothing stored. If a portal reorders its
form next month, one component changes and no stored record is suddenly wrong.

One derived value: the category the second portal asks for, worked out from the
type the user picked. The "live" case deliberately returns the choice
**unresolved** — that portal offers five sibling options there and only the
person who attended knows which one it was. Picking one for her would be this
product inventing a fact about her course.

The certificate downloads under the course title rather than the uploaded file
name, because the uploaded name is whatever the provider's portal generated and
is routinely a reference number, which is what makes a folder of certificates
unsearchable a year later. The title is read from the stored record on the
server, never accepted from the browser — it ends up in a response header.

Tested by mounting the real component and clicking the buttons, not by checking
the text is on screen: a button that copied a trimmed or reformatted version
would pass the easy test and fail the person at the portal, who would not notice
until the form rejected the paste. Seven assertions, including that a blank
field is disabled rather than copying an empty string.

## Acceptance — every number

| Check | Result |
|---|---|
| Unit tests | **368 passed, 0 failed** (50 new) |
| Lint and type checking | **0 errors** (1 pre-existing warning, unrelated) |
| Production build | **green**, 20 pages, three new routes |
| Row-level-security suite | **55 assertions, 0 failures** |
| Artifact audit | **30 PASS, 0 FAIL — clean** |
| Verified-artifact checksums | **8 of 8 unmodified** |
| Branch database assertions | **50, 0 failures** |
| Interactive targets below the 44px floor | **0**, at 390px and 1280px, on every new screen |
| Text below the 12px floor | **0**, at both widths |
| Horizontal overflow at 390px | **none** |

Screens captured at 390px and 1280px: the name-field label before and after, the
credential form with the NBRC sub-dropdown shown, the credential form with the
membership-number field shown, the add-a-course form, and the copy sheet.

## What could NOT be measured, and why

Two acceptance items could not be completed, and neither is a code problem:

- **The interactive walk-through on a throwaway account** — add, edit, attach,
  package, export, erase, with the browser console watched throughout. The
  container's outbound network policy refuses connections to the database host,
  so the application running locally cannot reach the branch at all. The
  database tooling reaches it by a different path, which is why the 50
  assertions above exist; the app cannot.
- **An independent contrast measurement on the new screens.** The screens were
  rendered from the real components with the real stylesheet, but the page
  background is a photograph and the ad-hoc measurement could not read it —
  it returned a ratio of exactly 1.00 for all 35 elements, which is the
  signature of a broken measurement rather than 35 real failures. **That number
  is discarded rather than reported.** What can be said: the new styling
  introduces no new colour pair at all — it reuses tokens the project's own
  contrast suite already enforces against the measured background, and that
  suite passes as part of the 368.

The geometric measurements above — target sizes, text sizes, overflow — do not
depend on the background and are real.

## Deliberately not done

No migration was applied to the live project. The two real accounts were not
touched. No verified artifact was altered. No expiry, status colour or reminder
was given to a course. The per-licence tally was recorded in the task list and
**not built** — a tally reading "18 of 24" beside a licence is one styling change
away from looking like a verdict about someone's standing, which this product
does not issue, so the shape of it is a decision to be made before it is code.
The three surviving false copy claims were reported, not rewritten.

## Waiting on the owner

- Whether to apply both migrations to the live project. **This is the gate.**
- The sign-up page sentence that is live and false.
- Ratification of the supporting form copy — hints, validation messages, empty
  states — written minimally and listed in the build repository's task list.
  None of it has reached a user.
- Two of the three earlier assumptions still stand as defaults; the third was
  answered by the record specification.

A development branch on the database project is billing by the hour while it
exists. It should be deleted once the migrations are applied, or if this work is
parked.
