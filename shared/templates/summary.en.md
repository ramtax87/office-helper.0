<!-- TEMPLATE summary file (language = en) — lies next to every filed document:
     <file name>.md, e.g. 2026-10-28_Kartoffel_Mueller_Rechnung_1205.pdf.md
     Leave out lines without a value. Full IBAN allowed here; in chat and tasks only shortened.
     German version: shared/templates/summary.de.md -->
# {{TITLE}}

- Date: {{DOC_DATE}}
- Sender: {{SENDER}}
- Reference: {{REFERENCE}}
- Category: {{CATEGORY}}
- Amount: {{GROSS}} (net {{NET}}, VAT {{VAT_PER_RATE}})
- Bank details: {{IBAN}} {{BIC}} {{GIROCODE_NOTE}}
- Deadlines: {{DEADLINES}} <!-- e.g. "payable by 20.12.2026 (do by 18.12.2026); 2 % discount (Skonto) until 07.11.2026" -->
- Action: {{ACTION}}
- Findings: {{FINDINGS}} <!-- § 14 UStG, arithmetic, IBAN change, e-invoice, TSE -->
- Format: {{ORIGINAL_KIND}} {{E_INVOICE_CHECK}} {{RECONSTRUCTION}}
- Keep until (Aufbewahren bis): {{RETENTION_UNTIL}}
- Paper original: {{PAPER}}
- Office ID: #{{ID}} · received {{RECEIVED}} via {{SOURCE}} · SHA-256 {{HASH_SHORT}}

{{SUMMARY}}
