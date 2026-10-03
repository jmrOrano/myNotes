---
type: idea
tags:
  - in-progress
  - idea
  - automation
  - local-government
status: in-progress
Date Generated: September 03, 2026
---
# Certificate of Indigency — Interview & Generation Automation

---
## **The idea**
---
Automating the interview-to-certificate workflow for issuing Certificate of Indigency (and PhilHealth Incapable certification) at the LGU office, where I work mostly alone handling both the client interview and the encoding.

---
## **Source of Idea**
---
Bottleneck: I'm usually the only one doing both the interview (getting family/purpose details) AND the encoding (typing up the certificate in Word, copy-pasting from old templates and manually editing each field). When there are many clients, this becomes a serious backlog. On top of that, family composition in the Philippines is often extended/nuclear-mixed, not simple nuclear-family, which makes the "who counts as household" question genuinely complex — not something a naive form could handle.

---
## **Current manual process**
---
1. Interview client to get details (usually handwritten first, then re-typed later)
2. Determine purpose of the certificate (format changes depending on purpose)
3. Encode family details: full name, age, address, civil status (single / spouses / not legally married / widowed / separated / solo parent)
4. Copy-paste the right Word template, manually edit every field, proofread

Two certificate types exist:
- **General Certificate of Indigency** — highly variable wording depending on purpose (scholarship, medical, SPES, civil registrar, livelihood, burial, legal, utility subsidy, or generic "whatever legal purpose")
- **PhilHealth Incapable Certification** — fixed format, only name + capable/incapable checkbox + date varies

