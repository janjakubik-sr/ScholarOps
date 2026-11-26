# Changelog

All notable changes to ScholarOps are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `read-pdf` skill: teaches agents how to extract text from PDF files when the
  built-in read tool cannot parse them. Provides a verified tool ladder:
  `pdftotext` in default reading-order mode for born-digital PDFs (correct for
  multi-column journal articles; `-layout` is banned there and reserved for
  layout inspection), `markitdown` (structure-preserving markdown, with sidebar
  fusion caveats), `ocrmypdf`/`tesseract` (scanned documents), and
  `pikepdf`/`pdftk`/`pdfminer` for encrypted, damaged, or otherwise problematic
  files. Includes a grep-based column-interleaving detector to catch garbled
  extractions. Extracts to `<file>.pdf.txt` beside the source, idempotently,
  and enforces verify-before-claim honesty rules.
- README: added `read-pdf` to the skills table.
- `read-docx` skill: teaches agents how to extract text from DOCX files via
  `pandoc` (GitHub-flavored markdown, idempotent, beside the source), with a
  preflight for legacy `.doc` files, tracked-changes caveat, and on-demand
  embedded-image extraction (`unzip word/media/*` or
  `pandoc --extract-media`). Enforces the same verify-before-claim honesty
  rules as `read-pdf`.
- `read-pdf` skill: added section 7 (figures and embedded images, on demand).
  Text extraction silently drops all images; the new section covers `pdfimages`
  for embedded rasters, `pdftoppm` page renders as the fallback for vector
  figures, caption-based triage before vision reads, and the rule to never
  infer figure content from captions alone.
- README: added `read-docx` to the skills table.
- `Reviewer_Assistant.md`, `Grant_Rapporteur_Assistant.md`, and
  `Editor_Assistant.md`: added a mandatory prompt-injection screening gate
  before substantive evaluation. All supplied materials, including hidden or
  non-rendered content and reviewer evaluations, are treated as untrusted.
  Detection or suspicion triggers immediate user notification and a complete
  halt; grant evaluation also stops work on the rest of the batch.
- `Narrative_summary.md`: added a study-limitations assessment and a dedicated
  output section covering substantial acknowledged and independently identified
  limitations, their evidentiary basis, and their potential impact.
- README: documented the prompt-injection screening gate and the narrative
  summary's study-limitations assessment.

### Removed

- `AI-Detector.md` and its dedicated `knowledge-base/AI-Detector.md` companion
  after the agent failed a basic behavior test. README inventory and usage
  references were removed as well.
