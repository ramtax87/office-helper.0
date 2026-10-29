# de/AGENTS.md — Anweisungen für die umsetzende KI (Deutsch)

Du liest eine **Bauanleitung**, keine fertige Anwendung. Deine Aufgabe: das System Office Helper auf diesem Rechner
für diesen Nutzer nach den Spezifikationen in `de/anleitung/` bauen und danach übergeben. Die Sprache wurde in der
`AGENTS.md` im Hauptverzeichnis gewählt. Die englische Fassung dieser Anleitung ist `en/AGENTS.md`.

## Lesereihenfolge
1. `de/anleitung/00_ueberblick.md` — was du baust und warum.
2. `de/anleitung/09_sicherheit.md` — die harten Regeln und **Freigabepunkte**. Sie gelten beim Aufbau und im fertigen
   System. Vor allem anderen lesen.
3. `de/anleitung/01_bauablauf.md` — die Phasen, die du der Reihe nach ausführst.
4. `de/anleitung/12_deutschland.md` — die deutschen Regeln, nach denen jeder Teil gebaut wird (Fristen, E-Rechnung,
   GoBD, Aufbewahrung, § 14 UStG, DIN 5008, DSGVO). Vor jedem Entwurf lesen.
5. `de/anleitung/02_ablage.md` … `de/anleitung/07_datenmodell.md` — die Spezifikation jedes Teils. Jeweils lesen,
   bevor du den Teil baust.
6. `de/anleitung/13_rekonstruktion.md` — Foto → echtes PDF.
7. `de/anleitung/08_agenten_und_anbieter.md` — wie das Agenten-Team im KI-Werkzeug des Nutzers eingerichtet wird.
8. `de/anleitung/10_abnahmetests.md` — die Tests, die vor der Übergabe bestanden sein müssen.
9. `de/anleitung/11_betrieb.md` — was der Nutzer damit macht. Erkläre es bei der Übergabe.

## Grundregeln für den Aufbau
- **Standardmäßig selbstständig.** Rechner untersuchen, Umsetzungsdetails wählen, Code und Konfiguration schreiben,
  Tests ausführen — ohne zu fragen. Halte nur an den Freigabepunkten aus `de/anleitung/09_sicherheit.md`.
- **Bau, was die Spezifikation verlangt, nicht mehr.** Nimm die einfachste Umsetzung, die die Spezifikation auf diesem
  Rechner erfüllt: Standardbibliothek und Systemwerkzeuge statt großer Frameworks, Dateien statt Server.
- **MUSS** ist eine Anforderung. **SOLL** ist ein starker Standard, von dem du mit genannter Begründung abweichen
  darfst. **KANN** ist optional.
- **Fragen bündeln.** Ein einziges Interview mit Standardwerten (Bauphase 2), keine Einzelfragen nacheinander.
- **Sprich mit dem Nutzer auf Deutsch.** Alle Ausgaben des fertigen Systems an den Nutzer sind Deutsch
  (`de/anleitung/12_deutschland.md` §1).
- **Nie mit Geheimnissen umgehen.** Passwörter und API-Schlüssel trägt der Nutzer selbst an der vorbereiteten Stelle
  ein. Du prüfst nur, ob sie gesetzt sind.
- **Bauprotokoll führen** (`install.log` neben der Umsetzung) — eine Zeile je Phase und Freigabe.
- **Dieses Repository bleibt eine Anleitung.** Leg die Umsetzung an dem in Phase 2 vereinbarten Ort ab (Standard
  `~/office-helper-system/`), nicht in diesem Repository, damit die Anleitung mit `git pull` aktualisiert werden kann.
- **⟳-Punkte neu prüfen.** Mit ⟳ markierte Punkte (deutsche Rechtsregeln, Versionen, Server) können sich nach dem
  Schreiben der Anleitung geändert haben. Prüf sie beim Aufbau gegen offizielle Quellen und protokolliere das Ergebnis.
- **Testdaten nur zur Bewertung.** Dateien `*.ground_truth.*` und Werte aus `EXPECTED.*.md` nie in das zu testende
  System einspeisen.
- Ist die Anleitung unklar oder widersprüchlich: die sicherere Lesart wählen, im Bauprotokoll und im Übergabebericht
  vermerken.
