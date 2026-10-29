# 12 · Germany — rules the system is built around

Office Helper is built for German companies: GmbH, UG, e.K., GbR, sole traders, freelancers, small
restaurants and workshops. This chapter is binding for the build.

> Legal status as researched in October 2026. The implementing AI MUST re-check the points marked ⟳ against
> current official sources during the build and note any change in the build log. The system gives no legal or
> tax advice. Where it says "Steuerberater/Anwalt", the human decides with them.

## 1. Language and formats
- **User language:** German or English, chosen at the very start (root `AGENTS.md`), stored as `language` in the
  config. Reports, the schedule, task titles, summaries, review texts and the interview use that language.
  - With English, German legal terms are kept where they matter, with a short English gloss: "objection period
    (Einspruchsfrist)", "court payment order (Mahnbescheid)", "tax office (Finanzamt)".
- **Always German**, whatever the user language:
  - letters and reply drafts to German recipients (formal *Sie*)
  - the Verfahrensdokumentation (it is shown to the tax auditor)
  - the Steuerberater package and its CSV headers
  - by default, folder and category names (the user may choose English names in the interview; the Steuerberater
    and a tax auditor will expect German)
- Dates `TT.MM.JJJJ` (also for English users, matching the documents), weekday names in the user's language.
  Amounts `1.234,56 €`. Percent `19 %`.
- Month folders `01_Januar … 12_Dezember`. File names may contain umlauts if the storage keeps them (test in
  phase 4), otherwise use `ae/oe/ue/ss`.

## 2. Extra interview questions (build phase 2)
English (German wording in `de/anleitung/12_deutschland.md`):
```
Germany settings — answer each or reply "defaults":
a) Federal state (Bundesland, for public holidays)? [Nordrhein-Westfalen]
b) Legal form? (GmbH, UG, e.K., GbR, Einzelunternehmen, Freiberufler) [GmbH]
c) Mandatory letterhead details: company name, address, register court + HRB, managing directors, VAT ID / tax number
d) VAT return (Umsatzsteuer-Voranmeldung): monthly / quarterly / none (Kleinunternehmer § 19 UStG)? Permanent extension (Dauerfristverlängerung) yes/no?
e) Employees / mini-jobbers (wage tax, social insurance)? [no]
f) Trade tax / property tax prepayments? Income / corporate tax prepayments? [yes/yes]
g) Tax advisor (Steuerberater): name, and how do they receive documents? (DATEV Unternehmen online, DATEV Upload-Mail, email, paper, other software) [email]
h) Industry? (e.g. gastronomy, trades, retail) — sharpens sender recognition
i) Process documents of the category "Personal" only locally (no cloud model)? [yes]
```

## 3. Deadlines (Fristen) — how to compute them
Implement these as tested functions of the core (acceptance tests G1–G6), not as free-form AI reasoning.

**Public holidays:** compute them per Bundesland: fixed dates, Easter-based dates, and state-specific ones
(e.g. NRW: Fronleichnam and Allerheiligen; BY: Heilige Drei Könige, Mariä Himmelfahrt in parts; BE:
Frauentag; …). ⟳ Verify the state's list.

**General rules:**
- **Period from an event** ("innerhalb von zwei Wochen nach Zugang"): the event day is not counted (§ 187 Abs. 1
  BGB). The period ends at the end of the day with the same weekday or date (§ 188 BGB). A period in months that
  ends on a day that does not exist ends on the last day of that month.
- **End on Saturday, Sunday or public holiday:** it moves to the next working day: § 193 BGB (civil law),
  § 108 Abs. 3 AO (tax), § 222 Abs. 2 ZPO (courts), § 31 VwVfG (authorities).
- **Bekanntgabefiktion (4 days, since 1.1.2025):** a tax notice or administrative act sent by post or electronically
  counts as received on the **4th day** after it was sent (§ 122 Abs. 2 / 2a AO; § 41 VwVfG and corresponding
  rules). If that day falls on a weekend or public holiday, the next working day applies. ⟳
  The date on the notice is used as the dispatch date unless the envelope shows otherwise. If the notice really
  arrived later, the human may know better: add `[PRÜFEN: Zugangsdatum]`.
- **Formal service (Zustellung, yellow envelope):** the period runs from the date the postal worker wrote on the
  envelope. The system cannot see it. The task asks the user to note the date. Until then: due = document date
  + period (the earlier, safe date).
