# 07 · Datenmodell

Die Datensätze sind das Gedächtnis des Systems und Teil der Bücher des Unternehmens. Sie MÜSSEN einen Wechsel des
KI-Anbieters überstehen, ohne KI einsehbar sein und wie die Dokumente aufbewahrt werden (`12_deutschland.md` §8).
Ablage unter `<ROOT>/_office/`.

## Speicherform
- **SOLL:** eine SQL-Datenbank in einer einzigen Datei (z. B. SQLite) — robust, abfragbar, kein Server.
- **KANN:** JSON/CSV-Dateien, wenn der Rechner keine Datenbank bietet. Dann atomar schreiben (in eine Temp-Datei
  schreiben, dann umbenennen), eine Datei je Datensatzart.
- In jedem Fall bietet der Kern einen **CSV-Export** jeder Tabelle (`;`-getrennt, UTF-8 mit BOM, deutsches
  Zahlenformat), damit Nutzer oder Steuerberater die Daten in einer Tabellenkalkulation öffnen können.
- **Fortlaufende Protokolle**, unabhängig von der Datenbank:
  - `hashlog.csv` — Zeitstempel; Office-ID; Datei; SHA-256 (eine Zeile je Original und je abgeleiteter Datei)
  - `protokoll.csv` — Zeitstempel; Akteur (Agent/Nutzer/System); Office-ID; Aktion; alter Wert; neuer Wert

  Sie werden nie neu geschrieben, nur ergänzt. Beim Start prüft der Kern, dass ihre letzten Zeilen intakt sind.

## Dokument
| Feld | Hinweise |
|---|---|
| id | Ganzzahl, eindeutig, nie wiederverwendet |
| hash | SHA-256 des Originals, eindeutig — Dublettenerkennung |
| original_name, source | Dateiname und Quelle (Eingang, Scan-Ordner, Postfachname) |
| original_kind | `photo` `image` `image_pdf` `text_pdf` `hybrid_pdf` `e_invoice_xml` `mail_text` `office` (03) |
| received_at | Zeitpunkt des Eingangs |
| status | `queued` → `filed` (→ `archived` optional) |
| path | aktueller Ort des Originals (Warteschlange, dann Baum) |
| companion_paths | Rekonstruktion, E-Rechnungs-XML/-Ansicht, Prüfbericht |
| doc_date | Belegdatum, ISO `JJJJ-MM-TT` |
| category, title, sender, reference | aus der Ablage |
| amount_gross, amount_net, vat (je Satz), currency | optional |
| iban, bic | vollständig nur im Datensatz; sonst gekürzt angezeigt |
| summary | 1–2 Sätze in der Sprache des Nutzers |
| action | eine oder mehrere von `pay` `reply` `provide_documents` `deliver` `check` `none` |
| findings | Liste: § 14-Lücken, Rechenfehler, IBAN-Wechsel, GiroCode-Abweichung, E-Rechnungsfehler, TSE |
| e_invoice | Format (XRechnung UBL/CII, ZUGFeRD-Profil), Prüfergebnis, Pfad des Berichts |
| reconstruction | Status `ok`/`review`, Qualitätswert, PDF-Pfad, Pfad des Prüfberichts |
| retention_until | `JJJJ-12-31`, aus der Dokumentart (`12_deutschland.md` §8) |
| paper_original | `aufbewahren` / `entbehrlich laut VD` |
| local_only | wahr für Kategorien, die nur lokal verarbeitet werden (`Personal`) |

