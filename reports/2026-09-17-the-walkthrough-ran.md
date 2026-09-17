# The twelve-step walk-through ran. All twelve pass, and it found two real defects.

**Date:** 2026-09-17
**Branch:** the contractor repositioning branch
**State:** the walk-through is done. Two defects found and fixed, held by tests
proven to fail. Three items left for the owner, none of them build decisions.

## The headline

**The end-to-end walk-through has been run, in full, for the first time.** It had
been blocked three separate times — first on container images, then on an
elevated credential, then on this session's outbound network policy. The owner
supplied the credential and opened the network. It then ran end to end, and
**all twelve steps pass.**

Nothing about that sentence rests on unit tests. It was run against the real
application, built the way it ships, in a real browser.

## How it was run, so the claim can be weighed

- **The production build**, not the development server — so what was exercised is
  what ships.
- **A real browser**, driven automatically, at desktop width, at phone width, and
  again at phone width with reduced motion switched on.
- **Two disposable accounts** against the walk-through environment's own sign-in
  and file storage.
- **Every assertion checked twice**: once against what the screen actually says,
  once against the row the database actually holds. A screen that claims
  something the database does not is the failure mode this guards against.
- The elevated credential came from the environment. It was never printed,
  logged, or written to a file, and it appears nowhere in the repository or here.

## Two things stood in, and both are named rather than glossed

1. **Account creation.** The walk-through environment has no mail transport, so
   the sign-up endpoint answers with a server error. The two disposable accounts
   were therefore created through the administrative interface, already
   confirmed. **Signing in was still done through the real form, every time**,
   and creating an account is not one of the twelve steps.

2. **The email leg of the send.** The mail provider's API is still refused by the
   network policy — only the database vendor was opened. So the delivery links
   were released directly, and the recipient's half of the journey was walked on
   links released that way.

   What that did **not** do is skip the send job. The job was run the way the
   platform runs it, against real rows, and what it does when the provider is
   unreachable was measured: it found all three sends due, sent none, reported
   all three as failed, wrote **no** record claiming a message that never left,
   and marked **no** recipient as sent. It fails closed and it does not lie about
   it. That is worth more than a green tick on a send that could not have
   happened.

## The twelve steps

All twelve pass: importing a book from a spreadsheet; adding documents including
a logo; building a packet; scheduling it to three contacts at three different
times; opening one link as the recipient; asking for a missing document;
fulfilling that request; confirming the receiving page updated; inviting someone
to act on the holder's behalf; having them send; revoking them; and confirming
they then see nothing.

Three details worth keeping:

- A company name containing a comma inside quotes survived parsing, writing and
  rendering — the case that breaks naive spreadsheet imports.
- The three recipients got **three distinct scheduled times** and **three
  distinct links**, which is the whole point of sending to a book rather than a
  mailing list.
- The person acting on someone else's behalf left a receipt saying so, and after
  being revoked could reach nothing and wrote nothing further.

One guard fired during the run and held: scheduling a packet to somebody already
scheduled for it was refused. That was the walk-through's own fixture being
wrong, not the product, and it is recorded because a reader should know the
guard was hit.

## What it found — two defects, both fixed

Both are on the recipient's page. **That is the only screen a general contractor
ever sees**, and it is the one screen no test had ever rendered.

### 1. The recipient's page was shipping without its stylesheet

The page sits outside every route grouping, and each grouping loads the
application stylesheet from its own shell. This page had no shell, so it
inherited only the global reset. Proved from the served page itself: the
stylesheet chunk that is present on the sign-in page is **absent** on the
recipient's page.

That chunk defines every layout and typography class the page uses — and it also
carries the project's **44-pixel minimum for anything you tap**. So the floor was
never applied there. That is precisely why every undersized control found in the
entire walk-through was on that one page, and none on any signed-in screen: five
of them, including the download button and the whole "ask for something else"
form. A general contractor reads this page on a phone, on a jobsite.

**Fixed** by giving the route a shell that loads the stylesheet. Afterwards:
**zero** undersized controls at all three viewports, and the page renders with
the typography and spacing it was designed with.

### 2. The recipient was not told who the packet was from

The page names the sender from a company field, falling back to a display name.
The company field is read in five places across the codebase and **written by
nothing** — no screen, no action, no script sets it. The display name is
optional and a new account has not set one.

So this is not a corner case. It is **every holder who has never opened
Settings**, which is the state every account starts in. What such a recipient got
was a blank main heading, the words "Sent to you by" followed by nothing, and —
for anyone who had uploaded one — a logo with no alternative text at all. The
comment above that image says a screen reader should hear who the packet is from,
because the image is the only thing that conveys it. It conveyed nothing.

**Fixed** by falling back to the packet's own title, which is required,
non-empty, and the sender's own words — and which invents no claim about who they
are, as a placeholder name would. The "sent by" lead and the sender's name line
now appear only when there is a name to follow them.

Both fixes are held by eight new assertions, and the tests were **proven to
fail**: reverting the two changes turns six of the eight red.

## Left for the owner — three items, none of them build decisions

1. **The company-name field is unreachable.** It exists in the data, five modules
   read it, and nothing in the product sets it. Either it gets a field in
   Settings or it should be removed. The fallback above makes the recipient's
   page correct either way.
2. **There is no favicon.** The site has no icon file at all, so every page in
   every browser logs one failed request. The fix is a brand asset.
3. **The receiving page reports a database failure as a bad link.** The code
   keeps the returned row and discards the returned error, so an unreachable
   database renders "this link is not valid" to someone holding a perfectly good
   one, with no signal to the sender. Distinguishing "we cannot check right now"
   from "this link is not valid" is recipient-facing wording, which is an owner
   call under the project's language rules.

## Measured at the end

- Unit tests: **718 passing** across 35 files, up from 710 across 34.
- Lint: **0 errors**, one pre-existing unused-import warning.
- Row-level-security suite: **294 passing** across 11 suites.
- Production build: green.
- Verified-artifact audit: **clean** — 30 passing, 0 failing.
- Undersized tap targets: **0**. Text below the size floor: **0**. Horizontal
  overflow: none. Console errors from the product: **0**.

**No checksummed artifact was touched. Nothing was applied to, or read from, the
live project. Neither real account was reached.**

One change to test tooling rather than the product: the row-level-security
harness forced a locale that Linux containers generally do not have, which made
it fail to create its throwaway database before the suite could start. It now
picks a locale the machine actually has.

The browser driver used for the walk-through was installed for the run and
deliberately left out of the dependency manifest, matching this project's
existing convention of keeping the visual harness in the session scratchpad.

## The walk-through environment — deleted, and what it cost

It is **deleted**. The gate instruction was to remove it once the walk-through
was done; while the run was still blocked that deletion had been put back to the
owner and the ruling was to keep it alive, so once the run succeeded the question
was put once more — deletion being irreversible — and the answer was to delete
it.

- Created the previous evening; **9.12 hours** alive.
- Published rate for a preview environment: **$0.01344 per hour**.
- **Cost: roughly twelve cents.**

That is elapsed-time arithmetic against the published rate. **No tool available
in a session here reads the billing ledger**, so it is not an invoice figure and
is not offered as one.

Confirmed after deletion: the parent lists one environment, the live one, **which
was not touched and was never read from**. Deleting took the walk-through's
disposable data with it — two throwaway accounts and their rows. None of it was
real.