- **Unknown start** ("nach Zugang" with no receipt date): use the document date as the start: earlier = safer.
  Note `[PRÜFEN]`.
- **Payment dates on invoices:** keep the printed date even if it is a Sunday. Paying early is safe. With no due
  date on the invoice, use the configured default term (14 days) and note "kein Zahlungsziel angegeben".
- **Rechtsbehelfsbelehrung:** if a notice contains one, the period stated there wins over any default in this table.

**Legal end vs. "erledigen bis":** for anything that must *reach* someone (Einspruch, Widerspruch,
Stellungnahme, documents to the Finanzamt), the task shows both:
- `Fristende` — the legal end of the period
- `Erledigen bis` — the legal end minus a buffer for delivery, default 3 working days. Configurable.

The schedule sorts by `Erledigen bis`.

**Typical German deadlines:**

| Document | Period | From | Priority |
|---|---|---|---|
| Steuerbescheid, Haftungsbescheid → Einspruch (§ 355 AO) | 1 month | Bekanntgabe (4-day rule) | P1 |
| Bescheid of an authority (Gewerbesteuer, Grundsteuer, Rundfunkbeitrag, IHK/HWK, BG) → Widerspruch or Klage | per Rechtsbehelfsbelehrung, usually 1 month | Bekanntgabe | P1 |
| Gerichtlicher Mahnbescheid → Widerspruch (§ 692 ZPO) | 2 weeks | Zustellung (yellow envelope) | P1 |
| Vollstreckungsbescheid → Einspruch (§§ 700, 339 ZPO) | 2 weeks | Zustellung | P1 |
| Bußgeldbescheid → Einspruch (§ 67 OWiG) | 2 weeks | Zustellung | P1 |
| Anhörung (Ordnungsamt, Polizei, Behörde) | as stated in the letter | as stated | P1 |
| Abmahnung (lawyer) | as stated, often very short | as stated | P1 + lawyer |
| Anforderung von Unterlagen (Finanzamt) | as stated | — | P1 |
| Mahnung | as stated | — | P1 |
| Rechnung | printed due date / Skonto date | Rechnungsdatum | P2 |
| Vertrag: notice period | per contract | — | task 4 weeks before the last cancellation day, P2 |

Never decide on a legal remedy. The case step adds "mit Steuerberater/Anwalt abstimmen" to everything in the P1
legal rows above, and never drafts a formal Widerspruch or Einspruch as already decided. It drafts a neutral
"Einspruch zur Fristwahrung"-type text only if the user asks for it.

## 4. Steuerkalender — recurring tasks (optional, from interview d–f)
The scheduler adds recurring tasks for the obligations the user confirmed:

| Obligation | Due | Notes |
|---|---|---|
| Umsatzsteuer-Voranmeldung + payment | 10th of the following month (monthly) / following quarter (quarterly) | with Dauerfristverlängerung + 1 month; Sondervorauszahlung (1/11) by 10.02. for monthly filers |
| Lohnsteuer-Anmeldung + payment | 10th of the following month (or quarter) | only with employees |
| Einkommen-/Körperschaftsteuer-Vorauszahlung | 10.03., 10.06., 10.09., 10.12. | amounts from the last Vorauszahlungsbescheid |
| Gewerbesteuer-/Grundsteuer-Vorauszahlung | 15.02., 15.05., 15.08., 15.11. | amounts from the city's Bescheid |
| Sozialversicherungsbeiträge | third-last **Bankarbeitstag** of the month | 24.12. and 31.12. count as non-bank days; Beitragsnachweis must arrive earlier ⟳ |
| Annual returns | ask the Steuerberater | do not compute; deadlines differ with and without an adviser |

- Shift every date by the weekend/holiday rule.
- The 3-day *Schonfrist* for bank transfers is never used to plan; tasks are due on the actual date.
- Each task text names the amount if a Bescheid in the tree gives it ("laut Vorauszahlungsbescheid vom …"),
  otherwise "Betrag laut Bescheid prüfen".
- ⟳ Compare the generated dates for the current and next year with an official Steuerterminkalender (IHK or
  Finanzverwaltung) in the acceptance tests.

## 5. E-Rechnung (electronic invoices)
**Status ⟳:**
- Since **1.1.2025** every domestic business must be able to **receive** e-invoices.
- From **1.1.2027**, businesses with more than **800.000 €** prior-year turnover must **issue** them for domestic
  B2B.
