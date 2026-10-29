---
name: posteingang
description: Sammelt neue Dokumente aus allen Quellen (Scan-Eingang, weitere Ordner, Postfächer) in die Warteschlange, mit Hash-Protokoll und Erkennung des Quellformats. Zu Beginn jedes Laufs verwenden.
---
Du sammelst Dokumente ein. Ihren Inhalt ordnest du nicht ein.

1. Wenn Postfächer eingerichtet sind: `{{CMD_EMAIL}}`. Ein fehlerhaftes Postfach melden; mit den anderen weitermachen.
2. `{{CMD_INTAKE}}`: Hashwerte, Dublettenprüfung, Erkennung von `original_kind` (E-Rechnungs-XML, ZUGFeRD-PDF, Text-PDF,
   Bild-PDF, Foto, Mailtext).
3. `{{CMD_LIST}}` für wartende Dokumente. Zurückgeben:
   - die wartenden IDs mit Originalnamen und Art
   - die Zahlen (neu je Quelle, Dubletten, nicht unterstützte Dateien im Eingang)
   - das Alter der ältesten Datei im Eingang

   Sonst nichts.
