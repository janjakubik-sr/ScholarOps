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

- Extract with `pdftotext "<in.pdf>" "<in.pdf>.txt"` (NO `-layout` flag),
  writing output beside the source file. Poppler's default reading-order mode
  handles multi-column journal articles correctly.
- **Never use `pdftotext -layout` on multi-column text**: it preserves physical
  positioning by splicing unrelated columns onto the same line (e.g. a
  "Received/Accepted" sidebar glued into the author list). Reserve `-layout`
  for cases where physical layout itself is the subject (posters, wide
  tables).
- Idempotent: skip extraction if `<in.pdf>.txt` exists and is newer than the
  PDF. Exception: if an existing extraction is garbled (see section 6),
  regenerate it with the correct command.
- For large files, extract page ranges with `pdftotext -f N -l M` and keep page
  numbers for citation. Read the `.txt` in small chunks; use grep to locate
  sections.
- Line-break hyphenation and occasional double line breaks are normal
  artifacts of correct extraction; do not "fix" the tool for them.

## 3. When structure matters

- Use `/home/roshi/markitdown/venv/bin/markitdown "<in.pdf>" -o "<in.pdf>.md"`
  to get markdown with heading and table hints. Slower than pdftotext — use
  selectively.
- Caveat: markitdown (pdfminer-based) can fuse narrow sidebars (dates,
  check-for-updates boxes) into adjacent author/body lines. For plain reading
  of multi-column journal articles, section-2 pdftotext output is usually
  cleaner; verify against section 6 if accuracy matters.

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
- Spot-check reading order on multi-column papers: open a page with a
  sidebar (dates, correspondence) and confirm it is not glued into body text.
  Detect interleaving with `grep -E '.{30} {5,}.{30}' "<in.pdf>.txt"`: many
  matches on a born-digital file mean the extraction spliced columns;
  re-extract without `-layout` before reading further.
- If OCR still yields nothing, report the failure to the user; never invent
  content.
- Only claim pages as read if they were actually extracted and read.

## 7. Figures and embedded images (on demand)

Text extraction (sections 2–4) silently drops all images: embedded rasters,
image-tables, and vector plots/diagrams. If figure content is
decision-relevant, extract images into `<in.pdf>.img/` beside the source and
load the ones that matter with the Read tool.

- Extract embedded rasters with
  `pdfimages -png [-f N -l M] "<in.pdf>" "<in.pdf>.img/img"`
  (create the directory first). Idempotent: skip if the directory exists and
  is newer than the PDF.
- `pdfimages` misses figures drawn as vector graphics (most journal plots).
  For those pages, render with
  `pdftoppm -r 150 -png -f N -l N "<in.pdf>" "<in.pdf>.img/page-N"`.
- Triage first: read the extracted text and captions, note which figures/pages
  matter, then load only those image files with Read. Vision reads are
  expensive — do not bulk-read every extracted image.
- Honesty: never infer figure content (axis values, trends, significance
  markers) from caption text alone. Only claim a figure as read if the image
  itself was loaded and inspected.
