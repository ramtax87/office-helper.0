# 01 · Bauablauf

Die umsetzende KI führt diese Phasen der Reihe nach aus. Jede Phase endet mit einer Zeile in `install.log`:
`2026-10-29 00:40 PHASE 4 ok — Baum angelegt unter ~/Buero (2026, 2027)`.

🔐 markiert einen Freigabepunkt (Regeln und Nachrichtenformat: `09_sicherheit.md`). Alles andere läuft ohne Rückfrage.
Jede Phase MUSS wiederholbar sein: prüfen, was schon existiert, Erledigtes überspringen, „bereits erledigt“
protokollieren.

Phase 0 — die Sprachfrage — wurde bereits in der `AGENTS.md` im Hauptverzeichnis gestellt. Sprich mit dem Nutzer auf
**Deutsch** (kurz, handytauglich). Das Bauprotokoll darf Englisch sein.

---

## Phase 1 · Rechner untersuchen
Ermitteln, ohne etwas zu ändern:
- Betriebssystem und Version, Shell, Benutzerverzeichnis, freier Speicher
- verfügbare Laufzeitumgebungen (z. B. Python, Node, Java für den E-Rechnungs-Validator) und git
- Dokumentwerkzeuge: PDF-Textextraktion, OCR **mit deutschen Sprachdaten**, Bildverarbeitung (OpenCV o. ä.),
  QR-/Barcode-Decoder, Vektorisierer (potrace), PDF/A-Validator (veraPDF)
- Schriften: die metrisch kompatiblen freien Schriften aus `13_rekonstruktion.md` (Liberation, Carlito, Caladea, …)
  und bereits installierte lizenzierte Schriften
- das KI-Werkzeug, in dem du läufst: Unterstützt es Sub-Agenten, eigene Befehle, einen Hintergrundmodus? Kann es
  Bilder ansehen (für das zweite Lesen bei der Rekonstruktion)? Siehe `08_agenten_und_anbieter.md`.
- ob schon ein früherer Office-Helper-Aufbau existiert (nach `install.log` und dem Umsetzungsordner suchen)

Eine Zusammenfassungszeile melden, z. B.
`Linux Mint 22 · Python 3.12 · Java ✘ · pdftotext ✔ · OCR ✔ · deu ✘ · OpenCV ✘ · potrace ✘ · Schriften: Liberation ✔ Carlito ✘ · Agent: Codex CLI (keine Sub-Agents, sieht Bilder)`.

## Phase 2 · Nutzer befragen (eine Nachricht)
Alles auf einmal fragen, nummeriert, jeweils mit Standardwert. Der Nutzer kann „Standard“ antworten.
```
Office Helper einrichten — bitte beantworten oder "Standard":
1. Firmenname? [Meine Firma]
2. Name des Hauptordners? [Buero]
3. Speicher: lokaler Ordner oder Google Drive? [lokal]
4. Wo? [~/Buero | Drive: synchronisierter Ordner, siehe de/anleitung/02_ablage.md]
5. Kategorien: Standard (de/anleitung/12_deutschland.md §9) oder Änderungen? [Standard]
6. E-Mail-Postfächer? (Anbieter + Adresse je Postfach, oder "keine") [keine]
7. Weitere Scan-Ordner? (z. B. ~/Scans, Netzwerkordner des Scanners) [keine]
8. Fotos/Bildscans automatisch als echtes PDF rekonstruieren? (immer / nur auf Wunsch) [immer]
9. Täglicher automatischer Lauf? (Uhrzeit, Wochentage) [nein]
10. Welche KI macht die automatischen Läufe? (dieses Werkzeug im Hintergrund oder ein API-Anbieter — de/anleitung/08_agenten_und_anbieter.md) [dieses Werkzeug]
11. Wo soll der Programmcode liegen? [~/office-helper-system]
```
Dazu in derselben Nachricht die **Deutschland-Einstellungen** a)–i) aus `12_deutschland.md` §2. Die Sprache ist
bereits gesetzt.

Die Antworten in die Konfigurationsdatei im Umsetzungsordner schreiben (Vorlage:
`shared/templates/config.example.toml`). Das Ergebnis in ≤ 12 Zeilen zeigen. Nie Passwörter hineinschreiben.

## Phase 3 · Systemwerkzeuge installieren 🔐
Nur installieren, was Phase 1 als fehlend gemeldet hat und die Spezifikation braucht, mit einer Freigabe für alle
Pakete zusammen:
- PDF-Textextraktion, OCR + deutsche Sprachdaten (`deu`)
- Bildverarbeitung, QR-/Barcode-Decodierung, potrace
- die freien Schriften für die Rekonstruktion
- eine Java-Laufzeit und den KoSIT-Validator mit der aktuellen XRechnung-Konfiguration
- veraPDF
- bei Google Drive: ein Werkzeug zum Einbinden oder Synchronisieren

