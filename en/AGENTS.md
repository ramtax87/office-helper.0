# en/AGENTS.md — instructions for the implementing AI (English)

You are reading a **build guide**, not an application. Your job is to build the Office Helper
system on this machine, for this user, from the specifications in `en/guide/`, and then hand it over.
The language was chosen in the root `AGENTS.md`. The German version of this guide is `de/AGENTS.md`.

## Read order
1. `en/guide/00_overview.md` — what you are building and why.
2. `en/guide/09_safety.md` — the hard rules and **approval gates**. They apply during the build and
   to the finished system. Read before doing anything.
3. `en/guide/01_build_process.md` — the phases you execute, in order.
4. `en/guide/12_germany.md` — the German rules every part is built around (deadlines, e-invoices, GoBD,
   retention, § 14 UStG, DIN 5008, DSGVO). Read before designing anything.
5. `en/guide/02_storage.md` … `en/guide/07_data_model.md` — specifications of each part. Read each one before building
   that part.
6. `en/guide/13_reconstruction.md` — photo → real PDF reconstruction.
7. `en/guide/08_agents_and_providers.md` — how to install the agent team into the user's AI tool.
8. `en/guide/10_acceptance_tests.md` — the tests you must pass before handover.
9. `en/guide/11_operations.md` — what the user will do with it; explain it in the handover.

## Ground rules for the build
- **Autonomous by default.** Inspect the machine, choose the implementation details, write code and
  configuration, run tests — without asking. Stop only at the approval gates in `en/guide/09_safety.md`.
- **Build what the specs require, not more.** Prefer the simplest implementation that meets the spec
  on this machine: standard library and system tools over frameworks, files over servers.
- **Where the guide says MUST, it is a requirement. SHOULD is a strong default you may change with a
  stated reason. MAY is optional.**
- **Batch questions.** One interview message with defaults (see build phase 2), not a drip of questions.
- **Talk to the user in the language chosen at the start** (root `AGENTS.md`; you are reading the English version,
  so normally English). Letters, the Verfahrensdokumentation and the Steuerberater package stay German
  (`en/guide/12_germany.md` §1).
- **Never handle secrets.** The user types passwords and API keys into the place you prepare; you only
  verify they exist.
- **Keep a build log** (`install.log` next to the implementation) — one line per phase and gate.
- **This repo stays a guide.** Put the implementation in the location agreed in phase 2
  (default `~/office-helper-system/`), not inside this repo, so the guide can be updated with `git pull`.
- **Re-verify ⟳ items.** Points marked ⟳ (German legal rules, versions, hosts) may have changed after the guide was
  written. Check them against official sources during the build and log what you found.
- **Test fixtures are for scoring only.** Never feed `*.ground_truth.*` files or `EXPECTED.md` values into the
  system under test.
- If the guide is unclear or contradicts itself, choose the safer reading, note it in the build log and
  in the handover report.
