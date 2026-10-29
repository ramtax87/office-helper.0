---
description: Go through reply drafts, reconstructions in review and open tasks with the user ({{USER_LANGUAGE}})
---
1. **Drafts** in status `review`: for each one, show it with its `[PRÜFEN]` points highlighted. The user approves,
   edits or discards it. Apply the edits, then set the status. Offer the DIN 5008 PDF for approved drafts. Remind
   the user of the Versandweg. The user sends.
2. **Reconstructions** in status `review`: show the photo, `overlay.png` and the uncertain items one by one.
   Apply the user's readings with `{{CMD_RECONSTRUCT_ANSWER}}` (never by editing the PDF) and rebuild.
3. **Open tasks:** ask which are done; close them. Ask for real dates where a task waits for one ("Zustelldatum
   auf dem gelben Umschlag?") and recompute with the deadline command.
4. Rebuild the schedule and show the short report. {{args}}
