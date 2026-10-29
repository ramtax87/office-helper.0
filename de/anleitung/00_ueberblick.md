# 00 · Überblick

## Zweck
Ein kleines deutsches Unternehmen bekommt jeden Tag Papier und E-Mails:
- Rechnungen, Mahnungen, E-Rechnungen (XRechnung/ZUGFeRD)
- Briefe vom Finanzamt, von der Stadt, der Krankenkasse und der Berufsgenossenschaft
- Lieferscheine, Verträge, Kassenbons, Newsletter, ab und zu ein Mahnbescheid

Office Helper ist ein KI-Assistent, der:
- **jedes Dokument an den richtigen Platz legt**
- **Fotos von Papier in echte, durchsuchbare PDFs verwandelt**
- **nach deutschem Recht ermittelt, was bis wann zu tun ist**
- **die Antworten vorbereitet**
- **eine einzige priorisierte Fristenliste führt**

So geht nichts verloren und keine Frist wird versäumt.

Der Nutzer behält die Kontrolle: Das System bereitet vor, der Mensch versendet, bezahlt und unterschreibt.
Alles, was der Nutzer sieht, ist in der gewählten Sprache (Deutsch oder Englisch). Briefe an deutsche Empfänger sind
immer Deutsch.

## Die sechs Aufgaben
| # | Aufgabe | Spezifikation |
|---|---|---|
| 1 | Ablagebaum anlegen: Hauptordner → Jahr → 12 Monate → Kategorien | `02_ablage.md` |
| 2 | Dokumente aus mehreren Quellen empfangen (Scanner, Handy, E-Mail, E-Rechnungen, Ordner) | `03_posteingang.md` |
| 3 | Jedes Dokument lesen und nach Datum und Inhalt ablegen | `04_ablegen.md` |
| 4 | Jedes Dokument als eigenen Vorgang mit Sub-Agenten bearbeiten; Antwortentwürfe zur Prüfung | `05_vorgaenge.md` |
| 5 | Prioritätenplan mit Frist, Erinnerung und Kurzbeschreibung je Aufgabe; deutscher Steuerkalender | `06_fristenplan.md` |
| 6 | Fotos und Bildscans als echte PDF-Dokumente rekonstruieren (Text, Schriften, Layout, Vektoren) | `13_rekonstruktion.md` |

Die deutschen Rechts- und Praxisregeln für alle sechs Aufgaben stehen in `12_deutschland.md`:
- Fristberechnung, Bekanntgabefiktion, Feiertage
- die Regeln zur E-Rechnung
- GoBD, Aufbewahrungsfristen und die Verfahrensdokumentation
- Rechnungsprüfung (§ 14 UStG) und Schutz vor IBAN-Betrug
- Briefe nach DIN 5008
- die Übergabe an den Steuerberater und DATEV
- DSGVO

## Architektur

```
 Scanner / Handyfoto          E-Mail (IMAP, mehrere Postfächer)        Weitere Ordner / ELSTER-Downloads
          │                           │                                      │
          └────────────────► EINGANG (_Eingang) ◄──────────────────────────────┘
                                     │
                    [posteingang]    │  übernehmen, Hash + Hash-Protokoll, Dubletten erkennen, registrieren
                                     ▼
                              WARTESCHLANGE
                    ┌────────────────┼────────────────────┐
           E-Rechnung XML /          │               Foto / Bildscan /
           ZUGFeRD-PDF               │               reines Bild-PDF
                    │                │                     │
          [ablage] prüfen,           │        [rekonstruktion] entzerren → OCR-Konsens →
          XML lesen, Ansicht         │        Schriften/Layout → Vektor-PDF + Herkunftsvermerk + Prüfbericht
                    └────────────────┼─────────────────────┘
                    [ablage]         ▼  Fakten → Kategorie, Aktion, Fristen, § 14-Prüfung, IBAN-Prüfung, Aufbewahrung
       ROOT / 2026 / 10_Oktober / Rechnungen_Eingang / 2026-10-28_Kartoffel_Mueller_Rechnung_1205.pdf (+ .md)
                                     │
                    [vorgang]        │  ein Vorgang je Dokument: Aufgaben, zugehörige Dokumente, Antwortentwurf DIN 5008
                                     ▼
                       AUFGABEN  +  ENTWÜRFE (Status: review)
                                     │
                    [fristenplan]    │  Fristende / erledigen bis, Steuerkalender, Feiertage, Erinnerungen
                                     ▼
             SCHEDULE.md (Sprache des Nutzers)  +  schedule.ics  +  monatliches Belegpaket für den Steuerberater
```

Zwei Schichten, bewusst getrennt:
- **Deterministischer Kern.** Das ist Code, den die umsetzende KI schreibt. Er umfasst:
  - Ordner, das Verschieben von Dateien, Datensätze, Hash- und Aktivitätsprotokoll (07)
  - den Fristenrechner und den Feiertagskalender
  - die E-Rechnungs-Prüfung
  - die Mess-, Erzeugungs- und Prüfschritte der Rekonstruktion
  - Fristenplan, Kalenderdatei und Exporte

  Gleiche Eingabe → gleiches Ergebnis.
- **KI-Schicht.** Das ist das Agenten-Team (08). Es liest und versteht Dokumente, entscheidet Kategorie und Aktion,
  schreibt Kurzbeschreibungen und Entwürfe und übernimmt das zweite Lesen unsicherer Textstellen. Es ändert den Zustand
  **nur über den Kern**. Gesetzliche Fristen berechnet der Kern, nicht die KI.

## Gestaltungsprinzipien
1. **Anbieterunabhängig.** Die Anweisungen sind einfaches Markdown, der Kern ist gewöhnlicher Code. Ein Wechsel des
   KI-Anbieters darf keine Datenmigration erfordern.
2. **Nichts erfinden.** Jedes Datum, jeder Betrag, jede Nummer stammt aus einem Dokument. Rekonstruktionen geben das
   Papier wieder, Fehler eingeschlossen.
3. **Originale unangetastet.** Dateien werden verschoben und umbenannt, nie bearbeitet. Hashwerte belegen das. Jedes
   abgelegte Dokument bekommt eine Kurzbeschreibung in der Sprache des Nutzers daneben.
4. **Mensch entscheidet.** Versenden, Bezahlen, Unterschreiben, Einreichen und das Vernichten von Papier: immer der
   Mensch.
5. **Ohne das System lesbar.** Ordnerbaum, Dateinamen, Kurzbeschreibungen, Fristenplan und Verfahrensdokumentation
   müssen für einen Menschen, einen Steuerberater oder einen Betriebsprüfer verständlich sein, der den Ordner öffnet —
   auch wenn die KI weg ist.
6. **Idempotent.** Jeder Schritt darf zweimal laufen, ohne Dokumente, Aufgaben oder Ordner zu verdoppeln.
7. **Für Deutschland gemacht.** Deutsches Recht, Fristen und Formate (`12_deutschland.md`); Gespräch auf Deutsch oder
   Englisch, wie zu Beginn gewählt.
