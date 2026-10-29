# 01 · Build process

The implementing AI executes these phases in order. Each phase ends with a line in `install.log`:
`2026-10-29 00:40 PHASE 4 ok — tree built at ~/Buero (2026, 2027)`.

🔐 marks an approval gate (rules and message format: `09_safety.md`). Everything else runs without asking.
Every phase MUST be re-runnable: check what already exists, skip what is done, log "already done".

Phase 0 — the language question — was already asked by the root `AGENTS.md`. Talk to the user in the **chosen
language** (short, phone-friendly). The build log may be English. (The German version of every message below is in
`de/anleitung/01_bauablauf.md`.)

---

## Phase 1 · Survey the machine
Find out, without changing anything:
- operating system and version, shell, user home, free disk space
- the available runtimes (e.g. Python, Node, Java for the e-invoice validator) and git
- document tools: PDF text extraction, OCR engine **with German language data**, image processing (OpenCV or
  similar), a QR/barcode decoder, a vector tracer (potrace), a PDF/A validator (veraPDF)
- fonts: the metric-compatible free fonts from `13_reconstruction.md` (Liberation, Carlito, Caladea, …) and any
  licensed fonts already installed
- the AI tool you are running in: does it support sub-agents, custom commands, a headless mode, and can it look at
  images (for the second reading in reconstruction)? See `08_agents_and_providers.md`.
- whether a previous Office Helper build exists (look for `install.log` and the implementation folder)

Report one summary line, e.g.
`Linux Mint 22 · Python 3.12 · Java ✘ · pdftotext ✔ · OCR ✔ · deu ✘ · OpenCV ✘ · potrace ✘ · Fonts: Liberation ✔ Carlito ✘ · Agent: Codex CLI (no sub-agents, can see images)`.

## Phase 2 · Interview the user (one message)
Ask everything at once, numbered, each with a default. The user can answer "defaults".
```
Setting up Office Helper — answer each or reply "defaults":
1. Company name? [Meine Firma]
2. Name of the main folder? [Buero]
3. Storage: local folder or Google Drive? [local]
4. Where? [~/Buero | Drive: a synced folder, see en/guide/02_storage.md]
5. Categories: German defaults (en/guide/12_germany.md §9) or changes? English folder names instead? [German defaults]
6. Email accounts to watch? (provider + address each, or "none") [none]
7. Other scan folders? (e.g. ~/Scans, the scanner's network folder) [none]
8. Reconstruct photos/image scans into real PDFs automatically? (always / on request) [always]
9. Daily automatic run? (time, weekdays) [no]
10. Which AI does the automatic runs? (this tool in headless mode, or an API provider — en/guide/08_agents_and_providers.md) [this tool]
11. Where should the system's code live? [~/office-helper-system]
```
Plus the **Germany settings** a)–i) from `12_germany.md` §2, in the same message. The language is already set.

Write the answers into the configuration file in the implementation folder
(`shared/templates/config.example.toml` is the template). Show the result in ≤ 12 lines. Never put passwords in it.

## Phase 3 · Install system tools 🔐
Install only what Phase 1 found missing and the specs need, in one gate for all packages:
- PDF text extraction, OCR + German language data (`deu`)
- image processing, QR/barcode decoding, potrace
- the free fonts for reconstruction
- a Java runtime and the KoSIT validator with the current XRechnung configuration
- veraPDF
- for Google Drive: a sync/mount tool

If you cannot run privileged commands yourself, give the user the exact command and wait for "done" ("fertig").
Downloads from GitHub or official sites (validator, its configuration, test files, fonts) are part of this gate.
Name each source.

## Phase 4 · Storage 🔐 (conditional)
Build the storage per `02_storage.md`.
- A new, empty local folder in the user's home → no gate.
- A folder that already exists with content, or one outside the home → 🔐.
- Google Drive → 🔐 for each step that needs the user (the account login in the browser, starting a background
  mount or sync service). You cannot do the login yourself.

Verify:
- one month folder shows every category
- the inbox exists
- a test file written to the root can be read back (for Drive, it also appears in Drive)
- a test file name with umlauts survives

## Phase 5 · Build the core
Implement the deterministic core in the implementation folder, following `03`–`07`, `12` and `13`:
- **Tree:** create and extend it (idempotent)
- **Intake:** collect from all sources into the queue, with hashing, the hash log and duplicate detection
- **Text extraction:** OCR fallback
- **E-invoices:** detection, XML extraction from ZUGFeRD PDFs, validation, readable view
- **Reconstruction pipeline** (`13_reconstruction.md`): rectification, layout, OCR with confidences,
  typography and font matching, graphics and codes, the document model, the PDF renderer with provenance, the
  verification and the review report. The second reading is done by the AI layer through a command that hands
  out crops and accepts answers.
- **Filing command:** moves a queued document to its folder, writes the summary file, updates the records and
  the activity log
