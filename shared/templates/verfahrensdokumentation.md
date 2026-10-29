<!-- VORLAGE — Die implementierende KI füllt diese Verfahrensdokumentation in Bauphase 11 mit den realen Werten
     der Installation aus (de/anleitung/12_deutschland.md · en/guide/12_germany.md §8) und legt sie unter <ROOT>/_office/Verfahrensdokumentation.md ab.
     Platzhalter {{…}} ersetzen; Unbekanntes als [OFFEN: …] stehen lassen, nicht erfinden.
     Hinweis für den Nutzer: Diese Vorlage ersetzt keine Prüfung durch den Steuerberater. -->

# Verfahrensdokumentation — Digitale Belegablage mit Office Helper

| | |
|---|---|
| Unternehmen | {{FIRMA}}, {{RECHTSFORM}}, {{ANSCHRIFT}} |
| Steuernummer / USt-IdNr. | {{STEUERNUMMER}} / {{UST_IDNR}} |
| Verantwortlich | {{VERANTWORTLICHER}} |
| Steuerberater | {{STEUERBERATER}} |
| Version / Stand | {{VERSION}} · {{DATUM}} |
| Gültig ab | {{GUELTIG_AB}} |

## 1. Allgemeine Beschreibung
Eingehende Belege und Geschäftsbriefe (Papier und elektronisch) werden mit dem System „Office Helper“ erfasst,
nach Jahr, Monat und Kategorie abgelegt, mit einer Kurzbeschreibung versehen und in einem Aufgabenplan mit Fristen
geführt. Das System bereitet Antwortentwürfe vor; versendet, bezahlt oder unterschreibt aber nichts selbst.

Erfasste Unterlagen: {{DOKUMENTARTEN}} <!-- z. B. Eingangsrechnungen, Kassenbons, Kontoauszüge, Bescheide, Geschäftsbriefe, E-Rechnungen -->

## 2. Anwenderdokumentation
### 2.1 Beteiligte Personen und Rechte
| Person | Rolle | Rechte |
|---|---|---|
| {{PERSON_1}} | {{ROLLE_1}} | {{RECHTE_1}} |

### 2.2 Ablauf
1. **Eingang Papier:** Posteingang am {{EINGANGSTAG}} sichten. Scannen/Fotografieren innerhalb von {{TAGE}} Tagen
   (Ziel: zeitnah, spätestens 10 Tage).
   Gerät/App: {{SCANNER_ODER_APP}} · Auflösung mind. 300 dpi · Farbe/Graustufen: {{FARBMODUS}}.
   Ablage der Bilddatei in `{{ROOT}}/_Eingang`.
2. **Eingang elektronisch:** E-Mail-Postfächer {{POSTFAECHER}} werden {{ABRUFINTERVALL}} abgerufen; Anhänge und
   Mailtexte werden unverändert übernommen. E-Rechnungen (XRechnung, ZUGFeRD) werden als XML im Original gespeichert
   und mit {{VALIDATOR}} geprüft; der Prüfbericht wird mit abgelegt.
3. **Erfassung:** Jede Datei erhält eine fortlaufende Office-ID und einen SHA-256-Hashwert (Hash-Protokoll
   `_office/hashlog.csv`). Dubletten werden erkannt und separiert.
4. **Lesbarmachung:** Fotos und Bildscans werden zusätzlich als durchsuchbare Lesefassung rekonstruiert
   (`…_Rekonstruktion.pdf`, mit sichtbarem Herkunftsvermerk). **Das Original (Bilddatei bzw. Papier) bleibt der Beleg.**
5. **Ablage:** `{{ROOT}}/<Jahr>/<NN_Monat>/<Kategorie>/<Datum>_<Absender>_<Art>_<Nummer>.<Endung>` plus
   Kurzbeschreibung (`.md`). Maßgeblich ist das Belegdatum.
