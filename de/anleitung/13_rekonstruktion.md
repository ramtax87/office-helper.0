# 13 · Aufgabe 6 — Foto → rekonstruiertes PDF

Ziel: Aus einem Foto oder Bildscan eines Papierdokuments entsteht ein **echtes PDF-Dokument**: echter Text in echten
(eingebetteten) Schriften, Vektorlinien, Tabellen und Codes, angeordnet wie im Original. Es muss wie das Original aussehen
und durchsuchbar, kopierbar und archivierbar sein.

**Nicht zulässig:** ein Bild der Seite im PDF, auch nicht mit unsichtbarer OCR-Textebene darunter. Das erzeugen Scanner-Apps
und `ocrmypdf`, und genau das ist ausdrücklich **nicht** Ziel dieser Aufgabe.

## Einordnung
- **Automatisch:** Beim Ablegen wird jedes Dokument, dessen Quelle ein Foto, ein Bildscan oder ein reines Bild-PDF ist,
  zuerst rekonstruiert (Konfiguration `reconstruction.mode = "auto"`). Die Ablage verwendet dann den geprüften Text der
  Rekonstruktion.
- **Auf Wunsch:** „Mach aus diesem Foto ein PDF“ / „Dokument 37 rekonstruieren“ — der Workflow `rekonstruieren`.
- **Mehrseitig:** Mehrere Fotos eines Dokuments (gleiche Mail, gleicher Eingang mit fortlaufenden Namen, oder der Nutzer
  sagt es) werden ein PDF in richtiger Seitenfolge. Seitenzahlen wie „Seite 2 von 3“ dienen zur Prüfung von Reihenfolge
  und Vollständigkeit.

## Was aufbewahrt wird (GoBD — siehe `12_deutschland.md`)
Das Foto ist der **Beleg** (das digitale Original des Papiers). Die Rekonstruktion ist eine abgeleitete **Lesefassung**.
Deshalb:
- Die Original-Bilddatei bleibt unverändert, mit Hashwert in den Datensätzen. Sie wird nie gelöscht oder ersetzt.
- Das rekonstruierte PDF liegt daneben: `…_Rekonstruktion.pdf`. Das Originalbild behält seinen Namen mit dem Zusatz
  `_Original`.
- Das PDF MUSS seine Herkunft tragen:
  - XMP-/Dokument-Metadaten: „Rekonstruktion“, Name der Quelldatei, SHA-256 der Quelle, Datum, Verfahren und Qualitätswert.
  - Ein **sichtbarer, unauffälliger Vermerk** im unteren Rand, außerhalb des Inhaltsbereichs (ca. 6 pt, grau), z. B.
    `Rekonstruiert aus Foto IMG_2041.jpg vom 29.10.2026 · Original: Office-ID #57 · Qualität 0,97`.
  - Grund: Eine Rekonstruktion, die als eigenes Original des Ausstellers durchgehen kann, ist in einer Betriebsprüfung und
    in Streitfällen ein Risiko. Dieser Vermerk ist nicht optional.
- SOLL PDF/A-3b sein, mit dem Originalbild als zugeordneter Datei eingebettet (`AFRelationship=Source`), damit das PDF seinen
  eigenen Nachweis mitträgt. Kann die Werkzeugkette kein gültiges PDF/A-3 erzeugen: PDF/A-2b erzeugen, das Bild daneben
  aufbewahren und den Ausweichweg im Bauprotokoll vermerken.
- Eine rekonstruierte Rechnung ist **keine E-Rechnung**, und das System erzeugt nie XRechnung-/ZUGFeRD-Daten so, „als ob“ der
  Absender sie ausgestellt hätte.

## Regeln für die Originaltreue
1. **Der Inhalt ist unantastbar.** Die Rekonstruktion gibt wieder, was auf dem Papier steht, Zeichen für Zeichen. Das
   schließt Fehler des Originals ein: Tippfehler, falsche Summen, veraltete Adressen. Nichts wird korrigiert, übersetzt,
   ergänzt oder „verbessert“. Fehler, die die Prüfungen finden (Schritt 7), werden **gemeldet**, nie im Dokument
   behoben.
2. **Nie stillschweigend raten.** Jedes Zeichen, bei dem das System unsicher ist, wird durch Konsens geklärt (Schritt 4)
   oder zur Prüfung durch den Menschen markiert. Kritische Felder MÜSSEN Konsens erreichen oder das Dokument geht ins
   Review.
