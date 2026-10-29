# Startprompt (Deutsch)

Öffne dieses Repository in einem KI-Werkzeug auf dem Rechner, auf dem das Büro-System laufen soll (Claude Code, OpenAI
Codex, Gemini CLI, Cursor, GitHub Copilot, Aider, …), und füge ein:

```
Du baust "Office Helper" auf diesem Rechner nach der Bauanleitung in diesem Repository.

1. Lies die AGENTS.md im Hauptverzeichnis und stell mir die Sprachfrage daraus. Folge danach de/AGENTS.md (Deutsch) oder
   en/AGENTS.md (Englisch), je nach meiner Antwort.
2. Lies das Sicherheits- und das Deutschland-Kapitel, bevor du irgendetwas tust.
3. Führe die Bauphasen nacheinander aus. Arbeite selbstständig; frag mich nur an den Freigabepunkten, im Format aus der
   Anleitung. Stelle alle Einrichtungsfragen gesammelt im Interview von Phase 2, jeweils mit Standardwert.
4. Frag mich nie im Chat nach Passwörtern oder API-Schlüsseln. Sag mir, wo ich sie eintragen soll, und prüfe nur, ob sie
   gesetzt sind.
5. Leg die Umsetzung außerhalb dieses Repositorys ab (Standard ~/office-helper-system) und führe dort install.log.
6. Prüfe die mit ⟳ markierten Regeln gegen aktuelle offizielle Quellen.
7. Übergib erst, wenn alle Abnahmetests bestanden sind. Schließe mit der Übergabenachricht und der ausgefüllten
   Verfahrensdokumentation ab.
```

## Kurzfassung (wenn das Werkzeug AGENTS.md automatisch liest)
```
Bau Office Helper nach dieser Anleitung. Beginne mit der Sprachfrage aus AGENTS.md; frag mich sonst nur an den Freigabepunkten.
```

## Fortsetzen / aktualisieren
```
Setz den Office-Helper-Aufbau fort. Lies install.log im Umsetzungsordner, wiederhole Phase 1 und mach bei der ersten
Phase weiter, die nicht "ok" ist. Behalte die in der Konfiguration gespeicherte Sprache.
```
```
Die Anleitung wurde aktualisiert (git pull). Vergleiche sie mit der bestehenden Office-Helper-Umsetzung, liste die
Unterschiede auf und setze sie um. Frag vorher bei allem, was bestehende Dokumente oder Datensätze ändert. Aktualisiere
danach die Verfahrensdokumentation.
```
