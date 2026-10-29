<!-- VORLAGE Antwortbrief — DIN 5008:2020 Form B. Wird vom case-Agenten ausgefüllt (de/anleitung/05_vorgaenge.md · en/guide/05_cases.md, de/anleitung/12_deutschland.md · en/guide/12_germany.md §10).
     ENTWURF – NICHT VERSENDET. Office-Dokument #{{DOC_ID}} · erstellt {{HEUTE}} · Status: review
     Alles Unsichere als [PRÜFEN: …] markieren. Pflichtangaben nie erfinden. -->

<!-- Briefkopf -->
**{{FIRMA}}**
{{FIRMA_STRASSE}} · {{FIRMA_PLZ_ORT}}
Tel. {{FIRMA_TELEFON}} · {{FIRMA_EMAIL}}

<!-- Rücksendeangabe (eine Zeile, klein, über dem Anschriftfeld) -->
<small>{{FIRMA}} · {{FIRMA_STRASSE}} · {{FIRMA_PLZ_ORT}}</small>

<!-- Anschriftfeld (Form B: 45 mm von oben, 20 mm von links; max. 6 Zeilen) -->
{{EMPFAENGER_ZUSATZ}}
{{EMPFAENGER_NAME}}
{{EMPFAENGER_ABTEILUNG}}
{{EMPFAENGER_STRASSE}}
{{EMPFAENGER_PLZ_ORT}}

<!-- Informationsblock (rechts) -->
| | |
|---|---|
| Ihr Zeichen | {{AKTENZEICHEN}} |
| Ihre Nachricht vom | {{BRIEFDATUM}} |
| Unser Zeichen | {{UNSER_ZEICHEN}} |
| Steuernummer | {{STEUERNUMMER_FALLS_FINANZAMT}} |
| Datum | {{DATUM}} |

<!-- Betreff: fett, ohne das Wort "Betreff" -->
**{{BETREFF}}**

Sehr geehrte Damen und Herren,
<!-- oder: Sehr geehrte Frau {{NAME}}, / Sehr geehrter Herr {{NAME}}, wenn ein Ansprechpartner genannt ist -->

{{EINLEITUNG}}
<!-- z. B. "vielen Dank für Ihr Schreiben vom {{BRIEFDATUM}}." / "wir nehmen Bezug auf Ihr Schreiben vom …" -->

{{INHALT}}
<!-- kurz, sachlich, keine Schuldanerkenntnisse, keine verbindlichen rechtlichen/steuerlichen Aussagen,
     keine Zahlungszusagen ohne Angabe des Nutzers. Offene Punkte: [PRÜFEN: …] -->

{{SCHLUSS}}
<!-- z. B. "Für Rückfragen stehen wir Ihnen gern zur Verfügung." -->

Mit freundlichen Grüßen

{{FIRMA}}

<!-- Platz für die Unterschrift -->

{{UNTERZEICHNER_NAME}}
{{UNTERZEICHNER_FUNKTION}}

**Anlagen**
{{ANLAGEN}}
<!-- je Anlage: Bezeichnung – Fundort im Ablagebaum, z. B. "Kontoauszüge 01–12/2025 – 2025/…/Bank" oder "fehlt" -->

<!-- Fußzeile mit Pflichtangaben (de/anleitung/12_deutschland.md · en/guide/12_germany.md §10) — Inhalt hängt von der Rechtsform ab -->
---
<small>{{FIRMA}} · Sitz: {{SITZ}} · Registergericht: {{REGISTERGERICHT}} {{HRB}} · Geschäftsführer: {{GESCHAEFTSFUEHRER}} · USt-IdNr.: {{UST_IDNR}} · {{BANK}} · IBAN {{IBAN}}</small>

---
### Vor dem Versand prüfen (wird nicht mitgedruckt)
- [ ] Alle `[PRÜFEN: …]`-Stellen geklärt
- [ ] Fristende: {{FRISTENDE}} · erledigen bis: {{ERLEDIGEN_BIS}}
- [ ] Versandweg: {{VERSANDWEG}} <!-- ELSTER / Einwurf-Einschreiben / Fax mit Sendebericht / E-Mail -->
- [ ] Anlagen beigefügt
- [ ] Bei Fristsachen mit Rechtswirkung: mit Steuerberater/Anwalt abgestimmt
- [ ] Kopie des versendeten Schreibens ablegen (Handelsbrief, 6 Jahre)
