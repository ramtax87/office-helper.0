# 04 · Aufgabe 3 — Lesen und ablegen

Ziel: Jedes Dokument aus der Warteschlange wird verstanden und unter einem sprechenden Namen in
`<JAHR>/<MONAT>/<KATEGORIE>/` abgelegt. Es bekommt eine deutsche Kurzbeschreibung, die deutschen Prüfungen und eine
**Aktionsklasse** für den Vorgangsschritt.

## 1. Inhalt beschaffen (nach `original_kind`, siehe 03)
- **`e_invoice_xml` / `hybrid_pdf`:**
  1. Mit dem offiziellen Validator prüfen. Den Bericht als `…_Pruefbericht.html`/`.xml` speichern.
  2. **Alle Fakten aus dem XML** lesen.
  3. Die lesbare Ansicht `…_Ansicht.pdf` erzeugen. Bei Hybriden das PDF-Bild mit dem XML vergleichen; eine Abweichung
     ist ein Befund (`12_deutschland.md` §5).
- **`photo` / `image` / `image_pdf`:** zuerst rekonstruieren (`13_rekonstruktion.md`). Den geprüften Text der
  Rekonstruktion verwenden. Ist die Rekonstruktion im Status `review`, das Dokument trotzdem ablegen; die kritischen
  Felder tragen `[PRÜFEN]`, und der Vorgangsschritt legt „Rekonstruktion prüfen“ an.
- **`text_pdf`, Text, Mail:** Textebene bzw. Text. Ist sie leer oder unsinnig, wie `image_pdf` behandeln.
- **Nichts lesbar:** Kategorie `Sonstiges`, Aktion `check`, Kurzbeschreibung „nicht lesbar – manuell prüfen“.

## 2. Fakten entnehmen (nur aus dem Dokument)
| Feld | Regel |
|---|---|
| Belegdatum | das aufgedruckte Datum: Rechnungsdatum, Briefdatum, Bescheiddatum, Lieferdatum. Nicht Scan- oder Maildatum. Fehlt es: Eingangsdatum, und die Kurzbeschreibung sagt das |
| Absender | kurzer Name der Firma oder Behörde, z. B. „Finanzamt Paderborn“, „Kartoffel Müller GmbH“. Bei einem Mahnbescheid: das Gericht **und** der Antragsteller |
| Art / Kategorie | eine eingerichtete Kategorie (Tabelle in §3) |
| Referenz | Rechnungsnr., Aktenzeichen, Geschäftsnummer, Kundennummer, Steuernummer-Bezug, Ticketnr. — was der Absender anzugeben bittet |
| Beträge | brutto; bei Rechnungen auch netto und USt je Satz; Zahlungen ans Finanzamt (Abschlusszahlung, Vorauszahlung) |
| Fristen | Zahlungsdatum, Skontodatum, Antwortfrist, Rechtsbehelfsfrist (aus der Rechtsbehelfsbelehrung), Liefer- oder Termindatum. **Vom Fristenrechner des Kerns berechnet** (`12_deutschland.md` §3), nicht geschätzt |
| Bankdaten | IBAN/BIC aus dem Dokument oder seinem GiroCode; geprüft (Modulo 97), im Datensatz gespeichert, sonst nur gekürzt angezeigt |
| Kurzbeschreibung | 1–2 deutsche Sätze: **wer will was bis wann** |
| Aufbewahrung | `Aufbewahren bis 31.12.JJJJ` je Dokumentart (`12_deutschland.md` §8) |
| Papieroriginal | „aufbewahren“ oder „laut Verfahrensdokumentation entbehrlich“ (`12_deutschland.md` §8) |

