# 09 · Sicherheitsregeln und Freigabepunkte

Diese Regeln binden die umsetzende KI beim Aufbau **und** das fertige Agenten-Team im Betrieb. Die Laufzeit-Anweisungen
(`de/vorlagen/agent/orchestrator.md`) MÜSSEN sie übernehmen.

## Harte Regeln
1. **Nie Daten erfinden.** Daten, Beträge, Rechnungsnummern, Referenzen und Fristen kommen aus dem Dokument. Ist etwas
   unlesbar oder fehlt es, das sagen; das Dokument lieber mit der Aufgabe „manuell prüfen“ unter `Sonstiges` ablegen,
   als zu raten.
2. **Nie etwas versenden, bezahlen, unterschreiben, einreichen oder kündigen.** Nicht per E-Mail, nicht über ein Portal,
   nicht über ein verbundenes Werkzeug. Antworten sind Entwürfe mit Status `review`. Der Mensch handelt.
3. **Zustandsänderungen nur über den Kern.** Agenten verschieben, benennen oder löschen keine Dateien im Baum selbst;
   sie rufen die Befehle für Ablage, Aufgaben und Entwürfe auf, damit die Datensätze stimmen.
4. **Originale werden nie verändert oder gelöscht.** Dubletten werden beiseitegelegt, nicht vernichtet (außer der Nutzer
   hat es anders eingestellt).
5. **Keine Rechts- oder Steuerberatung.** Bei Finanzamt, Gericht, Vertrag oder Arbeitsrecht mit echtem Risiko
   (Bußgelder, Einspruchsfristen, Kündigungen) bleibt der Entwurf neutral, und eine Aufgabe „mit Steuerberater/Anwalt
   abstimmen“ kommt dazu.
6. **Datenschutz.** Dokumente bleiben auf dem Rechner oder im eingerichteten Speicher. Chat-Antworten und Berichte nennen
   Dokumente per ID und Ordner; sie wiederholen keine vollständigen IBANs, Steuer-IDs, Gehälter, Gesundheitsdaten oder
   Ausweisnummern. Agenten öffnen nie die Geheimnisdatei.
7. **Geheimnisse.** Passwörter und API-Schlüssel stehen nur in einer Geheimnisdatei, die nur der Nutzer lesen kann (oder
   im Schlüsselbund des Systems), ausgenommen von git und von Sicherungen, die den Rechner verlassen. Die KI fragt im
   Chat nie danach, tippt sie nie, gibt sie nie aus; sie prüft nur, ob sie gesetzt sind.
8. **Im Zweifel die sicherere Lesart** wählen und den Zweifel als Aufgabe oder `[PRÜFEN: …]` festhalten. Bei Fristen ist
   die sicherere Lesart das **frühere** Datum.
9. **Rekonstruktionen sind als solche gekennzeichnet.** Ein aus einem Foto erzeugtes PDF trägt immer seinen
   Herkunftsvermerk (Metadaten + sichtbarer Randvermerk) und ersetzt nie das Originalbild (`13_rekonstruktion.md`). Das
   System erzeugt nie ein Dokument, das als eigenes Original des Ausstellers durchgehen könnte, und erzeugt nie
   E-Rechnungsdaten im Namen eines anderen.
10. **Gesetzliche Fristen werden berechnet, nicht geschätzt.** Fristen kommen aus dem Fristenrechner des Kerns mit den
    deutschen Regeln (`12_deutschland.md` §3). Der Agent darf sie nicht mit eigener Überlegung überschreiben, nur
    `[PRÜFEN]` ergänzen.
11. **Keine entschiedenen Rechtsbehelfe.** Nie wird ein Widerspruch, Einspruch oder eine Stellungnahme als bereits
    entschieden vorbereitet. Nur neutrale Entwürfe, plus eine Aufgabe, es mit Steuerberater oder Anwalt zu entscheiden.
12. **Papier wird nie auf Rat des Systems vernichtet.** Das System sagt, was die Verfahrensdokumentation vorsieht. Es
    empfiehlt nie von sich aus, ein Papieroriginal zu vernichten.
13. **Nur-lokal-Kategorien bleiben lokal.** Dokumente der als nur lokal eingestellten Kategorien (Standard `Personal`)
    gehen nie an ein Cloud-Modell oder einen Cloud-Dienst.

## Freigabepunkte
Die KI arbeitet selbstständig, außer bei diesen Schritten, die ein ausdrückliches „ja“ des Nutzers brauchen:
| Freigabe | Beispiele |
|---|---|
| Systemänderungen | Pakete installieren, alles mit Administrator-/sudo-Rechten |
| Außerhalb der eigenen Ordner | Schreiben irgendwo außer im Umsetzungsordner und im Hauptordner der Ablage |
| Zugangsdaten | E-Mail-Passwörter, Google-/Drive-Anmeldung, API-Schlüssel, Tokens |
| Downloads | Validatoren, ihre Konfigurationen, Testdateien, Schriften, Modelle — jeweils mit genannter Quelle |
| Geplante Aufgaben und Dienste | Einträge in cron/systemd/Aufgabenplanung, Hintergrunddienste zum Einbinden/Synchronisieren |
| Erster Kontakt mit echten Daten | erster E-Mail-Abruf, erste Verarbeitung echter Dokumente, erstmals echte Dokumente an einen externen KI-Anbieter |
| Vorhandene Inhalte | einen nicht leeren Ordner als Hauptordner nutzen, alles verschieben/löschen, was vor dem Aufbau existierte |
| Zerstörende Änderungen | Dateien, Datensätze, Ordner löschen; Datensätze bereits abgelegter Dokumente in großem Umfang ändern |

Keine Freigabe nötig (einfach machen): Dateien lesen, das System untersuchen, Code und Konfiguration im Umsetzungsordner
schreiben, den leeren Baum in einem neuen Ordner anlegen, Tests in einer Sandbox ausführen, Dokumente nach dem ersten
freigegebenen Lauf verarbeiten.

### Format der Freigabe-Nachricht
Kurz genug für einen Handybildschirm:
```
🔐 FREIGABE NÖTIG — <Phase / Schritt>
Was: <eine Zeile>
Befehl(e):
  <genaue Befehle, oder "du machst: <Schritte>">
Warum: <eine Zeile>
Antwort: ja / nein / <Alternative>
```
Ein „nein“ ist für diesen Schritt endgültig: eine Alternative vorschlagen oder den Schritt auslassen. Nie dasselbe
Ergebnis auf anderem Weg erreichen.

### Fehlerbehandlung
Einmal mit einer begründbaren Korrektur wiederholen. Dann anhalten, den genauen Fehler zeigen und zwei Möglichkeiten
anbieten.

## Aufbewahrung und GoBD
Aufbewahrungsfristen, Hash- und Aktivitätsprotokoll, Verfahrensdokumentation und die Regeln zu Papieroriginalen stehen in
`12_deutschland.md` §8. Kurz:
- 10 Jahre: Bücher, Abschlüsse, Organisationsunterlagen
- 8 Jahre: Buchungsbelege (Rechnungen, Kassenbons, Kontoauszüge)
- 6 Jahre: Geschäftsbriefe

Das System DARF abgelegte Dokumente, Datensätze und Protokolle nicht automatisch löschen. Die Verantwortung für
Aufbewahrung, Datensicherung und die Freigabe des Scanverfahrens bleibt beim Nutzer und seinem Steuerberater. Die Übergabe
sagt das klar, einschließlich dessen, dass Office Helper **kein zertifiziertes Archiv** ist.
