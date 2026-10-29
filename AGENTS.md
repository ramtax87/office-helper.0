# AGENTS.md — Office Helper (Deutschland)

**Zuerst / First:** Bevor du irgendetwas anderes tust, frag den Nutzer nach der Sprache — genau so:
**Before you do anything else, ask the user for the language — exactly like this:**

```
Welche Sprache möchtest du verwenden? / Which language do you want to use?
1) Deutsch
2) English
```

Warte auf die Antwort. / Wait for the answer.

| Antwort / Answer | Weiter mit / Continue with |
|---|---|
| 1, Deutsch, German, de | `de/AGENTS.md` — Bauanleitung auf Deutsch (`de/anleitung/`, `de/vorlagen/`, `de/prompts/`) |
| 2, English, Englisch, en | `en/AGENTS.md` — build guide in English (`en/guide/`, `en/templates/`, `en/prompts/`) |

Regeln für beide Sprachen / Rules for both languages:
- Die gewählte Sprache gilt für das ganze Gespräch, den Aufbau und alle Ausgaben des fertigen Systems an den
  Nutzer. Schreib sie als `language` in die Konfiguration.
  The chosen language applies to the whole conversation, the build, and everything the finished system shows the
  user. Store it as `language` in the configuration.
- **Immer Deutsch / Always German** (deutscher Markt / German market):
  - Briefe und Antwortentwürfe an deutsche Empfänger / letters and drafts to German recipients
  - die Verfahrensdokumentation / the Verfahrensdokumentation (GoBD)
  - das Belegpaket für den Steuerberater / the package for the Steuerberater
  - standardmäßig die Ordner- und Kategorienamen / by default, the folder and category names
- Beide Fassungen beschreiben dasselbe System. Bei Widerspruch zwischen den Fassungen gilt die strengere Regel.
  Both versions describe the same system. If they ever disagree, the stricter rule applies.
- Gemeinsame Dateien / Shared files: `shared/templates/` (Konfiguration/config, Antwortbrief, Verfahrensdokumentation,
  Kurzbeschreibung/summary, Dokumentmodell/document model) und `examples/` (Testdokumente/test documents).

`CLAUDE.md` und `GEMINI.md` verweisen nur hierher. / `CLAUDE.md` and `GEMINI.md` only point here.
