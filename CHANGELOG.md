# Changelog

All notable changes to ScholarOps are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- `read-pdf` skill: teaches agents how to extract text from PDF files when the
  built-in read tool cannot parse them. Provides a verified tool ladder:
  `pdftotext` (born-digital PDFs), `markitdown` (structure-preserving markdown),
  `ocrmypdf`/`tesseract` (scanned documents), and `pikepdf`/`pdftk`/`pdfminer`
  for encrypted, damaged, or otherwise problematic files. Extracts to
  `<file>.pdf.txt` beside the source, idempotently, and enforces
  verify-before-claim honesty rules.
- README: added `read-pdf` to the skills table.
