# 2026-09-16 — Task 1: the documents a subcontractor sends to a general contractor

**Session type:** build, first of a multi-session run. **Branch only. Nothing
applied to the live project. The two real accounts were not touched.**

## Account check, both halves

Stated before the first save, as the rulebook requires:

- **Publishing to:** the studio account. Confirmed by querying the platform for
  the authenticated identity, not by reading a config file.
- **Signing as:** the studio address, held by a repository-local override.
- The last commit on the branch this work starts from carries the same address.

Both correct. No default was trusted.

## Current state, verified rather than assumed

- The two migrations from the previous build (`0008`, `0009`) live on the
  continuing-education review branch. Confirmed by listing the files on each
  branch, not by reading a handoff.
- The blind critic pass on them reached **PASS**: two piece rounds passed, the
  integration round **failed** on a real finding — the lock suite had stopped
  being evidence about the work, because its harness applied only the first
  three migrations while the ninth rewrites one of the policies under test — and
  the integration re-run, after the fix, passed.
- That review branch is green and open.

## What this session built

A new branch from the review branch, so both earlier migrations come along, and
a tenth migration that does three things.

### 1. The price ban, as a mechanism

**A packet is proof of qualification, never a bid.** The moment a general
contractor can read a number off one, it has stopped being proof and become a
bid, and the product has quietly turned into something nobody authorised.

That is not a thing to remember. It is a thing to notice, so it is enforced in
three places:

- **In the database.** The migration adds a function that reads the live catalog
  and refuses any column in the application schema named like money changing
  hands. The migration calls it on its own last line, so a future migration that
  adds such a column fails *at the moment it is applied* rather than at the
  moment a general contractor reads a number.
- **Proven able to fail.** The verification file plants four such columns in
  turn — one per banned word — and requires the guard to refuse each, then
  confirms the schema is clean again. A guard nobody has watched fail is not a
  guard.
- **In the application.** A test scans every migration for column definitions,
  every component for form-field names, and the cover sheet and email templates
  for copy.

**The application half shipped with a real hole and its own can-it-fail
assertions found it.** The obvious word-boundary pattern does not match a name
like `unit_price`, because the underscore counts as part of a word — so the
likeliest name for the exact column the guard exists to stop would have sailed
straight through. It now uses letter boundaries instead, and the four assertions
that caught it are still in the file.

**One deliberate exclusion, and it is the ban rather than a hole in it.** The
public marketing pages are not scanned. The ban is on a price inside a *packet*;
what this product charges its own users lives on a page no general contractor
ever receives, and the brief forbids changing public copy. A guard that forces
you to break a rule in order to satisfy it gets deleted by the next person.

### 2. Thirteen new document types

The paperwork a specialty trade subcontractor is asked for before a general
contractor will take a bid: insurance certificates and endorsements, the
contractor licence with its state, the bonding letter, the safety record and
programme, training cards, tax and business registration, trade and manufacturer
certifications, references and the capability statement.

Same naming rule as the clinical half: **every label names a piece of paper,
never a standing.** A certificate of insurance is what the document is called; it
is not a claim that the cover is in force.

A trade certification also gets a free-text field for the body that issued it,
scoped to that type in both directions by the database. Free text rather than a
list, because the certifying bodies across the trades are many, regional and
changing, and a fixed list would tell a user their real certification does not
exist.

### 3. The first user keeps her vault

The product is being repositioned, not replaced, and there is a real person with
a live account and rows in this table. Every clinical type stays valid, stays in
the picker, and behaves exactly as it did. Most of the new test file exists for
this, because it is the easy thing to break: a widened list that quietly drops a
value, a picker rebuilt from the new half, a form that hides a field her rows
depend on.

## Two judgement calls, recorded because they are arguable

**Expiration is decided by the form, not by the database.** Whether a document
expires varies by type, but the database cannot tell "this type has no expiry"
from "I do not have the renewal in front of me right now". Refusing the second
would make the vault refuse a document the user is holding, which is the opposite
of what it is for. So the column stays optional for everything, and the form
shows the date field a type actually has. A type with no expiry unmounts the
field rather than leaving it blank — an empty date box invites a user to invent a
date, and an invented expiry on a document going to a general contractor is worse
than no date at all. "Expected" never means "required".

**The issuing-state field was deliberately left alone.** It has been offered on
every type since the first migration, rows may already carry it, and scoping it
to the two types that obviously need it would have cleared those values the first
time their owner edited the row. A tidier form is not worth silently deleting
someone's data.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **393 passed, 0 failed** (25 new) |
| Lock suite | **95 assertions, 0 failures** (was 75) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green** |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **byte-identical**, all nine |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px | **0** |
| Horizontal overflow | **none** |
| Console errors | **0** |

Three form states were rendered and photographed at both widths: a document that
expects a date, one that has no date at all, and the trade certification showing
its issuing-body field. The measurements above were taken from those renders, not
asserted.

## What could not be done, and why

**A fresh development database could not be created.** Three attempts, under two
names; each timed out on the platform's side after a minute, and a listing after
each one showed nothing created. Retrying further risks a stray database
appearing late and billing quietly, so it was stopped and recorded instead.

The database work is therefore proven against a **throwaway local database** with
all ten migrations applied — which is the right instrument for this task anyway,
and is what the lock suite has always used. A hosted development branch matters
for the interactive walk-through in the last task, not for this one. It is the
first thing to retry next session.

The previous build's development database still exists and was **not** touched:
applying this work to it would mean the artifact sitting at the owner's gate is
no longer the one that was verified.

## Pending the owner

Every user-facing string written this session is **[PENDING — owner]** and has
reached no user: thirteen type labels, three picker group headings, three field
strings, two validation messages. The full block is in the pull request and is
repeated here for approval as one block.

One open question: **a manufacturer certification has nowhere to record the
manufacturer.** The brief scopes the issuing-body field to trade certifications
only, so that is what shipped. Extending it is a one-line change.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. Nothing renamed.
No public copy changed. Nothing built that carries a price. Tasks 2 through 10
not started — one task per session, as instructed.
