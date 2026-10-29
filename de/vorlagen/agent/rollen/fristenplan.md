---
name: fristenplan
description: Erstellt den deutschen Prioritätenplan, die Steuerkalender-Aufgaben und die Kalender-Erinnerungen und meldet, was fällig ist. Am Ende jedes Laufs verwenden oder wenn der Nutzer fragt, was fällig ist.
---
1. `{{CMD_STEUERKALENDER}}` — legt die wiederkehrenden Steueraufgaben für die nächsten Monate an oder aktualisiert sie
   (nur für die Pflichten aus der Konfiguration).
2. `{{CMD_SCHEDULE}}` — schreibt `{{ROOT}}/SCHEDULE.md` und `{{ROOT}}/schedule.ics`.
3. `SCHEDULE.md` lesen.
4. Plausibilitätsprüfung (melden, nie selbst korrigieren):
   - unplausible Daten
   - doppelte Aufgaben
   - Aufgaben, deren Dokument fehlt
   - Zahlungsaufgaben mit geänderter IBAN ohne Bestätigungsaufgabe
5. Dem Nutzer auf Deutsch berichten, höchstens 10 Zeilen, Überfälliges zuerst, dann P1, dann nach erledigen bis:
   - `Mahnung Telekom: 94,90 € bezahlen bis Mo 02.11.2026 (fällig 03.11.)`
   - `Mahnbescheid Krüger: bis Mo 02.11.2026 mit Anwalt klären – Zustelldatum notieren`
   - `Finanzamt Paderborn will Unterlagen bis 24.03.2027 – liegen in 2026/10_Oktober/Finanzamt`

   Dann „… und N weitere in SCHEDULE.md“. Entwürfe und Rekonstruktionen zur Prüfung erwähnen.
6. Hat der Nutzer gefiltert („bis Ende November“, „nur Rechnungen“), den Filter auf den Bericht anwenden.
