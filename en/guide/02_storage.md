# 02 · Task 1 — Storage tree

## Questions to ask (build phase 2)
- **Name** of the main folder (default `Buero`).
- (Everything else is in the interview of `01_build_process.md` phase 2 and `12_germany.md` §2.)
- **Storage unit**: local folder or Google Drive. (Other cloud drives — OneDrive, Nextcloud, Dropbox —
  work the same way as Drive: a synced folder.)
- **Location** (full path).
- **Categories** — keep defaults or adjust.

## Layout (MUST)
```
<ROOT>/                                 main folder, name from the user
├── _Eingang/                           inbox: scans and other new files land here
│   └── email/                          attachments fetched from mailboxes
├── _office/                            the system's own data — not for manual editing
│   ├── queue/                          registered, not yet filed
│   ├── drafts/                         reply drafts waiting for review
│   ├── duplicates/                     files recognised as duplicates
│   ├── reconstruction/<id>/            intermediate results and review reports of photo → PDF (13)
│   ├── hashlog.csv                     append-only hash log of every original (12 §8)
│   ├── protokoll.csv                   append-only activity log (12 §8)
│   ├── Verfahrensdokumentation.md      GoBD process description (12 §8)
│   └── records (database or files, see 07)
├── SCHEDULE.md                         priority list (06)
├── schedule.ics                        calendar file with reminders (06)
└── 2026/                               one folder per year
    ├── 01_Januar/
    │   ├── Rechnungen_Eingang/
    │   ├── Rechnungen_Ausgang/
    │   ├── …every category…
    │   └── Sonstiges/
    ├── 02_Februar/
    ⋮
    └── 12_Dezember/
```
Rules:
- The **year level** above the months is required, so years never mix.
- Month folders are numbered `01_` … `12_` so they sort correctly; month names follow the configured
  language (German default: Januar, Februar, März, April, Mai, Juni, Juli, August, September,
  Oktober, November, Dezember).
- Every month has **every** category folder, even if empty — people browse by month.
- Building the tree MUST be idempotent: re-running adds missing folders and never removes or renames.
- Create the current year; from October on also the next year. New years are added on demand or
  automatically on the first document of a new year.
- Folder names avoid characters that break on other systems (`/ \ : * ? " < > |`). Umlauts are allowed
  (test them on the chosen storage; fall back to `ae/oe/ue` if the storage mangles them).

## Default categories (SHOULD) — Germany
| Folder | Contains |
|---|---|
| `Rechnungen_Eingang` | invoices and e-invoices received, Mahnungen, credit notes received |
| `Rechnungen_Ausgang` | invoices the company issued |
| `Kassenbelege` | Kassenbons, Quittungen, Bewirtungsbelege, Tankbelege |
| `Finanzamt` | Finanzamt, Bundeszentralamt für Steuern: Bescheide, Anforderungen, Prüfungsanordnungen |
| `Behörden` | Stadt/Gemeinde (Steueramt, Gewerbe-, Ordnungs-, Bauamt), Zoll, IHK/HWK, Beitragsservice, Lebensmittelüberwachung, Schornsteinfeger |
| `Sozialversicherung` | Krankenkassen, Minijob-Zentrale, Deutsche Rentenversicherung, Berufsgenossenschaft, Agentur für Arbeit |
| `Bank` | statements, direct-debit notices, loan letters |
| `Versicherungen` | policies, premium invoices, claims |
| `Verträge` | contracts, terms, cancellations, leasing, telecom/energy contracts |
| `Personal` | staff matters, payroll, applications — **local processing only** by default (12 §12) |
| `Lieferungen` | delivery notes, order confirmations, delivery notices |
| `Recht_Gericht` | Mahnbescheide, court letters, Inkasso, lawyers, Abmahnungen |
| `Steuerberater` | letters and requests from the Steuerberater, Auswertungen (BWA) |
| `Briefe` | other business letters, especially ones needing a reply |
| `Tickets` | travel, events, support/service tickets |
| `Info` | newsletters, price lists, notices without action |
| `Sonstiges` | everything else and unreadable documents |

The user may add, rename or remove categories. Removing a category never deletes its folders.

## Local storage
A normal folder in the user's home. Nothing else to do. Recommend a backup (11_operations).

## Google Drive (or another cloud drive)
The system only needs a **path**. Make Drive appear as a folder:
- **Linux**: `rclone` with a mount (`rclone mount <remote>: ~/GoogleDrive --vfs-cache-mode full`) run as a
  user service so it survives reboots, or `rclone bisync` on a timer for a local copy that syncs.
  The user must do the browser login in `rclone config` themselves (🔐).
- **Windows / macOS**: the official "Google Drive for desktop" app provides a drive letter / folder.
- Alternative for any OS: keep storage local and sync it to Drive on a schedule.

Requirements when using a cloud drive:
- **GoBD:** a cloud folder is not a revision-safe archive by itself. Enable the storage's version history, keep
  the hash log, restrict write access, and keep an independent backup (12 §8). The handover says so.
- **DSGVO:** cloud storage processes personal data on the company's behalf → AV-Vertrag with the provider,
  EU region where available (12 §12).
- Only **one** machine runs the system against the same root (the records are not built for
  concurrent writers).
- Verify after setup: a file written into the root appears in the Drive web interface.
- Keep the records inside the root so they are backed up with the documents; if the record format
  is a single-file database, make sure the sync tool does not corrupt it while open (mount with a
  write cache, or keep the database local and copy a snapshot into the root after each run).
