---
name: read-docx
description: Read and extract text and images from DOCX files (Word manuscripts, reports) when the read tool cannot parse them. Use whenever an agent must quote, summarize, or analyze DOCX content, including embedded figures.
---

# Read DOCX

Never guess DOCX content. Extract first, verify, then read the extracted text
in chunks. Embedded images are dropped by text extraction — handle them on
demand (section 3).

## 1. Preflight

- Confirm the file is a real DOCX: `unzip -l "<in.docx>" | head` should list
  `word/document.xml`. Legacy `.doc` (OLE binary) must be converted first
  (e.g. `libreoffice --headless --convert-to docx`).
- If the file is corrupt or password-protected, tell the user; do not
  fabricate content.

## 2. Text extraction (default path)

- Extract with `pandoc "<in.docx>" -t gfm -o "<in.docx>.md"`, writing output
  beside the source file. GitHub-flavored markdown keeps headings, tables,
  and footnotes.
- Idempotent: skip extraction if `<in.docx>.md` exists and is newer than the
  DOCX.
- Verify: the word count of the `.md` should be plausible for the document
  length; spot-read a page-sized chunk before analyzing.
- For tracked-changes-heavy documents, pandoc shows the accepted view;
  if insertions/deletions matter, say so explicitly and use
  `python-docx` or unzip `word/document.xml` to inspect them.

## 3. Embedded images (on demand)

Text extraction silently drops embedded images (figures, diagrams, scanned
tables). If their content is decision-relevant, extract and load them.

- Extract with `unzip -j -o "<in.docx>" "word/media/*" -d "<in.docx>.img/"`.
  File names vary by producer: Word uses `image1.png`, `image2.jpeg`, ... in
  document order, but other generators may use names like `rId20.png`.
  List the extracted files and rely on order/content, not names.
- Alternative: `pandoc "<in.docx>" --extract-media="<in.docx>.media" -t gfm`
  both extracts media and rewrites the markdown to reference each image in
  place — useful when image position in the text matters.
- Triage first: read the extracted text and captions, decide which figures
  matter, then load only those image files with the Read tool. Vision reads
  are expensive — do not bulk-read every extracted image.

## 4. Honesty

- Never infer figure content (axis values, trends, significance markers)
  from caption text alone.
- Only claim a figure or table as read if it was actually extracted and
  read — either as text in the `.md` or as an inspected image file.
- If extraction fails (corrupt file, missing tools), report the failure to
  the user; never invent content.