3. **Text ist Text.** Jeder gedruckte Text wird echter Text in einer echten Schrift. Das gilt auch für Tabelleninhalte,
   Kleingedrucktes, gedrehten Text und Stempel mit gedruckter Schrift.
4. **Handschrift wird nie gesetzt.** Unterschriften, Kürzel, handschriftliche Notizen und Haken werden als
   **Vektorpfade** nachgezeichnet (oder, wenn das misslingt, als eng zugeschnittenes Bild behalten). Sie werden nie in
   einer Schrift dargestellt. Lesbare Handschrift wird nur in Kurzbeschreibung und Metadaten transkribiert.
5. **Raster nur, wo unvermeidbar:** Fotos im Dokument, Logos, die sich nicht sauber vektorisieren lassen, und
   Unterschriften oder Stempel, die sich nicht nachzeichnen lassen. Jedes eng auf seinen Rahmen zugeschnitten. Ihre
   Gesamtfläche SOLL ≤ 15 % der Seite bleiben; jedes Rasterelement steht mit Begründung im Prüfbericht.

## Ablauf
Zwischenergebnisse je Dokument in `_office/reconstruction/<id>/` aufbewahren, zur Fehlersuche und Prüfung.

### 1. Entzerren
- Blattränder finden, die Perspektive korrigieren („Vier-Punkt-Transformation“) und gerade ausrichten. Gewölbte Seiten bei
  Bedarf glätten (Dewarping).
- Lichtverlauf und Schatten entfernen. Farbe erhalten (Stempel, Logos, Markierungen) und eine saubere Graustufen-/Binärfassung
  für die OCR erzeugen.
- **Seitenformat:** das Seitenverhältnis den Standardformaten zuordnen (DIN A4 210×297, A5, A6, DL, US Letter). Bei
  Kassenbons 57/80 mm Breite mit gemessener Länge. Damit ist der Maßstab in Millimetern festgelegt.
- Ergebnis: `01_rectified.png` mit mindestens 300 dpi Entsprechung, plus Maßstab mm↔px.

### 2. Layoutanalyse
Finden und einordnen:
- Textblöcke, Zeilen und Wörter mit Rahmen
- Lesereihenfolge
- Tabellen: Gitterlinien, Zellen, Kopfzeile, Spaltenausrichtung
- Linien, Rahmen und gefüllte Flächen
- Logos und Bilder
- Stempel
- Unterschriften und Handschrift
- QR-Codes und Barcodes
- DIN-5008-Elemente: Falzmarken, Lochmarke, Rücksendeangabe, Anschriftfeld, Informationsblock, Fußzeilenspalten

### 3. Text erkennen
- OCR mit deutschen Sprachdaten, mit Rahmen und Konfidenzen je Wort.
- Mindestens **zwei unabhängige Lesungen**: zwei OCR-Engines, oder OCR plus ein bildfähiges Modell, das den Ausschnitt liest.
  Bei Zeilen mit schon sehr hoher Konfidenz genügt eine zusätzliche Lesung.
- Deutsche Besonderheiten:
  - die Zeichen ä ö ü Ä Ö Ü ß €, das Paragrafenzeichen § und der Halbgeviertstrich –
  - Zahlenformat `1.234,56`, Datum `TT.MM.JJJJ`
  - IBAN in Vierergruppen
  - typische Wörter: Rechnungsbetrag, zzgl. USt, Steuernummer, Aktenzeichen

### 4. Konsens und Prüfung
- Wo die Lesungen mit hoher Konfidenz übereinstimmen, den Text übernehmen.
- Wo sie abweichen, wird der Ausschnitt erneut gelesen (höhere Auflösung, andere Vorverarbeitung, Bildmodell). Dann:
  - eine klare Mehrheit wird übernommen;
  - alles andere bekommt die wahrscheinlichste Lesung **und** einen Eintrag in der Prüfliste (Ausschnittbild,
    Kandidaten).
- **Kritische Felder** MÜSSEN Konsens haben, sonst ist der Dokumentstatus `review`:
  - Beträge und Summen
  - Daten
  - Rechnungs-, Kunden- und Aktenzeichen
  - IBAN und BIC
  - Steuernummer und USt-IdNr.
  - Namen im Anschriftfeld

