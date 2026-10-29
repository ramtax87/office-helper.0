# 13 · Task 6 — Photo → reconstructed PDF

Goal: from a photo or image scan of a paper document, produce a **real PDF document**: real text in real
(embedded) fonts, vector lines, tables and codes, laid out like the original. It must look like the
original and be searchable, copyable and archivable.

**Not acceptable:** a picture of the page inside a PDF, even with an invisible OCR text layer underneath.
That is what scanner apps and `ocrmypdf` produce, and it is explicitly **not** what this task is for.

## Where it fits
- **Automatic:** during filing, every document whose source is a photo, an image scan or an image-only PDF
  is reconstructed first (config `reconstruction.mode = "auto"`). The filing step then uses the
  reconstruction's verified text.
- **On request:** "Mach aus diesem Foto ein PDF" / "reconstruct document 37" — the `reconstruct` workflow.
- **Multi-page:** several photos of one document (same mail, same intake batch with consecutive names, or the
  user says so) become one PDF in the right page order. Page numbers like "Seite 2 von 3" are used to check
  the order and completeness.

## What is kept (GoBD — see `12_germany.md`)
The photo is the **Beleg** (the digital original of the paper). The reconstruction is a derived **Lesefassung**
(readable copy). Therefore:
- The original image file is kept unchanged, with its hash in the records. It is never deleted or replaced.
- The reconstructed PDF is stored next to it: `…_Rekonstruktion.pdf`. The original image keeps its name with
  `_Original` added.
- The PDF MUST carry provenance:
  - XMP/document metadata: "Rekonstruktion", the source file name, the SHA-256 of the source, the date, the method
    and the quality score.
  - A **visible, discreet note** in the bottom margin, outside the content area (≈ 6 pt, grey), e.g.
    `Rekonstruiert aus Foto IMG_2041.jpg vom 29.10.2026 · Original: Office-ID #57 · Qualität 0,97`.
  - Reason: a reconstruction that can pass as the issuer's own original is a risk in a tax audit and in disputes.
    This note is not optional.
- SHOULD be PDF/A-3b with the original image embedded as an associated file (`AFRelationship=Source`), so the PDF
  carries its own evidence. If the toolchain cannot produce valid PDF/A-3, produce PDF/A-2b, keep the image next
  to it, and note the fallback in the build log.
- A reconstructed invoice is **not an E-Rechnung**, and the system never generates XRechnung/ZUGFeRD data
  "as if" issued by the sender.

## Fidelity rules
1. **Content is sacred.** The reconstruction reproduces what is on the paper, character for character. That
   includes errors on the original: typos, wrong sums, outdated addresses. Nothing is corrected, translated,
   completed or "improved". Errors found by the checks (step 7) are **reported**, never fixed in the document.
2. **Never guess silently.** Every character the system is not sure about is resolved by consensus (step 4) or
   flagged for human review. Critical fields MUST either reach consensus or send the document to review.
3. **Text is text.** All printed text becomes real text with a real font. That includes table contents, small
   print, rotated text and stamps with printed lettering.
4. **Handwriting is never typed.** Signatures, initials, handwritten notes and ticks are traced as **vector
   paths** (or, if tracing fails, kept as a tightly cropped image). They are never rendered in a font.
   Readable handwriting is transcribed into the summary and the metadata only.
5. **Raster only where unavoidable**: photos inside the document, logos that cannot be vectorised cleanly, and
   signatures or stamps that cannot be traced. Each one is cropped tightly to its own box. Their total area
   SHOULD stay ≤ 15 % of the page; every raster element is listed in the review report with the reason.

## Pipeline
Keep intermediate results per document in `_office/reconstruction/<id>/`, for debugging and review.

### 1. Rectify
- Find the paper edges, apply the perspective correction ("four-point transform") and deskew. Flatten
  curved pages if needed (dewarping).
