# 2026-09-16 — Task 3: send to a list, with receipts and a scheduler

**Session type:** build, third of a multi-session run. **Branch only. Nothing
applied to the live project. The two real accounts were not touched.**

## Account check

Unchanged and re-stated: publishing to the studio account, signing with the
studio address, both confirmed rather than assumed. The container's own global
identity is neither, so the repository-local override is the only thing holding
the line.

## The constraint that shaped everything

**The existing single send had to keep working, unchanged.** It does. The route,
the delivery log, its append-only trigger and its frozen status vocabulary are
all byte-identical, and the verification file asserts both of the latter
directly. An account that has only ever sent to one address at a time sees
exactly what it saw before. This is a second way to send, not a replacement for
the first.

That is also why the receipts live in their own table. The delivery log cannot be
rewritten, deliberately — it is the user's own record and a trigger enforces it. A
receipt has the opposite lifetime: written when a send is scheduled, then updated
as the recipient opens and downloads. Putting both in one table would mean
weakening the guarantee that protects the log.

## In the recipient's morning, not the sender's

A subcontractor in Texas sending to a general contractor in California at 9am
their time is sending at 7am the recipient's, which is fine. The same send at 4pm
on a Friday lands at 2pm Friday, and a packet that arrives on a Friday afternoon
is read on Monday if it is read at all.

So the default send time is the next business morning **where the recipient is**,
and every local time is resolved through the platform's own zone database rather
than by arithmetic on offsets. "Add seven hours" is wrong twice a year, and the
wrong answer here is a packet arriving at 3am.

The tests do not check three convenient moments. They sweep **every hour of a
full week in two zones**, asserting each time that the chosen instant reads 7:00
on the recipient's clock, falls on a weekday, and is strictly in the future — plus
both sides of a daylight-saving change, and an hour that does not exist at all on
the spring-forward morning.

**Warnings, never refusals.** Friday afternoon, the weekend and a time already
past are all flagged, in the recipient's week, and none of them blocks anything. A
sender who knows their contact reads mail on a Saturday should be able to say so.

## What the dispatcher will not do

Three rules, carried over from the single-send route because the reasoning
transfers exactly:

- **A send is marked sent only after the provider accepted it.** Marking first
  would leave a receipt permanently asserting a packet went out when a transient
  failure meant it did not — and the receipt is the sender's evidence, so a false
  one is worse than none.
- **A failure is recorded with its reason, not swallowed**, so the sender can see
  that the address with a typo in it never worked.
- **It never retries.** A retry loop on outbound email is how one bad address
  becomes forty messages to a general contractor.

And when mail is not configured, the run does nothing at all and writes no
failures. That outage would be ours, not the sender's, and a receipt full of our
failures is a lie about their recipient's address. This product has shipped with
mail unconfigured before, which is why that case has its own test.

## A receipt is evidence

Its owner can reschedule it before it goes and cannot touch it afterwards. There
is no delete policy **and** no delete privilege, so the attempt is refused
outright rather than quietly filtered — two locks, not one. Deleting a contact
from the book keeps the receipt, and keeps the address it actually went to: "on
the 3rd I sent this and they opened it on the 5th" stays true after the person
changes jobs.

Each recipient gets their own link token, and the database refuses two rows that
share one. That uniqueness is the whole security model of the receiving page: the
token is the capability, so a shared one would let either recipient open the
other's link and credit the wrong receipt.

## The nudge

Its own template module, because the existing email templates file is checksummed
and must stay byte-identical. It says **"not opened yet" and nothing else** — the
product knows a link was not followed, and does not know whether the message
arrived, was filed, or was read in a preview pane. It goes to the **sender**:
nudging the recipient would turn a qualification packet into a chase, which is
the sender's decision and not this product's.

## Found by looking, not by a test

The screenshot pass caught three things no test would have:

1. **The recipient checkboxes were the browser default, 13 pixels, against a
   floor the project calls non-negotiable at 44.** Worse, the first fix was wrong
   in an instructive way: a 24-pixel box with 10 pixels of margin *measures* 44
   across and is not a 44-pixel target, because margin is not hit-tested — the
   finger still has 24 pixels to find. The control is now 44 and draws its own
   mark, while staying a real checkbox so the platform's keyboard behaviour and
   assistive-technology semantics are untouched.
2. **Horizontal overflow at phone width**, caused by an email address having no
   space in it and therefore nothing to wrap at.
3. **A label that rendered as three broken columns**, because the shared label
   component is a flex row and its three spans became columns.

The stylesheet also gained the styles for the surfaces the previous session
added: those classes had been referenced before they existed, which was invisible
in the measurements — nothing was undersized because nothing was a checkbox — but
left the screens leaning on browser defaults.

One more was caught by the project's own forbidden-language check, which greps
the mail directory for compliance words and cannot tell a comment from a subject
line. It flagged a word in a code comment. In a directory whose strings land in a
general contractor's inbox that strictness is right, so the comment moved and the
guard did not.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **484 passed, 0 failed** (45 new) |
| Lock suite | **140 assertions, 0 failures** (was 121) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green**, two new routes |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **byte-identical**, all eleven |
| The existing single-send route | **byte-identical** |
| Tap targets under 44px, at 390 and 1280 | **0** (was 3, see above) |
| Horizontal overflow | **none** (was present at 390) |
| Console errors | **0** |

## Still not possible

A hosted development database still cannot be created; the failure is recorded in
the previous report and nothing about it has changed. All of the above is proven
against a throwaway local database with twelve migrations applied.

The end-to-end walk-through — schedule to three contacts at three times, open one
link as the recipient — needs the receiving page, which is the next task, and a
reachable database, which this container does not have. It belongs to the final
verification pass.

## Pending the owner

Everything user-facing in this session is **[PENDING — owner]** and has reached no
user: roughly thirty strings across the scheduling screen, the receipt headings
and the nudge email. They will be listed as one block with the earlier tasks' in
the gate report.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. **Nothing carrying
a price** — and the migration calls the guard on its own last line, because a send
is the moment a document reaches a general contractor and is where a price would
most plausibly appear.

The receiving page itself is the next task and is not built. The link the emails
point at has no page behind it yet, which is stated here rather than discovered
later.
