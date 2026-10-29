# 02 · Aufgabe 1 — Ablagebaum

## Fragen (Bauphase 2)
- **Name** des Hauptordners (Standard `Buero`).
- **Speicherort:** lokaler Ordner oder Google Drive. (Andere Cloud-Speicher — OneDrive, Nextcloud, Dropbox — funktionieren
  wie Drive: als synchronisierter Ordner.)
- **Pfad** (vollständig).
- **Kategorien:** Standard übernehmen oder anpassen.
- (Alles Weitere steht im Interview in `01_bauablauf.md` Phase 2 und in `12_deutschland.md` §2.)

## Aufbau (MUSS)
```
<ROOT>/                                 Hauptordner, Name vom Nutzer
├── _Eingang/                           Eingang: Scans und andere neue Dateien landen hier
│   └── email/                          aus Postfächern abgerufene Anhänge
├── _office/                            eigene Daten des Systems — nicht von Hand bearbeiten
│   ├── queue/                          registriert, noch nicht abgelegt
│   ├── drafts/                         Antwortentwürfe zur Prüfung
│   ├── duplicates/                     erkannte Dubletten
│   ├── reconstruction/<id>/            Zwischenergebnisse und Prüfberichte Foto → PDF (13)
│   ├── hashlog.csv                     fortlaufendes Hash-Protokoll jedes Originals (12 §8)
│   ├── protokoll.csv                   fortlaufendes Aktivitätsprotokoll (12 §8)
│   ├── Verfahrensdokumentation.md      GoBD-Verfahrensbeschreibung (12 §8)
│   └── Datensätze (Datenbank oder Dateien, siehe 07)
├── SCHEDULE.md                         Prioritätenliste (06)
├── schedule.ics                        Kalenderdatei mit Erinnerungen (06)
└── 2026/                               ein Ordner je Jahr
    ├── 01_Januar/
    │   ├── Rechnungen_Eingang/
    │   ├── Rechnungen_Ausgang/
    │   ├── …jede Kategorie…
    │   └── Sonstiges/
    ├── 02_Februar/
    ⋮
    └── 12_Dezember/
```
Regeln:
- Die **Jahresebene** über den Monaten ist Pflicht, damit sich Jahre nie vermischen.
- Monatsordner sind `01_` … `12_` nummeriert, damit sie richtig sortieren. Monatsnamen: Januar, Februar, März,
  April, Mai, Juni, Juli, August, September, Oktober, November, Dezember.
- Jeder Monat hat **jeden** Kategorieordner, auch leer — man sucht nach Monat.
- Das Anlegen MUSS idempotent sein: Wiederholen ergänzt fehlende Ordner und entfernt oder benennt nie etwas um.
- Das laufende Jahr anlegen, ab Oktober zusätzlich das nächste. Neue Jahre auf Wunsch oder automatisch beim ersten
  Dokument eines neuen Jahres.
- Ordnernamen ohne Zeichen, die auf anderen Systemen Probleme machen (`/ \ : * ? " < > |`). Umlaute sind erlaubt
  (auf dem gewählten Speicher testen; wenn der Speicher sie verstümmelt: `ae/oe/ue`).

## Standardkategorien (SOLL) — Deutschland
| Ordner | Inhalt |
|---|---|
| `Rechnungen_Eingang` | erhaltene Rechnungen und E-Rechnungen, Mahnungen, erhaltene Gutschriften |
| `Rechnungen_Ausgang` | vom Unternehmen ausgestellte Rechnungen |
| `Kassenbelege` | Kassenbons, Quittungen, Bewirtungsbelege, Tankbelege |
| `Finanzamt` | Finanzamt, Bundeszentralamt für Steuern: Bescheide, Anforderungen, Prüfungsanordnungen |
| `Behörden` | Stadt/Gemeinde (Steueramt, Gewerbe-, Ordnungs-, Bauamt), Zoll, IHK/HWK, Beitragsservice, Lebensmittelüberwachung, Schornsteinfeger |
| `Sozialversicherung` | Krankenkassen, Minijob-Zentrale, Deutsche Rentenversicherung, Berufsgenossenschaft, Agentur für Arbeit |
| `Bank` | Kontoauszüge, Lastschriftankündigungen, Kreditschreiben |
| `Versicherungen` | Policen, Beitragsrechnungen, Schadensfälle |
| `Verträge` | Verträge, AGB, Kündigungen, Leasing, Telefon-/Energieverträge |
| `Personal` | Personalangelegenheiten, Lohn, Bewerbungen — standardmäßig **nur lokal verarbeitet** (12 §12) |
| `Lieferungen` | Lieferscheine, Auftragsbestätigungen, Lieferavise |
| `Recht_Gericht` | Mahnbescheide, Gerichtsschreiben, Inkasso, Anwälte, Abmahnungen |
| `Steuerberater` | Schreiben und Anfragen des Steuerberaters, Auswertungen (BWA) |
| `Briefe` | sonstige Geschäftsbriefe, besonders solche, die eine Antwort brauchen |
| `Tickets` | Reisen, Veranstaltungen, Support-/Service-Tickets |
| `Info` | Newsletter, Preislisten, Hinweise ohne Handlungsbedarf |
| `Sonstiges` | alles andere und unlesbare Dokumente |

Der Nutzer kann Kategorien ergänzen, umbenennen oder entfernen. Das Entfernen einer Kategorie löscht nie ihre Ordner.

## Lokaler Speicher
Ein normaler Ordner im Benutzerverzeichnis. Sonst nichts zu tun. Eine Datensicherung empfehlen (11_betrieb).

## Google Drive (oder ein anderer Cloud-Speicher)
Das System braucht nur einen **Pfad**. Drive muss als Ordner erscheinen:
- **Linux:** `rclone` mit Einbindung (`rclone mount <remote>: ~/GoogleDrive --vfs-cache-mode full`), als
  Benutzerdienst, damit es Neustarts übersteht. Oder `rclone bisync` per Zeitplan für eine lokale, synchronisierte
  Kopie. Die Browser-Anmeldung in `rclone config` macht der Nutzer selbst (🔐).
- **Windows / macOS:** die offizielle App „Google Drive für Desktop“ stellt einen Laufwerksbuchstaben bzw. Ordner bereit.
- Alternative für jedes System: lokal speichern und regelmäßig nach Drive synchronisieren.

Anforderungen bei Cloud-Speicher:
- **GoBD:** Ein Cloud-Ordner ist für sich kein revisionssicheres Archiv. Versionsverlauf des Speichers aktivieren,
  Hash-Protokoll führen, Schreibrechte einschränken, eine unabhängige Datensicherung halten (12 §8). Die Übergabe sagt
  das.
- **DSGVO:** Der Cloud-Speicher verarbeitet personenbezogene Daten im Auftrag → AV-Vertrag mit dem Anbieter, EU-Region,
  wo verfügbar (12 §12).
- Nur **ein** Rechner arbeitet mit demselben Hauptordner (die Datensätze sind nicht für gleichzeitige Schreiber
  gebaut).
- Nach der Einrichtung prüfen: Eine in den Hauptordner geschriebene Datei erscheint in der Drive-Weboberfläche.
- Die Datensätze im Hauptordner halten, damit sie mit den Dokumenten gesichert werden. Ist die Datenbank eine einzelne
  Datei, darauf achten, dass das Synchronisierungswerkzeug sie nicht im geöffneten Zustand beschädigt (Einbindung mit
  Schreibcache, oder Datenbank lokal halten und nach jedem Lauf eine Kopie in den Hauptordner legen).