- Remove the lighting gradient and shadows. Keep colour (stamps, logos, highlighting) and produce a clean
  grayscale/binarised version for OCR.
- **Page size:** match the aspect ratio to the standard formats (DIN A4 210×297, A5, A6, DL, US Letter). For
  receipts, use 57/80 mm width with a measured length. That fixes the physical scale in millimetres.
- Output: `01_rectified.png` at ≥ 300 dpi equivalent, plus the mm↔px scale.

### 2. Layout analysis
Find and classify:
- text blocks, lines and words with boxes
- reading order
- tables: grid lines, cells, header row, column alignment
- rules, boxes and filled areas
- logos and images
- stamps
- signatures and handwriting
- QR codes and barcodes
- DIN 5008 elements: fold marks, punch mark, return-address line, address field, information block, footer columns

### 3. Recognise text
- OCR with German language data and word-level boxes and confidences.
- Run at least **two independent readings**: two OCR engines, or OCR plus a vision-capable model reading the
  crop. One extra reading is enough for lines that already have very high confidence.
- German specifics:
  - the characters ä ö ü Ä Ö Ü ß €, the section sign § and the en dash –
  - number format `1.234,56`, dates `TT.MM.JJJJ`
  - IBAN groups of four
  - typical words: Rechnungsbetrag, zzgl. USt, Steuernummer, Aktenzeichen

### 4. Consensus and review
- Where readings agree with high confidence, take the text.
- Where they disagree, the crop is read again (higher resolution, other preprocessing, vision model). Then:
  - a clear majority is taken;
  - anything else gets the most likely reading **and** an entry in the review list (crop image, candidates).
- **Critical fields** MUST have consensus, otherwise the document status is `review`:
  - amounts and sums
  - dates
  - invoice, customer and case numbers
  - IBAN and BIC
  - Steuernummer and USt-IdNr.
  - names in the address field

### 5. Typography
For every line or span, determine:
- **size** in pt, from cap height and x-height via the mm scale
- **weight** (regular/bold), from stroke width
- **style** (upright/italic), from slant
- **colour**
- **alignment**: left, right, centred, justified, decimal-aligned numbers
- **letter spacing**

**Font identification:**
1. Classify the face: sans, serif, mono, condensed, display.
2. Render the line in candidate fonts at the measured size and compare with the crop: glyph shapes and the
   line width. The best match wins.
3. Prefer **metric-compatible free fonts**, which line-break exactly like the commercial originals:

| Seen on German documents | Use (free, metric-compatible) |
|---|---|
| Arial, Helvetica | Liberation Sans or Arimo |
| Times New Roman | Liberation Serif or Tinos |
| Courier New, typewriter | Liberation Mono or Cousine |
| Calibri (Word default) | Carlito |
| Cambria | Caladea |
| Thermal receipt printers | Liberation Mono, or a matching dot-matrix-style mono font |
| DIN 1451-style (signs, some letterheads) | D-DIN or similar |
| Corporate fonts (e.g. Telekom, Sparkasse) | the closest free font by glyph comparison. Never a font file the user does not have a licence for |

4. Use any font the user has installed and licensed (e.g. MS Core Fonts) when it matches better.
5. Embed every font as a subset with Unicode mapping, so copy, search and screen readers work.
6. Fit each line to its original width: tiny tracking or horizontal-scale adjustment, at most ±3 %.
   If it needs more, the font choice is wrong; try the next candidate.

### 6. Graphics and codes
- **Rules, boxes, fills, table borders:** vector, with measured line width and colour.
- **QR codes** (for example the GiroCode/EPC-QR on German invoices) and **barcodes** (EAN, Code 128, DataMatrix):
  decode them, then regenerate them as vector with the identical payload. The payload goes into the review
  report. If a code cannot be decoded, keep it as a cropped image and flag it.
- **Logos:** vectorise (colour quantisation + tracing). If the result does not visually match, use a cropped
  image.
