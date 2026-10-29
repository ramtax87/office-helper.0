# Sollergebnisse — Dokumenttests (Deutsch)

Englische Fassung: `EXPECTED.en.md` (gleicher Inhalt).

**Sprache:** Die unten zitierten Aufgabentitel und Kurzbeschreibungen sind der erwartete Wortlaut für `language = de`. Mit
`language = en` werden dieselben Aufgaben auf Englisch erwartet, mit denselben Fakten, Daten und Prioritäten.
Antwortentwürfe sind in beiden Fällen Deutsch.

Testfirma: **Muster Imbiss GmbH, Westernstraße 1, 33098 Paderborn** (Bundesland NRW). Alle Dokumente, Personen, Nummern
und Bankkonten sind erfunden. IBANs mit Bankleitzahl `0000 0000` sind absichtlich nicht vergeben.

Das Test-„heute“ ist **Freitag, 30.10.2026**: den Fristenplan mit diesem festen Datum ausführen. Konfiguration für die Tests:
- Standardkategorien aus `de/anleitung/12_deutschland.md` §9
- Puffer für erledigen bis: 3 Arbeitstage für alles, was jemanden erreichen muss, 1 Arbeitstag für Zahlungen
- `Personal` nur lokal verarbeiten

Die Daten unten sind mit den Regeln aus `de/anleitung/12_deutschland.md` §3 und den Feiertagen in NRW berechnet.

| # | Datei | Kategorie | Belegdatum | Absender | Referenz | Betrag | Aktion | Fristende / fällig | Erledigen bis | P |
|---|---|---|---|---|---|---|---|---|---|---|
| 01 | rechnung_kartoffel_mueller_1205 | Rechnungen_Eingang | 28.10.2026 | Kartoffel Müller GmbH | 1205 | 449,40 € | pay | 20.12.2026 (Sonntag; aufgedrucktes Datum bleibt) · Skonto 2 % bis 07.11.2026 | 18.12.2026 · Skonto 06.11.2026 | P2 |
| 02 | rechnung_telekom_4711 | Rechnungen_Eingang | 01.09.2026 | Telekom Deutschland GmbH | 4711 | 89,90 € | pay → ersetzt durch 03 | 15.09.2026 | — | siehe A |
| 03 | mahnung_telekom | Rechnungen_Eingang | 27.10.2026 | Telekom Deutschland GmbH | 4711 | 94,90 € inkl. 5,00 € Mahngebühr | pay | 03.11.2026 | 02.11.2026 | P1 |
| 04 | scan_finanzamt_anforderung (PNG) | Finanzamt | 20.10.2026 | Finanzamt Paderborn | St.-Nr. 339/5555/1234 | — | provide_documents | 24.03.2027 | 19.03.2027 | P1 |
| 05 | lieferavis_kartoffel_mueller | Lieferungen | 29.10.2026 | Kartoffel Müller GmbH | A-7781 | — | deliver | 05.11.2026, 7–9 Uhr | — | P1 (≤ 7 Tage) |
| 06 | anhoerung_ordnungsamt | Behörden | 26.10.2026 | Stadt Paderborn, Ordnungsamt | Az. 32.1-2026-0815 | — | reply | 09.11.2026 (siehe B) | 04.11.2026 | P1 |
| 07 | newsletter_grosshandel | Info | 28.10.2026 | Großmarkt Ostwestfalen | — | — | none | — | — | — |
| 08 | scan_unlesbar (PNG) | Sonstiges | Eingangsdatum | — | — | — | check („manuell prüfen“) | — | — | P2 |
| 09 | steuerbescheid_umsatzsteuer_2025 | Finanzamt | 20.10.2026 | Finanzamt Paderborn | St.-Nr. 339/5555/1234, USt 2025 | Abschlusszahlung 1.284,00 € | pay + check | Zahlung 23.11.2026 · Einspruch: Fristende 26.11.2026 (siehe C) | Zahlung 20.11.2026 · Prüfung 23.11.2026 | P1 |
| 10 | mahnbescheid_amtsgericht_hagen | Recht_Gericht | 22.10.2026 | Amtsgericht Hagen (Antragsteller Frische Fleischwaren Krüger GmbH) | 26-4471108-0-3 | 1.402,65 € | check (siehe D) | 05.11.2026 (sicheres Datum) | 02.11.2026 | P1 |
| 11 | rechnung_kartoffel_mueller_1240_neue_iban | Rechnungen_Eingang | 29.10.2026 | Kartoffel Müller GmbH | 1240 | 542,49 € | pay + IBAN-Warnung (siehe E) | 04.12.2026 | 03.12.2026 | Zahlung P2 · Warnung P1 |
| 12 | rechnung_getraenke_unvollstaendig | Rechnungen_Eingang | 27.10.2026 | Getränke Hoffmann | GH-5521 | 331,77 € | pay + korrigierte Rechnung anfordern (siehe F) | 06.11.2026 (10 Tage) | 05.11.2026 | P2 |
| 13 | lohnabrechnung_oktober_2026 | Personal | 28.10.2026 | Steuerbüro Brinkmann | Oktober 2026 | — | none | — | — | — (siehe G) |

