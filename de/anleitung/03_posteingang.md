# 03 · Aufgabe 2 — Dokumente empfangen

Ziel: Jedes neue Dokument aus jeder Quelle landet **genau einmal** in der Warteschlange, registriert mit seiner
Herkunft und mit gespeichertem Hashwert.

## Quellen
| Quelle | Wie es ankommt | Hinweise |
|---|---|---|
| Scanner | speichert PDFs in `<ROOT>/_Eingang` oder einen Netzwerkordner | 300 dpi, Farbe oder Graustufen |
| Handy | Foto- oder Scan-App (Google-Drive-Scan, Microsoft Lens, Adobe Scan, Kamera) speichert in `_Eingang` auf Drive | Fotos werden rekonstruiert (13). Dateinamen egal |
| Weitere Ordner | z. B. `~/Scans`, ein Download-Ordner | in der Konfiguration eingetragen |
| E-Mail | Anhänge und reine Mails aus einem oder mehreren IMAP-Postfächern | siehe unten |
| Portale | ELSTER-Postfach, DATEV, Bank- oder Lieferantenportale: Der Nutzer lädt Dokumente nach `_Eingang` herunter | das System meldet sich nie in Portalen an |
| Von Hand | der Nutzer legt Dateien in `_Eingang` | |

**Unterstützte Typen:**
- PDF: Text-PDFs, reine Bild-PDFs (gehen zur Rekonstruktion) und hybride ZUGFeRD/Factur-X-PDFs
- Bilder: JPG, PNG, TIFF, HEIC (falls der Rechner es umwandeln kann)
- **E-Rechnungs-XML** (XRechnung UBL/CII)
- Text, `.eml`
- Office-Dokumente (DOCX/XLSX/ODT): Textauszug, wenn ein Werkzeug vorhanden ist; sonst fragt der Agent den Nutzer einmal

Nicht unterstützte Dateien bleiben im Eingang und werden gemeldet.

## Quellformat bestimmen (MUSS)
Beim Eingang `original_kind` setzen (07):

| original_kind | Erkannt an | Nächster Schritt |
|---|---|---|
| `e_invoice_xml` | XML-Wurzel `Invoice`/`CreditNote` (UBL) oder `CrossIndustryInvoice` (CII) | E-Rechnungsweg (`12_deutschland.md` §5) |
| `hybrid_pdf` | PDF mit eingebettetem `factur-x.xml`, `zugferd-invoice.xml` oder `xrechnung.xml` | E-Rechnungsweg. Das XML ist führend |
| `text_pdf` | PDF mit brauchbarer Textebene | Ablage |
| `image_pdf` | PDF ohne brauchbare Textebene (Scannerausgabe oder Bild + unsichtbarer OCR-Text) | Rekonstruktion (13), dann Ablage |
| `photo` / `image` | JPG/PNG/HEIC/TIFF | Rekonstruktion (13), dann Ablage |
| `mail_text` | Mail ohne Anhang, als Text gespeichert | Ablage |

Eine E-Mail mit XML-E-Rechnung **und** einem PDF derselben Rechnung ist ein Dokument: Das XML ist das Original, das PDF
hängt am Datensatz. Erkennbar an gleicher Rechnungsnummer und gleichem Absender.

## E-Mail (MUSS, wenn Postfächer eingerichtet sind)
- **Protokoll:** IMAP über SSL. Ein Konfigurationsblock je Postfach: Name, Server, Benutzer, Ordner, Startdatum und der
  **Name** des Geheimnisses, das das Passwort enthält (nie das Passwort selbst).
- **Übliche deutsche Server ⟳:**

| Anbieter | Server | Hinweis |
|---|---|---|
| Gmail / Google Workspace | `imap.gmail.com` | braucht 2-Faktor-Anmeldung + App-Passwort |
| GMX | `imap.gmx.net` | IMAP muss in den Einstellungen aktiviert sein |
| web.de | `imap.web.de` | IMAP muss in den Einstellungen aktiviert sein |
| IONOS | `imap.ionos.de` | |
| Strato | `imap.strato.de` | |
| T-Online | `secureimap.t-online.de` | |
| Outlook/Hotmail | `outlook.office365.com` | braucht ggf. OAuth; dann dem Nutzer sagen, was nötig ist |

  Im Zweifel beim Anbieter prüfen.
- **Was gespeichert wird:**
  - jeder Anhang
  - bei Mails **ohne** Anhang die Mail selbst als Textdatei (Absender, Datum, Betreff, Text) — viele Rechnungen und
    Hinweise kommen als reine Mail
  - Links in Mails („Ihre Rechnung steht im Kundencenter bereit“) werden nicht aufgerufen; sie werden zur Aufgabe (P3)
    „Rechnung im Kundencenter von <Absender> abrufen“
  - Signaturbilder und Logos unter wenigen KB werden ignoriert
- **Mails nie löschen oder verschieben.** Verarbeitete Mails markieren, damit sie nicht doppelt abgerufen werden: ein
  IMAP-Schlüsselwort wie `$OfficeHelper` oder eine gespeicherte Liste der Message-IDs.
- Gespeicherten Dateien Maildatum und Postfach voranstellen, damit die Herkunft sichtbar bleibt.

## Registrierung, Hashwerte und Dubletten (MUSS)
- Von jeder Datei den SHA-256 berechnen.
- Ist der Hash schon registriert, ist die Datei eine Dublette: mit Zeitstempel nach `_office/duplicates/` verschieben
  und nichts registrieren.
- Sonst:
  1. Dokumentdatensatz (07) mit Status `queued`, Originalname, Quelle, `original_kind` und Eingangszeit anlegen.
  2. Eine Zeile an `_office/hashlog.csv` anhängen (Zeitstempel; Office-ID; Originalname; SHA-256).
  3. Den Vorgang an `_office/protokoll.csv` anhängen.
  4. Die Datei unter ihrer Datensatz-ID nach `_office/queue/` verschieben.
- Mehrere kurz nacheinander aufgenommene Fotos mit fortlaufenden Namen KÖNNEN als ein mehrseitiges Dokument
  vorgeschlagen werden. Die Rekonstruktion bestätigt das anhand von Seitenzahlen und Inhalt.
- Nach dem Posteingang liegen im Eingang nur noch nicht unterstützte Dateien.

## Ergebnis
- Liste der registrierten IDs mit Originalnamen und Art.
- Zahlen: neu, Dubletten, nicht unterstützt — je Quelle.
- Alter der ältesten wartenden Datei im Eingang (GoBD-Warnung nach 10 Tagen, `12_deutschland.md` §8).
- Fehler einer Quelle (z. B. Anmeldung an einem Postfach fehlgeschlagen) werden gemeldet und halten die anderen Quellen
  nicht auf.
