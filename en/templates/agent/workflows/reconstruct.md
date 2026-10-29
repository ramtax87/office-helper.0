---
description: Make a real PDF from a photo or image scan ("Mach aus diesem Foto ein PDF")
argument-hint: <document id | image file(s)> [notes]
---
Act as role `reconstruction` for {{args}}. If image files are given that are not registered yet, run intake
for them first (they then get an Office-ID and are filed as usual). Several images of one document → one PDF,
in page order. Report the PDF path, the quality score and anything to review.
