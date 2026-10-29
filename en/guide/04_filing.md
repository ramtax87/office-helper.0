# 04 · Task 3 — Reading and filing

Goal: each queued document is understood and stored in `<YEAR>/<MONTH>/<CATEGORY>/` under a meaningful name. It
gets a summary in the user's language, the German checks, and an **action class** for the case step.

## 1. Get the content (by `original_kind`, see 03)
- **`e_invoice_xml` / `hybrid_pdf`:**
  1. Validate with the official validator. Store the report as `…_Pruefbericht.html`/`.xml`.
  2. Read **all facts from the XML**.
  3. Generate the readable view `…_Ansicht.pdf`. For hybrids, compare the PDF picture with the XML; a mismatch is
     a finding (`12_germany.md` §5).
- **`photo` / `image` / `image_pdf`:** reconstruct first (`13_reconstruction.md`). Use the reconstruction's
  verified text. If the reconstruction is in status `review`, still file the document, but the critical fields
  carry `[PRÜFEN]` and the case step adds "Rekonstruktion prüfen".
- **`text_pdf`, text, mail:** the text layer or text. If it is empty or nonsense, treat it as `image_pdf`.
- **Nothing readable:** category `Sonstiges`, action `check`, summary "nicht lesbar – manuell prüfen".

## 2. Extract the facts (only from the document)
| Field | Rule |
|---|---|
| document date | the date printed on it: Rechnungsdatum, Briefdatum, Bescheiddatum, Lieferdatum. Not the scan or mail date. If there is none: the received date, and the summary says so |
| sender | short name of the company or authority, e.g. "Finanzamt Paderborn", "Kartoffel Müller GmbH". A Mahnbescheid: the court **and** the Antragsteller |
| type / category | one configured category (table in §3) |
| reference | invoice no., Aktenzeichen, Geschäftsnummer, Kundennummer, Steuernummer reference, ticket no. — whatever the sender asks to be quoted |
| amounts | gross; for invoices also net and VAT per rate; payments to the Finanzamt (Abschlusszahlung, Vorauszahlung) |
| deadlines | payment date, Skonto date, answer deadline, legal remedy period (from the Rechtsbehelfsbelehrung), delivery or appointment date. **Computed by the core's deadline calculator** (`12_germany.md` §3), not estimated |
| bank data | IBAN/BIC from the document or its GiroCode; checked (mod 97), stored in the record, shown shortened |
| summary | 1–2 sentences in the user's language: **who wants what, by when**. German legal terms are kept (e.g. "objection period (Einspruchsfrist)") |
| retention | `Aufbewahren bis 31.12.JJJJ` from the document type (`12_germany.md` §8) |
| paper original | "aufbewahren" or "laut Verfahrensdokumentation entbehrlich" (`12_germany.md` §8) |

## 3. Classification (German office documents)
| Document | Category | Action class | Priority hint |
|---|---|---|---|
| Rechnung (received), E-Rechnung | Rechnungen_Eingang | `pay` (+ § 14 check) | P2 |
| Mahnung / Zahlungserinnerung | Rechnungen_Eingang | `pay` (link to the invoice) | P1 |
| Rechnung (issued), Gutschrift | Rechnungen_Ausgang | `check` / `none` | P3 |
| Kassenbon, Quittung | Kassenbelege | `none` | — |
| Bewirtungsbeleg | Kassenbelege | `check` ("ergänzen: Anlass, Teilnehmer, Unterschrift") | P2 |
| Steuerbescheid, Haftungsbescheid | Finanzamt | `check` (Einspruchsfrist) + `pay` if a payment is due | P1 |
| Vorauszahlungsbescheid | Finanzamt / Behörden | `check` → updates the Steuerkalender amounts | P2 |
| Anforderung von Unterlagen, Prüfungsanordnung | Finanzamt | `provide_documents` | P1 |
| Gewerbesteuer-/Grundsteuerbescheid | Behörden | `check` + `pay` | P1 |
| Rundfunkbeitrag, IHK/HWK-Beitrag | Behörden | `pay` (+ `check` if it is a Bescheid) | P2 |
| Anhörung, Bußgeldbescheid | Behörden | `reply` / `check` | P1 |
| Krankenkasse, Minijob-Zentrale, BG, DRV | Sozialversicherung | per content (Beitragsbescheid → `pay`/`check`; Betriebsprüfung → `provide_documents`) | P1/P2 |
| Mahnbescheid, Vollstreckungsbescheid | Recht_Gericht | `check` (never auto-Widerspruch) | P1 |
| Inkasso, Abmahnung, lawyer's letter | Recht_Gericht | `check` / `reply` + lawyer task | P1 |
| Letter with a question, complaint | Briefe | `reply` | P2 |
| Lieferschein, Auftragsbestätigung, Lieferavis | Lieferungen | `deliver` or `check` | P2 |
| Vertrag, Kündigung, Vertragsänderung | Verträge | `check` (notice periods) | P2 |
| Versicherungspolice, Beitragsrechnung | Versicherungen | `pay` / `check` | P2 |
| Kontoauszug, Lastschriftankündigung | Bank | `none` / `check` | P3 |
| Lohnabrechnung, AU-Bescheinigung, staff letters | Personal | per content — **local processing only** | P2 |
| Request from the Steuerberater | Steuerberater | `provide_documents` | P2 |
| Ticket, booking | Tickets | `deliver` (date) or `none` | P3 |
| Newsletter, price list, info | Info | `none` | — |

Action classes:
- `pay`
- `reply`
- `provide_documents`
- `deliver` — something happens on a date (a delivery to accept, an appointment)
- `check` — read and decide
- `none`

## 4. German checks during filing (findings go to the case step)
- **§ 14 UStG completeness** for incoming invoices, and the Kleinbetragsrechnung rule (`12_germany.md` §6).
- **Arithmetic:** line totals, net + VAT = gross, VAT per rate. Report only real mismatches.
- **IBAN change:** compare the IBAN with earlier documents of the same sender (`12_germany.md` §7).
- **GiroCode vs. printed data.**
- **E-invoice hint from 2027** (`12_germany.md` §5, item 7).
- **TSE data on Kassenbons** (hint).

## 5. File it (through the core)
- Target folder: `<ROOT>/<year of the document date>/<NN_Month>/<Category>/`.
- File name: `YYYY-MM-DD_<Sender>_<Type>_<Reference>.<ext>`, e.g.
  `2026-10-28_Kartoffel_Mueller_Rechnung_1205.pdf`.
  - Spaces → `_`. Strip unsafe characters.
  - Max ~100 characters.
  - Add `_2`, `_3` on collisions. Never overwrite.
- **Companion files** with the same base name:

| Companion file | When |
|---|---|
| `…_Original.<ext>` | the photo |
| `…_Rekonstruktion.pdf` | photo / image source |
| `….xml` | e-invoice original |
| `…_Ansicht.pdf` | e-invoice view |
| `…_Pruefbericht.*` | validation report |

- Write the **summary file** next to it, `<base>.md` (template: `shared/templates/summary.en.md`). It makes the tree
  readable without the system and searchable for the case step.
- Update the document record (status `filed`, paths, extracted fields) and append to `protokoll.csv`.

## 6. Hand over to the case step
For each document, return:
- the ID
- the action class(es)
- the computed deadlines (Fristende / erledigen bis)
- the findings from §4
- a one-line reason

Documents with action `none` and no findings are finished here.
