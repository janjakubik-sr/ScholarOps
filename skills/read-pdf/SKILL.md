---
name: read-pdf
description: Read and extract text from PDF files (.pdf attachments, manuscripts, scanned documents) when the read tool cannot parse them. Use whenever an agent must quote, summarize, or analyze PDF content.
---

# Read PDF

Never guess PDF content. Extract first, verify the extraction, then read the
extracted text in chunks.

## 1. Preflight

- Run `pdfinfo "<file.pdf>"` to get page count, encryption flag, and producer.
- If the file is encrypted and no password is available, tell the user; do not
  fabricate content.

## 2. Born-digital PDFs (default path)

- Extract with `pdftotext -layout "<in.pdf>" "<in.pdf>.txt"`, writing output
  beside the source file.
- Idempotent: skip extraction if `<in.pdf>.txt` exists and is newer than the
  PDF.
- For large files, extract page ranges with `pdftotext -f N -l M` and keep page
  numbers for citation. Read the `.txt` in small chunks; use grep to locate
  sections.

## 3. When structure matters

- Use `/home/roshi/markitdown/venv/bin/markitdown "<in.pdf>" -o "<in.pdf>.md"`
  to get markdown with headings and tables. Slower than pdftotext — use
  selectively.

## 4. Scanned / image PDFs

- Detect: Tier-2 output is ~0 chars per page (compare `wc -c` of the `.txt`
  against the page count).
- Run `ocrmypdf --skip-text --sidecar "<in.pdf>.txt" "<in.pdf>" /tmp/ocr.pdf`;
  the sidecar file yields the text while the original stays untouched.
- Fallback: render with `pdftoppm -r 300 -png` per page, then
  `tesseract page.png stdout`. Only English (`eng`) is installed.

## 5. Problem files

- Decrypt or repair: `python3 -c "import pikepdf; pikepdf.open(p, password='').save(out)"`
- Split pages: `pdftk "<in.pdf>" burst output /tmp/page_%d.pdf`
- Garbled layout from poppler: use pdfminer
  (`python3 -m pdfminer.high_level "<in.pdf>" > out.txt`).

## 6. Verification and honesty

- After extraction, check that characters per page are non-trivial and sample a
  page against the reported page count.
- If OCR still yields nothing, report the failure to the user; never invent
  content.
- Only claim pages as read if they were actually extracted and read.
