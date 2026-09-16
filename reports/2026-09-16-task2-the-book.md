# 2026-09-16 — Task 2: the book

**Session type:** build, second of a multi-session run. **Branch only. Nothing
applied to the live project. The two real accounts were not touched.**

## Account check, both halves

Unchanged from the previous session and re-stated rather than assumed: the work
publishes to the studio account, confirmed by asking the platform who the
authenticated identity is; it signs with the studio address, held by a
repository-local override. The container's own global identity is neither, so
that override is the only thing holding the line — which is exactly the failure
the rulebook was written about.

## What was built

The book: the companies a subcontractor chases, the people at them, and what each
one has asked for before.

### Three tables, not one

A company outlives its people — a preconstruction manager moves on and the
company still wants the same paperwork — and what the company asks for outlives
both. Flattening them into one table would lose the requirement when the contact
leaves.

### The attack file was written first, and earned its keep immediately

The verification file is B trying to reach A's book. Every pass in it is a
refusal, with two controls so that a policy which simply denied everyone could
not pass as one that works: A can do the things B cannot, and the
parent-ownership branch is attacked separately from the row-ownership branch.

It failed on its first assertion, and the failure was real. **The first draft
wrote every access policy and no grant.** Policies are not privileges: the
original migration granted the signed-in role access to the tables that existed
then and set no default for later ones, so a table created now starts with none.
The locks looked perfect in the catalog while every screen would have failed with
"permission denied". Nothing but running the attack would have found that.

It also caught, for the second time in two sessions, the trap where a
cross-tenant write is answered by **filtering rather than by raising**. Asserting
"this throws" would have failed while the lock was working perfectly — and worse,
would have passed for a hundred wrong reasons. Those assertions now run the write
and then look at the row, from the side that can actually see it.

### Merge, not double — as a guarantee rather than an intention

Two unique indexes: one company per name per user, case- and
whitespace-insensitive; one address per person per company, lowercased. The
import's merge keys are written to match those expressions **exactly**, because a
plan that disagrees with the constraint previews "2 new companies" and then dies
halfway through on a duplicate key, leaving a half-written book.

**Contact uniqueness is scoped to the company, not the whole book.** A shared
estimating address genuinely belongs to two different companies, and a book-wide
rule would refuse the second one and quietly lose a real contact. Within one
company, the same address twice is the duplicate the import exists to collapse.

### The import shows a plan before it writes anything

The file is parsed in the browser and the plan computed from it, so the user sees
exactly what would happen while their book is still untouched. An import that
silently created forty companies because a column was mis-mapped is not something
anyone can undo by hand.

It also means the file never leaves the device unless the user confirms. A
contact list is the user's commercial relationships; sending one to a server
merely to look at it would be taking something the product does not need.

The parser handles what exported files actually contain: quoted commas, newlines
inside quotes, doubled quotes, Windows line endings, a leading byte-order mark,
blank rows, and a last row with no newline. Without the byte-order mark handling,
the first column header matches nothing and every value carries a trailing
carriage return — which then becomes part of an email address and makes every
send fail.

### A drift risk, treated as one

The requirement list restates the document-type vocabulary from the vault. A
future migration that widened one and forgot the other would leave a user unable
to record that a company requires a document they can file. So the verification
file pulls **both** constraint definitions out of the database's own catalog and
fails if the value sets stop matching.

### Erasure

Deleting an account already cascades from the authentication table, and the new
tables inherit that without any change to the erasure function. But "it should
cascade" and "it cascades" are different claims, so a third user is created,
given a book, erased, and checked — with a control proving another user's book
survived the same operation.

## What the tooling caught that review did not

The production build rejected the work with an error no amount of reading would
have produced: a server-actions module may export **only** async functions, and a
type exported from one fails the build even though the type itself is erased at
compile time. Both types moved to the validation module.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **439 passed, 0 failed** (46 new) |
| Lock suite | **121 assertions, 0 failures** (was 95) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green**, four new routes |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **byte-identical**, all ten |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px | **0** |
| Horizontal overflow | **none** |
| Console errors | **0** |

Three screens rendered and photographed at both widths. One change came from
looking at them rather than from a test: the document-type field and its button
carried the same sentence, which reads as a stutter. Fixed before commit.

Route protection is asserted by a test that reads the route directories off disk
and requires each to be guarded — not by looping over the list of guarded routes,
which would prove nothing. The new area is covered by that.

## What could not be done

**A development database still cannot be created.** A fourth attempt, under a
third name, timed out on the platform's side exactly as the first three did, with
nothing created. Four failures under three names is a platform fault rather than
a fluke, and further retries risk a stray database appearing late and billing
quietly. The database work is proven against a throwaway local database with all
eleven migrations applied.

## Pending the owner

Everything user-facing in this session is **[PENDING — owner]** and has reached no
user: roughly seventy strings across the book, its forms, and the import. They
are held in one content module and will be listed as one block, together with
Task 1's twenty-one, in the gate report.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. No public copy
changed. **Nothing carrying a price** — and the migration calls the price guard on
its own last line to prove the three new tables did not smuggle one in.

Not built, because they belong to later tasks: sending to a list, receipts,
scheduling, the receiving page, and the packets-sent history on a company's page.
That last one is deliberately absent rather than stubbed — an empty "Packets sent"
heading reads as "we lost them".
