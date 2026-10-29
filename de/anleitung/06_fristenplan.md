# 06 · Aufgabe 5 — Prioritätenplan

Ziel: eine Liste auf Deutsch mit allem, was der Nutzer tun muss, nach Dringlichkeit geordnet. Jeder Eintrag hat Frist,
Erinnerung („Timer“), Kurzbeschreibung und den Ordner, in dem die Dokumente liegen.

## Prioritäten (MUSS)
- **P1** — gesetzliche oder steuerliche Fristen, Mahnungen, Mahnbescheide, IBAN-Wechsel-Warnungen, alles mit Bußgeld oder
  Rechtswirkung, oder fällig innerhalb von 7 Tagen
- **P2** — normale Rechnungen, Briefe mit Antwortbedarf, Lieferungen/Termine, unvollständige Rechnungen
- **P3** — Informationen, Hinweise (E-Rechnungs-Hinweis, Portal-Download), lange Sicht

Die Priorität wird beim Anlegen gesetzt und beim Erstellen des Plans neu berechnet: Eine P2-Aufgabe, die in ≤ 7 Tagen
fällig ist, erscheint als P1.

## Daten
- Jede Aufgabe hat `due` (aufgedrucktes oder gesetzliches Datum: Fristende, Zahlungsdatum, Lieferdatum) und `do_by`
  (erledigen bis). `do_by` berechnet der Kern (`12_deutschland.md` §3):
  - was jemanden erreichen muss: minus 3 Arbeitstage
  - Zahlungen: minus 1 Arbeitstag
  - Ereignisse (Lieferung, Termin): `do_by` = `due`
- Wochenenden und die Feiertage des eingestellten Bundeslands werden beachtet.
- Der Plan sortiert und gruppiert nach **`do_by`** und zeigt immer beide Daten, wenn sie sich unterscheiden.

## Gruppen und Reihenfolge
1. **Überfällig** — `do_by` liegt in der Vergangenheit
2. **Diese Woche** — `do_by` ≤ 7 Tage
3. **Nächste 30 Tage**
4. **Später**
5. **Entwürfe zur Prüfung**
6. **Zur Prüfung** — Rekonstruktionen und Dokumente im Status review

Innerhalb jeder Gruppe: nach `do_by`, dann Priorität.

## Format der Einträge (`<ROOT>/SCHEDULE.md`)
```
# Fristen und Aufgaben
_Stand: Freitag, 30.10.2026 · 14 offene Aufgaben · 2 überfällig_

## Diese Woche
- **P1 · erledigen bis Mo 02.11.2026** (fällig 03.11.2026) — Mahnung Telekom bezahlen
  94,90 € inkl. Mahngebühr · zur Rechnung 4711 vom 01.09.2026 (Dok. #2)
  Ordner: 2026/10_Oktober/Rechnungen_Eingang · Aufgabe #12 · Dokument #3
- **P1 · erledigen bis Mo 02.11.2026** (Fristende 05.11.2026) — Mahnbescheid Krüger: Widerspruch prüfen – mit Anwalt abstimmen
  1.402,65 € · [PRÜFEN: Zustelldatum auf dem gelben Umschlag notieren] · Rechnung 88-2026-117 nicht im Archiv
  Ordner: 2026/10_Oktober/Recht_Gericht · Aufgabe #18 · Dokument #10

## Nächste 30 Tage
- **P1 · erledigen bis Mo 23.11.2026** (Fristende Do 26.11.2026) — Steuerbescheid USt 2025 prüfen – mit Steuerberater abstimmen
  Abschlusszahlung 1.284,00 € fällig 23.11.2026 (eigene Aufgabe #21) · Versandweg: ELSTER
  Ordner: 2026/10_Oktober/Finanzamt · Aufgabe #22 · Dokument #9

## Später
- **P1 · erledigen bis Fr 19.03.2027** (Frist 24.03.2027) — Unterlagen an Finanzamt Paderborn senden
  Kontoauszüge 2025 (fehlt), Kassenbuch 2025 (fehlt), Einnahmenüberschussrechnung 2025 (fehlt)
  Ordner: 2026/10_Oktober/Finanzamt · Aufgabe #9 · Dokument #4
```
Anforderungen:
- Datum `TT.MM.JJJJ` mit kurzem Wochentag; „in N Tagen“ / „N Tage überfällig“ / „heute“ KANN ergänzt werden.
- Kopfzeile mit Datum und Anzahl offener und überfälliger Aufgaben.
- Auf dem Handy lesbar: einfaches Markdown, keine breiten Tabellen.
- IBANs nur gekürzt. Keine Namen oder Gehälter von Beschäftigten aus `Personal`-Dokumenten.

