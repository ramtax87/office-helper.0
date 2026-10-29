# 11 · Betrieb

So sieht das fertige System im Alltag aus. Die umsetzende KI MUSS jeden Punkt zum Laufen bringen und bei der Übergabe in
der Sprache des Nutzers erklären.

## Tagesablauf (Nutzer)
1. Das Papier des Tages nach `_Eingang` scannen oder fotografieren (Scanner oder Handy). E-Mails kommen von selbst.
2. **„Tageslauf“** sagen, oder den geplanten Lauf morgens erledigen lassen.
3. Kurzbericht und `SCHEDULE.md` lesen.
4. Warten Entwürfe, **„Entwürfe prüfen“** sagen: freigeben, bearbeiten oder verwerfen. Freigegebene selbst versenden (der
   Versandweg ist vermerkt).
5. Dem Agenten sagen, was erledigt ist („Rechnung 1205 ist bezahlt“, „Einspruch per ELSTER eingelegt“), er schließt die
   Aufgabe.

| Sagen | Wirkung |
|---|---|
| „Tageslauf“ / „Post bearbeiten“ | Posteingang → Rekonstruktion/Ablage → Vorgänge → Fristenplan + Bericht |
| „Was ist fällig?“ / „Was muss ich bis Ende November bezahlen?“ | Bericht aus dem Fristenplan, gefiltert |
| „Dokument 37: Lieferung war unvollständig, schreib eine Reklamation“ | einzelner Vorgang mit Anweisungen |
| „Entwürfe prüfen“ | Entwurf für Entwurf, danach Aufgabenabgleich |
| „Mach aus diesem Foto ein PDF“ (+ Foto) | Rekonstruktion, das PDF wird abgelegt |
| „Wo ist die Telekom-Rechnung vom August und wie hoch war sie?“ | Suche in den Kurzbeschreibungen, Antwort mit Pfad und Betrag |
| „Dokument 37 gehört zu Verträge“ | Umlegen über den Kern |
| „Neue Kategorie Fahrzeuge“ | Konfiguration ändern + Baum erweitern (vorhandene Dateien bleiben) |
| „Baum für 2028 anlegen“ | neue Jahresordner |
| „Belege Oktober für den Steuerberater“ | Belegpaket + CSV + E-Mail-Entwurf (`12_deutschland.md` §11) |
| „Zustelldatum Mahnbescheid war der 24.10.“ | berechnet die Frist mit dem echten Datum neu |

Weitere Prompts zum Kopieren: `de/prompts/ALLTAG.md`.

## Monatlich
- Belegpaket für den Steuerberater vorbereiten (erste Arbeitstage des Monats).
- `Sonstiges` und die Dokumente im Review durchsehen.
- Steuerkalender für die nächsten Monate ansehen; Beträge aus neuen Vorauszahlungsbescheiden übernehmen.
- Offene Aufgaben stornieren, die nicht mehr zutreffen.

## Jährlich
- Im Herbst den Baum für das nächste Jahr anlegen (ab Oktober automatisch).
- **Aufbewahrungsbericht:** Dokumente nach Ablauf ihrer Frist auflisten. Der Nutzer entscheidet mit dem Steuerberater;
  das System löscht nichts.
- Die Verfahrensdokumentation prüfen und neu datieren (mindestens jährlich und bei jeder Änderung).
- Die ⟳-Regeln aus `12_deutschland.md` (Feiertage, Steuerkalender, E-Rechnungsregeln) gegen offizielle Quellen prüfen.
  Hat sich eine Regel geändert: Umsetzung und Tests anpassen.

## Datensicherung (MUSS bei der Übergabe empfohlen werden)
- Wichtig ist der Hauptordner (Dokumente + Datensätze + Protokolle). Google Drive ist eine Kopie, keine Sicherung:
  zusätzlich regelmäßig anderswo sichern (externe Festplatte, zweite Cloud, NAS) und einmal eine Wiederherstellung testen.
- Der Umsetzungsordner lässt sich aus dieser Anleitung neu bauen. Seine Konfiguration und die Geheimnisdatei getrennt und
  sicher sichern.

## Fehlerbehebung
| Symptom | Wahrscheinliche Ursache → Lösung |
|---|---|
| Scans ergeben keinen oder kaputten Text („Kontoausziige“) | OCR oder deutsche Sprachdaten fehlen; Scan unter 300 dpi |
| Eine Foto-Rekonstruktion wirkt falsch | Blattränder nicht gefunden (auf dunkler, einfarbiger Fläche fotografieren), starke Wölbung, Spiegelung; `overlay.png` und `review.md` ansehen |
| Falsche Schrift in einer Rekonstruktion | eine besser passende freie oder lizenzierte Schrift fehlt → installieren und neu rekonstruieren |
| E-Rechnung „ungültig“ | Prüfbericht ansehen. Formatfehler → beim Lieferanten eine korrekte E-Rechnung anfordern; veraltete Validator-Konfiguration → aktualisieren ⟳ |
| E-Mail-Anmeldung schlägt fehl | App-Passwort nötig (Gmail); IMAP in den Postfacheinstellungen deaktiviert (GMX, web.de); Passwort nicht in der Geheimnisdatei |
| Dokument unter dem Scandatum abgelegt | das aufgedruckte Datum wurde nicht gefunden → OCR- oder Rekonstruktionsqualität prüfen; umlegen |
| Frist wirkt falsch | `deadline_basis` der Aufgabe ansehen; das echte Zustell- oder Leistungsdatum angeben („Zustelldatum war …“) |
| Drive-Ordner nicht gefunden oder leer | der Dienst zum Einbinden/Synchronisieren läuft nicht → neu starten; prüfen, ob die Anmeldung abgelaufen ist |
| Dieselbe Aufgabe erscheint doppelt | Doppelprüfung im Kern fehlt → Abnahmetest C9 |
| Der Kalender zeigt Doppel | UIDs nicht fest → Abnahmetest C12 |
| Fehler im eigenständigen Runner | Schlüssel nicht gesetzt, falsche Basis-URL oder Modell ohne Tool-Calling (`08`) |
| Der Agent hält sich nicht an die Regeln | Anweisungsdatei vom Werkzeug nicht geladen → Einbindung prüfen (`08`), Phase 6 wiederholen |