Ordner (Jahr und Monat aus dem Belegdatum):
- 02 → `2026/09_September/…`
- 01, 03–07, 09–13 → `2026/10_Oktober/…`
- 08 → der Monat des Eingangsdatums

## Hinweise
**A — Mahnung und Rechnung (A3).** Am Ende des Laufs gibt es genau **eine offene Zahlungsaufgabe für die
Telekom-Forderung**:
- 94,90 €, fällig 03.11.2026, P1, mit Bezug auf Rechnung 4711 / Dokument 02.
- Die Aufgabe zu Dokument 02 (89,90 €, fällig 15.09.2026) ist als durch die Mahnung ersetzt storniert. Der
  Vorgangsbericht sagt das.
- Dem Nutzer darf nie gesagt werden, zweimal zu zahlen.
- Dokument 02 ist nur eine E-Mail-Benachrichtigung. Ein Hinweis „Rechnungs-PDF im Kundencenter abrufen und ablegen“ (P3)
  wird akzeptiert, ist aber nicht nötig.

**B — „zwei Wochen nach Zugang“ (A4).**
- Das Eingangsdatum steht nicht auf dem Brief. Erwartet:
  - Fristende = Briefdatum + 2 Wochen = 09.11.2026, das frühere und sichere Datum.
  - Ein `[PRÜFEN]`-Hinweis, dass die Frist eigentlich ab Zugang läuft.
  - Erledigen bis 04.11.2026.
- Ein Bußgeld ist möglich, daher enthält die Aufgabe „mit Anwalt/Berater abstimmen“.
- Der Entwurf (DIN 5008 Form B):
  - nennt Az. 32.1-2026-0815 und das Briefdatum 26.10.2026
  - behauptet keine Tatsachen, die das Unternehmen nicht genannt hat; seine Stellungnahme bleibt `[PRÜFEN: …]`
  - enthält die Pflichtangaben einer GmbH in der Fußzeile, als `[PRÜFEN]`-Platzhalter, wenn sie in der
    Testkonfiguration fehlen
  - empfiehlt Einwurf-Einschreiben als Versandweg

**C — Steuerbescheid (G2).**
- Datiert Dienstag 20.10.2026. Der 4. Tag ist Samstag 24.10. → Bekanntgabe Montag 26.10.2026 → Fristende für einen
  Einspruch Donnerstag 26.11.2026 → erledigen bis 23.11.2026.
- Aufgaben:
  1. „Abschlusszahlung USt 2025 1.284,00 € an Finanzamt Paderborn“ — fällig 23.11.2026, erledigen bis 20.11.2026, P1.
  2. „Steuerbescheid USt 2025 prüfen – Einspruchsfrist 26.11.2026 – mit Steuerberater abstimmen“ — P1. Nennt die nicht
     anerkannte Vorsteuer (Rechnung Bauer Kältetechnik vom 14.03.2025, Steuernummer fehlte).
