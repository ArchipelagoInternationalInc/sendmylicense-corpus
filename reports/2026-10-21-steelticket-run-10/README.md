# 2026-10-21 — Steelticket, run 10: the real database (partial)

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commit:** 9a52d23 (the one-time setup
file).
Nothing merged. Nothing touched the SendMyLicense live site, its database or
Doppler.

**Status: not finished.**

- **Applied:** migrations **0001–0005** (plus one setup step) are in
  Steelticket's new, empty database, and each is proven byte for byte.
- **Not applied:** **0006–0027**. The way this session reaches the database
  will not send them.
- **Waiting on the owner:** pasting one file into the database's SQL editor.
  The verification for that is prepared.

## (a) Were 0001–0027 applied, and did the checks pass?

**No — 0001–0005 only.**

### How the database was reached

- **No direct connection:** this cloud session cannot open a direct database
  connection. Its network proxy carries web traffic only, and its rules say to
  report that rather than tunnel around it. The database password the owner
  supplied was therefore never used. It is held privately and appears nowhere
  else.
- **The Supabase connector instead:** the migrations went through Supabase's
  own connector, which works over the web.

### One setup step, before 0001

The new project started from Supabase's older, looser default for new tables.
Under it, every table hands the **public, signed-out role** full read and
write rights.

- Row-level security would still have blocked those rights.
- But it is not the setup the 339 database checks verified, and those checks
  assert the public role holds none.
- **What I changed:** reset that default to match the existing live project
  exactly.
- **Effect:** it only removes rights.
- **Where it lives:** the repo, as `supabase/setup/new_project_default_acl.sql`
  (commit 9a52d23). It is not a numbered migration, and 0001–0027 stay
  byte-identical.

### 0001–0005: applied and proven

Supabase stores the text of every migration it runs.

- **Proof:** for each of 0001–0005, that stored text has **exactly the same
  fingerprint (MD5) as the repo file**. The database received the verified
  files byte for byte.
- **Measured afterwards:**

| Check | Result |
|---|---|
| Tables | 9, all with row-level security **on and forced** |
| Public (signed-out) role: read/write on any table | **none** (0 of 9) |
| TRUNCATE (the append-only bypass) held by any role | **none** |
| Server role: read/write on every table | 9 of 9 |
| Policies per table | profiles 2, credentials 4, documents 4, reminder rules 4, packages 4, delivery records 2, reminder sends 1, delivery events 1, audit log 0 — as written |
| Append-only triggers | 4 of 4 |
| New-user profile trigger | present |
| Storage buckets | 2, both **private**: documents (15 MB; PDF, PNG, JPEG only) and packages (100 MB) |
| Storage rules | 5, each limited to the owner's own folder |
| Default rights for future tables | identical to the existing live project after its 0003 |

### 0006–0027: not applied

- **The symptom:** the connector timed out on 0006 four times, at the same
  60-second limit each time. **Nothing reached the database**: no change, no
  lock and no half-applied state, checked after every attempt.
- **The likely cause:** 0006 is the first migration with a real `DELETE`
  statement, inside the account-deletion function. The connector holds
  "destructive" statements for the owner to confirm, and that confirmation
  never appeared on the owner's screen.
- **Not a one-off:** most later migrations also contain DROP or DELETE
  statements, so the same would happen to them.
- **Not worked around:** I did not change the verified files to get past the
  confirmation.
- **Tried again with the owner approving each prompt (22 October):** the
  prompt appeared on the owner's screen and was approved. The request still
  timed out at 60 seconds and nothing reached the database, twice more (six
  attempts in all). The approval does not get back to the connector in time,
  so the connector cannot apply 0006–0027 from this session. The SQL-editor
  paste is the remaining way through.

**What is ready for the owner:**

- **The paste:** one file, 0006–0027 in order and each exactly as in the repo,
  to paste into the new project's SQL editor and run.
- **The comparison afterwards:**
  - A fingerprint of my local copy, which has all 27 migrations on the same
    Postgres version (17.11): 20 tables, 206 columns, 130 constraints,
    67 indexes, 77 policies, 17 functions, 23 triggers and 3 buckets, each
    section hashed.
  - The same query then runs on the new project, so any difference shows
    section by section.

### The database checks

- **Not run against the new database, by rule.** The checks
  (`npm run test:rls`) must never run against a cloud database. They insert
  test users and write rows to the append-only tables, rows that can never be
  deleted afterwards.
- **What runs instead:** the read-only measurements above, the comparison
  against the local copy, and Supabase's advisors.
- **Locally:** all 339 checks passed against the same 27 migrations (run 9B).

### Supabase's advisors, at 0001–0005

- **Missing search_path pins, two functions:** migration 0019 fixes these. It
  is pending.
- **`handle_new_user` callable through the API:** this is the trigger that
  creates a profile for each new account. The advisor reports that signed-out
  and signed-in callers could invoke it through the API. A trigger function
  cannot actually run that way (Postgres refuses), so nothing is exposed. Still,
  it is a one-line tidy-up for a future migration, and the owner's call. It is
  the same on the existing live project.
