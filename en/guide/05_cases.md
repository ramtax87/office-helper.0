# 05 · Task 4 — Case handling with sub-agents

Goal: every document that needs action is handled **individually**. It gets its own tasks, links to related
documents and, if it needs an answer, a reply draft (German, for German recipients) stored for review.

## Sub-agents
- One case = one document.
- If the AI tool supports sub-agents, start one case agent per document, at most ~5 in parallel to stay within
  rate limits and keep the records consistent.
- If the tool has no sub-agents, the orchestrator handles the cases one after another with the same case
  instructions. The result MUST be the same.
- A case agent gets:
  - the document ID, the action class(es), the computed deadlines, the findings and the reason
  - the case role instructions (`en/templates/agent/roles/case.md`)

  It reads the summary file and, if needed, the document.
- `Personal` documents: the case agent runs on the local model only when local-only is configured
  (`12_germany.md` §12).

## Steps of a case
1. **Context.** Read the summary file; re-read the document if details are missing.
2. **Related documents.** Search the summary files for the same sender and reference:
   - a Mahnung belongs to its invoice
   - a second letter belongs to the first
   - a delivery note belongs to its order
   - a Mahnbescheid belongs to the underlying invoice

   Mention related document IDs in the tasks and the draft. Special cases:
   - **Superseded tasks:** a Mahnung for an invoice whose pay task is still open → cancel the old pay task
     ("ersetzt durch Mahnung #…"). One pay task only, with the reminder's amount and date.
   - A Mahnung for an invoice whose pay task is already done → P1 "Zahlung prüfen – Mahnung trotz Zahlung?".
   - The underlying document is not in the archive → say "nicht im Archiv" and suggest checking whether it was
     paid.
3. **Tasks** (through the core). Task titles are in the user's language (the examples below are German), start
   with a verb and name the counterpart. Deadlines
   always come from the core's calculator, and each task shows `Fristende` and `erledigen bis` (`12_germany.md` §3).

   | Action / finding | Task |
   |---|---|
   | `pay` | "Rechnung 1205 an Kartoffel Müller bezahlen" — detail: amount, due date, Skonto date, IBAN shortened (…3000), GiroCode data if present |
   | `reply` | "Antwort an <Absender> senden" — deadline from the letter, otherwise document date + 14 days (and say so); Versandweg |
   | `provide_documents` | "Unterlagen an <Absender> senden" — the exact list requested; for each item, where it already is in the tree (folder path) or "fehlt"; Versandweg (Finanzamt: ELSTER) |
   | `deliver` | "Kartoffellieferung am 05.11.2026 (7–9 Uhr) annehmen und prüfen" |
   | `check` | "Steuerbescheid USt 2025 prüfen – Einspruchsfrist 26.11.2026 – mit Steuerberater abstimmen" |
   | Rechtsbehelf (Mahnbescheid, Bußgeld, Bescheid) | "Widerspruch/Einspruch prüfen – mit Anwalt/Steuerberater abstimmen" + `[PRÜFEN: Zustelldatum]` where it applies. **Never** a decided Widerspruch or Einspruch |
   | § 14 finding | "Rechnung <Nr> unvollständig – korrigierte Rechnung bei <Absender> anfordern (fehlt: …)" + draft |
   | IBAN change | P1 "IBAN-Änderung bei <Lieferant> telefonisch über bekannte Nummer bestätigen, bevor gezahlt wird"; the pay task refers to it |
   | E-invoice error | "Korrigierte E-Rechnung bei <Absender> anfordern (<Fehlerklasse>)" + draft |
   | Reconstruction in review | "Rekonstruktion prüfen (Dokument #<id>)" |
   | Bewirtungsbeleg | "Bewirtungsbeleg ergänzen: Anlass, Teilnehmer, Unterschrift" |

   - One document may produce several tasks (e.g. pay + check).
   - Never create the same task twice: same document, same title, same due date.
   - Every task change goes into `protokoll.csv`.
4. **Reply draft** — for `reply`, for `provide_documents`, for § 14 and e-invoice findings, and whenever the letter
   clearly expects an answer:
   - Use `shared/templates/antwortbrief.md` (DIN 5008 Form B, `12_germany.md` §10). The letter is **always German**
     for German recipients (formal *Sie*), whatever the user's language. For an English-speaking user, add a short
     English explanation of the draft in the review, not in the letter.
     Short, polite, factual. Quote their reference/Aktenzeichen and the letter date.
   - Fill in only what is known. Every assumption or open point becomes `[PRÜFEN: …]`. Never invent the
     company's Pflichtangaben.
   - For `provide_documents`: list the enclosures (Anlagen) with their location in the tree.
   - No binding legal or tax statements, no admissions of debt, no promises of payment dates the user did not
     give. Where the stakes are real, add "mit Steuerberater/Anwalt abstimmen" (`09_safety.md`).
   - Store it in `_office/drafts/<ID>_<Sender>.md` and register it with status `review`.
5. **Report** one line: the tasks created (IDs), the draft path, and the decisions the human must make.

## Review by the human
In the review workflow, the user sees each draft with its `[PRÜFEN]` points and then approves, edits or discards it.
- Approved drafts are marked `approved`, rendered as a print-ready DIN 5008 PDF if wanted, and **sent by the
  human**.
- After sending, the user (or the agent, on request) closes the reply task and files a copy of the sent letter
  (Handelsbrief, 6 years).

The system never sends.
