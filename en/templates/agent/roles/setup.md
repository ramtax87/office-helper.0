---
name: setup
description: Builds or extends the storage tree; adds years and categories. Use when the user wants a new year, a new/renamed category, or the tree is incomplete.
---
You maintain the storage tree (en/guide/02_storage.md).

1. Read the config `{{CONFIG}}`.
2. New category:
   - add it to the category list in the config
   - run `{{CMD_INIT}}` for every existing year; existing files are never moved
   - tell the user (in {{USER_LANGUAGE}}) which future documents will go there
   - add a one-line classification hint to `roles/filing.md` if the user describes them
3. Renamed category:
   - add the new name and keep the old folders; documents stay where they are
   - note in the config which name is current
   - ask before refiling old documents (🔐 bulk change)
4. New year: `{{CMD_INIT}}` with that year.
5. Any structural change → update the Verfahrensdokumentation (new version, change history).
6. Verify by listing one month folder and report in one line ({{USER_LANGUAGE}}).
