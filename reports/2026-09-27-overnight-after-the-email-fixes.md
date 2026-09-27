# 2026-09-27 — Overnight after the email fixes

**Session type:** continuation, watch only. **No code changed. No decisions taken.**

Filed because the calendar turned over while the session was still watching,
and the record has to cover every day it runs. Nothing new to report.

## What happened

The review branch was left after run 5c (yesterday's email fixes: the recipient
disclaimer, the "Sent with Steelticket." footer, and times in the sender's own
time zone). Since then, a check-in every three hours has made the same three
read-only queries. The results have not changed once:

- same head commit;
- both continuous-integration jobs passed;
- the preview-comment check passed;
- the database preview check was skipped, as it always is;
- no reviews and no review threads;
- still a draft.

The check-run identifiers are the same ones recorded when those runs finished.
So these are the original runs, not new ones that happen to agree.

Nothing was pushed, merged, re-run or commented on.

## Still with the owner

- Confirm the run-5c test packet email arrived. The mail service accepted it,
  but the key cannot read delivery status.
- Move the live sending address to the new domain, when ready.
- Apply the pending database changes, in order, when the branch ships.

## Deliberately not done

No code, no merge, nothing touched on the live site or live database, no email
sent, no tracking switched on, and no edits to the Terms or the Privacy Policy.
