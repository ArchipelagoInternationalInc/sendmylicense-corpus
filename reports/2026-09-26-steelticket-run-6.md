# 2026-09-26 — Steelticket, run 6: the packet email, and one word everywhere

Filed 2026-10-01 under the run's own date, as asked.

**GitHub account:** ArchipelagoInternationalInc
**Signing identity:** ArchipelagoInternational@proton.me
**Branch:** the contractor branch. **Commit:** 4d67012.
Nothing merged. Nothing touched the live site, the live database, Vercel or
Doppler. Nothing in this run adds any tracking: the request sent to the mail
service carries no open- or click-tracking fields.

## Step 1 — the packet email, in the owner's words

Both ways a packet email goes out now use the owner's wording exactly:

- **Subject:** *"[Company] sent you their paperwork: [packet title]"*
- **Line:** *"[Company] sent you their company paperwork through Steelticket."*
- **Line:** *"Open it with the button below. The link expires on [date]."*
- **Button:** *"Open the packet"*

The two paths:

- **The immediate send,** to one address at a time.
- **The scheduled send,** to the address book.

**[Company] is one name, used everywhere.** The email and the recipient
disclaimer get the same value:

- The sender's company name.
- If there is no company name, the person's name.
- If there is neither, "the sender".
- Never an email address.

**What this fixed.** Before this run, the two paths named the sender in
different ways:

- The immediate send used the person's name, and fell back to the account's
  email address when there was no name.
- The scheduled send fell back to "A Steelticket user".

**What stayed as it was:**

- **The date** is in the sender's time zone, as in run 5c.
- **The recipient disclaimer** and *"Sent with Steelticket."* are unchanged.

**One thing kept, for the owner to decide.** When the user wrote a note while
putting the packet together, the email still shows it as *"Message from
[Company]: "…""*.

- This line was in the email before this run, and it isn't part of the new
  wording.
- It only appears when there is a note.
- It can be taken out on request.

## Step 2 — "packet" everywhere a person reads it

Every place a user or a recipient reads "package" now says "packet":

- **Tab and headings**
  - The menu tab: "Packages" → **"Packets"**.
  - The page title: "Packages" → **"Packets"**.
  - The list heading: "Packages you've prepared" → **"Packets you've
    prepared"**.
  - The button: "New package" → **"New packet"**.
- **Empty list**
  - "No packages yet" → **"No packets yet"**.
  - "A package bundles a cover sheet…" → **"A packet bundles a cover
    sheet…"**.
- **Packet page**
  - "…when you assembled this package…" → **"…this packet…"**.
  - The removed-file notes: "…in this package… still inside the package." →
    **"…in this packet… still inside the packet."** Both the one-file and the
    several-files versions changed.
- **Delete**
  - Heading: **"Delete this packet"**.
  - Button: **"Delete packet"**.
  - Confirmation box: **"Delete this packet? Anyone you sent it to will lose
    access. This can't be undone."**
  - The box's confirm button: **"Delete packet"**.
  - Error: **"The packet couldn't be deleted. Try again."**
- **Deleted list:** "Deleted packages" → **"Deleted packets"**.
- **New packet page**
  - Title: **"New packet"**.
  - Intro: "…name the packet…".
  - Field: **"Packet name"**.
  - Button: **"Create packet"**.
  - Done box: **"Your packet is ready"** and "…from the packets list."
- **New packet page, when there is nothing to use:** "Nothing to package yet"
  → **"Nothing to put in a packet yet"**, and "A packet is built from…".
  - **Flag:** this one is the only change that wasn't a straight swap,
    because "packet" can't be used as a verb the way "package" was.
- **Packet errors**
  - "Give the packet a name…"
  - "…more documents than one packet holds. Split it into a couple of
    packets."
  - "…more than a packet can hold…"
  - "Couldn't build that packet."
  - "That packet isn't in your vault." This one appears in two places.
- **Send form**
  - Heading: **"Send this packet"**.
  - Button: **"Send packet"**.
  - Warning: "Anyone with the link can download the packet until it
    expires…"
  - Receipts: **"Sent from this packet"**, "You haven't sent this packet to
    anyone yet."
- **Send errors**
  - "That's a lot of packets in a short time…"
  - "…download the packet and email it yourself for now."
- **Deliveries page**
  - "Every packet you've sent…"
  - "When you send a packet to someone…"
  - Button: **"Go to your packets"**.
  - Column label: **"Packet"**.
  - Note: **"This packet has been removed"**.
- **Home and settings**
  - "Nothing is shared until you send a packet yourself."
  - The name nudge: "…so packets you send show who prepared them."
  - The company-name hint: "…cover sheet of packets you send…"
  - Account export, account deletion and the security-record text: "…packets…"
    (three places).