## 3. Einordnung (deutsche Bürodokumente)
| Dokument | Kategorie | Aktionsklasse | Priorität |
|---|---|---|---|
| Rechnung (erhalten), E-Rechnung | Rechnungen_Eingang | `pay` (+ § 14-Prüfung) | P2 |
| Mahnung / Zahlungserinnerung | Rechnungen_Eingang | `pay` (mit Rechnung verknüpfen) | P1 |
| Rechnung (ausgestellt), Gutschrift | Rechnungen_Ausgang | `check` / `none` | P3 |
| Kassenbon, Quittung | Kassenbelege | `none` | — |
| Bewirtungsbeleg | Kassenbelege | `check` („ergänzen: Anlass, Teilnehmer, Unterschrift“) | P2 |
| Steuerbescheid, Haftungsbescheid | Finanzamt | `check` (Einspruchsfrist) + `pay`, wenn eine Zahlung fällig ist | P1 |
| Vorauszahlungsbescheid | Finanzamt / Behörden | `check` → aktualisiert die Beträge im Steuerkalender | P2 |
| Anforderung von Unterlagen, Prüfungsanordnung | Finanzamt | `provide_documents` | P1 |
| Gewerbesteuer-/Grundsteuerbescheid | Behörden | `check` + `pay` | P1 |
| Rundfunkbeitrag, IHK/HWK-Beitrag | Behörden | `pay` (+ `check`, wenn es ein Bescheid ist) | P2 |
| Anhörung, Bußgeldbescheid | Behörden | `reply` / `check` | P1 |
| Krankenkasse, Minijob-Zentrale, BG, DRV | Sozialversicherung | je nach Inhalt (Beitragsbescheid → `pay`/`check`; Betriebsprüfung → `provide_documents`) | P1/P2 |
| Mahnbescheid, Vollstreckungsbescheid | Recht_Gericht | `check` (nie automatischer Widerspruch) | P1 |
| Inkasso, Abmahnung, Anwaltsschreiben | Recht_Gericht | `check` / `reply` + Aufgabe Anwalt | P1 |
| Brief mit einer Frage, Beschwerde | Briefe | `reply` | P2 |
| Lieferschein, Auftragsbestätigung, Lieferavis | Lieferungen | `deliver` oder `check` | P2 |
| Vertrag, Kündigung, Vertragsänderung | Verträge | `check` (Kündigungsfristen) | P2 |
| Versicherungspolice, Beitragsrechnung | Versicherungen | `pay` / `check` | P2 |
| Kontoauszug, Lastschriftankündigung | Bank | `none` / `check` | P3 |
| Lohnabrechnung, AU-Bescheinigung, Personalschreiben | Personal | je nach Inhalt — **nur lokal verarbeitet** | P2 |
| Anfrage des Steuerberaters | Steuerberater | `provide_documents` | P2 |
| Ticket, Buchung | Tickets | `deliver` (Datum) oder `none` | P3 |
| Newsletter, Preisliste, Info | Info | `none` | — |

Aktionsklassen:
- `pay` — bezahlen
- `reply` — antworten
- `provide_documents` — Unterlagen liefern
- `deliver` — an einem Datum passiert etwas (eine Lieferung annehmen, ein Termin)
- `check` — lesen und entscheiden
- `none` — nichts zu tun

## 4. Deutsche Prüfungen beim Ablegen (Befunde gehen an den Vorgangsschritt)
- **§ 14 UStG Vollständigkeit** bei Eingangsrechnungen, und die Regel für Kleinbetragsrechnungen
  (`12_deutschland.md` §6).
- **Rechnerische Prüfung:** Positionssummen, Netto + USt = Brutto, USt je Satz. Nur echte Abweichungen melden.
- **IBAN-Wechsel:** IBAN mit früheren Dokumenten desselben Absenders vergleichen (`12_deutschland.md` §7).
- **GiroCode gegen aufgedruckte Daten.**
- **E-Rechnungs-Hinweis ab 2027** (`12_deutschland.md` §5, Punkt 7).
- **TSE-Daten auf Kassenbons** (Hinweis).

## 5. Ablegen (über den Kern)
- Zielordner: `<ROOT>/<Jahr des Belegdatums>/<NN_Monat>/<Kategorie>/`.
- Dateiname: `JJJJ-MM-TT_<Absender>_<Art>_<Referenz>.<Endung>`, z. B. `2026-10-28_Kartoffel_Mueller_Rechnung_1205.pdf`.
  - Leerzeichen → `_`. Problematische Zeichen entfernen.
  - Höchstens ca. 100 Zeichen.
  - Bei Namensgleichheit `_2`, `_3` anhängen. Nie überschreiben.
- **Begleitdateien** mit demselben Grundnamen:

| Begleitdatei | Wann |
|---|---|
| `…_Original.<Endung>` | das Foto |
| `…_Rekonstruktion.pdf` | Quelle Foto/Bild |
| `….xml` | Original der E-Rechnung |
| `…_Ansicht.pdf` | Ansicht der E-Rechnung |
| `…_Pruefbericht.*` | Prüfbericht |

- Die **Kurzbeschreibung** daneben schreiben: `<Grundname>.md` (Vorlage: `shared/templates/summary.de.md`). Sie macht
  den Baum ohne das System lesbar und für den Vorgangsschritt durchsuchbar.
- Den Dokumentdatensatz aktualisieren (Status `filed`, Pfade, entnommene Felder) und an `protokoll.csv` anhängen.

## 6. An den Vorgangsschritt übergeben
Je Dokument zurückgeben:
- die ID
- die Aktionsklasse(n)
- die berechneten Fristen (Fristende / erledigen bis)
- die Befunde aus §4
- eine einzeilige Begründung

Dokumente mit Aktion `none` und ohne Befunde sind hier fertig.