Wenn du keine Befehle mit Administratorrechten ausführen kannst: dem Nutzer den genauen Befehl geben und auf „fertig“
warten. Downloads von GitHub oder offiziellen Seiten (Validator, Konfiguration, Testdateien, Schriften) gehören zu
dieser Freigabe. Jede Quelle nennen.

## Phase 4 · Speicher 🔐 (bedingt)
Ablage nach `02_ablage.md` aufbauen.
- Neuer, leerer lokaler Ordner im Benutzerverzeichnis → keine Freigabe.
- Ordner existiert schon mit Inhalt oder liegt außerhalb des Benutzerverzeichnisses → 🔐.
- Google Drive → 🔐 für jeden Schritt, der den Nutzer braucht (Konto-Anmeldung im Browser, Start eines
  Hintergrunddienstes zum Einbinden/Synchronisieren). Die Anmeldung kannst du nicht selbst machen.

Prüfen:
- ein Monatsordner zeigt alle Kategorien
- der Eingang existiert
- eine Testdatei im Hauptordner lässt sich zurücklesen (bei Drive: erscheint auch in Drive)
- ein Testdateiname mit Umlauten bleibt erhalten

## Phase 5 · Kern bauen
Den deterministischen Kern im Umsetzungsordner nach `03`–`07`, `12` und `13` umsetzen:
- **Baum:** anlegen und erweitern (idempotent)
- **Posteingang:** alle Quellen in die Warteschlange einsammeln, mit Hashwerten, Hash-Protokoll und Dublettenerkennung
- **Textextraktion:** mit OCR als Rückfallebene
- **E-Rechnungen:** Erkennung, XML-Extraktion aus ZUGFeRD-PDFs, Prüfung, lesbare Ansicht
- **Rekonstruktion** (`13_rekonstruktion.md`): Entzerrung, Layout, OCR mit Konfidenzen, Typografie und
  Schriftzuordnung, Grafiken und Codes, Dokumentmodell, PDF-Erzeugung mit Herkunftsvermerk, Prüfung und Prüfbericht.
  Das zweite Lesen übernimmt die KI-Schicht über einen Befehl, der Ausschnitte ausgibt und Antworten entgegennimmt.
- **Ablage-Befehl:** verschiebt ein Dokument aus der Warteschlange in seinen Ordner, schreibt die Kurzbeschreibung,
  aktualisiert Datensätze und Aktivitätsprotokoll
- **Datensätze:** für Dokumente, Aufgaben und Entwürfe (`07_datenmodell.md`)
- **Deutsche Rechner** (`12_deutschland.md`):
  - Fristen (§§ 187–193 BGB, § 108 AO, 4-Tage-Regel) mit Fristende / erledigen bis
  - Feiertage je Bundesland, Bankarbeitstage
  - Steuerkalender
  - Aufbewahrungsdaten
  - Prüfung von IBAN, USt-IdNr. und Steuernummer
  - GiroCode lesen und abgleichen
- **Fristenplan:** `SCHEDULE.md` auf Deutsch und `schedule.ics` (`06_fristenplan.md`)
- **Exporte:** CSV-Exporte und das monatliche Belegpaket für den Steuerberater
- **Status-Befehl**

Anforderungen an den Kern:
- MUSS über die Kommandozeile nutzbar sein, damit jedes KI-Werkzeug (und ein Mensch) ihn aufrufen kann.
- MUSS ungültige Eingaben (unbekannte Kategorie, unmögliches Datum) mit klarer Meldung ablehnen.
- DARF keinen Netzzugriff brauchen, außer für den E-Mail-Abruf und den konfigurierten Speicher.
- SOLL wenige Abhängigkeiten haben: Standardbibliothek und bekannte Pakete für OCR, Bildverarbeitung und PDF.
- SOLL kurze, maschinenlesbare Ausgaben liefern (eine Zeile je Ergebnis), damit Agenten sie lesen können.

Im Umsetzungsordner eine kurze `README` mit jedem Befehl und je einem Beispiel anlegen.

## Phase 6 · Agenten-Team einrichten
Die Laufzeit-Anweisungen aus `de/vorlagen/agent/` erstellen: Orchestrator, die sechs Rollen und die Workflows. Die
Platzhalter mit den echten Befehlen aus Phase 5 füllen. Danach wie in `08_agenten_und_anbieter.md` beschrieben im
KI-Werkzeug des Nutzers einbinden (Anweisungsdatei, eigene Befehle, Sub-Agenten — was das Werkzeug kann). Alles im
Umsetzungsordner ablegen.

