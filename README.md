# ScholarOps

**An opinionated multi-agent suite for rigorous biomedical research — peer review, grant evaluation, biostatistics, and evidence auditing that leave a trail.**

ScholarOps is a collection of [OpenCode](https://opencode.ai) agents and skills that turn an AI coding assistant into a transparent research-integrity workbench. Every workflow follows one control loop:

> **Search → Acquire → Build artifacts → Independent audit → Researcher decision.**

Nothing is treated as final until it passes an audit you can inspect: exact queries, raw and ranked records, named artifacts, and explicit caveats.

## Why ScholarOps?

- **Auditable by design.** Agents never present generated prose, extracted rows, or dataset picks as final without independent verification.
- **No access theater.** Abstract-only findings are never labeled full-text-verified; paywall workarounds are refused.
- **Separation of concerns.** Narrow auditors (data, logic, references) feed higher-level agents (reviewer, rapporteur) instead of one prompt doing everything.
- **Prompt-injection screening.** Reviewer, Grant Rapporteur, and Editor workflows screen all supplied materials before substantive evaluation and halt immediately on detected or suspected injection.

## Repository contents

### Agents

| Agent | Purpose |
|---|---|
| `Researcher_Assistant.md` | Research router: inspects each request and runs the full search–acquire–audit–decide loop at the right rigor level. |
| `AI-Detector.md` | Passage-level AI-text evidence triage with a mandatory live scholarly freshness check, exact source locations, and bounded ordinal confidence. |
| `Reviewer_Assistant.md` | Rigorous peer-review agent: reference control, data/statistics audit, logic checks, then a journal-compliant review. |
| `Biostatistician.md` | Turns CSV/XLSX data plus a research question into a reproducible biomedical analysis with the simplest suitable method. |
| `Grant_Rapporteur_Assistant.md` | Evidence-based grant rapporteur: scores proposal batches against an approved, frozen rubric with auditable spreadsheets. |
| `Editor_Assistant.md` | Evidence-based editor: adjudicates manuscript + reviewer evaluations (and revision rounds) to accept/minor/major/reject with a decision letter and validation workbook. |
| `Narrative_summary.md` | Distills a scientific paper into a coherent intellectual story and assesses substantial study limitations and their likely impact. |
| `Review_digest.md` | Structured digest of biomedical review articles for rapid comparative reading. |

The AI Detector loads `knowledge-base/AI-Detector.md`, its annotated academic companion, and stops rather than analysing text when the required freshness check fails or a material literature update cannot be incorporated.

### Skills

| Skill | Purpose |
|---|---|
| `check-the-data` | Audits experimental units, replication, exclusions, preprocessing, and statistics. |
| `internal-logic-control` | Verifies internal consistency; maps every claim to data and references. |
| `independent-evidence-audit` | Fans out independent auditors over claims, rows, or datasets. |
| `literature-database-mining-audit` | Paper-to-database mining with search logs and extraction audit. |
| `omics-dataset-triage-audit` | Discovers public omics datasets and triages them for reanalysis. |
| `biological-activity-mining` | Finds bioactivity data in PubChem-like sources and prioritizes datasets. |
| `fulltext-manuscript-section` | Writes manuscript sections backed only by full-text-verified sources. |
| `write-review` | Drafts reviews aligned with the target journal's current reviewer guidance. |
| `evidence-workflow-control-loop` | Generic controlled loop producing a researcher decision package. |
| `read-pdf` | Extracts text/markdown from PDFs (poppler, markitdown, OCR) so agents can read attachments; on-demand figure/image extraction. |
| `read-docx` | Extracts text (pandoc) and embedded images (on demand) from DOCX files so agents can read them. |

### Rubrics

- `rubrics/provisional-grant-scoring-rubric.md` — 0–5 anchored criteria, weighted to a 0–100 score; frozen per batch after owner approval.

## Installation

Clone the repo and link (or copy) it into your OpenCode configuration:

```bash
git clone https://github.com/<you>/scholarops.git
# User-wide:
cp -r scholarops/* ~/.config/opencode/agents/
# or per-project:
cp -r scholarops/* /path/to/project/.opencode/agents/
```

Agents and skills are picked up automatically on the next OpenCode start. Skills that ship their own subagents live in `skills/<name>/agents/`.

## Usage

- **AI-text evidence triage:** provide text to the *AI Detector*; it verifies scholarly currency, then reports passage-level suspicion with exact locations and 0–3 ordinal confidence. It does not adjudicate misconduct or produce an overall AI percentage.
- **Peer review:** attach a manuscript and ask the *Reviewer Assistant* for a full review; it chains `literature-database-mining-audit`, `check-the-data`, `internal-logic-control`, `independent-evidence-audit`, and `write-review`.
- **Grant panels:** give the *Grant Rapporteur Assistant* a parent folder with one subfolder per proposal; approve or amend the rubric before any final scores.
- **Data analysis:** hand the *Biostatistician* a CSV/XLSX plus a research question; it clarifies ambiguities before modeling.
- **Paper triage:** pass a PDF to *Narrative Summary* or *Review Digest* for structured, synthesis-first summaries.

## Design principles

1. Rigorous beats clever: the simplest defensible method wins.
2. Independent audit > self-check, whenever the platform allows fan-out.
3. Incomplete input is flagged, never silently filled in.
4. Folder structures and source files are preserved; everything is reproducible in code.
