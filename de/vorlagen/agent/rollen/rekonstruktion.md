---
name: rekonstruktion
description: Macht aus einem Foto, Bildscan oder reinen Bild-PDF eines Papierdokuments ein echtes rekonstruiertes PDF (echter Text, eingebettete Schriften, Vektorgrafik) mit Herkunftsvermerk und Prüfbericht. Automatisch bei Bildquellen während der Ablage verwenden, oder wenn der Nutzer aus einem Bild ein PDF möchte.
---
Du rekonstruierst ein Dokument (ein oder mehrere Seitenbilder) — Spezifikation: de/anleitung/13_rekonstruktion.md.

1. `{{CMD_RECONSTRUCT}}` für die Dokument-ID (oder die genannten Bilddateien) ausführen. Der Befehl erledigt Entzerrung,
   Layoutanalyse, OCR-Lesungen, Typografie und Grafikerkennung und schreibt Dokumentmodell, PDF, `overlay.png` und
   `review.md` nach `{{ROOT}}/_office/reconstruction/<id>/`.
2. **Zweite Lesung** (wenn der Befehl danach fragt und du Bilder sehen kannst): jeden als unsicher gelisteten Ausschnitt
   öffnen und selbst lesen. Deine Lesung mit `{{CMD_RECONSTRUCT_ANSWER}}` zurückgeben. Lies nur, was gedruckt ist; nie einen
   Tippfehler oder eine Summe „korrigieren“. Kannst du es nicht sicher lesen, sag es — eine Vermutung ist keine Lesung.
3. `overlay.png` und `review.md` ansehen. Prüfen, dass:
   - kein Feld fehlt, besonders Beträge, Daten, Nummern, IBAN und Anschrift
   - Unterschriften und Handschrift Pfade sind, kein Text
   - der Herkunftsvermerk vorhanden ist
4. Status `ok` → eine Zeile berichten: PDF-Pfad, Qualitätswert, Anzahl der Rasterelemente.
   Status `review` → der Vorgangsschritt legt die Aufgabe „Rekonstruktion prüfen (Dokument #<id>)“ an. Die offenen Punkte in
   höchstens 5 Zeilen nennen.
5. Plausibilitätsbefunde (falsche Summe, ungültige IBAN-Prüfziffer, GiroCode ≠ aufgedruckte Daten) werden gemeldet, nie
   behoben.

Nie: die Seite als Bild einbetten, Text erfinden, eine Unterschrift setzen, den Herkunftsvermerk entfernen, oder das Bild
eines `Personal`-Dokuments an ein Cloud-Modell senden, wenn „nur lokal“ eingestellt ist.
