# 2026-09-16 — The gate opened: two migrations applied to the live project

**Session type:** release. **This is the first session in this run that changed
the live project.** It did so on the owner's standing authorization, given for
after critic PASS, which had been reached.

## Account check

Both halves, read rather than assumed. The repo-local commit identity is the
studio address — the one the deployment platform will build from — and the
publishing account is the studio organization, not the personal login. The
repo-local override is the only thing holding that line, since the machine's
global identity is different; `core.hooksPath` is set, so the pre-commit author
guard is armed.

## What was applied

The two migrations that had been waiting at the gate since 2026-09-14: the
widened credential vocabulary with its sub-credential, and the continuing
education table with the document-parent change that goes with it.

**A before-and-after snapshot was taken across every table**, because "additive"
is a claim and a release is the wrong moment to take a claim on trust. Users,
profiles and the audit log came back with identical counts on both sides.
Nothing was rewritten. The new table exists with row-level security **forced**
and four policies; the document parent column is nullable as intended, with the
second parent column present; the two new credential columns are there.

**A note about what "data intact" means here.** The two accounts hold no
credentials, no documents and no packages — their vaults are empty. The audit log
has rows and is unchanged. It would have been easy to write "all user data
preserved" and leave an impression of documents rescued from a risky migration;
there were none, and the honest sentence is the smaller one.

## Then the merge, the deploy, and the check

The branch merged to the default branch, which is itself the deploy trigger —
the platform's own deploy tool is not used here, for reasons a previous session
recorded after it produced unqueryable phantom deployments twice.

The deployment reached READY and took the apex domain. **The authorship ledger
behaved exactly as the project's own documentation says it does**: the merge
commit carries the studio address, and it went READY. That rule has now been
demonstrated once more rather than merely quoted.

The live sign-in page was fetched and returns 200 from the new build, with both
the password and one-time-link paths rendering and the standing independence
line in place.

**What could not be verified, stated plainly rather than glossed:** the two
accounts were not signed into. There are no credentials for them here, and the
brief forbids touching them beyond confirming they work. What was confirmed, from
the database and read-only: both are email-confirmed, neither is banned, neither
is deleted, and both still carry a password — so sign-in is available to them.
That is a different and weaker statement than "they signed in", and it is the
true one.

## One finding, reported rather than fixed

The post-release advisory scan surfaced one item **introduced by this release**:
the new trigger function has a mutable search path. It is not a definer-rights
function and its body touches nothing that could be shadowed, so the practical
risk is low — and it matches two functions that predate it and carry the same
warning.

It was left alone. The instruction for this sequence was to stop and report
rather than improvise a fix on the live project, and a warning discovered ten
minutes after a release is exactly the kind of thing that gets "just quickly"
patched into an unreviewed change. It is recorded for a session that can do it
properly, with the other two, as one deliberate piece of work.

The scan also surfaced items that predate this release and were not touched.

## The development branch is gone, and what it cost

The database branch the gated work was proven on has been deleted. It ran for
**42.8178 hours** at **$0.01344/hour**, which is **$0.5755**.

The rate was read from the platform at deletion time rather than carried forward
from an older note. The figure is therefore elapsed-time arithmetic against a
current rate; the billed amount as the provider finally renders it is only
readable from a dashboard this session cannot reach, and that distinction is
stated rather than papered over.

## The contractor branch, rebased

The five-task branch was rebased onto the new default branch: eleven commits
replayed with no conflicts. The full suite was re-run **after** the rebase rather
than before, because a clean rebase is not evidence that the result works — 526
unit tests, 181 lock assertions, lint and build green, the verified-artifact
audit clean with all eight checksums intact, and one clean migration sequence
with no duplicates.

The rebase also settled a question that had been recorded as a risk: the branch
does **not** revert the approved public sentences that shipped separately. They
came through the rebase intact.

Its pull request was retargeted so its diff reads true, and its description was
corrected for the second time — it had reverted to describing a branch parked on
two questions that have since been answered.

## What the owner settled

Two answers arrived and both removed work rather than adding it:

- **An EIN and insurance policy numbers will not be stored.** The answer bank
  will show a button that opens the stored W-9 or certificate of insurance. No
  encryption decision is needed, because the numbers already live inside
  documents the vault protects. A column would have been a second and weaker home
  for the most sensitive strings in the product.
- **The logo column was authorized**, contradicting an earlier self-correction
  that had called it scope creep. The correction was itself wrong and has been
  corrected in both the repository and the reports that carried it. What was
  genuinely undone is the upload.

The remaining task text now lives in the repository. It had been lost once to a
compacted conversation, and a paraphrase is not something to build a feature
from.

## Deliberately not done

The contractor branch was **not** deployed. No account was touched beyond the
read-only check above. Nothing was renamed and no public copy changed. The
advisory finding was not patched. The migration files themselves were not edited
to satisfy a linter after the fact — what was applied is what was verified.