---
## **Audit findings (from 748 records, general indigency file)**
---
Purpose category breakdown (approximate, from actual usage):
- Scholarship/Educational (CHED, TESDA, ESC, SHS Voucher) — largest group
- "Whatever legal purpose it may serve" (generic, no specific purpose) — second largest
- Medical assistance (MHO, PJG Medical Center, AMH Malasakit Center, Congressman's office)
- Civil Registrar (birth certificate)
- SPES (student employment program)
- Financial assistance (Board/Bar exam review, DSWD, Congressman)
- Utility subsidy (Automatic Lifeline Rate, AURELCO reduced billing)
- Burial, Livelihood/Puhunan, Legal assistance — smaller volume

Civil status variants that change wording: Single, Spouses (legally married), Not legally married, Widowed, Separated, Solo Parent.

PhilHealth Incapable file (254 records): fully fixed template, only 3 variables (name, capable/incapable checkbox, date). No further complexity found.

---
## **Interview flow (actual order, as conducted)**
---
1. Purpose of the certificate
2. Barangay
3. Who is the subject (name)
4. Does the subject have a spouse?
   - **Yes** → get the subject's own household composition (spouse + children)
   - **No (single)** → subject is likely still under a parent/guardian's household:
     - Get parent(s)/guardian name(s) and age(s)
     - Determine if parent is: living-cohabiting (counted), living-separately (not counted), or deceased (not counted)
     - Get total extended household count (siblings, aunts/uncles, grandparents if present)
     - For each: relationship to subject, age, whether they have their own job/family (independent = not counted)
     - Aggregated income comes from whoever in the counted household is earning

**Core dependency rule** (confirmed): a household member counts toward family size / income pooling only if BOTH (a) they live in the household, AND (b) they are not already financially independent (no job/family of their own). This single rule replaces what looked like several separate branching cases (parent alive/separated/deceased, sibling with own family, etc.) — those are really just instances of the same two-part test.

---
## **Certificate text structure (General Indigency)**
---
Reference template (actual wording used):

```
THIS IS TO CERTIFY that the family of (spouses) [FATHER'S NAME], [age] and
[MOTHER'S NAME], [age] from [address], San Luis, Aurora belongs to the
poverty group having a monthly income of [income] from [aggregated / odd
jobs / family head's occupation] supporting [number of family] members.

THIS CERTIFICATION has been issued for [purpose] for [relationship to
subject] THE SUBJECT'S NAME.

ISSUED this [date] here at San Luis, Aurora.
```

Key insight (v3 → v4 correction): the certificate has TWO separate roles that must not be conflated —
- **Paragraph 1 subject** = the household HEAD(s) — could be the applicant themself (if married/independent), or a parent/guardian (if applicant is single and still dependent)
- **Paragraph 2 subject** = always the actual APPLICANT/interviewee, referenced by their relationship to whoever was named as household head in paragraph 1 (daughter, son, grandson, nephew, etc.)

Relationship word rule:
- Household head = applicant themself, alone (no spouse, no guardian) → "for the above client"
- Household head = applicant + spouse → repeat applicant's own name
- Household head = parent/guardian (applicant is single & dependent) → applicant's name + relationship word (daughter/son/grandson/nephew/niece/etc.) — this relationship word must be asked explicitly, not derived, since it can't be reliably inferred

---
## **Design principles established**
---
- Purpose format is genuinely case-by-case (e.g. whether a single-with-guardian applicant gets "family of guardian" format vs individual format) — **cannot be fully automated**, needs a manual override toggle rather than a hard rule
- PhilHealth Incapable cert is always individual format regardless of guardian situation — no override needed there
- Full name field needs explicit spelling confirmation during interview — a misspelled name means the certificate needs to be re-signed by the social worker, so this is a real cost, not a nice-to-have
- Free-text case notes field needed, separate from the certificate output — for human judgment on complex family situations (doesn't appear on the printed cert)
- Digital record needs to be searchable by full name, so repeat clients (2nd+ visit) don't need to be re-interviewed from scratch — search existing record, confirm/update details, issue new certificate

---
## **Process so far**
---
Went through 4 prototype iterations (built as interactive artifacts, not yet a deployed tool):
- v1: naive nuclear-family-only model — wrong, didn't fit Philippine extended-family reality
- v2: added dynamic household members list with the cohabiting+independent dependency rule
- v3: added guardian identification as a distinct step + manual format-override toggle
- v4: corrected the household-head vs subject/relationship split in the certificate's 2nd paragraph — this is the current best logic

Working method that got us here: audit real records first (don't design from assumption) → build a working prototype fast, even if imperfect → test it against the actual workflow → let real corrections drive the next iteration. Each correction came from a real gap between the prototype's assumption and the actual interview process — not from guessing upfront.

---
## **Deployment context (confirmed)**
---
This is a self-initiated idea (not assigned by the office) — but the actual use case and deployment target is the **workplace**, not the homelab. Deliberately kept separate from the homelab/Docker setup, since that's a different environment with different constraints. Documenting this thoroughly because it's also intended to become a portfolio/achievement piece once completed.

Physical setup: clients are interviewed just outside the office (in a queue, inside the building but not at a fixed desk) — interview and encoding happen on the spot, not at a stationary workstation. This is why the tool needs to be portable, not tied to a fixed machine.

Confirmed constraints:
- **Device (alpha phase)**: personal laptop, brought to work. Office-machine integration is a possible *later* option once the workflow is validated — not a v1 requirement.
- **Connectivity**: office has internet, but it's explicitly **not to be relied on** — the interview-to-certificate workflow must not stall if the connection drops. Offline-first is a hard requirement, not a nice-to-have.
- **Multi-device sync**: not a priority right now. What matters instead is that each device's records stay in an **easy-to-open, easy-to-merge-later format** (e.g. Excel/CSV) so multiple devices' data could be manually aggregated down the line if needed — no real-time sync required.
- **Correction on terminology**: what was being called "backend" here really means a **local persistence/storage layer** — a local file the app reads/writes on the same machine — not a server or hosted database. No network calls, no hosting involved.

---
## **CHECKPOINT — Architecture decisions (app shape + certificate generation)**
---
*Marks the point where "idea/logic design" transitions into "concrete build plan." Everything below is decided, not just proposed.*

**App shape — Python-based local mini web server:**
- Single self-contained folder. Contents: a small Python script (acts as both local server and the logic for storage/certificate generation), the local data file, and the HTML/CSS/JS frontend served by that script.
- Run by double-clicking a start script (e.g. `start.bat`) — launches the Python server in the background, opens the browser to `localhost`.
- No installation beyond Python itself (one-time). No internet dependency — everything runs on the same machine, nothing calls out to a network.
- Why Python over a plain static HTML file: the same script needs to also generate the certificate (.docx), which requires actual file read/write access that a browser sandbox can't do on its own — so a local script was needed either way, may as well have it serve the UI too and get automatic save (no manual export/import step).
- Portable to other staff later: copying the whole folder to another laptop gives that person their own independent copy (their own script instance, their own local data file) — no shared server, no conflicts. Not a current priority, but the shape doesn't block it.

**Certificate generation — resolved:**
- Signature block: only ONE social worker signs per certificate (not three, as first assumed — confirmed by auditing the actual 748 records: one name is on any given certificate, but it rotates between 3 known social workers depending on who's on duty at the time). Implementation: a dropdown to pick which of the 3 to place in the signature block — no complex multi-signature layout needed.
- ~~Archive behavior~~ **SUPERSEDED — see "Archive decision reversed" in the implementation log below.** (Original plan was one continuously-growing .docx with page breaks per certificate, mirroring the office's manual Ctrl+F workflow. Reversed to one .docx file per client after direct experience with the manual file's performance degrading at scale.)
- "Change of signatory after cert is ready but before issuance" (raised as a nice-to-have, not urgent): since the interview data is logged separately from the generated document, this becomes a non-issue — just regenerate the certificate from the same logged data with a different signatory selected. No special mechanism needed beyond keeping the data log intact.

**CSV column structure — core identity fields locked in (batch 1 of N):**
- Full Name
- Sex
- Age
- Address (Barangay)

Note: "Sex" wasn't previously discussed in the audit or interview-flow notes above — likely needed for correct pronoun handling in the certificate text (his/her, son/daughter) instead of the placeholder "her/his" used in the v4 prototype. Remaining columns (purpose, civil status, guardian info, income, household members, etc.) still to be defined — core identity fields deliberately locked first as the foundation.

**Important distinction confirmed: CSV columns ≠ certificate template variables.** Not every stored field needs to appear in the printed certificate text. Specifically: Sex will be collected during the interview and stored in the CSV (for record-keeping/searchability), but will NOT be inserted into the certificate wording itself — the certificate continues using the existing relationship-word / neutral phrasing logic already established in v4, regardless of the stored Sex value. This distinction (data log is a superset of what's printed) should be kept in mind as more columns get defined.

---
## **IMPLEMENTATION LOG — Core loop build (Phase 1)**
---
*Marks the transition from architecture planning into actual running code.*

**Setup:**
- Project location: `F:\indigency-automation` (separate from the Obsidian vault — this is the actual codebase)
- Git repo initialized for the project (for potential future GitHub upload)
- Stack: Python + Flask (chosen over Node.js despite an existing Node/Express test project, because python-docx will be needed later for certificate generation — one language for the whole backend, fewer moving parts)
- Installed: Flask, python-docx

**What's built and confirmed working (tested live on the actual laptop, not just in a sandbox):**
- Flask server (`app.py`) serving a single-page interview form (`templates/index.html` + `static/app.js`)
- CSV-backed storage (`data/records.csv`) — create/update records, auto-assigns record IDs
- Search by full name (existing record lookup)
- Certificate text generation (preview only — not yet a real .docx) implementing the full v4 logic: household head vs subject/relationship split, guardian handling, cohabiting+independent dependency rule for household counting
- Client-side + server-side input validation (age, income, spouse/guardian ages must be numeric — added after a real bug was caught: a non-numeric age silently produced broken output with no error shown)

**Bugs found through real usage and fixed:**
1. Non-numeric age/income accepted silently, produced broken certificate text with no error — fixed with validation on both frontend (before the request is even sent) and backend (defense in depth)
2. Income wasn't formatted as currency (e.g. showed `2500` instead of `2,500.00`) — fixed with a `format_currency()` helper
3. "supporting 1 members" appeared even when the applicant is completely alone (no spouse, no guardian, no household members) — fixed: the "supporting N members" clause is now omitted entirely when the total household count is exactly 1. Rule confirmed: this is based on the actual household count, NOT on marital status label (e.g. a widowed person living alone should also omit this clause)
4. Age fields were missing "years old" (e.g. showed "JUANA SANTOS DELA CRUZ, 44" instead of "...44 years old") — fixed across all four places ages appear: subject, spouse, guardian 1, guardian 2

**Purpose/Institution dropdown audit (from the 748-record file):**
Replaced the original 10-category purpose grouping with two separate, audited dropdowns (each with an "Others" free-text fallback) after realizing the original categories were too coarse to match real certificate wording:
- **Purpose labels** (16 options incl. Others): Educational Assistance, Scholarship Program, Medical Assistance, SPES, Financial Assistance, Securing Certificate of Live Birth, Late Registration of Birth, Correction of Entry (R.A. 9048), Burial Assistance, Tulong Puhunan Program, Legal Assistance, Senior High School Voucher Program, ESC Program, TES, Whatever legal purpose (generic)
- **Institutions** (13 options incl. Others): Provincial Government of Aurora, CHED-TDP, TESDA Baler, MCRO San Luis, MHO San Luis, Aurora Memorial Hospital, LGU San Luis, Office of the Congressman, PSWDO, PCA, DOST, ASCOT — frequency-ranked from the actual 748 records (Provincial Government of Aurora was by far the most common, ~204 occurrences)
- Kept as two independent dropdowns rather than one combined list, since a purpose (e.g. Scholarship) isn't tied to one fixed institution in practice

**Currently in progress:** manual audit of exact certificate wording per purpose/institution combination. Turned out to be harder than expected — the 748 records don't follow one uniform phrasing even within the same purpose category (e.g. "issued for the purpose of availing X" vs "issued for X" vs special cases like the Board/Bar Exam Review certificate referencing a specific Municipal Ordinance). This confirms the earlier design principle that purpose wording is genuinely case-by-case and not fully automatable — the current generation logic produces a structurally correct but not always word-perfect certificate; manual review/edit before printing is still expected at this stage.

---
## **IMPLEMENTATION LOG — Phase 2 (real .docx generation)**
---
*Covers the transition from text-preview-only to actual downloadable, letterhead-formatted Word documents.*

**Approach:** rather than recreate the office letterhead (seal + DSWD logo + header text) from scratch, JohnMark built two "empty shell" .docx files himself in Word — correct margins, paper size, header/logo already in place — and supplied them alongside the original 748-record archive for reference. `docx_generator.py` opens a fresh copy of the relevant shell per generation and appends the certificate body + signature block programmatically. This split (shell = visual letterhead, code = structural content) avoided the fragility of trying to rebuild a Word header/logo purely from python-docx.

**Signatories — PhilHealth vs General Indigency differ:**
- General Indigency: name + title only ("Social Worker")
- PhilHealth Incapable: name + title + License No. + Valid Until — a 4-line signature block. Title also differs per person, not always "Social Worker":
  - CHRISAN JOYCE P. DELOS SANTOS — Social Worker — License No. 0038895 — Valid until January 2029
  - MYLIN A. LUCQUIAO — Social Worker — License No. 0010659 — Valid until April 2026
  - NIMFHA G. JACINTO — MSWDO — License No. 0011195 — Valid until May 2029

**Formatting bugs found and fixed (through direct screenshot comparison against expected output):**
1. `KeyError: no style with name 'No Spacing'` — the shell files don't have that built-in Word style defined until it's actually been used once in the document. Fixed by setting paragraph spacing (space_before/after = 0) directly rather than depending on a named style existing.
2. PhilHealth checkbox turned out to be a drawing/inline-shape object in the original, not a text character — not practically reproducible via python-docx. Replaced with Unicode ☐/☒ characters, close enough visually and far simpler/more reliable.
3. Paragraph indentation ("THIS IS TO CERTIFY...", "THIS CERTIFICATION...") is a genuine paragraph-level `first_line_indent` (0.5") in the original, not literal leading spaces — audited directly from the source docx's XML and matched exactly.
4. Signature block wasn't spaced/positioned correctly at first — fixed with `left_indent` (2.5") combined with center alignment, matching the audited original.
5. Excess blank paragraphs between "CERTIFICATE OF INDIGENCY" and "TO WHOM IT MAY CONCERN:" — partly leftover in the shell file itself (5 blank paragraphs, trimmed down to 2 via a one-off script) and partly a redundant blank line the generator code was also adding — both fixed.
6. Font wasn't reliably Arial 12pt even after JohnMark set it in the shell's "Normal" style in Word — turned out the shell's Normal style had no font *name* set (only a size), so Word fell back to a system default. Fixed by setting Arial 12pt directly on every run in code, rather than depending on the shell's style being configured correctly.

**Bold/superscript formatting (per explicit spec):** "TO WHOM IT MAY CONCERN:", all names (subject/spouse/guardian/signatory) wherever they appear, and the literal words "THIS CERTIFICATION" (not the whole sentence) render bold; ordinal day suffixes (1st/2nd/3rd/4th...) render as true superscript. Implemented via a `_add_mixed_para()` helper that splits a line into multiple runs at the matched substrings, since a plain paragraph can't have mixed formatting — `generate_certificate_text()` in `app.py` now also returns a `bold_names` list alongside the text, so the docx generator doesn't have to re-parse/guess which substrings are names.

**New wording rule — guardian marital status (single applicant living with guardian):** mirrors the subject's own marital-status wording logic, but applied to the guardian(s) instead:
- One guardian only → ask Widowed or Separated → renders as a parenthetical suffix, e.g. "(Widowed)"
- Two guardians → ask Legally married or Not legally married → "spouses" prefix (no parentheses — corrected from an earlier "(spouses)" formatting mistake) if married, "(not legally married)" suffix if not
This was missed in the original interview-flow audit because it's a distinct question from the subject's own civil status — it only comes up when the subject is single and dependent on two-parent or single-parent guardians.

**Archive decision reversed:** the original plan (see CHECKPOINT above) was one continuously-growing .docx archive with a page break per certificate, mirroring the office's current manual Ctrl+F workflow. This is now explicitly abandoned — JohnMark reported direct experience with that pattern degrading badly in Word as the file scales to hundreds of pages. Since the CSV data log already provides fast, real search (no need to page through a huge Word doc), the actual design is simply **one standalone .docx file per certificate**, which is what `/api/generate_docx` already produces. No further "append" work is needed — this part of the original plan is complete, just different from what was originally scoped.

---
## **Open questions — not yet decided**
---
- [x] **Storage format for the data log**: **CSV — decided.** Meets both requirements cleanly: easy to open (Excel/any text editor, no special tooling), easy to manually aggregate later across devices if that ever becomes necessary. Note: this is separate from the certificate archive .docx — the data log (CSV) is the searchable/editable record; the .docx is the printable/archival output. Implementation note for later: the household members list (dynamic, variable-length per record) doesn't flatten naturally into a single CSV row — will likely need a second linked CSV (e.g. members.csv with a record ID column) rather than forcing it into one row. Not a blocker, just a detail for the build phase.
- [ ] **Data sensitivity**: this is real resident PII (income, family composition, address) — needs at least basic protection even as local storage, since it's beneficiary/VAW-adjacent data
- [ ] **Backup**: what happens if the laptop running this fails/is lost? Old records were at least on paper/Word files before — need to not regress on durability
- [ ] Prototype v4 itself still needs wider testing against more of the 748 records to catch edge cases not yet seen (e.g. purpose types beyond the ones already categorized)
- [ ] **Manual wording audit (in progress)**: going through the 748 records purpose-by-purpose / institution-by-institution to capture the exact phrasing each combination actually uses, since a single generic template doesn't match real usage. Findings to be logged here as they come in.

