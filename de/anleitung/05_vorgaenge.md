# 05 · Aufgabe 4 — Vorgänge mit Sub-Agenten

Ziel: Jedes Dokument, das etwas verlangt, wird **einzeln** bearbeitet. Es bekommt eigene Aufgaben, Verknüpfungen zu
zugehörigen Dokumenten und, wenn eine Antwort nötig ist, einen deutschen Antwortentwurf zur Prüfung.

## Sub-Agenten
- Ein Vorgang = ein Dokument.
- Kann das KI-Werkzeug Sub-Agenten starten: ein Vorgangs-Agent je Dokument, höchstens ca. 5 gleichzeitig, damit
  Ratenlimits eingehalten werden und die Datensätze stimmig bleiben.
- Ohne Sub-Agenten bearbeitet der Orchestrator die Vorgänge nacheinander mit denselben Anweisungen. Das Ergebnis MUSS
  gleich sein.
- Ein Vorgangs-Agent bekommt:
  - die Dokument-ID, die Aktionsklasse(n), die berechneten Fristen, die Befunde und die Begründung
  - die Rollenanweisung (`de/vorlagen/agent/rollen/vorgang.md`)

  Er liest die Kurzbeschreibung und bei Bedarf das Dokument.
- `Personal`-Dokumente: Der Vorgangs-Agent läuft nur mit dem lokalen Modell, wenn „nur lokal“ eingestellt ist
  (`12_deutschland.md` §12).

## Schritte eines Vorgangs
1. **Kontext.** Kurzbeschreibung lesen; bei fehlenden Details das Dokument erneut lesen.
2. **Zugehörige Dokumente.** Die Kurzbeschreibungen nach gleichem Absender und gleicher Referenz durchsuchen:
   - eine Mahnung gehört zu ihrer Rechnung
   - ein zweiter Brief gehört zum ersten
   - ein Lieferschein gehört zur Bestellung
   - ein Mahnbescheid gehört zur zugrunde liegenden Rechnung

   Die IDs zugehöriger Dokumente in Aufgaben und Entwurf nennen. Sonderfälle:
   - **Ersetzte Aufgaben:** Mahnung zu einer Rechnung, deren Zahlungsaufgabe noch offen ist → alte Zahlungsaufgabe
     stornieren („ersetzt durch Mahnung #…“). Nur eine Zahlungsaufgabe, mit Betrag und Datum der Mahnung.
   - Mahnung zu einer Rechnung, deren Zahlungsaufgabe schon erledigt ist → P1 „Zahlung prüfen – Mahnung trotz
     Zahlung?“.
   - Das zugrunde liegende Dokument ist nicht im Archiv → „nicht im Archiv“ sagen und vorschlagen zu prüfen, ob bezahlt
     wurde.
3. **Aufgaben** (über den Kern). Aufgabentitel beginnen mit einem Verb und nennen das Gegenüber. Fristen kommen immer
   aus dem Fristenrechner des Kerns, und jede Aufgabe zeigt `Fristende` und `erledigen bis` (`12_deutschland.md` §3).

   | Aktion / Befund | Aufgabe |
   |---|---|
   | `pay` | „Rechnung 1205 an Kartoffel Müller bezahlen“ — Details: Betrag, Fälligkeit, Skontodatum, IBAN gekürzt (…3000), GiroCode-Daten falls vorhanden |
   | `reply` | „Antwort an <Absender> senden“ — Frist aus dem Brief, sonst Briefdatum + 14 Tage (und das sagen); Versandweg |
   | `provide_documents` | „Unterlagen an <Absender> senden“ — genau die angeforderte Liste; je Posten, wo er schon im Baum liegt (Ordnerpfad) oder „fehlt“; Versandweg (Finanzamt: ELSTER) |
   | `deliver` | „Kartoffellieferung am 05.11.2026 (7–9 Uhr) annehmen und prüfen“ |
   | `check` | „Steuerbescheid USt 2025 prüfen – Einspruchsfrist 26.11.2026 – mit Steuerberater abstimmen“ |
   | Rechtsbehelf (Mahnbescheid, Bußgeld, Bescheid) | „Widerspruch/Einspruch prüfen – mit Anwalt/Steuerberater abstimmen“ + `[PRÜFEN: Zustelldatum]`, wo zutreffend. **Nie** ein bereits entschiedener Widerspruch oder Einspruch |
   | § 14-Befund | „Rechnung <Nr> unvollständig – korrigierte Rechnung bei <Absender> anfordern (fehlt: …)“ + Entwurf |
   | IBAN-Wechsel | P1 „IBAN-Änderung bei <Lieferant> telefonisch über bekannte Nummer bestätigen, bevor gezahlt wird“; die Zahlungsaufgabe verweist darauf |
   | E-Rechnungsfehler | „Korrigierte E-Rechnung bei <Absender> anfordern (<Fehlerklasse>)“ + Entwurf |
   | Rekonstruktion im Review | „Rekonstruktion prüfen (Dokument #<id>)“ |
   | Bewirtungsbeleg | „Bewirtungsbeleg ergänzen: Anlass, Teilnehmer, Unterschrift“ |

   - Ein Dokument kann mehrere Aufgaben erzeugen (z. B. zahlen + prüfen).
   - Nie dieselbe Aufgabe doppelt anlegen: gleiches Dokument, gleicher Titel, gleiche Frist.
   - Jede Aufgabenänderung kommt in `protokoll.csv`.
4. **Antwortentwurf** — bei `reply`, bei `provide_documents`, bei § 14- und E-Rechnungsbefunden und immer, wenn der
   Brief erkennbar eine Antwort erwartet:
   - `shared/templates/antwortbrief.md` verwenden (DIN 5008 Form B, `12_deutschland.md` §10), Deutsch, förmliches
     *Sie*. Kurz, höflich, sachlich. Ihr Zeichen/Aktenzeichen und Briefdatum angeben.
   - Nur Bekanntes eintragen. Jede Annahme und jeden offenen Punkt als `[PRÜFEN: …]` markieren. Die Pflichtangaben des
     Unternehmens nie erfinden.
   - Bei `provide_documents`: die Anlagen mit ihrem Ort im Baum auflisten.
   - Keine verbindlichen rechtlichen oder steuerlichen Aussagen, keine Schuldanerkenntnisse, keine Zahlungszusagen, die
     der Nutzer nicht gegeben hat. Bei echtem Risiko „mit Steuerberater/Anwalt abstimmen“ ergänzen
     (`09_sicherheit.md`).
   - Unter `_office/drafts/<ID>_<Absender>.md` speichern und mit Status `review` registrieren.
5. **Bericht** in einer Zeile: angelegte Aufgaben (IDs), Pfad des Entwurfs, Entscheidungen, die der Mensch treffen muss.

## Prüfung durch den Menschen
Im Prüf-Workflow sieht der Nutzer jeden Entwurf mit seinen `[PRÜFEN]`-Stellen und gibt ihn frei, bearbeitet oder
verwirft ihn.
- Freigegebene Entwürfe werden als `approved` markiert, auf Wunsch als druckfertiges DIN-5008-PDF erzeugt und **vom
  Menschen versendet**.
- Nach dem Versand schließt der Nutzer (oder auf Wunsch der Agent) die Antwortaufgabe und legt eine Kopie des
  versendeten Briefs ab (Handelsbrief, 6 Jahre).

Das System versendet nie.