## Steuerkalender (wiederkehrende Aufgaben)
Hat der Nutzer steuerliche Pflichten bestätigt (`12_deutschland.md` §2 d–f), hält der Fristenplan die wiederkehrenden
Aufgaben der nächsten 3 Monate im Plan (`12_deutschland.md` §4):
- Umsatzsteuer-Voranmeldung, Lohnsteuer-Anmeldung
- ESt/KSt-, Gewerbesteuer- und Grundsteuer-Vorauszahlungen
- SV-Beiträge
- die Sondervorauszahlung

Jeweils mit verschobenem Datum und dem Betrag aus dem letzten Bescheid im Baum, falls vorhanden. Wiederkehrende Aufgaben
haben einen festen Schlüssel (z. B. `ustva-2026-11`), damit sie nur einmal angelegt und als erledigt markiert werden
können.

## Erinnerungen — Kalenderdatei (MUSS)
`<ROOT>/schedule.ics` (iCalendar) wird bei jedem Planlauf neu erstellt:
- ein ganztägiger Termin je offener Aufgabe am `do_by`-Datum
  - Titel `[P1] <Aufgabentitel>`
  - Beschreibung: Kurzbeschreibung, `Fristende <due>`, Ordner
- Alarme (VALARM) N Tage vor `do_by` — einstellbar, Standard 7, 3 und 1 Tag
- eine feste UID je Aufgabe (z. B. `office-helper-task-<id>@local`), damit ein erneuter Import aktualisiert statt
  verdoppelt
- erledigte Aufgaben verschwinden beim nächsten Lauf
- Zeitzone `Europe/Berlin` für Termine mit Uhrzeit (Lieferungen mit Zeitfenster)

Der Nutzer importiert oder abonniert diese Datei in Google Kalender, Thunderbird, Outlook oder dem Handy-Kalender. Liegt
der Hauptordner auf Drive, hält ein Abonnement über einen Freigabelink sie aktuell. Die umsetzende KI KANN zusätzlich
Termine direkt in einem verbundenen Kalender anlegen. Das ist ein Zugangsdaten-Freigabepunkt (🔐), und die Kalenderdatei
bleibt die maßgebliche Quelle.

## Kurzbericht (Chat)
Nach jedem Lauf meldet der Fristenplan, höchstens 10 Zeilen:
```
Mahnung Telekom: 94,90 € bezahlen bis Mo 02.11.2026 (fällig 03.11.)
Mahnbescheid Krüger: bis Mo 02.11.2026 mit Anwalt klären – Zustelldatum auf dem gelben Umschlag notieren
Kartoffellieferung Do 05.11.2026, 7–9 Uhr annehmen
IBAN-Änderung Kartoffel Müller vor Zahlung telefonisch bestätigen
Finanzamt Paderborn will Unterlagen bis 24.03.2027 – liegen in 2026/10_Oktober/Finanzamt
… und 9 weitere in SCHEDULE.md · 3 Entwürfe zur Prüfung
```
Überfälliges zuerst, dann P1, dann nach `do_by`.

## Plausibilitätsprüfung (melden, nie selbst korrigieren)
- Fälligkeiten weit in der Vergangenheit oder Zukunft (Tippfehler im Jahr?)
- doppelte Aufgaben zu gleichem Dokument und gleicher Frist
- Aufgaben, deren Dokument nicht mehr am Pfad liegt
- eine Zahlungsaufgabe, deren IBAN von der früheren des Lieferanten abweicht, ohne bestätigte Warnaufgabe
