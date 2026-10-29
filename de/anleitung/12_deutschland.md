# 12 · Deutschland — die Regeln, nach denen das System gebaut wird

Office Helper ist für deutsche Unternehmen gebaut: GmbH, UG, e.K., GbR, Einzelunternehmen, Freiberufler, kleine
Gastronomie- und Handwerksbetriebe. Dieses Kapitel ist für den Aufbau verbindlich.

> Rechtsstand recherchiert im Oktober 2026. Die umsetzende KI MUSS die mit ⟳ markierten Punkte beim Aufbau gegen
> aktuelle offizielle Quellen prüfen und jede Änderung im Bauprotokoll vermerken. Das System leistet keine Rechts- oder
> Steuerberatung. Wo „Steuerberater/Anwalt“ steht, entscheidet der Mensch mit ihnen.

## 1. Sprache und Formate
- **Sprache des Nutzers:** Deutsch oder Englisch, ganz zu Beginn gewählt (`AGENTS.md` im Hauptverzeichnis), als
  `language` in der Konfiguration gespeichert. Berichte, Fristenplan, Aufgabentitel, Kurzbeschreibungen, Prüftexte und
  das Interview verwenden diese Sprache.
  - Bei Englisch bleiben deutsche Fachbegriffe stehen, wo sie wichtig sind, mit kurzer englischer Erklärung:
    „objection period (Einspruchsfrist)“, „court payment order (Mahnbescheid)“, „tax office (Finanzamt)“.
- **Immer Deutsch**, unabhängig von der Sprache des Nutzers:
  - Briefe und Antwortentwürfe an deutsche Empfänger (förmliches *Sie*)
  - die Verfahrensdokumentation (sie wird dem Betriebsprüfer vorgelegt)
  - das Steuerberater-Paket und seine CSV-Spaltennamen
  - standardmäßig die Ordner- und Kategorienamen (der Nutzer kann im Interview englische Namen wählen; Steuerberater und
    Betriebsprüfer erwarten Deutsch)
- Datum `TT.MM.JJJJ` (auch für englische Nutzer, passend zu den Dokumenten), Wochentage in der Sprache des Nutzers.
  Beträge `1.234,56 €`. Prozent `19 %`.
- Monatsordner `01_Januar … 12_Dezember`. Dateinamen dürfen Umlaute enthalten, wenn der Speicher sie erhält (Test in
  Phase 4), sonst `ae/oe/ue/ss`.

## 2. Zusätzliche Interviewfragen (Bauphase 2)
```
Deutschland-Einstellungen — bitte beantworten oder "Standard":
a) Bundesland (für Feiertage)? [Nordrhein-Westfalen]
b) Rechtsform? (GmbH, UG, e.K., GbR, Einzelunternehmen, Freiberufler) [GmbH]
c) Briefkopf-Pflichtangaben: Firma, Anschrift, Registergericht + HRB, Geschäftsführer, USt-IdNr./Steuernummer
d) Umsatzsteuer-Voranmeldung: monatlich / vierteljährlich / keine (Kleinunternehmer § 19 UStG)? Dauerfristverlängerung ja/nein?
e) Angestellte / Minijobber (Lohnsteuer, Sozialversicherung)? [nein]
f) Gewerbesteuer-/Grundsteuer-Vorauszahlungen? Einkommen-/Körperschaftsteuer-Vorauszahlungen? [ja/ja]
g) Steuerberater: Name, und wie bekommt er die Belege? (DATEV Unternehmen online, DATEV Upload-Mail, E-Mail, Papier, anderes Programm) [E-Mail]
h) Branche? (z. B. Gastronomie, Handwerk, Handel) — schärft die Absendererkennung
i) Sollen Dokumente der Kategorie "Personal" nur lokal verarbeitet werden (kein Cloud-Modell)? [ja]
```

## 3. Fristen — wie sie berechnet werden
Als getestete Funktionen des Kerns umsetzen (Abnahmetests G1–G6), nicht als freie KI-Überlegung.

**Feiertage:** je Bundesland berechnen — feste Daten, ostergebundene Daten und landesspezifische (z. B. NRW:
Fronleichnam und Allerheiligen; BY: Heilige Drei Könige, Mariä Himmelfahrt in Teilen; BE: Frauentag; …). ⟳ Die Liste
des Landes prüfen.

