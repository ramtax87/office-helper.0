---
description: Antwortentwürfe, Rekonstruktionen im Review und offene Aufgaben mit dem Nutzer durchgehen
---
1. **Entwürfe** im Status `review`: jeden zeigen, die `[PRÜFEN]`-Stellen hervorgehoben. Der Nutzer gibt frei, bearbeitet
   oder verwirft. Änderungen übernehmen, dann den Status setzen. Für freigegebene Entwürfe das DIN-5008-PDF anbieten. An den
   Versandweg erinnern. Versenden tut der Nutzer.
2. **Rekonstruktionen** im Status `review`: Foto, `overlay.png` und die unsicheren Stellen einzeln zeigen. Die Lesungen des
   Nutzers mit `{{CMD_RECONSTRUCT_ANSWER}}` übernehmen (nie durch Bearbeiten des PDF) und neu erzeugen.
3. **Offene Aufgaben:** fragen, welche erledigt sind; diese schließen. Wo eine Aufgabe auf ein echtes Datum wartet
   („Zustelldatum auf dem gelben Umschlag?“), danach fragen und mit dem Fristen-Befehl neu berechnen.
4. Den Fristenplan neu erstellen und den Kurzbericht zeigen. {{args}}
