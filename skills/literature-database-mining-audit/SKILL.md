---
name: literature-database-mining-audit
description: Literature-to-database mining workflow with source acquisition and independent extraction audit. Use when the user wants to turn papers on any research topic into a structured evidence table or database, with search logs, full-text/manual-download status, extraction schema, row-level caveats, and independent auditor review.
---

# Literature Database Mining With Audit

> **Install.** Codex: drop this folder in `~/.codex/skills/`, invoke `$literature-database-mining-audit`. Claude Code: drop it in `~/.claude/skills/` (or a project's `.claude/skills/`), invoke `/literature-database-mining-audit` or let the description auto-trigger it. (`agents/openai.yaml` is Codex-only; Claude Code ignores it.)

Use this skill when the goal is a database-like evidence resource rather than a
plain literature summary.

## Required Workflow

1. Run literature search.
2. Acquire sources and metadata.
3. Define extraction schema.
4. Build database-ready evidence rows.
5. Run independent extraction audit.
6. Produce researcher decision table.

## Search

Create a search log with:

- research question
- search sources
- exact queries
- date searched
- counts
- inclusion/exclusion criteria

Do not mix broad hits with curated rows.

## Acquire

For each candidate record, collect:

- PMID, DOI, PMCID, URL
- title, year, journal
- abstract
- full-text access label
- full-text file path if acquired
- `manual download needed` flag if applicable

Use full text for final extraction when possible. If only abstract-level
information is available, mark row confidence lower.

**Actually save the acquired full text** to a local `fulltext/` folder and make the
acquisition inventory's `full-text file path if acquired` a real path, not a
promise. Extraction and audit read from these files,
and the researcher keeps them to read during the final human review. The agent may
write the files, but the harness verifies each one on disk and updates the
acquisition inventory (never trusting a self-reported "saved"); paywalled records
are stored as abstract-only with a `manual download needed` flag. No paywall
workarounds.

## Define Schema Before Extraction

Create a schema before reading deeply. Use field names that match the topic.

Generic fields:

- `evidence_id`
- `source_id`
- `entity`
- `intervention_or_exposure`
- `method`
- `target_model`
- `outcome`
- `readout`
- `evidence_level`
- `caveat`
- `source_location`
- `audit_status`

Example (swap for your domain): for biomedical delivery topics, typical fields
include source material, isolation method, cargo type, target cell/model, route,
dose, readout, and functional interpretation.

## Extract Rows

Each row should represent one interpretable evidence unit, not a whole paper
summary.

Keep:

- direct quote or paraphrased source note
- location in full text when available
- confidence label (recorded in the `evidence_level` field)
- ambiguity flag (recorded in the `caveat` field)
- reason for inclusion

Separate related but different concepts, such as uptake, delivery, functional
effect, therapeutic effect, and mechanism.

## Independent Extraction Audit (fan out)

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

Give each auditor the schema, its batch of rows, and those rows' source files —
not the intended conclusions.

Audit each row for:

- correct source paper
- correct field mapping
- unsupported field values
- conflated endpoints
- missing caveats
- full-text vs abstract-only support
- row suitability for database inclusion

Use audit labels:

- `pass`
- `minor edit`
- `major revision`
- `manual full-text check`
- `exclude`

## Final Output

Produce:

- search log
- acquisition inventory
- extraction schema
- evidence table
- audit table
- rejected/needs-manual-check list — aggregates both `manual download needed`
  acquisition flags (full text to obtain) and `manual full-text check` audit labels
  (rows to re-verify against full text), plus any `exclude`d rows

End with a short note on what the database can and cannot support.
