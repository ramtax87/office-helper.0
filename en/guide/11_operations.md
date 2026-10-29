# 11 · Operations

What the finished system looks like in daily use. The implementing AI MUST make each of these work and explain
them in the handover, in the user's language.

## Daily routine (user)
1. Scan or photograph the day's paper into `_Eingang` (scanner or phone). Email arrives on its own.
2. Say **"daily run"** ("Tageslauf"), or let the scheduled run do it in the morning.
3. Read the short report and `SCHEDULE.md`.
4. When drafts are waiting, say **"review drafts"** ("Entwürfe prüfen"): approve, edit or discard them. Send
   approved ones yourself (the delivery route is noted).
5. Tell the agent what is done ("invoice 1205 is paid", "objection filed via ELSTER") and it closes the task.

| Say (English / Deutsch) | Effect |
|---|---|
| "daily run" / "Tageslauf" | intake → reconstruction/filing → cases → schedule + report |
| "What is due?" / "What do I have to pay by the end of November?" | schedule report, filtered |
| "Document 37: the delivery was incomplete, write a complaint" | single case with instructions; the letter is German |
| "review drafts" / "Entwürfe prüfen" | draft-by-draft review, then a task check |
| "Make a PDF from this photo" (+ photo) | reconstruction, the PDF is filed |
| "Where is the Telekom invoice from August and how much was it?" | search the summaries, answer with path and amount |
| "Document 37 belongs in Verträge" | refile through the core |
| "New category Fahrzeuge" | config change + tree extension (existing files untouched) |
| "Create the tree for 2028" | new year folders |
| "October documents for the tax advisor" | Belegpaket + CSV + email draft (`12_germany.md` §11) |
| "The delivery date of the Mahnbescheid was 24.10." | recomputes the deadline from the real date |

More copy-paste prompts: `en/prompts/DAILY_PROMPTS.md`.

## Monthly
- Prepare the Belegpaket for the Steuerberater (first working days of the month).
- Check `Sonstiges` and the documents in review.
- Look at the Steuerkalender for the next months; enter the amounts from new Vorauszahlungsbescheide.
- Cancel open tasks that no longer apply.

## Yearly
- Create the next year's tree in autumn (automatic from October).
- **Retention report:** list the documents past their retention period. The user decides with the Steuerberater;
  the system deletes nothing.
- Review and re-date the Verfahrensdokumentation (at least yearly, and on every change).
- Check the ⟳ rules of `12_germany.md` (holidays, Steuerkalender, e-invoice rules) against official sources.
  Update the build and its tests if a rule changed.

## Backup (MUST be recommended in the handover)
- The storage root (documents + records + logs) is what matters. Google Drive is a copy, not a backup: keep a
  periodic backup elsewhere as well (external disk, second cloud, NAS), and test a restore once.
- The implementation folder can be rebuilt from this guide. Back up its config and secrets file separately and
  securely.

## Troubleshooting
| Symptom | Likely cause → fix |
|---|---|
| Scans produce no or broken text ("Kontoausziige") | OCR or German language data missing; scan below 300 dpi |
| A photo reconstruction looks off | page edges not found (photograph on a dark, plain surface), strong curl, glare; see `overlay.png` and `review.md` |
| Wrong font in a reconstruction | a better-matching free or licensed font is missing → install it and re-run `reconstruct` |
| E-invoice "ungültig" | look at the validation report. Format error → ask the supplier for a correct e-invoice; an outdated validator configuration → update it ⟳ |
| Email login fails | App-Passwort needed (Gmail); IMAP disabled in the mailbox settings (GMX, web.de); password not set in the secrets file |
| Document filed under the scan date | the printed date was not found → check the OCR or reconstruction quality; refile |
| Deadline looks wrong | check `deadline_basis` of the task; enter the real delivery/service date ("Zustelldatum war …") |
| Drive folder not found or empty | the mount/sync service is not running → restart it; check whether the login expired |
| The same task appears twice | duplicate check in the core missing → acceptance test C9 |
| The calendar shows duplicates | UIDs not stable → acceptance test C12 |
| Standalone runner errors | key not set, wrong base URL, or a model without tool calling (`08`) |
| The agent ignores the rules | instruction file not loaded by the tool → check the wiring (`08`), re-run phase 6 |