### 5. Typografie
Für jede Zeile bzw. jeden Abschnitt ermitteln:
- **Größe** in pt, aus Versal- und x-Höhe über den mm-Maßstab
- **Strichstärke** (normal/fett), aus der Strichbreite
- **Lage** (aufrecht/kursiv), aus der Neigung
- **Farbe**
- **Ausrichtung**: links, rechts, zentriert, Blocksatz, an Dezimalstellen ausgerichtete Zahlen
- **Zeichenabstand**

**Schrift bestimmen:**
1. Die Schriftart einordnen: serifenlos, Serifen, Festbreite, schmal, Auszeichnungsschrift.
2. Die Zeile in Kandidatenschriften in der gemessenen Größe setzen und mit dem Ausschnitt vergleichen: Glyphenformen und
   Zeilenbreite. Die beste Übereinstimmung gewinnt.
3. **Metrisch kompatible freie Schriften** bevorzugen. Sie brechen Zeilen genau wie die kommerziellen Originale um:

| Auf deutschen Dokumenten zu sehen | Verwenden (frei, metrisch kompatibel) |
|---|---|
| Arial, Helvetica | Liberation Sans oder Arimo |
| Times New Roman | Liberation Serif oder Tinos |
| Courier New, Schreibmaschine | Liberation Mono oder Cousine |
| Calibri (Word-Standard) | Carlito |
| Cambria | Caladea |
| Thermodrucker von Kassen | Liberation Mono oder eine passende Festbreitenschrift im Nadeldruck-Stil |
| DIN-1451-Stil (Schilder, manche Briefköpfe) | D-DIN o. ä. |
| Hausschriften (z. B. Telekom, Sparkasse) | die per Glyphenvergleich nächstliegende freie Schrift. Nie eine Schriftdatei, für die der Nutzer keine Lizenz hat |

4. Eine installierte und lizenzierte Schrift des Nutzers (z. B. MS Core Fonts) verwenden, wenn sie besser passt.
5. Jede Schrift als Teilmenge mit Unicode-Zuordnung einbetten, damit Kopieren, Suchen und Bildschirmleser funktionieren.
6. Jede Zeile auf ihre Originalbreite einpassen: minimale Laufweiten- oder Breitenanpassung, höchstens ±3 %. Braucht sie
   mehr, ist die Schriftwahl falsch; den nächsten Kandidaten nehmen.

### 6. Grafiken und Codes
- **Linien, Rahmen, Flächen, Tabellenlinien:** als Vektor, mit gemessener Strichstärke und Farbe.
- **QR-Codes** (z. B. der GiroCode/EPC-QR auf deutschen Rechnungen) und **Barcodes** (EAN, Code 128, DataMatrix): lesen und
  mit identischem Inhalt als Vektor neu erzeugen. Der Inhalt kommt in den Prüfbericht. Lässt sich ein Code nicht lesen:
  als zugeschnittenes Bild behalten und markieren.
- **Logos:** vektorisieren (Farbreduktion + Nachzeichnen). Passt das Ergebnis optisch nicht: ein zugeschnittenes Bild.
- **Stempel:** gedruckte Schrift wird Text in passender Schrift mit Drehung und Farbe. Der Rahmen wird als Vektor
  gezeichnet. Die Farbstruktur der Stempelfarbe wird nicht nachgebildet.
- **Unterschriften und Handschrift:** nachgezeichnete Vektorpfade (siehe Regel 4).

### 7. Plausibilitätsprüfungen (melden, nie korrigieren)
- IBAN-Prüfziffer (Modulo 97) und die deutsche IBAN-Länge von 22.
- Format und Prüfziffer der USt-IdNr. (DE + 9 Ziffern).
- Format der Steuernummer.
- Positionssummen = Menge × Einzelpreis.
- Netto + USt = Brutto. USt je Satz bei Kassenbons.
- Daten existieren. Das Rechnungsdatum liegt nicht nach dem Scandatum.
- Der Inhalt des GiroCodes stimmt mit IBAN, Betrag und Verwendungszweck auf der Seite überein.
- Jede Abweichung kommt in den Prüfbericht, z. B. „Summe auf dem Beleg 17,20 € ≠ Positionen 17,30 €“.

### 8. PDF erzeugen
Das **Dokumentmodell** füllen (`shared/templates/reconstruction/document_model.schema.json`):
- Seitenformat
- Elemente mit mm-Positionen
- formatierte Textabschnitte, Tabellen, Vektorpfade, Codes und Bildausschnitte
- Konfidenz je Element