6. **Qualitätskontrolle:** Vollständigkeit und Lesbarkeit werden geprüft (OCR-Abgleich, Pflichtfelder, bei
   Rekonstruktionen Bildvergleich und Prüfbericht). Unsichere Dokumente erhalten die Aufgabe „manuell prüfen“ bzw.
   „Rekonstruktion prüfen“ und werden von {{PRUEFER}} gesichtet.
7. **Bearbeitung:** Aufgaben (zahlen, antworten, Unterlagen senden) mit Fristen; Antwortentwürfe werden von
   {{FREIGEBER}} freigegeben und von ihm/ihr versendet.
8. **Weitergabe an den Steuerberater:** {{WEITERGABE}} <!-- z. B. monatliches Belegpaket per E-Mail / DATEV Upload-Mail -->

### 2.3 Umgang mit Papieroriginalen (ersetzendes Scannen)
{{PAPIERREGELUNG}}
<!-- Standard bis zur Abstimmung mit dem Steuerberater: "Papieroriginale werden bis auf Weiteres vollständig aufbewahrt."
     Niemals vernichten: notarielle Urkunden, Eröffnungsbilanzen/Jahresabschlüsse, Zolldokumente, Wechsel/Schecks,
     Dokumente mit beweiserheblichen Originalunterschriften. -->

## 3. Technische Systemdokumentation
| Komponente | Beschreibung |
|---|---|
| Rechner / Betriebssystem | {{RECHNER}} · {{BETRIEBSSYSTEM}} |
| Office Helper | Version {{VERSION_SYSTEM}}, installiert in `{{IMPLEMENTIERUNGSORDNER}}` |
| KI-Werkzeug / Anbieter | {{KI_WERKZEUG}} · Verarbeitung lokal/Cloud: {{KI_VERARBEITUNG}} · AV-Vertrag: {{AV_VERTRAG}} |
| Speicher | {{SPEICHER}} (lokal / Google Drive / …) · Versionierung: {{VERSIONIERUNG}} |
| OCR / Texterkennung | {{OCR}} |
| Datenbank / Protokolle | `_office/` (Datensätze, `hashlog.csv`, `protokoll.csv`) |
| Automatisierung | {{AUTOMATISIERUNG}} |

## 4. Betriebsdokumentation
- **Unveränderbarkeit:** Originale werden nicht verändert; Hashwerte im Hash-Protokoll; Änderungen (Umbenennen,
  Umlegen, Statuswechsel) werden im Aktivitätsprotokoll mit Zeitstempel festgehalten. Schreibschutz: {{SCHREIBSCHUTZ}}.
- **Datensicherung:** {{BACKUP}} <!-- Was, wie oft, wohin, Wiederherstellungstest -->
- **Zugriffsschutz:** {{ZUGRIFF}}
- **Aufbewahrung:** 10 Jahre (Bücher, Abschlüsse, Organisationsunterlagen inkl. dieser Dokumentation), 8 Jahre
  (Buchungsbelege), 6 Jahre (Handels-/Geschäftsbriefe), jeweils ab Ende des Kalenderjahres; keine automatische Löschung.
- **Prüfung dieser Dokumentation:** mindestens jährlich und bei Änderungen am Verfahren · nächste Prüfung: {{NAECHSTE_PRUEFUNG}}

## 5. Datenschutz
- Verzeichnis von Verarbeitungstätigkeiten — Eintrag „Belegverarbeitung“: Zweck {{ZWECK}}, Datenkategorien
  {{DATENKATEGORIEN}}, Empfänger {{EMPFAENGER}}, Löschfristen gemäß Abschnitt 4.
- Auftragsverarbeiter (Art. 28 DSGVO): {{AUFTRAGSVERARBEITER}}
- Dokumente der Kategorie „Personal“ werden {{PERSONAL_LOKAL}} verarbeitet.

## 6. Änderungshistorie
| Version | Datum | Änderung | durch |
|---|---|---|---|
| {{VERSION}} | {{DATUM}} | Ersterstellung bei Installation | {{ERSTELLER}} |
