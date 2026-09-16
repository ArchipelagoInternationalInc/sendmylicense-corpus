# 2026-09-16 — Task 4b: the logo upload

**Session type:** build. **Branch only. Nothing applied to the live project. The
two real accounts were not touched.**

## Account check

Unchanged and re-stated: publishing to the studio account, signing with the
studio address, both read rather than assumed.

## What this finishes

Task 4 added a column for the sender's logo and a page that never drew it. This
adds every way to fill it and the rendering that makes it worth filling.

**It also closes a correction.** An earlier session recorded the column as
unauthorised scope creep and corrected two filed reports accordingly. The owner
has since corrected that correction, quoting the original task text, which asked
for the logo explicitly. Both the repository and the reports now say so. The
narrower defect underneath still stood and was real: the storage key was being
read from the database and handed across the boundary to a page that ignored it.

## A fourth bucket, not a folder in an existing one

A logo is not a document, and the difference is not tidiness:

- The document bucket caps at fifteen megabytes and admits PDF. A logo is an
  image, and a fifteen-megabyte one is a mistake rather than a choice — so this
  one caps at two and admits no PDF at all.
- A document belongs to a credential or a course and is listed in a packet. A
  logo belongs to the profile, is listed nowhere, and is **the one stored object
  this product shows to somebody outside the account**.
- Account erasure walks buckets. A separate one means deletion never has to
  reason about which objects under a user's prefix were paperwork and which were
  decoration.

It is private, like every bucket here. That is the tempting exception — a logo is
not secret, and a public bucket would save a signed URL — and it stays private
anyway, because the bucket is keyed by account and a public one would turn
guessing an identifier into an account-existence oracle.

## A separate validator, not a widened one

The existing upload validator is a checksummed verified artifact. Widening it to
take a second mode would have changed its bytes and put one function in charge of
two different questions with different answers.

What is reused is the part worth reusing: the function that reads magic bytes,
already verified against real and spoofed files. So there is still one answer to
"what are these bytes actually".

Two stages, as there: the declared intent is checked before an upload URL is
signed, and the bytes are checked afterwards. **The second is the real one.** A
PDF renamed with an image extension and declared as an image passes the first and
fails the second — and the sharp part is that the sniffer *recognises* PDF, so
this surface has to reject it **after** recognition rather than rely on it being
unknown. There is a test for exactly that.

## The key never reaches the recipient

The module that resolves a recipient's link returns a short-lived signed URL, not
a storage key. Its own header promises it returns exactly what the page renders;
returning a key to a page that draws an image would have been the same defect
this task was partly written to fix, in a new place.

## Replacing a logo removes the one it replaced

An orphan in the bucket is a file nobody can see, nobody can delete from the
interface, and erasure still has to carry. The removal runs **after** the row is
updated, so a failure leaves an unreferenced object rather than a row pointing at
bytes that are gone. A cleanup failure is logged, never surfaced: the user's logo
is already correct by then, and telling them the save failed would be false.

## What is NOT covered, stated rather than implied

**The four storage policies are outside the automated lock suite.** The harness
does not simulate the storage layer, which is why the original storage migration
has always been excluded from it too. The verification file says so in its own
header rather than leaving a reader to infer it from an absence. They are the
same shape as the existing ones, which were verified against the real platform,
and the application keeps its own prefix check before anything touches storage
precisely because of the gap.

What **is** covered: the profile column, attacked from the second account across
read, write and delete, with controls proving the lock is about ownership rather
than a table that refuses everyone.

## Two things the screenshots found

1. **The empty state was a sentence, not a state.** It sat one line under the
   description and read as the end of it. It now occupies the same bordered slot
   the preview does, so a reader can see it is a state.
2. **Rendering the panel twice proved the file input's identifier was a
   literal.** The input is visually hidden and the label is the control, so a
   duplicate would have given a button that focuses somebody else's input. It
   now generates its identifier, as the profile form already did.

## Numbers

| Check | Result |
|---|---|
| Unit tests | **539 passed, 0 failed** (13 new) |
| Lock suite | **190 assertions, 0 failures** (was 181) |
| Lint and typecheck | **0 errors** (1 pre-existing warning) |
| Production build | **green** |
| Verified-artifact audit | **CLEAN** |
| Earlier migrations | **untouched** |
| Tap targets under 44px, at 390 and 1280 | **0** |
| Text under 12px, horizontal overflow | **0 / none** |
| Console errors through a full interaction cycle | **0** |

## Pending the owner

Every string is **[PENDING — owner]**: the panel's heading and hints, the four
button labels, and three refusal messages.

## Deliberately not done

No live migration. No deployment. No verified artifact altered. No price
anywhere. The logo is not placed on the PDF cover sheet — that artifact is
checksummed, the task asked for the receiving page, and adding it there would
have been the easy scope creep this run exists to avoid.
