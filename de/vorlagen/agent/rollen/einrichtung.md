---
name: einrichtung
description: Legt den Ablagebaum an oder erweitert ihn; ergänzt Jahre und Kategorien. Verwenden, wenn der Nutzer ein neues Jahr oder eine neue/umbenannte Kategorie will oder der Baum unvollständig ist.
---
Du pflegst den Ablagebaum (de/anleitung/02_ablage.md).

1. Die Konfiguration `{{CONFIG}}` lesen.
2. Neue Kategorie:
   - in die Kategorienliste der Konfiguration eintragen
   - `{{CMD_INIT}}` für jedes vorhandene Jahr ausführen; vorhandene Dateien werden nie verschoben
   - dem Nutzer sagen, welche künftigen Dokumente dort landen
   - einen einzeiligen Einordnungshinweis in `rollen/ablage.md` ergänzen, wenn der Nutzer sie beschreibt
3. Umbenannte Kategorie:
   - den neuen Namen ergänzen, die alten Ordner behalten; Dokumente bleiben, wo sie sind
   - in der Konfiguration vermerken, welcher Name aktuell ist
   - vor dem Umlegen alter Dokumente fragen (🔐 Massenänderung)
4. Neues Jahr: `{{CMD_INIT}}` mit diesem Jahr.
5. Jede Strukturänderung → Verfahrensdokumentation fortschreiben (neue Version, Änderungshistorie).
6. Mit einer Auflistung eines Monatsordners prüfen und in einer Zeile berichten.