Dann mit einer PDF-Bibliothek erzeugen, die Text absolut positioniert und Schriften einbettet. Anforderungen:
- echte Textobjekte in Lesereihenfolge, mit Unicode-Zuordnung
- kein Element außerhalb der Seite
- Vektorgrafik als Vektor
- Herkunftsvermerk und Metadaten
- PDF/A wie oben

### 9. Prüfen
Das erzeugte PDF in der Auflösung des entzerrten Bildes neu rendern. Mit `01_rectified.png` zur Deckung bringen und
berechnen:

| Prüfung | Anforderung |
|---|---|
| Kein Seitenbild | Rasterbilder über die ganze Seite oder über Textbereiche: keine (Bildliste des PDF prüfen) |
| Schriften | alle Schriften eingebettet |
| Textebene | aus dem PDF gelesener Text = Konsenstext (Zeichenfehlerrate 0 gegenüber der eigenen Quelle) |
| Kritische Felder | alle vorhanden, identisch mit den Konsenswerten |
| Geometrie | Positionen der Textzeilen: Abweichung im Median ≤ 0,5 mm, höchstens ≤ 1,5 mm |
| Typografie | Schriftgröße innerhalb ±0,5 pt; Stärke und Schriftart-Klasse wie erkannt |
| Optische Ähnlichkeit | SSIM der Binärbilder bei 150 dpi, als Qualitätswert festgehalten (SOLL ≥ 0,90 bei sauberen Drucken) |
| PDF/A | besteht einen PDF/A-Validator (z. B. veraPDF), wenn PDF/A angegeben wird |

`overlay.png` (Original rot, Rekonstruktion blau: lila = Übereinstimmung) und `review.md` speichern. `review.md` listet:
- die unsicheren Wörter mit Ausschnitten
- die Rasterelemente mit Begründung
- die Plausibilitätsbefunde
- den Qualitätswert

Scheitert eine MUSS-Prüfung: Status `review`. Der Vorgangsschritt legt dann die Aufgabe „Rekonstruktion prüfen“ an.

## Prüfung durch den Menschen
Im Prüf-Workflow sieht der Nutzer `overlay.png` und die unsicheren Stellen neben dem Foto. Korrekturen des Nutzers werden
ins Dokumentmodell übernommen (nie durch Bearbeiten des PDF von Hand), und das PDF wird neu erzeugt. Jede Korrektur wird
protokolliert.

## Leistung
SOLL unter etwa 2 Minuten je A4-Seite auf einem normalen Laptop ohne GPU liegen, einschließlich zweiter Lesung per
Modell. Schwere Werkzeuge (Layoutmodelle, Bildmodelle) KÖNNEN genutzt werden, wenn der Rechner es zulässt. Bei einem
Cloud-Modell gelten die Datenschutzregeln aus `08` und `12`: Keine `Personal`-Dokumente verlassen den Rechner, außer der
Nutzer hat es erlaubt.

## Werkzeughinweise (wählen, was zum Rechner passt; aktuelle Versionen prüfen)
- **Entzerrung und Bildverarbeitung:** OpenCV.
- **OCR mit Wortrahmen:**
  - Tesseract 5 mit `deu` (Ausgabe hOCR/ALTO/TSV)
  - PaddleOCR (inklusive Layout und Tabellenstruktur)
  - docTR
  - Surya (Layout, Lesereihenfolge, Tabellen)
- **Zweite Lesung:** ein bildfähiges Modell über das KI-Werkzeug oder den Anbieter des Nutzers.
- **Codes:** zxing-cpp oder die QR-Erkennung von OpenCV. Neue Codes mit einer QR-Bibliothek erzeugen; GiroCode-Inhalt nach
  EPC069-12.
- **Nachzeichnen:** potrace (einfarbig); Farbreduktion + potrace für Logos.
- **Schriftbestimmung:**
  - fontTools für Metriken
  - FreeType/Pillow zum Setzen der Kandidaten
  - fontconfig zum Finden installierter Schriften
- **PDF:** ReportLab, PyMuPDF oder pikepdf (für PDF/A und Anhänge); oder HTML/CSS mit absoluter Positionierung über
  WeasyPrint; oder Typst. Mit veraPDF prüfen.
- **Vergleich:** SSIM (scikit-image), pdffonts/pdfimages/pdftotext (poppler) für die strukturellen Prüfungen.
