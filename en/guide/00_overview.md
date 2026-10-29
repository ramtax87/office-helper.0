# 00 · Overview

## Purpose
A small German company receives paper and email every day:
- invoices, Mahnungen, e-invoices (XRechnung/ZUGFeRD)
- letters from the Finanzamt, the city, the Krankenkasse and the Berufsgenossenschaft
- delivery notes, contracts, Kassenbons, newsletters, now and then a Mahnbescheid

Office Helper is an AI assistant that:
- **puts every document in the right place**
- **turns photos of paper into real, searchable PDFs**
- **works out what has to be done and by when, under German law**
- **prepares the answers**
- **keeps one prioritised list of deadlines**

So nothing is lost and no Frist is missed.

The user stays in control: the system prepares; the human sends, pays and signs. Everything the user sees is in
the language the user chose (German or English); letters to German recipients are always German.

## The six jobs
| # | Job | Spec |
|---|---|---|
| 1 | Build a storage tree: main folder → year → 12 months → categories | `02_storage.md` |
| 2 | Receive documents from several sources (scanner, phone, email, e-invoices, folders) | `03_intake.md` |
| 3 | Read each document and file it by date and content | `04_filing.md` |
| 4 | Handle each document as its own case with sub-agents; draft replies for review | `05_cases.md` |
| 5 | Keep a priority schedule with deadlines, a reminder and a short summary per task; German tax calendar | `06_schedule.md` |
| 6 | Reconstruct photos and image scans into real PDF documents (text, fonts, layout, vector) | `13_reconstruction.md` |

The German legal and practical rules that shape all six are in `12_germany.md`:
- deadline calculation, Bekanntgabefiktion, holidays
- the e-invoice rules
- GoBD, retention periods and the Verfahrensdokumentation
- invoice checks (§ 14 UStG) and IBAN fraud protection
- DIN 5008 letters
- the handover to the Steuerberater and DATEV
- DSGVO

## Architecture

```
 Scanner / phone photo        Email (IMAP, several accounts)        Extra folders / ELSTER downloads
          │                           │                                      │
          └────────────────► INBOX (_Eingang) ◄───────────────────────────────┘
                                     │
                     [intake]        │  copy in, hash + hash log, deduplicate, register
                                     ▼
                                  QUEUE
                    ┌────────────────┼────────────────────┐
           e-invoice XML /           │               photo / image scan /
           ZUGFeRD PDF               │               image-only PDF
                    │                │                     │
          [filing] validate,         │        [reconstruction] rectify → OCR consensus →
          read XML, make view        │        fonts/layout → vector PDF + provenance + review
                    └────────────────┼─────────────────────┘
                     [filing]        ▼  facts → category, action, Fristen, § 14 check, IBAN check, retention date
       ROOT / 2026 / 10_Oktober / Rechnungen_Eingang / 2026-10-28_Kartoffel_Mueller_Rechnung_1205.pdf (+ .md)
                                     │
                     [case]          │  one case per document: tasks, related documents, DIN 5008 reply draft
                                     ▼
                         TASKS  +  DRAFTS (status: review)
                                     │
                     [scheduler]     │  Fristende / erledigen bis, Steuerkalender, holidays, reminders
                                     ▼
              SCHEDULE.md (user's language)  +  schedule.ics  +  monthly Belegpaket for the Steuerberater
```

There are two layers, kept apart on purpose:
- **Deterministic core.** This is code the implementing AI writes. It covers:
  - folders, moving files, records, hash log and activity log (07)
  - the deadline calculator and holiday calendar
  - the e-invoice validation
  - the reconstruction pipeline's measuring, rendering and checking steps
  - the schedule, the calendar file and the exports

  Same input → same result.
- **AI layer.** This is the agent team (08). It reads and understands documents, decides the category and the
  action, writes summaries and drafts, and does the second reading of uncertain text. It changes state **only
  through the core**. Legal deadlines are computed by the core, not estimated by the AI.

## Design principles
1. **Provider-neutral.** The instructions are plain markdown and the core is ordinary code. Switching the AI
   provider must not require migrating data.
2. **Nothing invented.** Every date, amount and number comes from a document. Reconstructions reproduce the paper,
   errors included.
3. **Originals untouched.** Files are moved and renamed, never edited. Hashes prove it. Every filed document gets
   a summary next to it, in the user's language.
4. **Human in the loop.** Sending, paying, signing, submitting and destroying paper: always the human.
5. **Readable without the system.** The folder tree, file names, summaries, the schedule and the
   Verfahrensdokumentation must make sense to a person, a Steuerberater or a tax auditor opening the folder, even
   if the AI is gone.
6. **Idempotent.** Running any step twice must not duplicate documents, tasks or folders.
7. **Made for Germany.** German law, deadlines and formats (`12_germany.md`); conversation in German or English, as chosen at the start.
