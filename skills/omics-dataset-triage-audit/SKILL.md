---
name: omics-dataset-triage-audit
description: Public omics dataset discovery and reanalysis triage workflow. Use when the user wants agents to search repositories such as GEO, SRA, ENA, BioStudies, ArrayExpress, PRIDE, or metabolomics resources, acquire metadata and sample/run information, label datasets for reanalysis or manual download, and run independent relevance audit before analysis.
---

# Omics Dataset Triage With Audit

> **Install.** Codex: drop this folder in `~/.codex/skills/`, invoke `$omics-dataset-triage-audit`. Claude Code: drop it in `~/.claude/skills/` (or a project's `.claude/skills/`), invoke `/omics-dataset-triage-audit` or let the description auto-trigger it. (`agents/openai.yaml` is Codex-only; Claude Code ignores it.)

Use this skill to decide whether public omics datasets are relevant and usable
for a research question before starting reanalysis.

## Required Workflow

1. Run repository search.
2. Acquire dataset metadata and file availability.
3. Build dataset inventory and priority labels.
4. Run independent relevance audit.
5. Recommend reanalysis, manual retrieval, or exclusion.

## Search

Choose repositories based on assay:

- GEO / SRA / ENA for transcriptomics and sequencing.
- BioStudies / ArrayExpress for expression studies.
- PRIDE / ProteomeXchange for proteomics.
- MetaboLights / Metabolomics Workbench for metabolomics.

Use synonym families:

- topic terms
- organism/model terms
- assay terms
- intervention/exposure terms
- accession terms from linked papers

Save exact queries, dates, counts, and API endpoints where possible.

## Acquire Metadata

For each candidate dataset, collect:

- accession
- repository
- title
- linked publication
- organism/model
- assay type
- sample count
- sample groups and controls
- raw-data status
- processed-data status
- file links or download instructions
- license/access notes

Do not download large raw files unless the user explicitly asks for reanalysis.
For this skill, acquisition usually means metadata, sample tables, and file
availability.

## Priority Labels

Assign one primary label:

- `ready for reanalysis`: relevant model, usable controls, sufficient metadata,
  and raw or processed data available.
- `manual download`: relevant but requires manual supplement/file retrieval.
- `metadata only`: accession exists but data are insufficient for analysis.
- `low relevance`: accessible but weakly aligned with the question.
- `not usable`: missing files, missing metadata, inaccessible, or wrong assay.

Add a reason for every label.

## Relevance Criteria

Score datasets against the user question:

- direct match to biological topic
- relevant organism or target model
- relevant assay and measurement
- clear case/control or treatment/control structure
- adequate sample metadata
- adequate sample count for intended analysis
- raw data or usable processed matrix available
- linked paper/methods sufficient for interpretation

Avoid promoting a dataset only because it is easy to download.

## Independent Dataset Audit (fan out)

Audit is mandatory before any artifact is used, and it runs as independent
auditors — not one self-check. Fan out as widely as the platform allows: ideally
one independent auditor per unit of work (claim, row, dataset, source), in
parallel, at least two so findings cross-check. Give each auditor only its slice
and that slice's sources, never the intended conclusion, never a shared draft —
independence is what makes agreement mean anything. How to partition is your
call; the goal is maximum independent coverage.

- Claude Code: spawn auditors in parallel with the Task/Agent tool. For a large
  or contested audit, run them as an Ultracode dynamic Workflow that fans out
  across units, re-audits only what stays flagged or contested, and votes.
- Codex / single-agent: parallel subagents if available, else independent
  sequential passes, each starting fresh from the artifact and sources. If only
  one pass is possible, still make it an explicit adversarial pass — stop
  generating, re-read against sources, review as a hostile reviewer.

Reconcile before deciding: any single `blocker`/`unsupported`/`exclude` is
decisive, and disagreements are settled by re-checking the source, not by
averaging. Carry every blocker and unresolved disagreement into the decision.

Give each auditor the dataset inventory and repository metadata for its share of
the candidates. Audit questions:

- Does the dataset truly match the research question?
- What kind of evidence is it (molecular profiling, perturbation/response,
  mechanism, or unrelated), and does that kind bear on the research question?
- Are controls and sample groups interpretable?
- Are raw or processed files available enough for the proposed analysis?
- Would reanalysis actually change a conclusion, or only add a dataset that
  looks relevant?

## Final Output

Produce:

- `dataset_search_log.md`
- `dataset_inventory.csv`
- `reanalysis_priority_table.csv`
- `dataset_audit_table.csv`
- short recommendation: the single dataset to reanalyze first, and why

State clearly when the next step is manual download rather than analysis.
