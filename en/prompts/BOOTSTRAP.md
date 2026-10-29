# Bootstrap prompt (English)

Open this repository in an AI tool on the computer that should run the office system (Claude Code, OpenAI Codex,
Gemini CLI, Cursor, GitHub Copilot, Aider, …) and paste:

```
You are going to build "Office Helper" on this computer from the build guide in this repository.

1. Read the root AGENTS.md and ask me the language question it contains. Then follow en/AGENTS.md (English) or
   de/AGENTS.md (German), depending on my answer.
2. Read the safety and Germany chapters before doing anything else.
3. Execute the build phases one after another. Work autonomously; ask me only at the approval gates, in the format
   the guide gives. Ask all setup questions together in the single interview of phase 2, each with a default.
4. Never ask me for passwords or API keys in chat. Tell me where to put them and only check that they are set.
5. Put the implementation outside this repository (default ~/office-helper-system) and keep install.log there.
6. Re-verify the rules marked ⟳ against current official sources.
7. Hand over only when all acceptance tests pass. Finish with the handover message and the filled-in
   Verfahrensdokumentation.
```

## Short version (if the tool reads AGENTS.md automatically)
```
Build Office Helper from this guide. Start with the language question in AGENTS.md; ask me only at the approval gates.
```

## Resume / update
```
Continue the Office Helper build. Read install.log in the implementation folder, re-run phase 1 and continue from the
first phase that is not "ok". Keep the language stored in the configuration.
```
```
The guide was updated (git pull). Compare it with the existing Office Helper implementation, list the differences and
apply them. Ask before anything that changes existing documents or records. Then update the Verfahrensdokumentation.
```