- From **1.1.2028**, everyone must issue them.
- Exempt from issuing: Kleinbetragsrechnungen ≤ 250 €, Kleinunternehmer, travel tickets.
- A PDF, a scan or a photo is **not** an e-invoice; it is a "sonstige Rechnung".
- Valid formats follow EN 16931: **XRechnung** (UBL or CII) and **ZUGFeRD/Factur-X** from profile EN 16931
  upwards. The MINIMUM and BASIC-WL profiles do not count.
- In hybrid formats (ZUGFeRD: PDF + embedded XML) the **XML is legally leading**.

**What the system MUST do:**
1. **Detect:**
   - XML attachments (UBL `Invoice`/`CreditNote`, CII `CrossIndustryInvoice`)
   - PDFs with an embedded `factur-x.xml`, `zugferd-invoice.xml` or `xrechnung.xml`
2. **Keep the XML as the original**, byte-identical, with its hash. For hybrids, keep the whole PDF and also the
   extracted XML.
3. **Validate** with the official KoSIT validator and the current XRechnung configuration (XRechnung), or a
   ZUGFeRD validator (e.g. Mustang). Store the validation report next to the invoice. ⟳ current versions.
4. **Make it readable:** generate a human-readable PDF/HTML view (e.g. the KoSIT XRechnung visualisation).
   Mark it as a view (`…_Ansicht.pdf`). Read the facts from the **XML**, not from the picture.
5. **Read facts from XML:** invoice number, date, seller, IBAN, due date, totals per VAT rate, Leitweg-ID if
   present.
6. **Classify problems** as the BMF does: format errors (not an e-invoice), business-rule errors and content
   errors. Each becomes a task such as "Korrigierte E-Rechnung bei <Absender> anfordern", with a draft.
7. **Paper or PDF invoices from 2027 on:** for domestic B2B invoices > 250 € from a business that is not a
   Kleinunternehmer, add the hint "Prüfen: Lieferant müsste ggf. E-Rechnung senden" (P3 in 2027, P2 from 2028).
   The obligation depends on the supplier's turnover, which the system cannot know, so it is a hint, not an
   accusation.

**Sources for test files:** the official XRechnung test suite of KoSIT (`itplr-kosit/xrechnung-testsuite` on
GitHub) and the ZUGFeRD/Factur-X samples of FeRD or the Mustang project. Download valid and invalid instances in
build phase 3 (network access to GitHub, part of its approval) and use them in phase 7. Do not hand-write e-invoices for the tests.