**Allgemeine Regeln:**
- **Frist ab einem Ereignis** („innerhalb von zwei Wochen nach Zugang“): Der Tag des Ereignisses zählt nicht mit (§ 187
  Abs. 1 BGB). Die Frist endet mit Ablauf des Tages mit gleichem Wochentag bzw. gleichem Datum (§ 188 BGB). Endet eine
  Monatsfrist an einem Tag, den es nicht gibt, endet sie am letzten Tag dieses Monats.
- **Ende an Samstag, Sonntag oder Feiertag:** verschiebt sich auf den nächsten Werktag: § 193 BGB (Zivilrecht), § 108
  Abs. 3 AO (Steuern), § 222 Abs. 2 ZPO (Gerichte), § 31 VwVfG (Behörden).
- **Bekanntgabefiktion (4 Tage, seit 1.1.2025):** Ein per Post oder elektronisch versandter Steuerbescheid oder
  Verwaltungsakt gilt am **vierten Tag** nach Aufgabe als bekannt gegeben (§ 122 Abs. 2 / 2a AO; § 41 VwVfG und
  entsprechende Regeln). Fällt dieser Tag auf ein Wochenende oder einen Feiertag, gilt der nächste Werktag. ⟳
  Als Aufgabedatum gilt das Datum auf dem Bescheid, sofern der Umschlag nichts anderes zeigt. Ist der Bescheid
  tatsächlich später angekommen, weiß es der Mensch besser: `[PRÜFEN: Zugangsdatum]` ergänzen.
- **Förmliche Zustellung (gelber Umschlag):** Die Frist läuft ab dem Datum, das der Zusteller auf den Umschlag
  geschrieben hat. Das System sieht es nicht. Die Aufgabe bittet den Nutzer, das Datum zu notieren. Bis dahin: Frist =
  Dokumentdatum + Zeitraum (das frühere, sichere Datum).
- **Unbekannter Beginn** („nach Zugang“ ohne Eingangsdatum): das Dokumentdatum als Beginn nehmen — früher = sicherer.
  `[PRÜFEN]` vermerken.
- **Zahlungsdaten auf Rechnungen:** das aufgedruckte Datum behalten, auch wenn es ein Sonntag ist. Früher zahlen ist
  sicher. Ohne Zahlungsziel auf der Rechnung das eingestellte Standardziel (14 Tage) nehmen und „kein Zahlungsziel
  angegeben“ vermerken.
- **Rechtsbehelfsbelehrung:** Enthält ein Bescheid eine, geht die dort genannte Frist jedem Standardwert dieser Tabelle
  vor.

**Gesetzliches Fristende und „erledigen bis“:** Bei allem, was jemanden *erreichen* muss (Einspruch, Widerspruch,
Stellungnahme, Unterlagen ans Finanzamt), zeigt die Aufgabe beides:
- `Fristende` — das gesetzliche Ende der Frist
- `Erledigen bis` — das gesetzliche Ende minus Puffer für die Zustellung, Standard 3 Arbeitstage. Einstellbar.

Der Fristenplan sortiert nach `Erledigen bis`.

**Typische deutsche Fristen:**

| Dokument | Zeitraum | Ab | Priorität |
|---|---|---|---|
| Steuerbescheid, Haftungsbescheid → Einspruch (§ 355 AO) | 1 Monat | Bekanntgabe (4-Tage-Regel) | P1 |
| Bescheid einer Behörde (Gewerbesteuer, Grundsteuer, Rundfunkbeitrag, IHK/HWK, BG) → Widerspruch oder Klage | laut Rechtsbehelfsbelehrung, meist 1 Monat | Bekanntgabe | P1 |
| Gerichtlicher Mahnbescheid → Widerspruch (§ 692 ZPO) | 2 Wochen | Zustellung (gelber Umschlag) | P1 |
| Vollstreckungsbescheid → Einspruch (§§ 700, 339 ZPO) | 2 Wochen | Zustellung | P1 |
| Bußgeldbescheid → Einspruch (§ 67 OWiG) | 2 Wochen | Zustellung | P1 |
| Anhörung (Ordnungsamt, Polizei, Behörde) | laut Schreiben | laut Schreiben | P1 |
| Abmahnung (Anwalt) | laut Schreiben, oft sehr kurz | laut Schreiben | P1 + Anwalt |
| Anforderung von Unterlagen (Finanzamt) | laut Schreiben | — | P1 |
| Mahnung | laut Schreiben | — | P1 |
| Rechnung | aufgedrucktes Zahlungsziel / Skontodatum | Rechnungsdatum | P2 |
| Vertrag: Kündigungsfrist | laut Vertrag | — | Aufgabe 4 Wochen vor dem letzten Kündigungstag, P2 |

