---
description: Prepare the monthly document package for the Steuerberater
argument-hint: [JJJJ-MM]
---
Build the Belegpaket for the month {{args}} (default: last month) per en/guide/12_germany.md §11 with
`{{CMD_BELEGPAKET}}`: all Buchungsbelege of that month, e-invoices as XML (+ view), reconstructions with their
originals, plus the CSV. Put it into the month folder as `Belege_JJJJ-MM/`. Prepare an email draft to the
Steuerberater with the package as attachment (status review). Do not send it. Report: number of documents, sum
gross, open items (documents in review, missing Pflichtangaben).
