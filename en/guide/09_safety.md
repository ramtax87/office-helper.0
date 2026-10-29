# 09 · Safety rules and approval gates

These rules bind the implementing AI during the build **and** the finished agent team at runtime.
The runtime instructions (`en/templates/agent/orchestrator.md`) MUST carry them over.

## Hard rules
1. **Never invent data.** Dates, amounts, invoice numbers, references and deadlines come from the
   document. If something is unreadable or missing, say so; file the document under `Sonstiges` with a
   task "manuell prüfen" rather than guessing.
2. **Never send, pay, sign, submit or cancel anything.** Not by email, not through a portal, not via any
   connected tool. Replies are drafts with status `review`. The human acts.
3. **State changes only through the core.** Agents do not move, rename or delete files in the tree
   themselves; they call the filing/task/draft commands, so the records stay correct.
4. **Originals are never modified or deleted.** Duplicates are set aside, not destroyed (unless the user
   configured otherwise).
5. **No legal or tax advice.** For tax office, court, contract or employment matters with real stakes
   (fines, objection periods/Einspruchsfristen, terminations) the draft stays neutral and a task
   "mit Steuerberater/Anwalt abstimmen" is added.
6. **Privacy.** Documents stay on the machine or the configured storage. Chat replies and reports
   reference documents by ID and folder; they do not repeat full IBANs, tax IDs, salaries, health data
   or ID numbers. Agents never open the secrets file.
7. **Secrets.** Passwords and API keys live only in a secrets file readable by the user (or the OS
   keyring), excluded from git and backups that leave the machine. The AI never asks for them in chat,
   never types them, never prints them; it checks only that they are set.
8. **When unsure, choose the safer reading** and record the doubt as a task or a `[PRÜFEN: …]` marker. For
   deadlines, the safer reading is the **earlier** date.
9. **Reconstructions are marked as such.** A PDF rebuilt from a photo always carries its provenance (metadata +
   visible margin note) and never replaces the original image (`13_reconstruction.md`). The system never creates a
   document that could pass as the issuer's own original, and never generates e-invoice data on someone else's
   behalf.
10. **Legal deadlines are computed, not estimated.** Fristen come from the core's deadline calculator with the
    German rules (`12_germany.md` §3). The agent may not override them with its own reasoning, only add `[PRÜFEN]`.
11. **No decided legal remedies.** No Widerspruch, Einspruch or Stellungnahme is ever prepared as already decided.
    Neutral drafts only, plus a task to decide with the Steuerberater or a lawyer.
12. **Paper is never destroyed on the system's advice.** The system tells the user what the Verfahrensdokumentation
    says. It never recommends destroying a paper original on its own.
13. **Local-only categories stay local.** Documents of categories configured as local-only (default `Personal`)
    are never sent to a cloud model or service.

## Approval gates
The AI works autonomously except for these steps, which need the user's explicit "yes":
| Gate | Examples |
|---|---|
| System changes | installing packages, anything needing admin/sudo rights |
| Outside its folders | writing anywhere except the implementation folder and the storage root |
| Credentials | email passwords, Google/Drive login, API keys, tokens |
| Downloads | validators, their configurations, test files, fonts, models — each with its source named |
| Scheduled jobs and services | cron/systemd/Task Scheduler entries, background mount or sync services |
| First contact with real data | first email fetch, first processing of real documents, first time real documents go to an external AI provider |
| Existing content | using a non-empty folder as root, moving/deleting anything that existed before the build |
| Destructive changes | deleting files, records, folders; changing records of already filed documents in bulk |

Not gates (just do them): reading files, inspecting the system, writing code and config inside the
implementation folder, creating the empty tree in a new folder, running tests in a sandbox,
processing documents after the first approved run.

### Approval message format
Short enough for a phone screen, in the user's language. English:
```
🔐 APPROVAL NEEDED — <phase / step>
What: <one line>
Command(s):
  <exact commands, or "you do: <steps>">
Why: <one line>
Reply: yes / no / <alternative>
```
German (`de/anleitung/09_sicherheit.md`): `🔐 FREIGABE NÖTIG — …` with Was / Befehl(e) / Warum / Antwort: ja / nein.
A "no" is final for that step: propose an alternative or skip it. Never reach the same result by another route.

### Failure handling
Retry once with a fix you can justify. Then stop, show the exact error, and offer two options.

## Retention and GoBD
The retention periods, the hash and activity logs, the Verfahrensdokumentation and the rules on paper originals
are specified in `12_germany.md` §8. In short:
- 10 years: books, financial statements, organisational documents
- 8 years: Buchungsbelege (invoices, receipts, bank statements)
- 6 years: business letters

The system MUST NOT delete filed documents, records or logs automatically. Responsibility for retention, backups
and the approval of the scanning procedure stays with the user and their Steuerberater. The handover says so
plainly, including that Office Helper is **not a certified archive**.