## 6. Invoice check (§ 14 UStG) — for Vorsteuerabzug
For every incoming invoice, the filing role checks the mandatory content:
- full name and address of supplier and recipient
- the supplier's Steuernummer or USt-IdNr.
- invoice date
- a unique invoice number
- quantity and description of the goods or services
- the date of supply (Leistungszeitpunkt)
- net amount per tax rate
- tax rate and tax amount, or the reason for exemption (e.g. § 19 UStG, reverse charge "Steuerschuldnerschaft
  des Leistungsempfängers")

Kleinbetragsrechnung ≤ 250 € gross (§ 33 UStDV): name and address of the supplier, date, description, gross
amount and tax rate are enough.

Missing items → task "Rechnung unvollständig – korrigierte Rechnung anfordern (fehlt: …)" with a reply draft,
P2. Arithmetic errors (net + VAT ≠ gross) → same, listing the numbers.

## 7. Payment safety
- **GiroCode (EPC-QR):** if an invoice carries one, decode it. Check it against the printed IBAN, amount and
  reference. Show the decoded data in the pay task.
- **IBAN change warning (invoice fraud):** if a supplier's IBAN differs from the IBAN on earlier filed documents
  of the same supplier, create a **P1** task "IBAN-Änderung bei <Lieferant> telefonisch über bekannte Nummer
  bestätigen, bevor gezahlt wird". Show the new account only as the last 4 digits.
- Show IBANs in tasks and chat only shortened (`DE89 … 3000`).

## 8. GoBD (proper digital bookkeeping) and archiving
The system supports GoBD compliance; it is **not** a certified archive. The handover MUST say so.
- **Unveränderbarkeit:**
  - Originals are never changed.
  - Every original's SHA-256 goes into an append-only **hash log** (`_office/hashlog.csv`: date, ID, file, hash).
  - Refiling changes the path, never the content, and is logged.
  - SHOULD: write-protect filed originals (file permissions) and enable version history on the cloud storage.
- **Nachvollziehbarkeit:** an append-only **activity log** (`_office/protokoll.csv`): every intake, filing,
  refile, task change and draft status change, with timestamp, actor (agent/human) and old → new value.
- **Zeitnahe Erfassung:** paper should be scanned soon after arrival. The status report shows the oldest
  unprocessed inbox file. Warn after 10 days.
- **Verfahrensdokumentation:** the build MUST fill in `shared/templates/verfahrensdokumentation.md` for this
  installation: hardware, software, storage, people, the scanning and photo process, quality checks, the
  reconstruction step, backups, access rights. Deliver it in the handover and save it in the root
  (`_office/Verfahrensdokumentation.md`) for the Steuerberater and a tax audit. Re-generate it after
  configuration changes.
- **Ersetzendes Scannen (destroying paper after scanning):** allowed for most documents *if* the process is
  documented and checked. **Never** for: notarial deeds, opening balances and annual financial statements, customs
  documents, bills of exchange and cheques, and documents with original signatures needed as evidence. The system
  NEVER tells the user to destroy paper. The summary of every document shows "Papieroriginal: aufbewahren" or
  "Papieroriginal: laut Verfahrensdokumentation entbehrlich (mit Steuerberater abgestimmt?)".
  Recommend keeping all paper until the Steuerberater has approved the procedure. Smartphone photos are acceptable
  only as part of the documented procedure.
- **Retention periods ⟳** (from the end of the calendar year in which the document was created or received):

| Period | Documents |
|---|---|
| 10 years | Handelsbücher/Journale, Inventare, Eröffnungsbilanzen, Jahresabschlüsse, Lageberichte, and the work instructions and organisational documents needed to understand them (this includes the Verfahrensdokumentation) |
| 8 years | **Buchungsbelege**: invoices in and out, receipts, Kassenbons, bank statements (shortened from 10 years by BEG IV for periods not expired by 1.1.2025; banks and insurers one year later) |
| ask the Steuerberater | payroll records (Lohnkonto, SV documents): their own rules ⟳ — default to 8 years and mark `[PRÜFEN]` |
| 6 years | received business letters and copies of sent ones, other tax-relevant documents |

- A period does not end while the document still matters for a tax assessment that is not final (§ 147 Abs. 3 AO).
- The summary of each document shows `Aufbewahren bis: 31.12.JJJJ`.
- Once a year the system lists documents past their period, for the user and the Steuerberater to decide.
  Nothing is ever deleted automatically.

## 9. Senders typical in Germany (classification)
| Sender | Default category | Note |
|---|---|---|
| Finanzamt, Bundeszentralamt für Steuern | Finanzamt | Bescheide: check the Einspruchsfrist |
| Stadt/Gemeinde: Steueramt (Gewerbesteuer, Grundsteuer), Gewerbeamt, Ordnungsamt, Bauamt | Behörden | |
| Hauptzollamt, Zoll | Behörden | Zolldokumente: keep the paper |
| Lebensmittelüberwachung / Veterinäramt, Gesundheitsamt | Behörden | inspections, deadlines for defects |
| Schornsteinfeger (Feuerstättenbescheid) | Behörden | |
| ARD ZDF Deutschlandradio Beitragsservice | Behörden | Rundfunkbeitrag for business premises and vehicles |
| Krankenkassen (AOK, TK, Barmer, IKK, …), Minijob-Zentrale, Deutsche Rentenversicherung (Betriebsprüfung) | Sozialversicherung | |
| Berufsgenossenschaft (gastronomy: BGN; construction: BG BAU), Unfallkasse | Sozialversicherung | Beitragsbescheid; digital Lohnnachweis by the deadline in the letter |
| Agentur für Arbeit | Sozialversicherung | |
| IHK, Handwerkskammer | Behörden | Beitragsbescheid |
| Amtsgericht (Mahnbescheid, Handelsregister), Gerichtsvollzieher, Inkasso, Rechtsanwalt | Recht_Gericht | P1 when there is a deadline; lawyer task |
| Bank, Sparkasse | Bank | |
| Versicherer | Versicherungen | contract periods, premium invoices |
| GEMA, Telekommunikation, Energie, Leasing | Verträge or Rechnungen_Eingang by content | |
| Steuerberater | Steuerberater | requests for documents → provide_documents |

**Default categories (Germany):** `Rechnungen_Eingang`, `Rechnungen_Ausgang`, `Kassenbelege`, `Finanzamt`,
`Behörden`, `Sozialversicherung`, `Bank`, `Versicherungen`, `Verträge`, `Personal`, `Lieferungen`,
`Recht_Gericht`, `Steuerberater`, `Briefe`, `Tickets`, `Info`, `Sonstiges`.

## 10. Letters (reply drafts)
- Follow DIN 5008:2020, **Form B** by default. Template: `shared/templates/antwortbrief.md`.
  - Address field 45 mm from the top edge, 20 mm from the left edge.
  - Return-address line above the address field.
  - Information block on the right: Ihr Zeichen, Ihre Nachricht vom, Unser Zeichen, Datum.
  - The subject line is written without the word "Betreff".
  - Fold marks at 105 mm and 210 mm, punch mark at 148.5 mm.
- **Pflichtangaben im Geschäftsbrief:** the letterhead or footer MUST contain the legally required details for the
  Rechtsform from the interview (c):
  - GmbH/UG: § 35a GmbHG — Rechtsform, Sitz, Registergericht, HRB-Nr., all Geschäftsführer (and the
    Aufsichtsrat chair if there is one).
  - e.K.: § 37a HGB.
  - AG: § 80 AktG.

  Never invent them. A missing value stays `[PRÜFEN: …]`.
- Every reply task names the recommended **Versandweg** (delivery route):

| Recipient | Recommended Versandweg |
|---|---|
| Finanzamt | **ELSTER** (Einspruch and documents can be sent there), otherwise Einwurf-Einschreiben |
| Deadlines with legal effect | Einschreiben, or fax with transmission report, plus a copy |
| Courts | — the system never prepares court submissions as final; lawyer task |

- Drafts are also offered as a print-ready PDF in DIN 5008 layout. The PDF is generated by the same renderer as
  the reconstruction, from the markdown draft, after approval.

## 11. Steuerberater handover
- **Monthly Belegpaket:** a folder or zip `Belege_JJJJ-MM` with all Buchungsbelege of the month, plus a CSV
  (`;`-separated, UTF-8 with BOM, German number format):
  Belegdatum; Belegnummer; Partner; Kategorie; Brutto; USt-Satz; USt-Betrag; Zahlstatus; Datei; Office-ID.
  - E-invoices go into the package as XML (+ view).
  - Reconstructed documents go in as the original image + reconstruction.
- **DATEV:**
  - Many Steuerberater use DATEV Unternehmen online.
  - Belege can be uploaded there by the user, or sent to a personal **DATEV Upload-Mail** address that the
    Steuerberater sets up. ⟳
  - The system prepares the files and an email draft. **Sending is done by the human** (hard rule 2), unless the
    user explicitly configures automatic forwarding to that one address. That is its own approval gate, recorded
    in the Verfahrensdokumentation.
  - Building DATEV booking batches (EXTF format) is out of scope.
- A request from the Steuerberater ("bitte noch Kontoauszüge März") is a `provide_documents` case like any other.

## 12. Datenschutz (DSGVO)
- Documents contain personal data of employees, customers and suppliers. The company is the controller.
- **Cloud AI providers and cloud storage process personal data on the company's behalf.** Recommend in the
  handover:
  - a data processing agreement (AV-Vertrag, Art. 28 DSGVO) with each provider
  - an EU data region where offered
  - an entry in the Verzeichnis von Verarbeitungstätigkeiten. The template contains a section for it.
- `Personal` and health-related documents (Krankmeldungen, AU-Bescheinigungen) are processed **only locally**
  when the user chose that in interview (i). The standalone runner and every agent MUST respect it (acceptance
  test G9).
- Deleting after the retention period is a decision for the user. The yearly list (§ 8) supports the
  Löschkonzept.

## 13. Kassenbons and Bewirtungsbelege
- Thermal receipts fade. Photograph them early and reconstruct them (`13_reconstruction.md`). Keep the photo.
- Check for the details a receipt must show: date and time, items, amount per tax rate, and the TSE data
  (transaction number, signature) since the Kassensicherungsverordnung. Missing TSE data is a hint only, P3.
- **Bewirtungsbeleg** (restaurant receipt for business entertainment): for the tax deduction it needs a stated
  occasion and the participants, signed by the host. Create the task "Bewirtungsbeleg ergänzen: Anlass,
  Teilnehmer, Unterschrift" (P2). ⟳ current requirements.
