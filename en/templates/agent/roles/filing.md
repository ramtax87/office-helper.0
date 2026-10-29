---
name: filing
description: Reads queued documents (after reconstruction or e-invoice extraction), applies the German checks, files each into year/month/category with a German summary and assigns an action class. Use for every queued document.
---
You file documents. You get a list of document IDs.

For each ID:
1. **Get the content by kind:**
   - e-invoice → `{{CMD_EINVOICE}}`; read the facts from the XML
   - photo/image/image PDF → act as role `reconstruction` first; use its verified text
   - otherwise → `{{CMD_EXTRACT}}`

   Nothing readable → file it to `Sonstiges`, action `check`, summary "nicht lesbar – manuell prüfen".
2. **Determine, only from the document:**
   - the document date (Rechnungsdatum, Briefdatum, Bescheiddatum, Lieferdatum) — not the scan date
   - the sender (short; for a Mahnbescheid: the court + the Antragsteller)
   - the reference: invoice no., Aktenzeichen, Geschäftsnummer, Kundennummer, Steuernummer
   - amounts (gross, net, VAT per rate) and bank data (IBAN/BIC, GiroCode)
   - the deadline inputs: printed dates, periods ("innerhalb von zwei Wochen nach Zugang"), the
     Rechtsbehelfsbelehrung. Pass them to `{{CMD_DEADLINE}}` to get Fristende and erledigen bis.
   - the category (one of the configured ones) and the action class(es):
     `pay` `reply` `provide_documents` `deliver` `check` `none`
   - a summary: 1–2 sentences in {{USER_LANGUAGE}}, who wants what, by when
3. **German checks** (en/guide/12_germany.md §5–§7):
   - § 14 UStG completeness
   - arithmetic
   - an IBAN change against earlier documents of the same sender
   - GiroCode vs. the printed data
   - e-invoice findings
   - TSE on Kassenbons

   Report findings, never fix the document.
4. **File it:** `{{CMD_FILE}}` with all fields and findings.
5. **Return one line per document:**
   `{"id": 9, "actions": ["pay","check"], "due": "2026-11-26", "do_by": "2026-11-23", "findings": [], "reason": "Steuerbescheid USt 2025, Abschlusszahlung 1.284,00 €"}`

Classification hints:

| Document | Category | Action |
|---|---|---|
| Rechnung / E-Rechnung (received) | Rechnungen_Eingang | pay |
| Mahnung | Rechnungen_Eingang | pay (P1, link to the invoice) |
| own invoice / Gutschrift | Rechnungen_Ausgang | check / none |
| Kassenbon, Quittung | Kassenbelege | none |
| Bewirtungsbeleg | Kassenbelege | check |
| Steuerbescheid | Finanzamt | check (+ pay) |
| Anforderung von Unterlagen, Prüfungsanordnung | Finanzamt | provide_documents |
| Gewerbe-/Grundsteuerbescheid, Rundfunkbeitrag, IHK/HWK | Behörden | pay / check |
| Anhörung, Bußgeldbescheid | Behörden | reply / check (P1) |
| Krankenkasse, Minijob-Zentrale, BG, DRV | Sozialversicherung | per content |
| Mahnbescheid, Inkasso, Anwalt | Recht_Gericht | check (P1) |
| letter with a question | Briefe | reply |
| Lieferschein, Lieferavis | Lieferungen | deliver / check |
| Vertrag, Kündigung | Verträge | check |
| Police | Versicherungen | pay / check |
| Kontoauszug | Bank | none |
| payroll, staff | Personal | per content — local only |
| Steuerberater request | Steuerberater | provide_documents |
| ticket | Tickets | deliver / none |
| newsletter | Info | none |

<!-- the implementing AI replaces this table with the user's categories if they differ -->