Nie über einen Rechtsbehelf entscheiden. Der Vorgangsschritt ergänzt bei allen rechtlichen P1-Zeilen oben „mit
Steuerberater/Anwalt abstimmen“ und entwirft nie einen förmlichen Widerspruch oder Einspruch als bereits entschieden.
Einen neutralen Text in der Art „Einspruch zur Fristwahrung“ entwirft er nur, wenn der Nutzer darum bittet.

## 4. Steuerkalender — wiederkehrende Aufgaben (optional, aus Interview d–f)
Der Fristenplan legt wiederkehrende Aufgaben für die Pflichten an, die der Nutzer bestätigt hat:

| Pflicht | Fällig | Hinweise |
|---|---|---|
| Umsatzsteuer-Voranmeldung + Zahlung | 10. des Folgemonats (monatlich) / des Folgequartals (vierteljährlich) | mit Dauerfristverlängerung + 1 Monat; Sondervorauszahlung (1/11) bis 10.02. bei monatlicher Abgabe |
| Lohnsteuer-Anmeldung + Zahlung | 10. des Folgemonats (oder -quartals) | nur mit Beschäftigten |
| Einkommen-/Körperschaftsteuer-Vorauszahlung | 10.03., 10.06., 10.09., 10.12. | Beträge aus dem letzten Vorauszahlungsbescheid |
| Gewerbesteuer-/Grundsteuer-Vorauszahlung | 15.02., 15.05., 15.08., 15.11. | Beträge aus dem Bescheid der Stadt |
| Sozialversicherungsbeiträge | drittletzter **Bankarbeitstag** des Monats | 24.12. und 31.12. gelten nicht als Bankarbeitstage; der Beitragsnachweis muss früher vorliegen ⟳ |
| Jahreserklärungen | beim Steuerberater erfragen | nicht berechnen; die Fristen unterscheiden sich mit und ohne Berater |

- Jedes Datum nach der Wochenend-/Feiertagsregel verschieben.
- Die 3-tägige *Schonfrist* bei Überweisungen wird nie zur Planung genutzt; Aufgaben sind am tatsächlichen Datum fällig.
- Jeder Aufgabentext nennt den Betrag, wenn ein Bescheid im Baum ihn enthält („laut Vorauszahlungsbescheid vom …“),
  sonst „Betrag laut Bescheid prüfen“.
- ⟳ In den Abnahmetests die erzeugten Daten für das laufende und das nächste Jahr mit einem offiziellen
  Steuerterminkalender (IHK oder Finanzverwaltung) vergleichen.

## 5. E-Rechnung
**Stand ⟳:**
- Seit **1.1.2025** muss jedes inländische Unternehmen E-Rechnungen **empfangen** können.
- Ab **1.1.2027** müssen Unternehmen mit mehr als **800.000 €** Vorjahresumsatz im inländischen B2B-Geschäft
  E-Rechnungen **ausstellen**.
- Ab **1.1.2028** müssen alle sie ausstellen.
- Von der Ausstellungspflicht ausgenommen: Kleinbetragsrechnungen ≤ 250 €, Kleinunternehmer, Fahrausweise.
- Ein PDF, ein Scan oder ein Foto ist **keine** E-Rechnung, sondern eine „sonstige Rechnung“.
- Gültige Formate entsprechen der EN 16931: **XRechnung** (UBL oder CII) und **ZUGFeRD/Factur-X** ab Profil EN 16931.
  Die Profile MINIMUM und BASIC-WL zählen nicht.
- Bei hybriden Formaten (ZUGFeRD: PDF + eingebettetes XML) ist **das XML rechtlich führend**.