## Phase 7 · Abnahmetests (Sandbox)
Alle Tests aus `10_abnahmetests.md` gegen eine **temporäre Kopie** der Konfiguration mit einem Wegwerf-Hauptordner
ausführen. Verwenden:
- `examples/test-documents/` und `examples/reconstruction/`
- die in Phase 3 geladenen offiziellen E-Rechnungs-Testdateien

Jeder MUSS-Test muss bestanden sein. Korrigieren und wiederholen, bis es so ist. Die Sandbox danach löschen. Die
Ergebnisse samt Rekonstruktionswerten in `install.log` festhalten.

## Phase 8 · E-Mail anbinden 🔐
Für jedes Postfach:
1. Dem Nutzer sagen, wohin das Passwort gehört: eine Geheimnisdatei, die nur er lesen kann, nie in der Konfiguration,
   nie in git.
2. Sagen, welche Art Passwort der Anbieter braucht (z. B. Gmail: App-Passwort).
3. Auf „fertig“ warten. Prüfen, dass das Passwort gesetzt ist, ohne es zu lesen, und die Anmeldung testen.

Der erste Abruf ist der erste Kontakt mit echten Daten → 🔐 (Hinweis: nur Mails seit <Datum>, nichts wird gelöscht).

## Phase 9 · Erster echter Lauf 🔐
Zeigen, wie viele Dateien im Eingang und in den Postfächern warten. Nach Freigabe:
1. Den vollständigen Tageslauf ausführen: Posteingang → Rekonstruktion/Ablage → Vorgänge → Fristenplan.
2. Laufbericht und den Anfang des Fristenplans zeigen.
3. Den Nutzer bitten, zwei abgelegte Dokumente, eine Rekonstruktion (Foto neben PDF) und einen Entwurf stichprobenartig
   zu prüfen.

## Phase 10 · Automatisierung 🔐 (nur wenn in Phase 2 gewünscht)
Den täglichen Lauf mit der gewählten KI einrichten (`08_agenten_und_anbieter.md`, Abschnitt Automatisierung). Den
genauen Eintrag der geplanten Aufgabe vor dem Anlegen zeigen und einmal von Hand ausführen. Gehen dabei zum ersten Mal
echte Dokumente an einen API-Anbieter, gehört das zu dieser Freigabe.

## Phase 11 · Verfahrensdokumentation und Übergabe
`shared/templates/verfahrensdokumentation.md` mit der echten Einrichtung ausfüllen und als
`<ROOT>/_office/Verfahrensdokumentation.md` speichern (`12_deutschland.md` §8). Danach dem Nutzer senden:
```
✅ Office Helper ist eingerichtet
Ablage: <root> (<lokal | Google Drive>)
Baum: <Jahre>, <n> Kategorien je Monat
Quellen: Eingang [+ <n> Postfächer] [+ <n> Scan-Ordner]
Foto → PDF: <immer | auf Wunsch>
Täglicher Lauf: <Uhrzeit + KI | manuell>
Tests: <n>/<n> bestanden · Rekonstruktion: Qualität <Wert>
Für dich:
  • Papier scannen/fotografieren nach <root>/_Eingang, dann "Tageslauf" sagen
  • "Entwürfe prüfen" für Antwortentwürfe
  • <root>/schedule.ics im Kalender abonnieren/importieren (Erinnerungen)
  • Verfahrensdokumentation mit dem Steuerberater durchgehen: <root>/_office/Verfahrensdokumentation.md
  • Papieroriginale vorerst aufbewahren, bis der Steuerberater das Verfahren freigegeben hat
  • Hinweis: Office Helper unterstützt die GoBD, ist aber kein zertifiziertes Archiv. Bitte zusätzliche Datensicherung.
  • Datenschutz: AV-Vertrag mit <KI-Anbieter/Speicher> prüfen
  • Offene Punkte: <aus dem Bauprotokoll>
```

---

## Bestehenden Aufbau aktualisieren
Wenn sich diese Anleitung ändert (`git pull`):
1. Phase 1 wiederholen.
2. Spezifikation und Umsetzung vergleichen, die Unterschiede auflisten.
3. Umsetzen. Die Abnahmetests wiederholen.

Änderungen, die bestehende Dokumente oder Datensätze betreffen, sind 🔐. Die Verfahrensdokumentation fortschreiben
(neue Version, Änderungshistorie).

## Deinstallation (jeder Schritt 🔐)
Geplante Aufgaben entfernen, Dienste zum Einbinden/Synchronisieren stoppen, die Agenten-Einbindung entfernen.
**Hauptordner, Datensätze und Protokolle nie löschen.** Sie gehören zu den Büchern des Unternehmens und unterliegen der
Aufbewahrungspflicht (`12_deutschland.md` §8). Löschen nur auf ausdrücklichen Wunsch, wenn der Nutzer den Ordnerpfad zur
Bestätigung selbst eintippt.
