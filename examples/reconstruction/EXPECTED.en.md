# Expected results — reconstruction tests (X1–X14, English)

German version: `EXPECTED.de.md` (same content).

| Input (photo) | Ground truth (for scoring only) | What it tests |
|---|---|---|
| `rechnung_schulte_foto.jpg` | `rechnung_schulte.ground_truth.pdf` + `.json` | DIN A4 invoice in DIN 5008 Form B: letterhead with a vector logo, address field, info block, table with a header fill and rules, totals, GiroCode, a red rotated stamp overlapping the subject line, a blue handwritten signature, a three-column footer in 7 pt, fold and punch marks. The photo is taken at an angle on a desk, with a shadow, uneven light, slight blur, noise and JPEG compression. |
| `kassenbon_baeckerei_foto.jpg` | `kassenbon_baeckerei.ground_truth.pdf` + `.json` | An 80 mm thermal receipt in a monospaced font. The paper is faded (lower contrast, warm tint) and the receipt has a custom page length, two VAT rates and TSE lines. |

**Never use the ground-truth files as input.** The reconstruction gets only the photo. The ground truth is read
only by the scoring step afterwards. Using it in any other way invalidates the test.

The ground-truth JSON lists, for every element, its exact position in mm from the top-left corner:
- every text line: text, font family, weight, size in pt, colour, alignment, `critical` field name
- rules and rectangles
- codes with their payload
- graphics: logo, stamp, signature

For comparison, normalise the reconstruction to the ground-truth page size first. Both are DIN A4 or 80 mm wide.

## Pass conditions
| # | Test | Pass |
|---|---|---|
| X1 | No page raster | `pdfimages -list` shows no image covering > 15 % of the page. Every image box is listed in `review.md` with a reason. For the invoice, ideally no image at all. |
| X2 | Real, embedded fonts | `pdffonts`: every font embedded, with a Unicode map |
| X3 | Text layer | `pdftotext` contains every ground-truth line. The character error rate against the ground truth is ≤ 0.5 % overall, and umlauts, ß and € are correct. |
| X4 | Critical fields exact | Invoice: 2026-0457, 29.10.2026, 27.10.2026, DE287654324, 170,00 €, 214,50 €, 37,80 €, 35,00 €, 457,30 €, 86,89 €, 544,19 €, 12.11.2026, IBAN DE32 0000 0000 0000 1234 56. Receipt: 17,20, 0,56, 1,39, 29.10.2026. No deviation allowed; otherwise status must be `review` (an honest "review" passes X4, a silent wrong value fails). |
| X5 | Geometry | Text line positions after normalisation: median deviation ≤ 0.5 mm, max ≤ 1.5 mm. Page size detected as DIN A4 / 80 mm width. |
| X6 | Typography | Family class correct: sans for the invoice, mono for the receipt. A metric-compatible font is used (Liberation Sans / Arimo / Arial for the invoice, Liberation Mono / Cousine / Courier New for the receipt). Size within ±0.5 pt. Bold detected for the letterhead, the subject, the table header, "Rechnungsbetrag" and "SUMME". Colour of the letterhead navy, the stamp red, the footer grey (ΔE ≤ 10). |
| X7 | Table | Header fill present; row rules present; numbers right-aligned on the same right edge as in the original (≤ 0.5 mm) |
| X8 | GiroCode | The QR is decoded and regenerated as **vector**. Its payload is byte-identical to the ground-truth `codes[0].payload`. Its data agrees with the printed IBAN, amount and invoice number. |
| X9 | Signature | The signature is a vector path (or a tight crop, listed in review), in blue. **No text object** in its area. |
| X10 | Stamp | "EINGEGANGEN" and "30. OKT. 2026" are text, red, rotated about 8° (± 2°). The frame is vector. The overlap with the subject line is resolved: both texts are intact. |
| X11 | Provenance | XMP/document metadata contains "Rekonstruktion", the source file name and the SHA-256 of the photo. The visible margin note is present and outside the content area. The original photo is unchanged (same hash) and stored next to the PDF. |
| X12 | Visual similarity and report | `overlay.png` and `review.md` exist. SSIM (binarised, 150 dpi, against the ground-truth rendering) is recorded. SHOULD be ≥ 0.90 for the invoice and ≥ 0.85 for the faded receipt. |
| X13 | Plausibility | The checks run and report no false error: the sums, the per-rate VAT, the IBAN check digit, the USt-IdNr. check digit and GiroCode vs. printed data are all consistent in these two documents. |
| X14 | PDF/A | If PDF/A is claimed, veraPDF passes. If PDF/A-3, the photo is embedded with `AFRelationship=Source`. |

How to score X5/X6: for each ground-truth text line, find the reconstructed line with the same text (fuzzy match)
and compare `x_mm`, `baseline_mm`, `size_pt`, `weight`, `font_family` class and `color`. Write the per-line
comparison into the build log.
