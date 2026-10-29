# 08 · Agent team and providers

## The team
| Agent | Job | Template |
|---|---|---|
| Orchestrator | receives the user's request, runs workflows, enforces the hard rules, reports | `en/templates/agent/orchestrator.md` |
| setup | builds/extends the storage tree, changes categories | `en/templates/agent/roles/setup.md` |
| intake | collects documents from all sources into the queue | `en/templates/agent/roles/intake.md` |
| filing | reads, classifies, files, assigns action class | `en/templates/agent/roles/filing.md` |
| case | one per document: tasks, related documents, reply draft | `en/templates/agent/roles/case.md` |
| scheduler | builds the schedule and Steuerkalender, reports what is due | `en/templates/agent/roles/scheduler.md` |
| reconstruction | photo/image → real PDF; second reading of uncertain text | `en/templates/agent/roles/reconstruction.md` |

Workflows (`en/templates/agent/workflows/`): `run` (intake → process → schedule), `intake`, `process`
(reconstruction → filing → cases), `case <id>`, `schedule`, `review`, `setup`, `reconstruct <id|file>`,
`steuerberater <JJJJ-MM>`.

The German version (`de/vorlagen/agent/`) uses German names: roles `einrichtung`, `posteingang`, `ablage`,
`vorgang`, `fristenplan`, `rekonstruktion`; workflows `tageslauf`, `posteingang`, `verarbeiten`, `vorgang`, `fristen`,
`pruefen`, `einrichten`, `rekonstruieren`, `steuerberater`.

**Language:** the agents talk to the user in the language chosen at the start (German or English) and write
summaries, tasks and reports in it. Letters, the Verfahrensdokumentation and the Steuerberater package stay German
(`12_germany.md` §1). Install the instruction files in the chosen language (`de/vorlagen/agent/` or
`en/templates/agent/`).

**Vision:** the reconstruction role's second reading needs a model that can look at images. If the user's AI
tool cannot, the core MUST use a second OCR engine instead, and every disagreement goes to human review.

The templates contain placeholders like `{{CMD_FILE}}`. In build phase 6 the implementing AI replaces
them with the real commands of the core it built, and writes the finished instructions into the
implementation folder. The finished set MUST stay plain markdown, so it works with any tool.

## Wiring into the user's AI tool
The implementing AI checks which tool it runs in and uses what that tool supports. All options point
to the same markdown files; nothing is duplicated by hand.

| Tool | Instruction file | Workflows as commands | Sub-agents |
|---|---|---|---|
| Claude Code | `CLAUDE.md` containing `@AGENTS.md` | `.claude/commands/<name>.md` (`$ARGUMENTS`) | `.claude/agents/<role>.md` |
| Gemini CLI | `GEMINI.md` containing `@AGENTS.md` | `.gemini/commands/<name>.toml` (`{{args}}`) | if supported by the version |
| OpenAI Codex CLI | `AGENTS.md` (native) | optional custom prompts in `~/.codex/prompts/` (outside the folder → 🔐) | if supported by the version |
| Cursor, GitHub Copilot agent, Windsurf, Zed | `AGENTS.md` (native in most) | — (plain sentences) | — |
| Aider | config `read: [AGENTS.md]` | — | — |
| Any other agent | tell it once: "read AGENTS.md and follow it" | — | — |

Tool conventions change; the implementing AI SHOULD check the current documentation of its own tool
before writing these files, and fall back to the plain `AGENTS.md` route when unsure.

Every tool MUST also work with **plain sentences in German and English** — the orchestrator maps them to workflows:
"Tageslauf" / "Post bearbeiten" → `run` · "Was ist fällig?" → `schedule` · "Dokument 37: …" → `case` ·
"Entwürfe prüfen" → `review` · "Neue Kategorie …" / "Baum für 2028" → `setup` ·
"Mach aus diesem Foto ein PDF" → `reconstruct` · "Belege für den Steuerberater" → `steuerberater`.
English: "daily run" → `run` · "what is due?" → `schedule` · "document 37: …" → `case` · "review drafts" →
`review` · "new category …" → `setup` · "make a PDF from this photo" → `reconstruct` · "documents for the tax
advisor" → `steuerberater`.

