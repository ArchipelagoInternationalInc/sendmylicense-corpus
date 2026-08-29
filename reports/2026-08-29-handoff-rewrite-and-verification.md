# 2026-08-29 — Handoff rewrite and state verification

**Session type:** verification and accounting. No feature work.
**Back-filed.** This session ran before the reports notebook existed; it is
recorded here so the record is not missing its first entry.

## Account check

- GitHub account: `ArchipelagoInternationalInc`.
- Signing identity on arrival: **wrong** — the container came up on the global
  identity, not the studio one. Corrected before any commit.
- All commits from this session are authored with the studio address.

**Finding worth keeping:** the repo-local identity override does not survive a
fresh clone. It lives in the local git config, which is not repository content,
so every new machine or container starts on whatever the global identity is.
The project guide says "do not unset it," and is right, but the failure that
actually happens is *absence on a new checkout*, not deliberate removal.

## The two claims from the ED handoff, accounted for

**1. The CI action-version bump.** The commit exists, is an ancestor of the
default branch, and its CI run passed. It was authored 2026-07-31. It does not
appear in the hosting ledger because the hosting project was not created until
2026-08-04 — three days later. Nothing was lost; the ledger does not reach back
that far.

**2. The "failed Preview deployment."** No Preview deployment had ever existed.
All 18 deployments on the project were production-target. The one deployment on
the orphaned preview project was also production-target and succeeded. The five
blocked deployments were all production and all authored under the personal
address, consistent with the authorship finding already on record.

**Git state:** working tree clean, nothing uncommitted, nothing unpushed, no
stashes. The newest commit at the time was 2026-08-09.

## What changed

One file: the session handoff's "Next exact task" section. It still opened
"Task 8 is complete. Start Task 9" and carried Task 9's pre-flight notes as live
instruction, three weeks after Task 9 shipped. A session reading top-down was
being pointed at finished work.

Rewritten to describe the present. The stale subsections below it were **not**
deleted — they are prior-task archaeology worth keeping — but three of their
claims read as present tense and are false now, and the section now names that
boundary explicitly rather than leaving a future session to discover it by
acting on one.

## Verified before acting

- The production apex returned 200 and served the current build, with the
  no-index directive intact and the standing independence line present.
- Verified-artifact checksums: 8 of 8 unmodified, zero drift.
- The audit script's test-suite section failed in a bare checkout for
  environmental reasons only — no dependencies installed, no local database.
  Recorded so it is not misread as artifact drift.

## Correction accepted

This session's rewrite set a numbered task order. **Sequencing is the PM seat's
call, not the Builder's.** Corrected in the following session: the section now
describes state and defers order to the PM.

## Deliberately not done

No feature work, no migrations, no changes to verified artifacts, no public copy
changes. One file, +54/−23.