**Was das System tun MUSS:**
1. **Erkennen:**
   - XML-Anhänge (UBL `Invoice`/`CreditNote`, CII `CrossIndustryInvoice`)
   - PDFs mit eingebettetem `factur-x.xml`, `zugferd-invoice.xml` oder `xrechnung.xml`
2. **Das XML als Original aufbewahren**, bytegleich, mit Hashwert. Bei Hybriden das ganze PDF und zusätzlich das
   extrahierte XML aufbewahren.
3. **Prüfen** mit dem offiziellen KoSIT-Validator und der aktuellen XRechnung-Konfiguration (XRechnung) oder einem
   ZUGFeRD-Validator (z. B. Mustang). Den Prüfbericht neben der Rechnung ablegen. ⟳ aktuelle Versionen.
4. **Lesbar machen:** eine lesbare PDF-/HTML-Ansicht erzeugen (z. B. mit der XRechnung-Visualisierung der KoSIT). Als
   Ansicht kennzeichnen (`…_Ansicht.pdf`). Die Fakten aus dem **XML** lesen, nicht aus dem Bild.
5. **Fakten aus dem XML:** Rechnungsnummer, Datum, Verkäufer, IBAN, Fälligkeit, Summen je Steuersatz, Leitweg-ID falls
   vorhanden.
6. **Probleme einordnen** wie das BMF: Formatfehler (keine E-Rechnung), Geschäftsregelfehler und inhaltliche Fehler.
   Jeder wird zu einer Aufgabe wie „Korrigierte E-Rechnung bei <Absender> anfordern“, mit Entwurf.
7. **Papier- oder PDF-Rechnungen ab 2027:** Bei inländischen B2B-Rechnungen > 250 € von einem Unternehmen, das kein
   Kleinunternehmer ist, den Hinweis „Prüfen: Lieferant müsste ggf. E-Rechnung senden“ ergänzen (P3 in 2027, P2 ab
   2028). Die Pflicht hängt vom Umsatz des Lieferanten ab, den das System nicht kennen kann — also ein Hinweis, kein
   Vorwurf.

**Quellen für Testdateien:** die offizielle XRechnung-Testsammlung der KoSIT (`itplr-kosit/xrechnung-testsuite` auf
GitHub) und die ZUGFeRD/Factur-X-Beispiele des FeRD oder des Mustang-Projekts. Gültige und ungültige Instanzen in Bauphase
3 laden (Netzzugriff auf GitHub, Teil dieser Freigabe) und in Phase 7 verwenden. Keine E-Rechnungen für die Tests selbst
schreiben.

## 6. Rechnungsprüfung (§ 14 UStG) — für den Vorsteuerabzug
Bei jeder Eingangsrechnung prüft die Ablage-Rolle die Pflichtangaben:
- vollständiger Name und Anschrift von leistendem Unternehmer und Leistungsempfänger
- Steuernummer oder USt-IdNr. des leistenden Unternehmers
- Rechnungsdatum
- eine einmalige Rechnungsnummer
- Menge und Bezeichnung der Lieferung oder Leistung
- Zeitpunkt der Lieferung oder Leistung (Leistungszeitpunkt)
- Entgelt je Steuersatz
- Steuersatz und Steuerbetrag, oder der Grund der Befreiung (z. B. § 19 UStG, Reverse Charge „Steuerschuldnerschaft des
  Leistungsempfängers“)

Kleinbetragsrechnung ≤ 250 € brutto (§ 33 UStDV): Name und Anschrift des leistenden Unternehmers, Datum, Bezeichnung,
Bruttobetrag und Steuersatz genügen.

Fehlende Angaben → Aufgabe „Rechnung unvollständig – korrigierte Rechnung anfordern (fehlt: …)“ mit Antwortentwurf, P2.
Rechenfehler (Netto + USt ≠ Brutto) → ebenso, mit den Zahlen.

## 7. Zahlungssicherheit
- **GiroCode (EPC-QR):** Trägt eine Rechnung einen, ihn lesen. Gegen aufgedruckte IBAN, Betrag und Verwendungszweck
  prüfen. Die gelesenen Daten in der Zahlungsaufgabe zeigen.
- **Warnung bei IBAN-Wechsel (Rechnungsbetrug):** Weicht die IBAN eines Lieferanten von der IBAN früher abgelegter
  Dokumente desselben Lieferanten ab, eine **P1**-Aufgabe anlegen: „IBAN-Änderung bei <Lieferant> telefonisch über
  bekannte Nummer bestätigen, bevor gezahlt wird“. Das neue Konto nur mit den letzten 4 Ziffern zeigen.
