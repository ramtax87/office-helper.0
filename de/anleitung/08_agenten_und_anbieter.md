# 08 · Agenten-Team und Anbieter

## Das Team
| Agent | Aufgabe | Vorlage |
|---|---|---|
| Orchestrator | nimmt die Anfrage des Nutzers entgegen, führt Workflows aus, setzt die harten Regeln durch, berichtet | `de/vorlagen/agent/orchestrator.md` |
| einrichtung | legt den Ablagebaum an oder erweitert ihn, ändert Kategorien | `de/vorlagen/agent/rollen/einrichtung.md` |
| posteingang | sammelt Dokumente aus allen Quellen in die Warteschlange | `de/vorlagen/agent/rollen/posteingang.md` |
| ablage | liest, ordnet ein, legt ab, vergibt die Aktionsklasse | `de/vorlagen/agent/rollen/ablage.md` |
| vorgang | einer je Dokument: Aufgaben, zugehörige Dokumente, Antwortentwurf | `de/vorlagen/agent/rollen/vorgang.md` |
| fristenplan | erstellt Fristenplan und Steuerkalender, meldet, was fällig ist | `de/vorlagen/agent/rollen/fristenplan.md` |
| rekonstruktion | Foto/Bild → echtes PDF; zweites Lesen unsicherer Textstellen | `de/vorlagen/agent/rollen/rekonstruktion.md` |

Workflows (`de/vorlagen/agent/workflows/`):
- `tageslauf` (posteingang → verarbeiten → fristen)
- `posteingang`
- `verarbeiten` (rekonstruktion → ablage → vorgänge)
- `vorgang <id>`
- `fristen`
- `pruefen`
- `einrichten`
- `rekonstruieren <id|datei>`
- `steuerberater <JJJJ-MM>`

Die englische Fassung (`en/templates/agent/`) heißt `run`, `intake`, `process`, `case`, `schedule`, `review`, `setup`,
`reconstruct`, `steuerberater`.

**Sprache:** Die Agenten sprechen mit dem Nutzer in der zu Beginn gewählten Sprache (Deutsch oder Englisch) und
schreiben darin Kurzbeschreibungen, Aufgaben und Berichte. Briefe, Verfahrensdokumentation und Steuerberater-Paket bleiben
Deutsch (`12_deutschland.md` §1). Die Anweisungsdateien in der gewählten Sprache installieren (`de/vorlagen/agent/` oder
`en/templates/agent/`).

**Bilder:** Das zweite Lesen der Rekonstruktion braucht ein Modell, das Bilder ansehen kann. Kann das KI-Werkzeug des
Nutzers das nicht, MUSS der Kern stattdessen eine zweite OCR-Engine verwenden, und jede Abweichung geht zur Prüfung an
den Menschen.

Die Vorlagen enthalten Platzhalter wie `{{CMD_FILE}}`. In Bauphase 6 ersetzt die umsetzende KI sie durch die echten
Befehle des gebauten Kerns und schreibt die fertigen Anweisungen in den Umsetzungsordner. Der fertige Satz MUSS
einfaches Markdown bleiben, damit er mit jedem Werkzeug funktioniert.

## Einbindung in das KI-Werkzeug des Nutzers
Die umsetzende KI prüft, in welchem Werkzeug sie läuft, und nutzt, was dieses kann. Alle Varianten verweisen auf
dieselben Markdown-Dateien; nichts wird von Hand doppelt gepflegt.

| Werkzeug | Anweisungsdatei | Workflows als Befehle | Sub-Agenten |
|---|---|---|---|
| Claude Code | `CLAUDE.md` mit `@AGENTS.md` | `.claude/commands/<name>.md` (`$ARGUMENTS`) | `.claude/agents/<rolle>.md` |
| Gemini CLI | `GEMINI.md` mit `@AGENTS.md` | `.gemini/commands/<name>.toml` (`{{args}}`) | sofern die Version es kann |
| OpenAI Codex CLI | `AGENTS.md` (nativ) | optional eigene Prompts in `~/.codex/prompts/` (außerhalb des Ordners → 🔐) | sofern die Version es kann |
| Cursor, GitHub Copilot Agent, Windsurf, Zed | `AGENTS.md` (meist nativ) | — (normale Sätze) | — |
| Aider | Konfiguration `read: [AGENTS.md]` | — | — |
| Jeder andere Agent | einmal sagen: „Lies AGENTS.md und folge ihr“ | — | — |

Die Konventionen der Werkzeuge ändern sich. Die umsetzende KI SOLL vor dem Schreiben dieser Dateien die aktuelle
Dokumentation ihres Werkzeugs prüfen und im Zweifel den einfachen `AGENTS.md`-Weg nehmen.

Jedes Werkzeug MUSS auch mit **normalen Sätzen auf Deutsch und Englisch** funktionieren — der Orchestrator ordnet sie
Workflows zu:

| Satz | Workflow |
|---|---|
| „Tageslauf“ / „Post bearbeiten“ | `tageslauf` |
| „Was ist fällig?“ | `fristen` |
| „Dokument 37: …“ | `vorgang` |
| „Entwürfe prüfen“ | `pruefen` |
| „Neue Kategorie …“ / „Baum für 2028“ | `einrichten` |
| „Mach aus diesem Foto ein PDF“ | `rekonstruieren` |
| „Belege für den Steuerberater“ | `steuerberater` |
| Englisch: „daily run“, „what is due?“, „document 37: …“, „review drafts“, „new category …“, „make a PDF from this photo“, „documents for the tax advisor“ | entsprechend |

**Mit oder ohne Sub-Agenten:** Mit Sub-Agenten läuft die Ablage in Gruppen und jeder Vorgang als eigener Agent
(höchstens ca. 5 gleichzeitig). Ohne führt der Orchestrator die Rollen nacheinander aus. Gleiches Ergebnis.