- **Stamps:** printed lettering becomes text in a matching font with rotation and colour. The frame is drawn as
  vector. Ink texture is not reproduced.
- **Signatures and handwriting:** traced vector paths (see fidelity rule 4).

### 7. Plausibility checks (report, never correct)
- The IBAN check digit (mod 97) and the German IBAN length of 22.
- The USt-IdNr. format and check digit (DE + 9 digits).
- The Steuernummer format.
- Line totals = quantity × unit price.
- Net + VAT = gross. Per-rate VAT on receipts.
- Dates exist. The invoice date is not after the scan date.
- The GiroCode payload agrees with the IBAN, amount and reference printed on the page.
- Any mismatch goes into the review report: for example "Summe auf dem Beleg 17,20 € ≠ Positionen 17,30 €".

### 8. Build the PDF
Fill the **document model** (`shared/templates/reconstruction/document_model.schema.json`):
- page size
- elements with mm positions
- styled spans, tables, vector paths, codes and image crops
- confidence per element

Then render it with a PDF library that positions text absolutely and embeds fonts. Requirements:
- real text objects in reading order, Unicode-mapped
- no element outside the page
- vector graphics as vector
- the provenance note and metadata
- PDF/A as above

### 9. Verify
Re-render the produced PDF at the rectified image's resolution. Align it with `01_rectified.png` and compute:

| Check | Requirement |
|---|---|
| No page image | page-sized or text-region raster images: none (check the image list of the PDF) |
| Fonts | all fonts embedded |
| Text layer | text extracted from the PDF = consensus text (character error rate 0 against its own source) |
| Critical fields | all present, identical to the consensus values |
| Geometry | text line positions: median deviation ≤ 0.5 mm, max ≤ 1.5 mm |
| Typography | font size within ±0.5 pt; weight and family class as detected |
| Visual similarity | SSIM of binarised images at 150 dpi, recorded as the quality score (SHOULD be ≥ 0.90 for clean prints) |
| PDF/A | passes a PDF/A validator (e.g. veraPDF) when PDF/A is claimed |

Save `overlay.png` (original in red, reconstruction in blue: purple = match) and `review.md`. `review.md` lists:
- the uncertain words with crops
- the raster elements with reasons
- the plausibility findings
- the score

If any MUST check fails, status `review`. The case step then creates the task "Rekonstruktion prüfen".

## Review by the human
In the review workflow, the user sees `overlay.png` and the uncertain items, side by side with the photo.
Corrections the user gives are applied to the document model (never by editing the PDF by hand) and the PDF is
rebuilt. Every correction is logged.

## Performance
SHOULD be under about 2 minutes per A4 page on a normal laptop CPU without a GPU, model-based second readings
included. Heavy engines (layout models, vision models) MAY be used if the machine allows. With a cloud model,
the privacy rules of `08` and `12` apply: no `Personal` documents leave the machine unless the user allowed it.

## Tooling hints (choose what fits the machine; verify current versions)
- **Rectification and image processing:** OpenCV.
- **OCR with word boxes:**
  - Tesseract 5 with `deu` (hOCR/ALTO/TSV output)
  - PaddleOCR (including layout and table structure)
  - docTR
  - Surya (layout, reading order, tables)
- **Second reading:** a vision-capable model through the user's AI tool or provider.
- **Codes:** zxing-cpp or OpenCV QR detection. Generate new codes with a QR library; GiroCode payload format
  per EPC069-12.
- **Tracing:** potrace (monochrome); colour quantisation + potrace for logos.
- **Font matching:**
  - fontTools for metrics
  - FreeType/Pillow to render candidates
  - fontconfig to find installed fonts
- **PDF:** ReportLab, PyMuPDF or pikepdf (for PDF/A and attachments); or HTML/CSS with absolute positioning
  via WeasyPrint; or Typst. Validate with veraPDF.
- **Comparison:** SSIM (scikit-image), pdffonts/pdfimages/pdftotext (poppler) for the structural checks.
