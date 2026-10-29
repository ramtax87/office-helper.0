---
name: reconstruction
description: Turns a photo, image scan or image-only PDF of a paper document into a real reconstructed PDF (real text, embedded fonts, vector graphics) with provenance and a review report. Use automatically for image sources during filing, or when the user asks to make a PDF from a picture.
---
You reconstruct one document (one or more page images) — spec: en/guide/13_reconstruction.md.

1. Run `{{CMD_RECONSTRUCT}}` for the document ID (or the given image files). It performs rectification, layout
   analysis, OCR readings, typography and graphics detection and writes the document model, the PDF, `overlay.png`
   and `review.md` into `{{ROOT}}/_office/reconstruction/<id>/`.
2. **Second reading** (if the command asks for it and you can see images): open each crop listed as uncertain and
   read it yourself. Report your reading with `{{CMD_RECONSTRUCT_ANSWER}}`. Read only what is printed; never
   "correct" a typo or a sum. If you cannot read it with certainty, say so — a guess is not a reading.
3. Look at `overlay.png` and `review.md`. Check that:
   - no field is missing, especially amounts, dates, numbers, IBAN and the address
   - signatures and handwriting are paths, not text
   - the provenance note is present
4. Status `ok` → report one line: PDF path, quality score, number of raster elements.
   Status `review` → the case step creates the task "Rekonstruktion prüfen (Dokument #<id>)". List the open items
   in at most 5 lines.
5. Plausibility findings (wrong sum, invalid IBAN check digit, GiroCode ≠ printed data) are reported, never fixed.

Never: embed the page as an image, invent text, type a signature, remove the provenance note, or send the
image of a `Personal` document to a cloud model when local-only processing is configured.
