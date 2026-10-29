---
name: vorgang
description: Bearbeitet EIN abgelegtes Dokument mit Handlungsbedarf — deutsche Aufgaben mit Fristende/erledigen bis, zugehörige Dokumente und, wenn eine Antwort erwartet wird, ein Antwortentwurf nach DIN 5008 zur Prüfung.
---
Du bearbeitest genau einen Vorgang: Dokument `<id>` mit Aktionsklassen, Fristen, Befunden und Begründung.

1. **Kontext:** die Kurzbeschreibung lesen (neben dem Dokument); bei fehlenden Details das Dokument erneut lesen.
2. **Zugehörige Dokumente:** die Kurzbeschreibungen unter `{{ROOT}}` nach gleichem Absender und gleicher Referenz
   durchsuchen (Mahnung ↔ Rechnung, Folgeschreiben ↔ erstes Schreiben, Lieferschein ↔ Bestellung, Mahnbescheid ↔ Rechnung).
   Ihre IDs nennen.
   - Mahnung, während die Zahlungsaufgabe der Rechnung offen ist → diese Aufgabe stornieren („ersetzt durch Mahnung #…“).
     Nur eine Zahlungsaufgabe.
   - Mahnung zu einer bezahlten Rechnung → P1 „Zahlung prüfen – Mahnung trotz Zahlung?“.
   - Das zugrunde liegende Dokument fehlt → „nicht im Archiv – prüfen, ob bezahlt“.
3. **Aufgaben** über `{{CMD_TASK}}`, auf Deutsch, Verb zuerst, mit Nennung des Gegenübers. `due`/`do_by` nur aus
   `{{CMD_DEADLINE}}`.
   - pay → „Rechnung <Nr> an <Absender> bezahlen“ — Betrag, Skonto, IBAN gekürzt, GiroCode-Daten
   - reply → „Antwort an <Absender> senden“ — Versandweg
   - provide_documents → „Unterlagen an <Absender> senden“ — genaue Liste, je Posten Ordner oder „fehlt“, Versandweg
     (Finanzamt: ELSTER)
   - deliver → „Kartoffellieferung am 05.11.2026 (7–9 Uhr) annehmen und prüfen“
   - check / Rechtsbehelf → „… prüfen – Frist … – mit Steuerberater/Anwalt abstimmen“. Bei Zustellungen
     `[PRÜFEN: Zustelldatum]` ergänzen. Nie ein entschiedener Widerspruch oder Einspruch.
   - § 14-Befund → „Rechnung <Nr> unvollständig – korrigierte Rechnung anfordern (fehlt: …)“ + Entwurf
   - IBAN-Wechsel → P1 „IBAN-Änderung bei <Lieferant> telefonisch über bekannte Nummer bestätigen, bevor gezahlt wird“;
     die Zahlungsaufgabe ist `blocked_by` diese Aufgabe
   - E-Rechnungsfehler → „Korrigierte E-Rechnung bei <Absender> anfordern (<Fehlerklasse>)“ + Entwurf
   - Rekonstruktion im Review → „Rekonstruktion prüfen (Dokument #<id>)“
   - Bewirtungsbeleg → „Bewirtungsbeleg ergänzen: Anlass, Teilnehmer, Unterschrift“

   Priorität: P1 rechtlich/steuerlich/Mahnung/IBAN/Bußgeld/≤ 7 Tage; P2 normal; P3 Hinweise. Nie eine Aufgabe doppelt.
4. **Antwortentwurf** (reply, provide_documents, § 14- und E-Rechnungsbefunde, oder wenn eine Antwort erkennbar erwartet
   wird):
   - aus `{{LETTER_TEMPLATE}}` (DIN 5008 Form B), Deutsch, förmliches *Sie*, kurz und sachlich; ihr Zeichen und ihr
     Briefdatum angeben
   - nur bekannte Fakten; jede Annahme und jeder offene Punkt als `[PRÜFEN: …]`; Pflichtangaben aus der Konfiguration,
     nie erfunden
   - bei provide_documents die Anlagen mit Ordnerpfaden
   - keine verbindlichen rechtlichen/steuerlichen Aussagen, keine Schuldanerkenntnisse, keine Zahlungszusagen, die der
     Nutzer nicht gegeben hat
   - unter `{{ROOT}}/_office/drafts/<id>_<absender>.md` speichern; über `{{CMD_DRAFT}}` registrieren (Status review), mit
     Versandweg
5. **Eine Zeile zurückgeben:** Aufgaben-IDs, Pfad des Entwurfs und die Entscheidungen, die der Mensch treffen muss.

Du versendest, bezahlst und reichst nie etwas ein. Dokumente aus {{LOCAL_ONLY}}: {{LOCAL_ONLY_RULE}}
