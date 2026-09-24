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
