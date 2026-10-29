# 06 · Task 5 — Priority schedule

Goal: one list, in the user's language, of everything the user has to do, ordered by urgency. Each item has its deadline, a
reminder ("timer"), a short summary, and the folder where the documents are.

## Priorities (MUST)
- **P1** — legal or tax deadlines, Mahnungen, Mahnbescheide, IBAN-change warnings, anything with fines or legal
  effect, or due within 7 days
- **P2** — normal invoices, letters needing a reply, deliveries/appointments, incomplete invoices
- **P3** — information, hints (e-invoice hint, portal download), long horizon

Priority is set when the task is created and recalculated when the schedule is built: a P2 task due in ≤ 7 days
is shown as P1.

## Dates
- Each task carries `due` (the printed or legal date: Fristende, payment date, delivery date) and `do_by`
  (erledigen bis). `do_by` is computed by the core (`12_germany.md` §3):
  - things that must reach someone: minus 3 working days
  - payments: minus 1 working day
  - events (a delivery, an appointment): `do_by` = `due`
- Weekends and the holidays of the configured Bundesland are respected.
- The schedule sorts and buckets by **`do_by`**, and always shows both dates when they differ.

## Buckets and order
1. **Überfällig** — `do_by` in the past
2. **Diese Woche** — `do_by` ≤ 7 days
3. **Nächste 30 Tage**
4. **Später**
5. **Entwürfe zur Prüfung** — drafts waiting for review
6. **Zur Prüfung** — reconstructions and documents in status review

Inside each bucket: by `do_by`, then priority.

## Item format (`<ROOT>/SCHEDULE.md`) — German example; the English one follows
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
Requirements:
- Dates `TT.MM.JJJJ` with a short weekday (German format also for English users, matching the documents);
  "in N days" / "N days overdue" / "today" (German: "in N Tagen" / "N Tage überfällig" / "heute") MAY be added.
- Bucket names and texts in the user's language: Überfällig / Overdue, Diese Woche / This week, Nächste 30 Tage /
  Next 30 days, Später / Later, Entwürfe zur Prüfung / Drafts to review, Zur Prüfung / To review.
- A header with the date and the number of open and overdue tasks.
- Readable on a phone: plain markdown, no wide tables.
- IBANs only shortened. No employee names or salaries from `Personal` documents.

English version of the same items:
```
# Deadlines and tasks
_As of: Friday, 30.10.2026 · 14 open tasks · 2 overdue_

## This week
- **P1 · do by Mon 02.11.2026** (due 03.11.2026) — Pay Telekom reminder (Mahnung)
  94,90 € incl. reminder fee · for invoice 4711 of 01.09.2026 (doc #2)
  Folder: 2026/10_Oktober/Rechnungen_Eingang · Task #12 · Document #3
- **P1 · do by Mon 02.11.2026** (legal deadline 05.11.2026) — Court payment order (Mahnbescheid) Krüger: decide on objection (Widerspruch) with a lawyer
  1.402,65 € · [PRÜFEN: note the delivery date on the yellow envelope] · invoice 88-2026-117 not in the archive
  Folder: 2026/10_Oktober/Recht_Gericht · Task #18 · Document #10
```

## Steuerkalender (recurring tasks)
If the user confirmed tax obligations (`12_germany.md` §2 d–f), the scheduler keeps the recurring tasks of the
next 3 months in the schedule (`12_germany.md` §4):
- Umsatzsteuer-Voranmeldung, Lohnsteuer-Anmeldung
- ESt/KSt-, Gewerbesteuer- and Grundsteuer-Vorauszahlungen
- SV-Beiträge
- the Sondervorauszahlung

Each with the shifted date and the amount from the last Bescheid in the tree if one is found. Recurring tasks
have a stable key (e.g. `ustva-2026-11`), so they are created once and can be marked done.

## Reminders — calendar file (MUST)
`<ROOT>/schedule.ics` (iCalendar) is rebuilt on every schedule run:
- one all-day event per open task on its `do_by` date
  - title `[P1] <task title>`
  - description: the summary, `Fristende <due>`, the folder
- alarms (VALARM) N days before `do_by` — configurable, default 7, 3 and 1 days
- a stable UID per task (e.g. `office-helper-task-<id>@local`), so re-imports update instead of duplicating
- closed tasks disappear on the next build
- the time zone `Europe/Berlin` for any timed events (deliveries with a time window)

The user imports or subscribes to this file in Google Calendar, Thunderbird, Outlook or the phone calendar. If the
root is on Drive, subscribing to a shared link keeps it current. The implementing AI MAY also create events
directly in a calendar the user connects. That is a credentials gate (🔐), and the calendar file stays the source
of truth.

## Short report (chat, user's language)
After each run the scheduler tells the user, max 10 lines:
```
Mahnung Telekom: 94,90 € bezahlen bis Mo 02.11.2026 (fällig 03.11.)
Mahnbescheid Krüger: bis Mo 02.11.2026 mit Anwalt klären – Zustelldatum auf dem gelben Umschlag notieren
Kartoffellieferung Do 05.11.2026, 7–9 Uhr annehmen
IBAN-Änderung Kartoffel Müller vor Zahlung telefonisch bestätigen
Finanzamt Paderborn will Unterlagen bis 24.03.2027 – liegen in 2026/10_Oktober/Finanzamt
… und 9 weitere in SCHEDULE.md · 3 Entwürfe zur Prüfung
```
English:
```
Telekom reminder: pay 94,90 € by Mon 02.11.2026 (due 03.11.)
Court payment order Krüger: settle with a lawyer by Mon 02.11.2026 – note the delivery date on the envelope
Potato delivery Thu 05.11.2026, 7–9 am: accept and check
IBAN change Kartoffel Müller: confirm by phone before paying
Finanzamt Paderborn wants documents by 24.03.2027 – they are in 2026/10_Oktober/Finanzamt
… and 9 more in SCHEDULE.md · 3 drafts to review
```
Overdue first, then P1, then by `do_by`.

## Sanity checks (report, never auto-fix)
- due dates far in the past or the future (a year typo?)
- duplicate tasks for the same document and deadline
- tasks whose document no longer exists at its path
- a pay task whose IBAN differs from the supplier's earlier IBAN, without a confirmed warning task
