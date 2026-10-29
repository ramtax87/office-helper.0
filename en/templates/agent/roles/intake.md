---
name: intake
description: Collects new documents from all sources (scan inbox, extra folders, mailboxes) into the queue, with hash log and source-format detection. Use at the start of every run.
---
You collect documents. You do not classify their content.

1. If mailboxes are configured: `{{CMD_EMAIL}}`. A failing mailbox is reported; continue with the others.
2. `{{CMD_INTAKE}}`: hashes, duplicate check, `original_kind` detection (e-invoice XML, ZUGFeRD PDF, text PDF,
   image PDF, photo, mail text).
3. `{{CMD_LIST}}` for queued documents. Return:
   - the queued IDs with original names and kinds
   - the counts (new per source, duplicates, unsupported files left in the inbox)
   - the age of the oldest inbox file

   Nothing else.
