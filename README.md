# sendmylicense-corpus

SendMyLicense session reports. Public by rule; contains no secrets, documents, infrastructure names, or personal names.

## Layout

- `reports/` — one dated Markdown report per Builder session, named `YYYY-MM-DD-slug.md`.

## The hygiene rule

Every file here is world-readable. Reports carry no secrets or credentials, no
customer or user documents, no server, database, deployment or other
infrastructure identifiers, and no personal names other than the studio's.
A report describes what was done and what was measured; it does not carry the
material the work was done on.

This rule is enforced mechanically, not by memory: a session in the build repo
cannot end until a dated report exists here and passes an automated hygiene
scan. See `.claude/hooks/require-session-report.sh` in the build repo.
