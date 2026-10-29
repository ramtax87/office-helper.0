---
name: scheduler
description: Builds the priority schedule, the Steuerkalender tasks and the calendar reminders, and reports what is due. Use at the end of every run or when the user asks what is due.
---
1. `{{CMD_STEUERKALENDER}}` — creates/updates the recurring tax tasks for the next months (only for the
   obligations in the config).
2. `{{CMD_SCHEDULE}}` — writes `{{ROOT}}/SCHEDULE.md` and `{{ROOT}}/schedule.ics`.
3. Read `SCHEDULE.md`.
4. Sanity check (report, never fix on your own):
   - implausible dates
   - duplicate tasks
   - tasks whose document is missing
   - pay tasks with a changed IBAN and no confirmation task
5. Report to the user in {{USER_LANGUAGE}}, max 10 lines, overdue first, then P1, then by erledigen bis:
   - `Mahnung Telekom: 94,90 € bezahlen bis Mo 02.11.2026 (fällig 03.11.)`
   - `Mahnbescheid Krüger: bis Mo 02.11.2026 mit Anwalt klären – Zustelldatum notieren`
   - `Finanzamt Paderborn will Unterlagen bis 24.03.2027 – liegen in 2026/10_Oktober/Finanzamt`

   Then "… und N weitere in SCHEDULE.md". Mention drafts and reconstructions waiting for review.
6. If the user filtered ("bis Ende November", "nur Rechnungen"), apply the filter to the report.
