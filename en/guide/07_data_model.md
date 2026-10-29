# 07 · Data model

The records are the system's memory and part of the company's books. They MUST survive a change of AI provider,
be inspectable without the AI, and be retained like the documents (`12_germany.md` §8).
Store them under `<ROOT>/_office/`.

## Storage choice
- **SHOULD:** a single-file SQL database (e.g. SQLite) — robust, queryable, no server.
- **MAY:** JSON/CSV files, if the machine has no database support. Then write atomically (write to a temp file,
  then rename) and keep one file per record type.
- Whatever is chosen, the core offers an **export to CSV** of each table (`;`-separated, UTF-8 with BOM, German
  number format), so the user or the Steuerberater can open the records in a spreadsheet.
- **Append-only logs**, independent of the database:
  - `hashlog.csv` — timestamp; office ID; file; SHA-256 (one line per original and per derived file)
  - `protokoll.csv` — timestamp; actor (agent/user/system); office ID; action; old value; new value

  They are never rewritten, only appended. On start, the core verifies that their last lines are intact.

## Document
| Field | Notes |
|---|---|
| id | integer, unique, never reused |
| hash | SHA-256 of the original, unique — duplicate detection |
| original_name, source | file name and source (inbox, scan folder, mailbox name) |
| original_kind | `photo` `image` `image_pdf` `text_pdf` `hybrid_pdf` `e_invoice_xml` `mail_text` `office` (03) |
| received_at | timestamp of intake |
| status | `queued` → `filed` (→ `archived` optional) |
| path | current location of the original (queue, then the tree) |
| companion_paths | reconstruction, e-invoice XML/view, validation report |
| doc_date | document date, ISO `YYYY-MM-DD` |
| category, title, sender, reference | from filing |
| amount_gross, amount_net, vat (per rate), currency | optional |
| iban, bic | full values in the record only; shown shortened elsewhere |
| summary | 1–2 sentences in the user's language |
| action | one or more of `pay` `reply` `provide_documents` `deliver` `check` `none` |
| findings | list: § 14 gaps, arithmetic, IBAN change, GiroCode mismatch, e-invoice errors, TSE |
| e_invoice | format (XRechnung UBL/CII, ZUGFeRD profile), validation result, report path |
| reconstruction | status `ok`/`review`, quality score, PDF path, review report path |
| retention_until | `YYYY-12-31`, from the document type (`12_germany.md` §8) |
| paper_original | `aufbewahren` / `entbehrlich laut VD` |
| local_only | true for categories processed only locally (`Personal`) |

## Task
| Field | Notes |
|---|---|
| id | integer |
| doc_id | the document it belongs to (empty for Steuerkalender tasks) |
| recurring_key | e.g. `ustva-2026-11`, unique — Steuerkalender tasks |
| title | user's language; verb + object + counterpart |
| detail | what exactly to do (amount, list of documents, place, Versandweg) |
| due | the printed or legal date (Fristende, payment date, event date), ISO |
| do_by | erledigen bis, computed (`12_germany.md` §3), ISO |
| deadline_basis | how `due` was computed, e.g. "Bescheid 20.10.2026 + 4 Tage → 26.10.2026 + 1 Monat" |
| priority | 1, 2, 3 |
| status | `open` → `done`, or `cancelled` (with a reason, e.g. "ersetzt durch Mahnung #3") |
| blocked_by | task id (e.g. pay waits for the IBAN confirmation) |
| created_at, done_at | timestamps |

## Draft
| Field | Notes |
|---|---|
| id | integer |
| doc_id | the document it answers |
| path | file in `_office/drafts/` (markdown); `pdf_path` once rendered |
| status | `review` → `approved` (→ `sent`, set by the human) or `discarded` |
| delivery_route | ELSTER, Einwurf-Einschreiben, Fax, E-Mail, Post |
| created_at | timestamp |

## Core commands (minimum set)
The implementing AI chooses names and syntax; the runtime instructions list them.

| Purpose | Input | Effect |
|---|---|---|
| init tree | years | creates missing folders |
| intake | (sources from config) | queue new files, hash log, set aside duplicates |
| list | status / category filter | one line per document |
| extract | document id | prints text (with OCR fallback) |
| einvoice | document id | detect, extract XML, validate, render the view, print the facts |
| reconstruct | document id or image files | full pipeline of `13`; prints status, score, crops needing a second reading |
| reconstruct-answer | document id, element, reading | feeds the AI's second reading back; re-renders |
| file | id, date, category, title, sender, reference, amounts, summary, action, findings | moves, renames, writes the summary file, updates the record and the log |
| refile | id, new category/date | moves again, updates the record, the summary file and the log |
| deadline | type, start date, period / explicit date | returns Fristende and erledigen bis with the basis (Bundesland from the config) |
| task add / done / cancel / list | … | manages tasks, rejects exact duplicates |
| draft add / approve / discard / render / list | … | manages drafts; renders DIN 5008 PDF |
| steuerkalender | months ahead | creates or updates the recurring tasks |
| schedule | optional fixed "today" (for tests) | writes `SCHEDULE.md` and `schedule.ics` |
| belegpaket | month | builds `Belege_JJJJ-MM/` with the CSV (`12_germany.md` §11) |
| retention-report | year | lists documents past their retention period (never deletes) |
| status | — | counts: queued, filed, open/overdue tasks, drafts and reconstructions in review, oldest inbox file |
| export | table | CSV |
| verify-log | — | checks the hash log against the files on disk |

All commands validate their input (known category, real date, existing id) and fail with a clear message.
