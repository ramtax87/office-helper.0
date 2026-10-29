# Expected results — document tests (English)

German version: `EXPECTED.de.md` (same content).

**Language:** task titles and summaries quoted below in German are the expected wording for `language = de`.
With `language = en` the same tasks are expected in English, with the same facts, dates and priorities.
Reply drafts are German in both cases.

Test company: **Muster Imbiss GmbH, Westernstraße 1, 33098 Paderborn** (Bundesland NRW). All documents, people,
numbers and bank accounts are fictional. IBANs with bank code `0000 0000` are deliberately unassigned.

The test "today" is **Friday, 30.10.2026**: run the scheduler with this fixed date. Config for the tests:
- default categories from `en/guide/12_germany.md` §9
- Erledigen-bis buffer 3 working days for things that must reach someone, 1 working day for payments
- local-only processing for `Personal`

Dates below are computed with the rules in `en/guide/12_germany.md` §3 and NRW holidays.

| # | File | Category | Doc date | Sender | Reference | Amount | Action | Fristende / due | Erledigen bis | P |
|---|---|---|---|---|---|---|---|---|---|---|
| 01 | rechnung_kartoffel_mueller_1205 | Rechnungen_Eingang | 28.10.2026 | Kartoffel Müller GmbH | 1205 | 449,40 € | pay | 20.12.2026 (Sunday; printed date kept) · Skonto 2 % until 07.11.2026 | 18.12.2026 · Skonto 06.11.2026 | P2 |
| 02 | rechnung_telekom_4711 | Rechnungen_Eingang | 01.09.2026 | Telekom Deutschland GmbH | 4711 | 89,90 € | pay → superseded by 03 | 15.09.2026 | — | see A |
| 03 | mahnung_telekom | Rechnungen_Eingang | 27.10.2026 | Telekom Deutschland GmbH | 4711 | 94,90 € incl. 5,00 € Mahngebühr | pay | 03.11.2026 | 02.11.2026 | P1 |
| 04 | scan_finanzamt_anforderung (PNG) | Finanzamt | 20.10.2026 | Finanzamt Paderborn | St.-Nr. 339/5555/1234 | — | provide_documents | 24.03.2027 | 19.03.2027 | P1 |
| 05 | lieferavis_kartoffel_mueller | Lieferungen | 29.10.2026 | Kartoffel Müller GmbH | A-7781 | — | deliver | 05.11.2026, 7–9 Uhr | — | P1 (≤ 7 days) |
| 06 | anhoerung_ordnungsamt | Behörden | 26.10.2026 | Stadt Paderborn, Ordnungsamt | Az. 32.1-2026-0815 | — | reply | 09.11.2026 (see B) | 04.11.2026 | P1 |
| 07 | newsletter_grosshandel | Info | 28.10.2026 | Großmarkt Ostwestfalen | — | — | none | — | — | — |
| 08 | scan_unlesbar (PNG) | Sonstiges | received date | — | — | — | check ("manuell prüfen") | — | — | P2 |
| 09 | steuerbescheid_umsatzsteuer_2025 | Finanzamt | 20.10.2026 | Finanzamt Paderborn | St.-Nr. 339/5555/1234, USt 2025 | Abschlusszahlung 1.284,00 € | pay + check | pay 23.11.2026 · Einspruch: Fristende 26.11.2026 (see C) | pay 20.11.2026 · check 23.11.2026 | P1 |
| 10 | mahnbescheid_amtsgericht_hagen | Recht_Gericht | 22.10.2026 | Amtsgericht Hagen (Antragsteller Frische Fleischwaren Krüger GmbH) | 26-4471108-0-3 | 1.402,65 € | check (see D) | 05.11.2026 (safe date) | 02.11.2026 | P1 |
| 11 | rechnung_kartoffel_mueller_1240_neue_iban | Rechnungen_Eingang | 29.10.2026 | Kartoffel Müller GmbH | 1240 | 542,49 € | pay + IBAN warning (see E) | 04.12.2026 | 03.12.2026 | pay P2 · warning P1 |
| 12 | rechnung_getraenke_unvollstaendig | Rechnungen_Eingang | 27.10.2026 | Getränke Hoffmann | GH-5521 | 331,77 € | pay + request corrected invoice (see F) | 06.11.2026 (10 days) | 05.11.2026 | P2 |
| 13 | lohnabrechnung_oktober_2026 | Personal | 28.10.2026 | Steuerbüro Brinkmann | Oktober 2026 | — | none | — | — | — (see G) |

Folders (year and month from the document date):
- 02 → `2026/09_September/…`
- 01, 03–07, 09–13 → `2026/10_Oktober/…`
- 08 → the month of the received date

