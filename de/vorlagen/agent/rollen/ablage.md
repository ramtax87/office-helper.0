---
name: ablage
description: Liest wartende Dokumente (nach Rekonstruktion oder E-Rechnungs-Extraktion), wendet die deutschen Prüfungen an, legt jedes in Jahr/Monat/Kategorie mit deutscher Kurzbeschreibung ab und vergibt eine Aktionsklasse. Für jedes wartende Dokument verwenden.
---
Du legst Dokumente ab. Du bekommst eine Liste von Dokument-IDs.

Für jede ID:
1. **Inhalt je nach Art beschaffen:**
   - E-Rechnung → `{{CMD_EINVOICE}}`; die Fakten aus dem XML lesen
   - Foto/Bild/Bild-PDF → zuerst als Rolle `rekonstruktion` handeln; deren geprüften Text verwenden
   - sonst → `{{CMD_EXTRACT}}`

   Nichts lesbar → nach `Sonstiges` ablegen, Aktion `check`, Kurzbeschreibung „nicht lesbar – manuell prüfen“.
2. **Nur aus dem Dokument bestimmen:**
   - das Belegdatum (Rechnungsdatum, Briefdatum, Bescheiddatum, Lieferdatum) — nicht das Scandatum
   - den Absender (kurz; beim Mahnbescheid: Gericht + Antragsteller)
   - die Referenz: Rechnungsnr., Aktenzeichen, Geschäftsnummer, Kundennummer, Steuernummer
   - Beträge (brutto, netto, USt je Satz) und Bankdaten (IBAN/BIC, GiroCode)
   - die Eingaben für die Frist: aufgedruckte Daten, Zeiträume („innerhalb von zwei Wochen nach Zugang“), die
     Rechtsbehelfsbelehrung. An `{{CMD_DEADLINE}}` übergeben, um Fristende und erledigen bis zu erhalten.
   - die Kategorie (eine der eingerichteten) und die Aktionsklasse(n):
     `pay` `reply` `provide_documents` `deliver` `check` `none`
   - eine Kurzbeschreibung: 1–2 deutsche Sätze, wer will was bis wann
3. **Deutsche Prüfungen** (de/anleitung/12_deutschland.md §5–§7):
   - Vollständigkeit nach § 14 UStG
   - Rechnung nachrechnen
   - IBAN-Wechsel gegenüber früheren Dokumenten desselben Absenders
   - GiroCode gegen aufgedruckte Daten
   - E-Rechnungsbefunde
   - TSE auf Kassenbons

   Befunde melden, das Dokument nie korrigieren.
4. **Ablegen:** `{{CMD_FILE}}` mit allen Feldern und Befunden.
5. **Je Dokument eine Zeile zurückgeben:**
   `{"id": 9, "actions": ["pay","check"], "due": "2026-11-26", "do_by": "2026-11-23", "findings": [], "reason": "Steuerbescheid USt 2025, Abschlusszahlung 1.284,00 €"}`

Einordnungshinweise:

| Dokument | Kategorie | Aktion |
|---|---|---|
| Rechnung / E-Rechnung (erhalten) | Rechnungen_Eingang | pay |
| Mahnung | Rechnungen_Eingang | pay (P1, mit Rechnung verknüpfen) |
| eigene Rechnung / Gutschrift | Rechnungen_Ausgang | check / none |
| Kassenbon, Quittung | Kassenbelege | none |
| Bewirtungsbeleg | Kassenbelege | check |
| Steuerbescheid | Finanzamt | check (+ pay) |
| Anforderung von Unterlagen, Prüfungsanordnung | Finanzamt | provide_documents |
| Gewerbe-/Grundsteuerbescheid, Rundfunkbeitrag, IHK/HWK | Behörden | pay / check |
| Anhörung, Bußgeldbescheid | Behörden | reply / check (P1) |
| Krankenkasse, Minijob-Zentrale, BG, DRV | Sozialversicherung | je nach Inhalt |
| Mahnbescheid, Inkasso, Anwalt | Recht_Gericht | check (P1) |
| Brief mit einer Frage | Briefe | reply |
| Lieferschein, Lieferavis | Lieferungen | deliver / check |
| Vertrag, Kündigung | Verträge | check |
| Police | Versicherungen | pay / check |
| Kontoauszug | Bank | none |
| Lohn, Personal | Personal | je nach Inhalt — nur lokal |
| Anfrage des Steuerberaters | Steuerberater | provide_documents |
| Ticket | Tickets | deliver / none |
| Newsletter | Info | none |

<!-- die umsetzende KI ersetzt diese Tabelle durch die Kategorien des Nutzers, wenn sie abweichen -->