- **Browser tab titles**
  - **"Packets — Steelticket"**.
  - **"New packet — Steelticket"**.
  - **"Packet — Steelticket"**.
- **Site description:** "…send an assignment-ready packet to an employer."
- **Cover sheet:** "Credential Package" → **"Credential Packet"**. This is the
  heading on page one of every download.
- **Downloads**
  - **Export notes:** the README inside the account export now says "Packets
    you assembled…" and "Every packet you sent…".
  - **Download file name:** a packet whose title has no usable letters now
    downloads as **packet.zip** instead of package.zip.
- **The email and the receiving page:** covered in step 1. Neither says
  "package" anywhere.

**How this is checked.**

- **The copy file:** a new test walks every word in the app's copy file and
  fails if "package" appears.
- **The pages:** the screenshot run read the full text of the following pages,
  each at 1280 and 390 px. None says "package".
  - the Packets page (also at 390 with reduced motion)
  - a packet's page
  - the delete box
  - the new-packet page
  - the deliveries page
  - the dashboard
  - the receiving page

### Not changed, on purpose

- **Code and database names, and web addresses**, as asked:
  - the `/packages` addresses;
  - the export file name `packages.json`;
  - audit event names;
  - storage names.
- **The legal pages.** Terms and Privacy are under outside review and were not
  touched; the privacy draft still says "packages".
- **The marketing site.** It is its own run.

## Step 3 — the test email went out

**The send.** One test packet email went to the studio address, through the
app's real send form.

- The browser was set to US Eastern time.
- The test account's company is a made-up example company.

**The first attempt was refused, on purpose.**

- The run-5c test link to the same address was still working, so the app asked
  for it to be stopped first.
- It was stopped with "Stop that link".
- The send then went through.

**The result.** The mail service **accepted** it: it answered with success and
a message id. The receipt is marked sent.

**Exactly as sent:**

- **Subject:** *"Example Framing Co. sent you their paperwork: Prequalification
  packet"*.
- **Expiry:** *"Thursday, October 8, 2026 at 12:36 AM EDT"*.
- **Disclaimer and footer:** the recipient disclaimer naming the same company,
  then *"Sent with Steelticket."*

**How it was captured.** `email-as-sent.png` is the email body exactly as the
app handed it to the mail service, captured from the outgoing request.

- This time it was not rebuilt afterwards.
- The From/To/Subject strip at the top shows that request's own values.
- The link's token is blanked.

**What I can't confirm.** Whether it has landed in the inbox. The mail key can
only send, not read delivery status.

**The key** stayed in the local test settings only.

## Step 4 — tests

- **Unit tests:** 1001 pass, 0 fail.
  - **New:** 10 tests for this run.
  - **Updated:** four older tests, because they pinned the old wording.
- **Database checks (local only):** all 339 pass.
- **Other checks:** type check clean, lint clean, production build passes.
- **Verified-file audit:** clean. Two checksummed files changed, the email
  templates and the cover sheet. Their master copies and checksums were
  updated.

## Review pack (`review-pack/` at the top of this notebook)

- **`emails.txt`:** reprinted.
  - Both packet emails show the owner's wording, with the example company as
    the sender.
  - The immediate-send entry carries a note on the wording and the kept
    message line.
- **`wording.txt`:** reprinted, because the copy file changed.
  - The count rose from 383 to 421: strings that used to match the main
    branch now differ from it.
  - One heading in it is a code name, explained at the top of the file.
- **`legal-pages.txt` and `data-facts.txt`:** unchanged.

## Screenshots

- **The test email**
  - `email-as-sent.png`: the test email as sent, at 640 px wide.
  - `email-as-sent-390.png`: the same email at phone width.
  - `test-email-sent-1280.png`: the packet page right after the send, with
    the new receipt on top and the stopped run-5c link below it.
- **The app**
  - `packets-page-1280.png`, `packets-page-390.png` and
    `packets-page-390-reduced-motion.png`: the Packets page.
  - `packet-detail-1280.png` and `packet-detail-390.png`: a packet's page, with
    the send form, the receipts and the delete section.
  - `delete-dialog-1280.png` and `delete-dialog-390.png`: the delete
    confirmation box. It was opened and closed without deleting anything.
  - `new-packet-1280.png` and `new-packet-390.png`: the new-packet page.
  - `deliveries-1280.png` and `deliveries-390.png`: the deliveries page.
  - `receiving-1280.png` and `receiving-390.png`: the page a recipient opens.

**Browser console:** zero errors across the whole screenshot run.

## Left over

- **For the owner:**
  - Confirm the test email arrived.
  - Decide whether to keep the "Message from [Company]" line.
- **The live From line** is still the old address. Changing it is the owner's
  step.
- **When the branch ships:** migrations 0019–0027 go to the live database, in
  order. This run adds none.