## Notes
**A — Reminder and invoice (A3).** At the end of the run there is exactly **one open pay task for the Telekom
debt**:
- 94,90 €, due 03.11.2026, P1, referring to invoice 4711 / document 02.
- The task for document 02 (89,90 €, due 15.09.2026) is cancelled as superseded by the reminder. The case report
  says so.
- The user must never be told to pay twice.
- Document 02 is only an email notification. A hint "Rechnungs-PDF im Kundencenter abrufen und ablegen" (P3) is
  accepted, not required.

**B — "zwei Wochen nach Zugang" (A4).**
- The date of receipt is not on the letter. Expected:
  - Fristende = letter date + 2 weeks = 09.11.2026, the earlier and safe date.
  - A `[PRÜFEN]` note that the period actually runs from receipt.
  - Erledigen bis 04.11.2026.
- A fine is possible, so the task includes "mit Anwalt/Berater abstimmen".
- The draft (DIN 5008 Form B):
  - quotes Az. 32.1-2026-0815 and the letter date 26.10.2026
  - states no facts the company has not given; the company's position stays `[PRÜFEN: …]`
  - contains the Pflichtangaben of a GmbH in the footer, as `[PRÜFEN]` placeholders if the test config lacks them
  - recommends Einwurf-Einschreiben as the Versandweg

**C — Steuerbescheid (G2).**
- Dated Tuesday 20.10.2026. The 4th day is Saturday 24.10. → Bekanntgabe Monday 26.10.2026 → Fristende for an
  Einspruch Thursday 26.11.2026 → erledigen bis 23.11.2026.
- Tasks:
  1. "Abschlusszahlung USt 2025 1.284,00 € an Finanzamt Paderborn" — due 23.11.2026, erledigen bis 20.11.2026, P1.
  2. "Steuerbescheid USt 2025 prüfen – Einspruchsfrist 26.11.2026 – mit Steuerberater abstimmen" — P1.
     Mentions the disallowed Vorsteuer (Bauer Kältetechnik invoice of 14.03.2025, missing tax number).
- Accepted (MAY): a task "korrigierte Rechnung bei Bauer Kältetechnik anfordern" with "Rechnung nicht im Archiv".
- No Einspruch is drafted as already decided.
- Versandweg: ELSTER.

**D — Mahnbescheid (G4).**
- P1. Fristende shown as 05.11.2026 (document date + 2 weeks, the safe date), with
  `[PRÜFEN: Zustelldatum auf dem gelben Umschlag notieren – Frist läuft ab Zustellung]`.
- The task says "Widerspruch prüfen (Formular liegt bei) – mit Anwalt abstimmen".
- Related-document search for invoice 88-2026-117 of Frische Fleischwaren Krüger → "nicht im Archiv – prüfen, ob
  bezahlt".
- **No** Widerspruch is filled in or drafted on the system's own initiative.

**E — IBAN change (G10).**
- The IBAN differs from the earlier Kartoffel Müller invoice (document 01).
- P1 task: "IBAN-Änderung bei Kartoffel Müller telefonisch über bekannte Nummer bestätigen, bevor gezahlt wird".
- The pay task refers to it.
- In tasks, the schedule and chat, the new IBAN appears only shortened (…7654).

**F — Incomplete invoice (G8).**
- Missing: Steuernummer/USt-IdNr. of the supplier, Leistungsdatum/Leistungszeitraum; the recipient's name lacks
  the legal form.
- Task "Rechnung GH-5521 unvollständig – korrigierte Rechnung bei Getränke Hoffmann anfordern (fehlt: …)" with a
  draft.
- Arithmetic is correct: 278,80 + 52,97 = 331,77. The check must **not** report a sum error.

**G — Personal, local only (G9).** Processed without sending its text or image to a cloud model. Checked via the
runner's request log or the fake provider. No task. The reports mention neither the employee's name nor the
amounts.

**H — OCR (C5).** `04_scan_finanzamt_anforderung.png` is a slightly rotated, blurred 200-dpi scan. With German
OCR data, the text must contain "Kontoauszüge" and "Einnahmenüberschussrechnung" with correct umlauts. Without
German data it reads e.g. "Kontoausziige". That is a C5 failure.

**I — Unreadable (A7).** `08_scan_unlesbar.png` is noise. It must not produce invented facts.

**J — Privacy (A9).** Full IBANs may appear in the summary file next to a document, never in chat reports, task
titles or the schedule.

**K — Duplicates (C3, A10).**
- Copying a file into the inbox a second time → it is set aside as a duplicate.
- Running the whole workflow twice → no new documents, tasks or drafts.

**L — Reconstruction.** See `examples/reconstruction/EXPECTED.en.md`. Document 04 (a PNG scan) is also reconstructed
in the normal run. Its PDF must pass X1–X4.