- **Records:** for documents, tasks and drafts (`07_data_model.md`)
- **German calculators** (`12_germany.md`):
  - deadlines (§§ 187–193 BGB, § 108 AO, the 4-day rule) and Fristende / erledigen bis
  - holidays per Bundesland, bank working days
  - the Steuerkalender
  - retention dates
  - IBAN, USt-IdNr. and Steuernummer checks
  - GiroCode decoding and checking
- **Schedule:** `SCHEDULE.md` in the user's language and `schedule.ics` (`06_schedule.md`)
- **Exports:** CSV exports and the monthly Belegpaket for the Steuerberater
- **Status command**

Requirements for the core:
- MUST be usable from the command line, so any AI tool (and a human) can call it.
- MUST reject invalid input (unknown category, impossible date) with a clear message.
- MUST NOT need network access except for email fetching and the configured storage.
- SHOULD use few dependencies: the standard library and well-known packages for OCR, image processing and PDF.
- SHOULD print short, parseable output (one line per result) so agents can read it.

Write a short `README` in the implementation folder listing every command with one example each.

## Phase 6 · Install the agent team
Create the runtime instructions from `en/templates/agent/`: the orchestrator, the six roles and the workflows.
Fill in the placeholders with the real commands from Phase 5. Then wire them into the user's AI tool as
described in `08_agents_and_providers.md` (instruction file, custom commands, sub-agents — whatever that tool
supports). Store them in the implementation folder.

## Phase 7 · Acceptance tests (sandbox)
Run all tests of `10_acceptance_tests.md` against a **temporary copy** of the configuration with a throwaway root.
Use:
- `examples/test-documents/` and `examples/reconstruction/`
- the official e-invoice test files downloaded in Phase 3

Every MUST test has to pass. Fix and re-run until they do. Delete the sandbox afterwards. Record the results,
including the reconstruction scores, in `install.log`.

## Phase 8 · Connect email 🔐
For each account:
1. Tell the user where to put the password: a secrets file only readable by them, never in the config, never in
   git.
2. Tell them which kind of password the provider needs (e.g. Gmail: App-Passwort).
3. Wait for "done". Verify the password is set without reading it, and test the login.

The first fetch is the first contact with real data → 🔐 (state: only mails since <date>, nothing is deleted).

## Phase 9 · First real run 🔐
Show how many files are waiting in the inbox and the mailboxes. After approval:
1. Run the full daily workflow: intake → reconstruction/filing → cases → schedule.
2. Show the run report and the top of the schedule.
3. Ask the user to spot-check two filed documents, one reconstruction (photo next to PDF) and one draft.

## Phase 10 · Automation 🔐 (only if asked in Phase 2)
Set up the daily run with the user's chosen AI (`08_agents_and_providers.md`, section Automation).
Show the exact scheduled-job entry before adding it, and run it once manually first.
If real documents go to an API provider for the first time, that is part of this gate.

## Phase 11 · Verfahrensdokumentation and handover
Fill in `shared/templates/verfahrensdokumentation.md` with the real setup and save it as
`<ROOT>/_office/Verfahrensdokumentation.md` (`12_germany.md` §8). Then send the user:
```
✅ Office Helper is set up
Storage: <root> (<local | Google Drive>)
Tree: <years>, <n> categories per month
Sources: inbox [+ <n> mailboxes] [+ <n> scan folders]
Photo → PDF: <always | on request>
Daily run: <time + AI | manual>
Tests: <n>/<n> passed · Reconstruction: quality <score>
For you:
  • Scan or photograph paper into <root>/_Eingang, then say "daily run"
  • Say "review drafts" for reply drafts (letters stay German)
  • Subscribe to / import <root>/schedule.ics in your calendar (reminders)
  • Go through the Verfahrensdokumentation with your Steuerberater: <root>/_office/Verfahrensdokumentation.md
  • Keep the paper originals for now, until your Steuerberater has approved the procedure
  • Note: Office Helper supports GoBD but is not a certified archive. Please keep an extra backup.
  • Data protection: check the data processing agreement (AV-Vertrag) with <AI provider/storage>
  • Open points: <from the build log>
```
(In German: the message in `de/anleitung/01_bauablauf.md`.)

---

## Updating an existing build
When this guide changes (`git pull`):
1. Re-run Phase 1.
2. Compare the specs with the implementation and list the differences.
3. Apply them. Re-run the acceptance tests.

Changes that touch existing documents or records are 🔐. Update the Verfahrensdokumentation (new version, change
history).

## Uninstall (every step 🔐)
Remove scheduled jobs, stop the mount/sync services, remove the agent wiring. **Never delete the storage root,
the records or the logs.** They are the company's books and must be retained (`12_germany.md` §8). Delete only on
explicit request, with the user typing the folder path back as confirmation.
