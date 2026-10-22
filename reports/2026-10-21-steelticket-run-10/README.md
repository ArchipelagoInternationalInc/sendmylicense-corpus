# 2026-10-21 — Steelticket, run 10: the real database

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commits:**

- 9a52d23: the one-time setup file;
- 90156d0: the 0006–0027 paste file;
- 00913ff: the resume file.
Nothing merged. Nothing touched the SendMyLicense live site, its database or
Doppler.

**Status: done.**

- **Applied:** all migrations **0001–0027** are in Steelticket's new
  database, plus one setup step before 0001.
- **Checked:** the database matches a local copy that has the same 27
  migrations, item for item, in every section measured.
- **Permissions:** correct on all 20 tables.
- **Advisors:** nothing new beyond known, by-design notes (listed below).

## (a) Were 0001–0027 applied, and did the checks pass?

**Yes. All of 0001–0027 are in, and the checks passed.** Details below, in
the order things happened.

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

### 0006–0027: the connector could not apply them

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

### 0006–0027: applied in the SQL editor, then one repair

- **The run:** the owner ran the migrations in the new project's SQL editor,
  from one file that holds 0006–0027 in order, byte for byte as in the repo
  (`supabase/setup/steelticket-0006-0027.sql`, checked by script).
- **The error:** a later run of the same file stopped at 0009 with
  "constraint ... already exists".
- **What a read-only comparison showed:** every object from 0006–0027 was
  already present. The file had been run before. The second run re-applied
  0006 and 0008, then stopped. Re-applying those two had put two objects back
  to their older versions:
  - **the credential-type check:** back to 0008's list; the current list is
    0010's;
  - **the append-only guard:** the same body, but it had lost the pinned
    search path set by 0019 and 0023.

  Nothing from the stopped 0009 was kept.
- **The repair:** `supabase/setup/steelticket-resume.sql` restores exactly
  those two objects, copied word for word from 0010 and 0023.
  - It is safe to run more than once. I ran it twice on the local copy, which
    stayed identical.
  - It ends by printing ok / NOT OK.
  - The owner ran it, and both read ok.

### The comparison, section by section

Every section was read from the new database and from a local database with
0001–0027 applied, on the same Postgres version (17.11):

| Section | Local | New database | Result |
|---|---|---|---|
| Tables | 20 | 20 | identical |
| Columns (type, nullability, default, comment) | 206 | 206 | identical |
| Constraints | 130 | 130 | identical |
| Indexes | 67 | 67 | identical |
| Policies (tables and storage) | 77 | 77 | identical |
| Functions (body fingerprint, settings) | 17 | 17 | identical |
| Triggers | 23 | 23 | identical |
| Who may call each function | 42 | 42 | identical |
| Storage buckets | 3 | 3 | identical |

**Remaining differences: none.**

### Permissions on all 20 tables

| Check | Result |
|---|---|
| Row-level security on and forced | 20 of 20 |
| Public (signed-out) role: any right on any table | **none** |
| TRUNCATE (the append-only bypass) held by any role | **none** |
| Server role: read/write | 20 of 20 |
| Signed-in role | **exactly what the migrations grant**, table by table, for all 20; for example, read and update only on document requests, and no delete on package recipients or calendar feeds |
| Storage buckets | 3, all **private**: documents (15 MB; PDF, PNG, JPEG), logos (2 MB; PNG, JPEG), packages (100 MB) |

The database holds no rows: no users, no stored files, no audit entries.

### Supabase's advisors, at 0001–0027

**Security:**

- **The audit log has no policies:** by design. Only the server reads or
  writes it.
- **One helper function has an unpinned search path:**
  `assert_no_price_columns`, from 0010. It is not a privileged function, and
  0019 did not cover it. A one-line tidy-up for a future migration, the
  owner's call.
- **Signed-in users can call six privileged functions through the API.**
  Five of them are by design: the helper-access checks `rep_acts_for`,
  `rep_can`, `rep_may_view_document` and `rep_may_view_credential`, and the
  calendar-feed lookup. The access rules call them, and 0017 and 0018 grant
  them to signed-in users on purpose.
- **The sixth, the new-account trigger `handle_new_user`:** the advisor says
  both signed-out and signed-in callers could invoke it. Postgres refuses to
  run a trigger function that way, so nothing is exposed. Still a one-line
  tidy-up, and the same on the existing live project.

**Performance:**

- **57 policies** call the sign-in check once per row rather than once per
  query.
- **19 table/action pairs** have two permissive policies: the owner's own and
  the helper's.
- **One foreign key has no index:** a document request's fulfilled-document
  link.
- **34 unused indexes:** expected on an empty database.
- None of these matters at this size. Fixing them means editing verified
  migrations, so it is a deliberate future pass, not a tidy-up.

### The database checks

- **Not run against the new database, by rule.** The checks
  (`npm run test:rls`) must never run against a cloud database. They insert
  test users and write rows to the append-only tables, rows that can never be
  deleted afterwards.
- **What runs instead:** the read-only measurements above, the comparison
  against the local copy, and Supabase's advisors.
- **Locally:** all 339 checks passed against the same 27 migrations (run 9B).

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
- **The database now has every migration,** so nothing on that side stands
  in the way of sign-up.
- **The site still needs the Vercel and Supabase settings listed in (b)**
  before sign-up and the app can work.

## Left over

- **The owner:**
  - add the settings in (b) to Vercel, and the two settings in Supabase;
  - then redeploy.
- **The owner's call, any time:**
  - the two one-line tidy-ups (the `handle_new_user` call rights, and the
    `assert_no_price_columns` search path);
  - the performance pass on the policies.