- IBANs in Aufgaben und Chat nur gekürzt zeigen (`DE89 … 3000`).

## 8. GoBD und Archivierung
Das System unterstützt die Einhaltung der GoBD; es ist **kein** zertifiziertes Archiv. Die Übergabe MUSS das sagen.
- **Unveränderbarkeit:**
  - Originale werden nie verändert.
  - Der SHA-256 jedes Originals kommt in ein fortlaufendes **Hash-Protokoll** (`_office/hashlog.csv`: Datum, ID, Datei,
    Hash).
  - Umlegen ändert den Pfad, nie den Inhalt, und wird protokolliert.
  - SOLL: abgelegte Originale schreibschützen (Dateirechte) und den Versionsverlauf des Cloud-Speichers aktivieren.
- **Nachvollziehbarkeit:** ein fortlaufendes **Aktivitätsprotokoll** (`_office/protokoll.csv`): jeder Eingang, jede
  Ablage, jedes Umlegen, jede Aufgabenänderung und jeder Statuswechsel eines Entwurfs, mit Zeitstempel, Akteur
  (Agent/Mensch) und altem → neuem Wert.
- **Zeitnahe Erfassung:** Papier soll bald nach Eingang gescannt werden. Der Statusbericht zeigt die älteste
  unbearbeitete Datei im Eingang. Warnung nach 10 Tagen.
- **Verfahrensdokumentation:** Der Aufbau MUSS `shared/templates/verfahrensdokumentation.md` für diese Installation
  ausfüllen: Hardware, Software, Speicher, Personen, Scan- und Fotoverfahren, Qualitätskontrolle, Rekonstruktionsschritt,
  Datensicherung, Zugriffsrechte. Bei der Übergabe aushändigen und im Hauptordner speichern
  (`_office/Verfahrensdokumentation.md`) für Steuerberater und Betriebsprüfung. Nach Konfigurationsänderungen neu
  erzeugen.
- **Ersetzendes Scannen (Papier nach dem Scannen vernichten):** für die meisten Dokumente erlaubt, *wenn* das Verfahren
  dokumentiert und geprüft ist. **Nie** bei: notariellen Urkunden, Eröffnungsbilanzen und Jahresabschlüssen,
  Zolldokumenten, Wechseln und Schecks, und Dokumenten mit Originalunterschriften, die als Beweis gebraucht werden. Das
  System sagt dem Nutzer NIE, Papier zu vernichten. Die Kurzbeschreibung jedes Dokuments zeigt „Papieroriginal:
  aufbewahren“ oder „Papieroriginal: laut Verfahrensdokumentation entbehrlich (mit Steuerberater abgestimmt?)“.
  Empfehlung: alles Papier aufbewahren, bis der Steuerberater das Verfahren freigegeben hat. Handyfotos sind nur als Teil
  des dokumentierten Verfahrens zulässig.
- **Aufbewahrungsfristen ⟳** (ab Ende des Kalenderjahres, in dem das Dokument entstanden ist bzw. eingegangen ist):

| Frist | Unterlagen |
|---|---|
| 10 Jahre | Handelsbücher/Journale, Inventare, Eröffnungsbilanzen, Jahresabschlüsse, Lageberichte, sowie die zu ihrem Verständnis nötigen Arbeitsanweisungen und Organisationsunterlagen (dazu gehört die Verfahrensdokumentation) |
| 8 Jahre | **Buchungsbelege**: Eingangs- und Ausgangsrechnungen, Quittungen, Kassenbons, Kontoauszüge (durch das BEG IV von 10 Jahren verkürzt für Fristen, die am 1.1.2025 noch nicht abgelaufen waren; Banken und Versicherer ein Jahr später) |
| beim Steuerberater erfragen | Lohnunterlagen (Lohnkonto, SV-Unterlagen): eigene Regeln ⟳ — Standard 8 Jahre und `[PRÜFEN]` vermerken |
| 6 Jahre | empfangene Handels- und Geschäftsbriefe und Kopien versandter, sonstige steuerlich bedeutsame Unterlagen |