## Aufgabe
| Feld | Hinweise |
|---|---|
| id | Ganzzahl |
| doc_id | das zugehörige Dokument (leer bei Steuerkalender-Aufgaben) |
| recurring_key | z. B. `ustva-2026-11`, eindeutig — Steuerkalender-Aufgaben |
| title | in der Sprache des Nutzers; Verb + Gegenstand + Gegenüber |
| detail | was genau zu tun ist (Betrag, Liste der Unterlagen, Ort, Versandweg) |
| due | aufgedrucktes oder gesetzliches Datum (Fristende, Zahlungsdatum, Termin), ISO |
| do_by | erledigen bis, berechnet (`12_deutschland.md` §3), ISO |
| deadline_basis | wie `due` berechnet wurde, z. B. „Bescheid 20.10.2026 + 4 Tage → 26.10.2026 + 1 Monat“ |
| priority | 1, 2, 3 |
| status | `open` → `done`, oder `cancelled` (mit Grund, z. B. „ersetzt durch Mahnung #3“) |
| blocked_by | Aufgaben-ID (z. B. Zahlung wartet auf IBAN-Bestätigung) |
| created_at, done_at | Zeitstempel |

## Entwurf
| Feld | Hinweise |
|---|---|
| id | Ganzzahl |
| doc_id | das Dokument, auf das geantwortet wird |
| path | Datei in `_office/drafts/` (Markdown); `pdf_path`, sobald erzeugt |
| status | `review` → `approved` (→ `sent`, vom Menschen gesetzt) oder `discarded` |
| delivery_route | ELSTER, Einwurf-Einschreiben, Fax, E-Mail, Post |
| created_at | Zeitstempel |

## Befehle des Kerns (Mindestumfang)
Namen und Syntax wählt die umsetzende KI; die Laufzeit-Anweisungen listen sie auf.

| Zweck | Eingabe | Wirkung |
|---|---|---|
| Baum anlegen | Jahre | legt fehlende Ordner an |
| Posteingang | (Quellen aus der Konfiguration) | neue Dateien in die Warteschlange, Hash-Protokoll, Dubletten beiseitelegen |
| Liste | Filter Status / Kategorie | eine Zeile je Dokument |
| Text | Dokument-ID | gibt den Text aus (mit OCR als Rückfallebene) |
| E-Rechnung | Dokument-ID | erkennen, XML extrahieren, prüfen, Ansicht erzeugen, Fakten ausgeben |
| Rekonstruieren | Dokument-ID oder Bilddateien | vollständige Pipeline aus `13`; gibt Status, Wert und Ausschnitte zum zweiten Lesen aus |
| Lesung zurückgeben | Dokument-ID, Element, Lesung | gibt das zweite Lesen der KI zurück; erzeugt neu |
| Ablegen | ID, Datum, Kategorie, Titel, Absender, Referenz, Beträge, Kurzbeschreibung, Aktion, Befunde | verschiebt, benennt um, schreibt die Kurzbeschreibung, aktualisiert Datensatz und Protokoll |
| Umlegen | ID, neue Kategorie/neues Datum | verschiebt erneut, aktualisiert Datensatz, Kurzbeschreibung und Protokoll |
| Frist | Art, Startdatum, Zeitraum / festes Datum | liefert Fristende und erledigen bis mit Berechnungsgrundlage (Bundesland aus der Konfiguration) |
| Aufgabe anlegen / erledigt / stornieren / Liste | … | verwaltet Aufgaben, lehnt exakte Doppel ab |
| Entwurf anlegen / freigeben / verwerfen / erzeugen / Liste | … | verwaltet Entwürfe; erzeugt DIN-5008-PDF |
| Steuerkalender | Monate voraus | legt die wiederkehrenden Aufgaben an oder aktualisiert sie |
| Fristenplan | optional festes „heute“ (für Tests) | schreibt `SCHEDULE.md` und `schedule.ics` |
| Belegpaket | Monat | baut `Belege_JJJJ-MM/` mit CSV (`12_deutschland.md` §11) |
| Aufbewahrungsbericht | Jahr | listet Dokumente nach Ablauf der Frist (löscht nie) |
| Status | — | Zahlen: wartend, abgelegt, offene/überfällige Aufgaben, Entwürfe und Rekonstruktionen im Review, älteste Datei im Eingang |
| Export | Tabelle | CSV |
| Protokoll prüfen | — | gleicht das Hash-Protokoll mit den Dateien ab |

Alle Befehle prüfen ihre Eingaben (bekannte Kategorie, echtes Datum, vorhandene ID) und brechen mit klarer Meldung ab.