- **The audit log has no policies:** by design. Only the server reads or
  writes it.
- **Performance:**
  - 22 policies call the sign-in check once per row rather than once per
    query. They live in the verified 0001–0002, so changing them is a
    deliberate future pass, not a tidy-up.
  - The unused-index notices are expected on an empty database.

## (b) Every setting the live site needs

**Vercel, the Steelticket project, Production environment.** NEXT_PUBLIC
settings are fixed into the site when it is built, so **redeploy after adding
or changing any of them**.

**Already set by the owner:** `NEXT_PUBLIC_SUPABASE_URL`,
`NEXT_PUBLIC_SUPABASE_ANON_KEY`, `APP_URL`, `EMAIL_FROM`.

- **The second public key:** the code reads only one public key, named
  `NEXT_PUBLIC_SUPABASE_ANON_KEY`. A key under any other name is not read.

**Still to add:**

| Name | Secret? | Where the owner finds it |
|---|---|---|
| `SUPABASE_SERVICE_ROLE_KEY` | **Secret** | Supabase dashboard → the **new** Steelticket project → Project Settings → API Keys. Use the `service_role` key if the public key in Vercel is the legacy `anon` key; use the `sb_secret_…` key if it is the `sb_publishable_…` one. It must come from the new project, never the SendMyLicense one. |
| `NEXT_PUBLIC_APP_URL` | Not secret | The same value as `APP_URL` (the production address, no trailing slash). **Needed:** the hourly scheduled-send job and the "not opened yet" email build their links from this name, not from `APP_URL`. Without it those emails carry broken links. Nothing documented this until now. |
| `CRON_SECRET` | **Secret** | Generated in this run: 64 random characters, given to the owner privately. Not in this report or the repo. **Needed:** both scheduled jobs (daily reminders, hourly scheduled sends and clean-up) refuse every call without it. Vercel Cron sends it automatically once it is set under this exact name. |
| `RESEND_API_KEY` | **Secret** | Resend dashboard → API Keys → create a key with sending access. It must be on the Resend account that verified the sending domain in `EMAIL_FROM`; the sender address is still the old domain until a new one is verified. Whether to create a new key for Steelticket or reuse the existing one is the owner's call. |
| `RESEND_WEBHOOK_SECRET` | **Secret** | Resend → Webhooks → add an endpoint at `APP_URL` + `/api/webhooks/resend` for the **email.bounced** event, then copy its signing secret (starts `whsec_`). Without it the bounce endpoint refuses every call, so bounces are not recorded. |
| `BREVO_API_KEY` | **Secret** | Brevo → SMTP & API → API keys. Without it, the footer newsletter sign-up refuses politely. |
| `BREVO_LIST_ID` | Not secret | Brevo → Contacts → Lists → the list's ID number. The same list the old site uses (run 9). |

**Optional (each has a safe default):**

- `DOCUMENT_MAX_BYTES`: 15728640.
- `DOWNLOAD_URL_TTL_SECONDS`: 900; the code caps it at 900 regardless.
- `DELIVERY_LINK_TTL_SECONDS`: 604800.
- `LOGO_MAX_BYTES`: 2097152.

**Not needed:**

- `SENTRY_DSN`: listed in the example file, but no code reads it.
- `STRIPE_SECRET_KEY` and `STRIPE_WEBHOOK_SECRET`: placeholders only in
  Phase 1.
- `SEED_ALLOWED`: for the seed script only. Never set it in production.

**Not in Vercel, but needed for sign-up to work.** These go in the new
Supabase project → Authentication:

- **URL Configuration:**
  - Site URL = the `APP_URL` value.
  - Add `APP_URL` + `/auth/callback` to the Redirect URLs.
- **SMTP Settings:** turn on custom SMTP with:
  - host `smtp.resend.com`, port 465;
  - username `resend`;
  - password = a Resend API key;
  - sender = the `EMAIL_FROM` address.

  This is how the existing live project sends its sign-up emails. Without it,
  Supabase's own mailer sends only a handful a day.

## (c) The live site already builds from this branch

- **What changed:** since 22 October, a Vercel project for Steelticket builds
  this branch, and its build is served at the production address. **Every
  push to this branch is now a release.**
- **This run's one push:** a database setup file only, so the site did not
  change.
- **The risk until the paste is run:** the site's signed-in pages point at a
  database that has only migrations 0001–0005. Sign-up and the app will fail.
  The marketing pages do not touch the database.

## Left over

- **The owner:** paste the 0006–0027 file into the new project's SQL editor
  and run it.
- **Me, after that:**
  - compare the new database with the local copy, section by section;
  - check permissions on all 20 tables;
  - re-run the advisors;
  - add the results to this report.
- **The owner:** add the settings above in Vercel and the two settings in
  Supabase, then redeploy.
- **The owner's call, any time:**
  - the `handle_new_user` tidy-up;
  - the 22 once-per-row policy calls.