- Eine Frist endet nicht, solange das Dokument für eine Steuerfestsetzung von Bedeutung ist, deren Festsetzungsfrist
  noch nicht abgelaufen ist (§ 147 Abs. 3 AO).
- Die Kurzbeschreibung jedes Dokuments zeigt `Aufbewahren bis: 31.12.JJJJ`.
- Einmal im Jahr listet das System die Dokumente nach Fristablauf, damit Nutzer und Steuerberater entscheiden. Nichts wird
  automatisch gelöscht.

## 9. Typische Absender in Deutschland (Einordnung)
| Absender | Standardkategorie | Hinweis |
|---|---|---|
| Finanzamt, Bundeszentralamt für Steuern | Finanzamt | Bescheide: Einspruchsfrist prüfen |
| Stadt/Gemeinde: Steueramt (Gewerbesteuer, Grundsteuer), Gewerbeamt, Ordnungsamt, Bauamt | Behörden | |
| Hauptzollamt, Zoll | Behörden | Zolldokumente: Papier aufbewahren |
| Lebensmittelüberwachung / Veterinäramt, Gesundheitsamt | Behörden | Kontrollen, Fristen zur Mängelbeseitigung |
| Schornsteinfeger (Feuerstättenbescheid) | Behörden | |
| ARD ZDF Deutschlandradio Beitragsservice | Behörden | Rundfunkbeitrag für Betriebsstätten und Fahrzeuge |
| Krankenkassen (AOK, TK, Barmer, IKK, …), Minijob-Zentrale, Deutsche Rentenversicherung (Betriebsprüfung) | Sozialversicherung | |
| Berufsgenossenschaft (Gastronomie: BGN; Bau: BG BAU), Unfallkasse | Sozialversicherung | Beitragsbescheid; digitaler Lohnnachweis bis zur Frist im Schreiben |
| Agentur für Arbeit | Sozialversicherung | |
| IHK, Handwerkskammer | Behörden | Beitragsbescheid |
| Amtsgericht (Mahnbescheid, Handelsregister), Gerichtsvollzieher, Inkasso, Rechtsanwalt | Recht_Gericht | P1 bei Frist; Aufgabe Anwalt |
| Bank, Sparkasse | Bank | |
| Versicherer | Versicherungen | Vertragslaufzeiten, Beitragsrechnungen |
| GEMA, Telekommunikation, Energie, Leasing | Verträge oder Rechnungen_Eingang je nach Inhalt | |
| Steuerberater | Steuerberater | Anforderung von Unterlagen → provide_documents |

**Standardkategorien (Deutschland):** `Rechnungen_Eingang`, `Rechnungen_Ausgang`, `Kassenbelege`, `Finanzamt`,
`Behörden`, `Sozialversicherung`, `Bank`, `Versicherungen`, `Verträge`, `Personal`, `Lieferungen`, `Recht_Gericht`,
`Steuerberater`, `Briefe`, `Tickets`, `Info`, `Sonstiges`.

## 10. Briefe (Antwortentwürfe)
- Nach DIN 5008:2020, standardmäßig **Form B**. Vorlage: `shared/templates/antwortbrief.md`.
  - Anschriftfeld 45 mm von der Oberkante, 20 mm vom linken Rand.
  - Rücksendeangabe über dem Anschriftfeld.
  - Informationsblock rechts: Ihr Zeichen, Ihre Nachricht vom, Unser Zeichen, Datum.
  - Die Betreffzeile steht ohne das Wort „Betreff“.
  - Falzmarken bei 105 mm und 210 mm, Lochmarke bei 148,5 mm.
- **Pflichtangaben im Geschäftsbrief:** Briefkopf oder Fußzeile MÜSSEN die gesetzlich vorgeschriebenen Angaben für die
  Rechtsform aus dem Interview (c) enthalten:
  - GmbH/UG: § 35a GmbHG — Rechtsform, Sitz, Registergericht, HRB-Nr., alle Geschäftsführer (und ggf. den Vorsitzenden
    des Aufsichtsrats).
  - e.K.: § 37a HGB.
  - AG: § 80 AktG.

  Nie erfinden. Ein fehlender Wert bleibt `[PRÜFEN: …]`.
- Jede Antwortaufgabe nennt den empfohlenen **Versandweg**:

| Empfänger | Empfohlener Versandweg |
|---|---|
| Finanzamt | **ELSTER** (Einspruch und Unterlagen lassen sich dort einreichen), sonst Einwurf-Einschreiben |
| Fristen mit Rechtswirkung | Einschreiben oder Fax mit Sendebericht, plus Kopie |
| Gerichte | — das System bereitet Schriftsätze an Gerichte nie als fertig vor; Aufgabe Anwalt |

- Entwürfe gibt es zusätzlich als druckfertiges PDF im DIN-5008-Layout. Das PDF erzeugt nach der Freigabe derselbe
  Renderer wie bei der Rekonstruktion, aus dem Markdown-Entwurf.

## 11. Übergabe an den Steuerberater
- **Monatliches Belegpaket:** ein Ordner oder Zip `Belege_JJJJ-MM` mit allen Buchungsbelegen des Monats, plus eine CSV
  (`;`-getrennt, UTF-8 mit BOM, deutsches Zahlenformat):
  Belegdatum; Belegnummer; Partner; Kategorie; Brutto; USt-Satz; USt-Betrag; Zahlstatus; Datei; Office-ID.
  - E-Rechnungen kommen als XML (+ Ansicht) ins Paket.
  - Rekonstruierte Dokumente kommen als Originalbild + Rekonstruktion ins Paket.
- **DATEV:**
  - Viele Steuerberater nutzen DATEV Unternehmen online.
  - Belege kann der Nutzer dort hochladen oder an eine persönliche **DATEV-Upload-Mail**-Adresse senden, die der
    Steuerberater einrichtet. ⟳
  - Das System bereitet die Dateien und einen E-Mail-Entwurf vor. **Versenden tut der Mensch** (harte Regel 2), außer der
    Nutzer richtet ausdrücklich die automatische Weiterleitung an genau diese eine Adresse ein. Das ist ein eigener
    Freigabepunkt und wird in der Verfahrensdokumentation festgehalten.
  - DATEV-Buchungsstapel (EXTF-Format) zu erzeugen, gehört nicht zum Umfang.
- Eine Anfrage des Steuerberaters („bitte noch Kontoauszüge März“) ist ein `provide_documents`-Vorgang wie jeder andere.

## 12. Datenschutz (DSGVO)
- Dokumente enthalten personenbezogene Daten von Beschäftigten, Kunden und Lieferanten. Das Unternehmen ist
  Verantwortlicher.
- **Cloud-KI-Anbieter und Cloud-Speicher verarbeiten personenbezogene Daten im Auftrag.** Bei der Übergabe empfehlen:
  - einen Auftragsverarbeitungsvertrag (AV-Vertrag, Art. 28 DSGVO) mit jedem Anbieter
  - eine EU-Datenregion, wo angeboten
  - einen Eintrag im Verzeichnis von Verarbeitungstätigkeiten. Die Vorlage hat dafür einen Abschnitt.
- `Personal`- und gesundheitsbezogene Dokumente (Krankmeldungen, AU-Bescheinigungen) werden **nur lokal** verarbeitet,
  wenn der Nutzer das in Interviewfrage (i) gewählt hat. Der eigenständige Runner und jeder Agent MÜSSEN das einhalten
  (Abnahmetest G9).
- Das Löschen nach Ablauf der Aufbewahrungsfrist entscheidet der Nutzer. Die jährliche Liste (§ 8) unterstützt das
  Löschkonzept.

## 13. Kassenbons und Bewirtungsbelege
- Thermobons verblassen. Früh fotografieren und rekonstruieren (`13_rekonstruktion.md`). Das Foto aufbewahren.
- Die Angaben prüfen, die ein Bon zeigen muss: Datum und Uhrzeit, Artikel, Betrag je Steuersatz und — seit der
  Kassensicherungsverordnung — die TSE-Daten (Transaktionsnummer, Signatur). Fehlende TSE-Daten sind nur ein Hinweis, P3.
- **Bewirtungsbeleg:** Für den Betriebsausgabenabzug braucht er Anlass und Teilnehmer, unterschrieben vom Gastgeber.
  Aufgabe „Bewirtungsbeleg ergänzen: Anlass, Teilnehmer, Unterschrift“ anlegen (P2). ⟳ aktuelle Anforderungen.
