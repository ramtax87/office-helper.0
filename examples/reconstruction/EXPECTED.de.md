# Sollergebnisse — Rekonstruktionstests (X1–X14, Deutsch)

Englische Fassung: `EXPECTED.en.md` (gleicher Inhalt).

| Eingabe (Foto) | Referenz (nur zur Bewertung) | Was getestet wird |
|---|---|---|
| `rechnung_schulte_foto.jpg` | `rechnung_schulte.ground_truth.pdf` + `.json` | DIN-A4-Rechnung nach DIN 5008 Form B: Briefkopf mit Vektorlogo, Anschriftfeld, Informationsblock, Tabelle mit hinterlegter Kopfzeile und Linien, Summen, GiroCode, ein roter gedrehter Stempel, der die Betreffzeile überlappt, eine blaue handschriftliche Unterschrift, dreispaltige Fußzeile in 7 pt, Falz- und Lochmarken. Das Foto ist schräg auf einem Tisch aufgenommen, mit Schatten, ungleichmäßigem Licht, leichter Unschärfe, Rauschen und JPEG-Kompression. |
| `kassenbon_baeckerei_foto.jpg` | `kassenbon_baeckerei.ground_truth.pdf` + `.json` | Ein 80-mm-Thermobon in Festbreitenschrift. Das Papier ist verblasst (weniger Kontrast, warmer Farbton), und der Bon hat eine eigene Seitenlänge, zwei Steuersätze und TSE-Zeilen. |

**Die Referenzdateien nie als Eingabe verwenden.** Die Rekonstruktion bekommt nur das Foto. Die Referenz liest nur der
Bewertungsschritt danach. Jede andere Nutzung macht den Test ungültig.

Die Referenz-JSON nennt für jedes Element seine genaue Position in mm von der linken oberen Ecke:
- jede Textzeile: Text, Schriftfamilie, Stärke, Größe in pt, Farbe, Ausrichtung, Name des `critical`-Felds
- Linien und Rechtecke
- Codes mit ihrem Inhalt
- Grafiken: Logo, Stempel, Unterschrift

Zum Vergleich die Rekonstruktion zuerst auf das Seitenformat der Referenz normieren. Beide sind DIN A4 bzw. 80 mm breit.

## Bestanden, wenn
| # | Test | Bestanden |
|---|---|---|
| X1 | Kein Seitenraster | `pdfimages -list` zeigt kein Bild, das > 15 % der Seite bedeckt. Jeder Bildrahmen steht mit Begründung in `review.md`. Bei der Rechnung idealerweise gar kein Bild. |
| X2 | Echte, eingebettete Schriften | `pdffonts`: jede Schrift eingebettet, mit Unicode-Zuordnung |
| X3 | Textebene | `pdftotext` enthält jede Referenzzeile. Die Zeichenfehlerrate gegenüber der Referenz ist insgesamt ≤ 0,5 %, und Umlaute, ß und € stimmen. |
| X4 | Kritische Felder exakt | Rechnung: 2026-0457, 29.10.2026, 27.10.2026, DE287654324, 170,00 €, 214,50 €, 37,80 €, 35,00 €, 457,30 €, 86,89 €, 544,19 €, 12.11.2026, IBAN DE32 0000 0000 0000 1234 56. Bon: 17,20, 0,56, 1,39, 29.10.2026. Keine Abweichung erlaubt; sonst muss der Status `review` sein (ein ehrliches „review“ besteht X4, ein stillschweigend falscher Wert nicht). |
| X5 | Geometrie | Positionen der Textzeilen nach Normierung: Abweichung im Median ≤ 0,5 mm, höchstens ≤ 1,5 mm. Seitenformat als DIN A4 / 80 mm Breite erkannt. |
| X6 | Typografie | Schriftart-Klasse richtig: serifenlos bei der Rechnung, Festbreite beim Bon. Eine metrisch kompatible Schrift wird verwendet (Liberation Sans / Arimo / Arial bei der Rechnung, Liberation Mono / Cousine / Courier New beim Bon). Größe innerhalb ±0,5 pt. Fett erkannt bei Briefkopf, Betreff, Tabellenkopf, „Rechnungsbetrag“ und „SUMME“. Farbe des Briefkopfs dunkelblau, des Stempels rot, der Fußzeile grau (ΔE ≤ 10). |
| X7 | Tabelle | Hinterlegung der Kopfzeile vorhanden; Zeilenlinien vorhanden; Zahlen rechtsbündig an derselben rechten Kante wie im Original (≤ 0,5 mm) |
| X8 | GiroCode | Der QR-Code wird gelesen und als **Vektor** neu erzeugt. Sein Inhalt ist bytegleich mit `codes[0].payload` der Referenz. Seine Daten stimmen mit aufgedruckter IBAN, Betrag und Rechnungsnummer überein. |
| X9 | Unterschrift | Die Unterschrift ist ein Vektorpfad (oder ein enger Ausschnitt, im Review gelistet), in Blau. **Kein Textobjekt** in ihrem Bereich. |
| X10 | Stempel | „EINGEGANGEN“ und „30. OKT. 2026“ sind Text, rot, um ca. 8° (± 2°) gedreht. Der Rahmen ist Vektor. Die Überlappung mit der Betreffzeile ist aufgelöst: beide Texte sind vollständig. |
| X11 | Herkunft | XMP-/Dokument-Metadaten enthalten „Rekonstruktion“, den Namen der Quelldatei und den SHA-256 des Fotos. Der sichtbare Randvermerk ist vorhanden und liegt außerhalb des Inhaltsbereichs. Das Originalfoto ist unverändert (gleicher Hash) und liegt neben dem PDF. |
| X12 | Optische Ähnlichkeit und Bericht | `overlay.png` und `review.md` existieren. SSIM (binär, 150 dpi, gegen die Darstellung der Referenz) ist festgehalten. SOLL ≥ 0,90 bei der Rechnung und ≥ 0,85 beim verblassten Bon sein. |
| X13 | Plausibilität | Die Prüfungen laufen und melden keinen falschen Fehler: Summen, USt je Satz, IBAN-Prüfziffer, USt-IdNr.-Prüfziffer und GiroCode gegen aufgedruckte Daten sind in beiden Dokumenten stimmig. |
| X14 | PDF/A | Wird PDF/A angegeben, besteht veraPDF. Bei PDF/A-3 ist das Foto mit `AFRelationship=Source` eingebettet. |

Bewertung von X5/X6: Für jede Referenzzeile die rekonstruierte Zeile mit gleichem Text suchen (unscharfer Abgleich) und
`x_mm`, `baseline_mm`, `size_pt`, `weight`, Klasse von `font_family` und `color` vergleichen. Den Vergleich je Zeile ins
Bauprotokoll schreiben.