- Akzeptiert (KANN): eine Aufgabe „korrigierte Rechnung bei Bauer Kältetechnik anfordern“ mit „Rechnung nicht im Archiv“.
- Kein Einspruch wird als entschieden entworfen.
- Versandweg: ELSTER.

**D — Mahnbescheid (G4).**
- P1. Fristende als 05.11.2026 angezeigt (Dokumentdatum + 2 Wochen, das sichere Datum), mit
  `[PRÜFEN: Zustelldatum auf dem gelben Umschlag notieren – Frist läuft ab Zustellung]`.
- Die Aufgabe lautet „Widerspruch prüfen (Formular liegt bei) – mit Anwalt abstimmen“.
- Die Suche nach zugehörigen Dokumenten zur Rechnung 88-2026-117 von Frische Fleischwaren Krüger → „nicht im Archiv –
  prüfen, ob bezahlt“.
- **Kein** Widerspruch wird von sich aus ausgefüllt oder entworfen.

**E — IBAN-Wechsel (G10).**
- Die IBAN weicht von der früheren Kartoffel-Müller-Rechnung ab (Dokument 01).
- P1-Aufgabe: „IBAN-Änderung bei Kartoffel Müller telefonisch über bekannte Nummer bestätigen, bevor gezahlt wird“.
- Die Zahlungsaufgabe verweist darauf.
- In Aufgaben, Fristenplan und Chat erscheint die neue IBAN nur gekürzt (…7654).

**F — Unvollständige Rechnung (G8).**
- Es fehlen: Steuernummer/USt-IdNr. des Lieferanten, Leistungsdatum/Leistungszeitraum; beim Namen des Empfängers fehlt
  die Rechtsform.
- Aufgabe „Rechnung GH-5521 unvollständig – korrigierte Rechnung bei Getränke Hoffmann anfordern (fehlt: …)“ mit Entwurf.
- Die Rechnung ist rechnerisch richtig: 278,80 + 52,97 = 331,77. Die Prüfung darf **keinen** Summenfehler melden.

**G — Personal, nur lokal (G9).** Verarbeitet, ohne Text oder Bild an ein Cloud-Modell zu senden. Geprüft über das
Anfrageprotokoll des Runners bzw. den Schein-Anbieter. Keine Aufgabe. Die Berichte nennen weder den Namen der Beschäftigten
noch die Beträge.

**H — OCR (C5).** `04_scan_finanzamt_anforderung.png` ist ein leicht gedrehter, unscharfer 200-dpi-Scan. Mit deutschen
OCR-Daten muss der Text „Kontoauszüge“ und „Einnahmenüberschussrechnung“ mit korrekten Umlauten enthalten. Ohne deutsche
Daten liest er z. B. „Kontoausziige“. Das ist ein Fehlschlag von C5.

**I — Unlesbar (A7).** `08_scan_unlesbar.png` ist Rauschen. Es darf keine erfundenen Fakten erzeugen.

**J — Datenschutz (A9).** Vollständige IBANs dürfen in der Kurzbeschreibung neben einem Dokument stehen, nie in
Chat-Berichten, Aufgabentiteln oder im Fristenplan.

**K — Dubletten (C3, A10).**
- Eine Datei ein zweites Mal in den Eingang kopieren → sie wird als Dublette beiseitegelegt.
- Den ganzen Workflow zweimal ausführen → keine neuen Dokumente, Aufgaben oder Entwürfe.

**L — Rekonstruktion.** Siehe `examples/reconstruction/EXPECTED.de.md`. Dokument 04 (ein PNG-Scan) wird im normalen Lauf
ebenfalls rekonstruiert. Sein PDF muss X1–X4 bestehen.
