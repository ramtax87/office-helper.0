# 03 · Task 2 — Receiving documents

Goal: every new document, from any source, ends up **exactly once** in the queue, registered with where it came
from, with its hash recorded.

## Sources
| Source | How it arrives | Notes |
|---|---|---|
| Scanner | saves PDFs into `<ROOT>/_Eingang` or a network folder | 300 dpi, colour or grayscale |
| Phone | photo or scan app (Google Drive scan, Microsoft Lens, Adobe Scan, the camera) saves into `_Eingang` on Drive | photos are reconstructed (13). File names don't matter |
| Extra folders | e.g. `~/Scans`, a download folder | listed in the config |
| Email | attachments and plain mails from one or more IMAP mailboxes | see below |
| Portals | ELSTER-Postfach, DATEV, bank or supplier portals: the user downloads documents into `_Eingang` | the system never logs into portals |
| Manual | the user drops files into `_Eingang` | |

**Supported types:**
- PDF: text PDFs, image-only PDFs (these go to reconstruction) and hybrid ZUGFeRD/Factur-X PDFs
- images: JPG, PNG, TIFF, and HEIC if the machine can convert it
- **e-invoice XML** (XRechnung UBL/CII)
- plain text, `.eml`
- office documents (DOCX/XLSX/ODT): text extraction if a tool exists, otherwise the agent asks the user once

Unsupported files stay in the inbox and are reported.

## Classifying the source format (MUST)
Set `original_kind` (07) at intake:

| original_kind | Detected by | Next step |
|---|---|---|
| `e_invoice_xml` | XML root `Invoice`/`CreditNote` (UBL) or `CrossIndustryInvoice` (CII) | e-invoice path (`12_germany.md` §5) |
| `hybrid_pdf` | PDF with an embedded `factur-x.xml`, `zugferd-invoice.xml` or `xrechnung.xml` | e-invoice path. The XML is leading |
| `text_pdf` | PDF with a usable text layer | filing |
| `image_pdf` | PDF without a usable text layer (scanner output, or an image + invisible OCR text) | reconstruction (13), then filing |
| `photo` / `image` | JPG/PNG/HEIC/TIFF | reconstruction (13), then filing |
| `mail_text` | mail without an attachment, saved as text | filing |

An email with an XML e-invoice **and** a PDF of the same invoice is one document: the XML is the original and the
PDF goes in as an attachment of the record. Recognise them by the same invoice number and sender.

## Email (MUST if accounts are configured)
- **Protocol:** IMAP over SSL. One config block per mailbox: name, host, user, folder, start date, and the **name**
  of the secret that holds the password (never the password itself).
- **Common German hosts ⟳:**

| Provider | Host | Note |
|---|---|---|
| Gmail / Google Workspace | `imap.gmail.com` | needs 2-step verification + App-Passwort |
| GMX | `imap.gmx.net` | IMAP must be enabled in the settings |
| web.de | `imap.web.de` | IMAP must be enabled in the settings |
| IONOS | `imap.ionos.de` | |
| Strato | `imap.strato.de` | |
| T-Online | `secureimap.t-online.de` | |
| Outlook/Hotmail | `outlook.office365.com` | may need OAuth; then tell the user what is needed |

  Verify with the provider if unsure.
- **What to save:**
  - every attachment
  - for mails **without** attachments, the mail itself as a text file (sender, date, subject, body) — many
    invoices and notices arrive as plain mail
  - links in mails ("Ihre Rechnung steht im Kundencenter bereit") are not followed; they become a task (P3)
    "Rechnung im Kundencenter von <Absender> abrufen"
  - signature images and logos below a few KB are ignored
- **Never delete or move mails.** Mark processed mails so they are not fetched twice: an IMAP keyword such as
  `$OfficeHelper`, or a stored list of message IDs.
- Prefix saved files with the mail date and the account, so the origin stays visible.

## Registration, hashes and duplicates (MUST)
- Compute the SHA-256 of each file.
- If the hash is already registered, the file is a duplicate: move it to `_office/duplicates/` with a timestamp
  and register nothing.
- Otherwise:
  1. Create a document record (07) with status `queued`, the original name, source, `original_kind` and received
     time.
  2. Append a line to `_office/hashlog.csv` (timestamp; office ID; original name; SHA-256).
  3. Append the event to `_office/protokoll.csv`.
  4. Move the file into `_office/queue/` under its record ID.
- Several photos taken within a short time with consecutive names MAY be proposed as one multi-page document.
  The reconstruction step confirms this from page numbers and content.
- After intake, the inbox contains only unsupported files.

## Output
- A list of queued IDs with original names and kinds.
- Counts: new, duplicates, unsupported — per source.
- The age of the oldest file waiting in the inbox (the 10-day GoBD warning, `12_germany.md` §8).
- Errors of one source (e.g. a mailbox login failed) are reported and do not stop the other sources.