**Sub-agents or not:** with sub-agent support, filing runs in groups and cases run one agent per
document (max ~5 parallel). Without it, the orchestrator performs the roles sequentially. Same result.

## Running without a coding agent (API providers)
For automatic daily runs, or if the user has no coding agent, the implementing AI MAY build a small
**standalone runner**: a script that sends the orchestrator + role instructions to a model API and
executes the model's tool calls.

Requirements for such a runner (MUST):
- Speaks the **OpenAI-compatible Chat Completions API with tool calling** — most providers offer it, so
  one runner covers all of them. Provider, base URL and model come from the config; the key from the
  secrets file.
- **Sandboxed tools only — no shell.** Expose exactly: the core commands with an allow-list
  (status, intake, list, extract, einvoice, reconstruct, reconstruct-answer, file, deadline, task, draft,
  steuerkalender, schedule, belegpaket), email fetch, read text files inside the
  storage root and the instruction folder (never the secrets file), search in summary files, write
  drafts into `_office/drafts/` (file name sanitised, no path escape).
- A step limit (e.g. 80 tool calls) and a log of every run.
- Interactive workflows (`setup`, `review`) stay with a human + coding agent.
- Tested offline against a **fake provider** (a local mock server returning scripted tool calls),
  including the sandbox refusals — see `10_acceptance_tests.md`.

| Provider | OpenAI-compatible base URL | Notes |
|---|---|---|
| OpenAI | `https://api.openai.com/v1` | |
| Anthropic | `https://api.anthropic.com/v1` | OpenAI SDK compatibility endpoint |
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/openai` | |
| xAI (Grok) | `https://api.x.ai/v1` | |
| Mistral | `https://api.mistral.ai/v1` | |
| OpenRouter | `https://openrouter.ai/api/v1` | many models behind one key |
| Ollama (local) | `http://localhost:11434/v1` | free, documents never leave the machine |
| LM Studio (local) | `http://localhost:1234/v1` | |

Verify endpoints against the provider's current documentation during the build.
**Model:** a current model of that provider **with tool/function calling**. Model names change; never
invent one — take it from the provider's list or ask the user:
[OpenAI](https://platform.openai.com/docs/models) ·
[Anthropic](https://docs.claude.com/en/docs/about-claude/models/overview) ·
[Gemini](https://ai.google.dev/gemini-api/docs/models) ·
[xAI](https://docs.x.ai/docs/models) ·
[Mistral](https://docs.mistral.ai/getting-started/models/) ·
[OpenRouter](https://openrouter.ai/models?supported_parameters=tools) ·
[Ollama](https://ollama.com/search?c=tools).
Small local models misread dates more often; recommend a strong model, and a few weeks of reviewing
results when using a small one.

**Privacy (DSGVO, `12_germany.md` §12):** with a cloud provider, the document text (and for reconstruction the
images) goes to that provider. Recommend:
- a business plan without training on customer data
- an AV-Vertrag (Art. 28 DSGVO) and an EU data region where offered
- or a local model

Categories configured as **local only** (default `Personal`) MUST never be sent to a cloud model. Both the
runner and the agent wiring enforce it: the runner refuses the tool call, and the coding-agent instructions say
so explicitly. Test G9.

## Automation
A scheduled job (cron, systemd timer, Windows Task Scheduler) runs the `run` workflow, e.g. weekdays
07:10, either
- with the standalone runner: `<runner> run`, or
- with the coding agent's headless mode and the prompt "Follow the run workflow" — the implementing
  AI checks that tool's docs for the flag that allows unattended command execution and limits it to the
  core commands.
Output goes to a log file in the implementation folder. Setting this up is 🔐.

## Switching provider
Data belongs to Office Helper, not to the AI. Switching = wiring the same instruction files into another
tool, or changing provider/model in the runner config. Mixing is fine (e.g. local model for the daily
run, a strong cloud model for review).
