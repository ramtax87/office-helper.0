---
description: Reconstruct, file and handle all queued documents
---
1. List the queued documents with their kinds.
2. For every photo / image / image-only PDF: act as role `reconstruction` (skip if the config says `on_request`;
   then the filing step uses plain OCR and notes it).
3. Act as role `filing` for every queued ID (sub-agents: groups of up to 5).
4. For every document whose action is not `none`, or that has findings, act as role `case` with its ID, actions,
   deadlines, findings and reason (sub-agents: one per document, max 5 in parallel). Documents in local-only
   categories are handled only by a local model.
5. Report per the orchestrator's "End of every run". {{args}}
