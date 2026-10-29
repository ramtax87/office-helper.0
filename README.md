# Office Helper — KI-Büroassistent für deutsche Unternehmen / AI office assistant for German companies

**[Deutsch](#deutsch) · [English](#english)**

---

## Deutsch

Ein **Bauplan** für einen KI-Assistenten, der in deutschen Unternehmen Papierablage, Fristen und Korrespondenz in Ordnung
hält. Das Repository enthält keine fertige App, sondern alles, was eine KI braucht, um das System **auf dem Zielrechner
selbst zu bauen**: Spezifikationen, den Bauablauf mit Freigabepunkten, Vorlagen, Startprompts und Testdokumente mit
Sollergebnissen. Die ganze Anleitung liegt auf **Deutsch und Englisch** vor; die KI fragt zuerst, welche Sprache du willst.

Funktioniert mit jeder KI, die Dateien lesen und Befehle ausführen kann: Claude Code, OpenAI Codex, Gemini CLI, Cursor,
GitHub Copilot, Aider, Grok oder ein lokales Modell.

### Was das fertige System kann
1. **Ablage aufbauen:** fragt nach Name und Speicherort (lokal oder Google Drive) und legt
   `Jahr / 01_Januar … 12_Dezember / Kategorie` an.
2. **Posteingang aus mehreren Quellen:** Scanner, Handyfotos, mehrere E-Mail-Postfächer, E-Rechnungen (XRechnung,
   ZUGFeRD), Downloads aus Portalen.
3. **Lesen und ablegen** nach Datum und Inhalt, mit Kurzbeschreibung. Prüft Rechnungen nach § 14 UStG, rechnet nach,
   warnt bei geänderter IBAN, berechnet die Aufbewahrungsfrist.
4. **Jeder Vorgang einzeln mit Sub-Agenten:** Aufgaben mit gesetzlich berechneten Fristen (4-Tage-Bekanntgabe,
   Wochenenden, Feiertage je Bundesland) und Antwortentwürfe nach DIN 5008 zur Freigabe — nie ein fertig entschiedener
   Widerspruch oder Einspruch.
5. **Prioritätenplan mit Erinnerungen:** „Fristende“ und „erledigen bis“, Steuerkalender, Kalenderdatei.
6. **Foto → echtes PDF:** Rekonstruktion mit echtem Text, eingebetteten Schriften, Vektorlinien, Tabellen und neu
   erzeugtem GiroCode — kein Bild im PDF, mit Herkunftsvermerk; das Foto bleibt der Beleg.

Dazu GoBD-Hilfen (Hash- und Aktivitätsprotokoll, ausgefüllte Verfahrensdokumentation), ein monatliches Belegpaket für den
Steuerberater und DSGVO-Einstellungen.

> Das System versendet, bezahlt, unterschreibt und vernichtet nichts. Es unterstützt die GoBD, ist aber kein
> zertifiziertes Archiv und ersetzt keine Steuer- oder Rechtsberatung.

### So geht's
1. Repository auf dem Rechner klonen, auf dem das System laufen soll:
   ```bash
   git clone git@github.com:ramtax87/office-helper.0.git office-helper && cd office-helper
   ```
2. Den Ordner in einem KI-Werkzeug öffnen und den Startprompt aus [de/prompts/START.md](de/prompts/START.md) einfügen.
3. Die KI fragt zuerst nach der Sprache, liest dann die Anleitung, stellt einmal alle Fragen, baut das System, besteht die
   Abnahmetests und übergibt. Rückfragen nur an den Freigabepunkten (Installationen, Passwörter, geplante Aufgaben, erster
   Kontakt mit echten Dokumenten, alles außerhalb ihrer Ordner).

---

## English

A **blueprint** for an AI assistant that keeps paperwork, deadlines and correspondence in order for companies in
Germany. This repository contains no finished app. It contains everything an AI needs to **build the system itself on
the target computer**: specifications, the build process with approval gates, templates, bootstrap prompts and test
documents with expected results. The whole guide exists in **German and English**; the AI first asks which language
you want.

Works with any AI that can read files and run commands: Claude Code, OpenAI Codex, Gemini CLI, Cursor, GitHub Copilot,
Aider, Grok or a local model.

### What the finished system does
1. **Storage tree:** asks for the name and the storage (local or Google Drive) and creates
   `year / 01_Januar … 12_Dezember / category`.
2. **Intake from several sources:** scanner, phone photos, several mailboxes, e-invoices (XRechnung, ZUGFeRD), portal
   downloads.
3. **Reading and filing** by date and content, with a summary. Checks invoices under § 14 UStG, recalculates them,
   warns about changed IBANs, computes the retention period.
4. **Every case on its own, with sub-agents:** tasks with legally computed deadlines (4-day rule, weekends, holidays per
   federal state) and DIN 5008 reply drafts for approval — never an objection decided on its own.
5. **Priority schedule with reminders:** legal deadline and "do by", German tax calendar, calendar file.
6. **Photo → real PDF:** reconstruction with real text, embedded fonts, vector lines, tables and a regenerated GiroCode —
   no picture inside the PDF, with a provenance note; the photo stays the original record.

Plus GoBD support (hash and activity logs, filled-in Verfahrensdokumentation), a monthly package for the tax advisor and
GDPR settings. Letters to German recipients are always written in German.

> The system never sends, pays, signs or destroys anything. It supports GoBD compliance but is not a certified archive
> and is no substitute for tax or legal advice.

### How to use it
1. Clone the repository on the computer that should run the system:
   ```bash
   git clone git@github.com:ramtax87/office-helper.0.git office-helper && cd office-helper
   ```
2. Open the folder in an AI tool and paste the bootstrap prompt from [en/prompts/BOOTSTRAP.md](en/prompts/BOOTSTRAP.md).
3. The AI first asks for the language, then reads the guide, asks all setup questions once, builds the system, passes the
   acceptance tests and hands over. It asks only at the approval gates (installs, passwords, scheduled jobs, first contact
   with real documents, anything outside its folders).

---

## Inhalt / Contents
| Pfad / Path | Inhalt / Content |
|---|---|
| `AGENTS.md` | Einstieg: Sprachfrage / entry point: language question |
| `de/` | Deutsche Fassung: `AGENTS.md`, `anleitung/00–13`, `vorlagen/agent/`, `prompts/` |
| `en/` | English version: `AGENTS.md`, `guide/00–13`, `templates/agent/`, `prompts/` |
| `shared/templates/` | Konfiguration, Antwortbrief DIN 5008, Verfahrensdokumentation, Kurzbeschreibung (de/en), Dokumentmodell / config, letter, process documentation, summary, document model |
| `examples/test-documents/` | 13 fiktive deutsche Testdokumente + `EXPECTED.de.md` / `EXPECTED.en.md` |
| `examples/reconstruction/` | Handyfotos + Referenz-PDFs für die Rekonstruktion / phone photos + reference PDFs |
| `CLAUDE.md`, `GEMINI.md` | verweisen auf `AGENTS.md` / point to `AGENTS.md` |

| Kapitel / Chapter | Deutsch | English |
|---|---|---|
| Überblick / Overview | `de/anleitung/00_ueberblick.md` | `en/guide/00_overview.md` |
| Bauablauf / Build process | `de/anleitung/01_bauablauf.md` | `en/guide/01_build_process.md` |
| Ablage / Storage | `de/anleitung/02_ablage.md` | `en/guide/02_storage.md` |
| Posteingang / Intake | `de/anleitung/03_posteingang.md` | `en/guide/03_intake.md` |
| Ablegen / Filing | `de/anleitung/04_ablegen.md` | `en/guide/04_filing.md` |
| Vorgänge / Cases | `de/anleitung/05_vorgaenge.md` | `en/guide/05_cases.md` |
| Fristenplan / Schedule | `de/anleitung/06_fristenplan.md` | `en/guide/06_schedule.md` |
| Datenmodell / Data model | `de/anleitung/07_datenmodell.md` | `en/guide/07_data_model.md` |
| Agenten & Anbieter / Agents & providers | `de/anleitung/08_agenten_und_anbieter.md` | `en/guide/08_agents_and_providers.md` |
| Sicherheit / Safety | `de/anleitung/09_sicherheit.md` | `en/guide/09_safety.md` |
| Abnahmetests / Acceptance tests | `de/anleitung/10_abnahmetests.md` | `en/guide/10_acceptance_tests.md` |
| Betrieb / Operations | `de/anleitung/11_betrieb.md` | `en/guide/11_operations.md` |
| Deutschland / Germany | `de/anleitung/12_deutschland.md` | `en/guide/12_germany.md` |
| Rekonstruktion / Reconstruction | `de/anleitung/13_rekonstruktion.md` | `en/guide/13_reconstruction.md` |