## Betrieb ohne Coding-Agent (API-Anbieter)
Für automatische tägliche Läufe, oder wenn der Nutzer keinen Coding-Agenten hat, KANN die umsetzende KI einen kleinen
**eigenständigen Runner** bauen: ein Skript, das Orchestrator- und Rollenanweisungen an eine Modell-API schickt und
deren Werkzeugaufrufe ausführt.

Anforderungen an einen solchen Runner (MUSS):
- Spricht die **OpenAI-kompatible Chat-Completions-API mit Tool-Calling** — die meisten Anbieter bieten sie, also deckt
  ein Runner alle ab. Anbieter, Basis-URL und Modell kommen aus der Konfiguration, der Schlüssel aus der Geheimnisdatei.
- **Nur abgeschirmte Werkzeuge — keine Shell.** Genau diese anbieten:
  - die Befehle des Kerns mit Freigabeliste (Status, Posteingang, Liste, Text, E-Rechnung, Rekonstruieren, Lesung
    zurückgeben, Ablegen, Frist, Aufgabe, Entwurf, Steuerkalender, Fristenplan, Belegpaket)
  - E-Mail-Abruf
  - Textdateien lesen im Hauptordner und im Anweisungsordner (nie die Geheimnisdatei)
  - Suche in den Kurzbeschreibungen
  - Entwürfe schreiben nach `_office/drafts/` (Dateiname bereinigt, kein Ausbrechen aus dem Pfad)
- Ein Schrittlimit (z. B. 80 Werkzeugaufrufe) und ein Protokoll jedes Laufs.
- Interaktive Workflows (`einrichten`, `pruefen`) bleiben bei Mensch + Coding-Agent.
- Offline getestet gegen einen **Schein-Anbieter** (lokaler Testserver mit vorgegebenen Werkzeugaufrufen), einschließlich
  der Ablehnungen der Abschirmung — siehe `10_abnahmetests.md`.

| Anbieter | OpenAI-kompatible Basis-URL | Hinweise |
|---|---|---|
| OpenAI | `https://api.openai.com/v1` | |
| Anthropic | `https://api.anthropic.com/v1` | Kompatibilitäts-Endpunkt für das OpenAI-SDK |
| Google Gemini | `https://generativelanguage.googleapis.com/v1beta/openai` | |
| xAI (Grok) | `https://api.x.ai/v1` | |
| Mistral | `https://api.mistral.ai/v1` | |
| OpenRouter | `https://openrouter.ai/api/v1` | viele Modelle hinter einem Schlüssel |
| Ollama (lokal) | `http://localhost:11434/v1` | kostenlos, Dokumente verlassen den Rechner nie |
| LM Studio (lokal) | `http://localhost:1234/v1` | |

Die Endpunkte beim Aufbau gegen die aktuelle Dokumentation des Anbieters prüfen.

**Modell:** ein aktuelles Modell des Anbieters **mit Tool-/Function-Calling**. Modellnamen ändern sich; nie einen
erfinden — aus der Liste des Anbieters nehmen oder den Nutzer fragen:
[OpenAI](https://platform.openai.com/docs/models) ·
[Anthropic](https://docs.claude.com/en/docs/about-claude/models/overview) ·
[Gemini](https://ai.google.dev/gemini-api/docs/models) ·
[xAI](https://docs.x.ai/docs/models) ·
[Mistral](https://docs.mistral.ai/getting-started/models/) ·
[OpenRouter](https://openrouter.ai/models?supported_parameters=tools) ·
[Ollama](https://ollama.com/search?c=tools).

Kleine lokale Modelle lesen Daten öfter falsch. Ein starkes Modell empfehlen, und bei einem kleinen Modell in den ersten
Wochen die Ergebnisse prüfen.

**Datenschutz (DSGVO, `12_deutschland.md` §12):** Bei einem Cloud-Anbieter geht der Dokumenttext (bei der
Rekonstruktion auch die Bilder) an diesen Anbieter. Empfehlen:
- einen Geschäftstarif ohne Training auf Kundendaten
- einen AV-Vertrag (Art. 28 DSGVO) und eine EU-Datenregion, wo angeboten
- oder ein lokales Modell

Als **nur lokal** eingestellte Kategorien (Standard `Personal`) DÜRFEN nie an ein Cloud-Modell gehen. Runner und
Agenten-Einbindung setzen das durch: Der Runner verweigert den Werkzeugaufruf, und die Anweisungen für den Coding-Agenten
sagen es ausdrücklich. Test G9.

## Automatisierung
Eine geplante Aufgabe (cron, systemd-Timer, Windows-Aufgabenplanung) führt den Workflow `tageslauf` aus, z. B. werktags
07:10. Entweder:
- mit dem eigenständigen Runner: `<runner> tageslauf`, oder
- mit dem Hintergrundmodus des Coding-Agenten und dem Prompt „Führe den Workflow tageslauf aus“. Die umsetzende KI prüft
  in der Dokumentation des Werkzeugs, welche Option unbeaufsichtigte Befehle erlaubt, und beschränkt sie auf die Befehle
  des Kerns.

Die Ausgabe geht in eine Logdatei im Umsetzungsordner. Das Einrichten ist 🔐.

## Anbieter wechseln
Die Daten gehören Office Helper, nicht der KI. Wechseln heißt: dieselben Anweisungsdateien in ein anderes Werkzeug
einbinden, oder Anbieter/Modell in der Runner-Konfiguration ändern. Mischen ist erlaubt (z. B. lokales Modell für den
täglichen Lauf, starkes Cloud-Modell für die Prüfung).
