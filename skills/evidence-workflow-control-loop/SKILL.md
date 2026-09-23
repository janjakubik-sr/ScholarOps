---
name: evidence-workflow-control-loop
description: Controlled research workflow for literature, manuscript, evidence-table, or dataset-mining projects. Use when the user wants a reproducible agent workflow that runs search, acquires sources or data, builds auditable artifacts, runs independent audit by fanning out as many auditors as the platform allows (parallel subagents or repeated independent passes), and produces a researcher decision package.
---

# Evidence Workflow Control Loop

> **Install.** Codex: drop this folder in `~/.codex/skills/`, invoke `$evidence-workflow-control-loop`. Claude Code: drop it in `~/.claude/skills/` (or a project's `.claude/skills/`), invoke `/evidence-workflow-control-loop` or let the description auto-trigger it. (`agents/openai.yaml` is Codex-only; Claude Code ignores it.)

Use this skill to organize a research task into a controlled, auditable workflow.
Apply the same loop to manuscript work, literature-to-database mining, and
dataset availability mining.

## Core Loop

Use this sequence unless the user explicitly requests a narrower task:

1. Run search.
2. Acquire sources or data.
3. Build artifacts.
4. Run independent audit.
5. Prepare researcher decision output.

Do not treat generated prose, extracted rows, or dataset candidates as final
until they pass audit.

## Step 1: Run Search

Create a search plan before searching:

- Research question and scope.
- Inclusion/exclusion criteria.
- Databases or repositories to search.
- Exact query strings.
- Date searched.

Save raw results when possible. Keep broad retrieval separate from curated
inclusion.

## Step 2: Acquire

Acquire what the downstream task needs:

- For literature: download legally available full text, XML, HTML, or PDF.
- For inaccessible papers: mark `manual download needed`, `abstract only`, or
  `not usable`.
- For datasets: collect accession metadata, sample tables, linked publications,
  raw-data status, and file locations.

**Persist what you acquire — do not fetch-and-discard.** Save each legally
available full text to a dedicated `fulltext/` folder (one file per source, e.g.
`PMID<id>__PMC<id>.pdf`, with a short header: title, authors, DOI, access status)
and record its path in the inventory. The researcher must be able to open and read
the actual sources, and downstream readers/auditors should work from these local
files rather than re-fetching. An acquire agent may write the file itself, but
the harness owns the guarantee: after acquisition, **verify on disk that each
expected file actually exists** (size > 0, header intact) and record each saved
file's path in `source_inventory.csv`, reconciled against the real `fulltext/`
directory listing — never trust an agent's self-reported "saved" flag. That keeps the corpus complete, consistently named,
and the run reproducible offline. Paywalled sources are saved as abstract-only
with a `manual download needed` flag and a DOI link; never use paywall workarounds.

Acquire PDFs through legal sources in order, and **prefer the PDF-with-figures for
the human-readable corpus** (the researcher must be able to read the actual paper —
figures, gels, tables — not just a text layer): (1) the PMC/EuropePMC open-access
render (`https://europepmc.org/articles/<PMCID>?pdf=render`), which bundles figures;
(2) **Unpaywall** (`api.unpaywall.org/v2/<DOI>?email=...`) — a legitimate finder of
*legally free* copies only (publisher bronze/gold or repository green versions), NOT
a paywall bypass; take its `url_for_pdf` and byte-verify the result; (3) if no free
PDF exists, save the abstract and add the DOI to `to_download_manually.md` for
the researcher's institutional access. Keep any text/XML you fetched for machine
extraction, but the readable PDF is what the human review needs.

Make the researcher hand-off explicit and reversible. Each `manual download needed`
item in `to_download_manually.md` carries an exact **save-as filename** keyed to its ID;
the researcher obtains the full text through institutional access, drops it into the
`fulltext/` folder under that name, and a re-scan flips it to available and updates
`source_inventory.csv` — downstream readers/auditors then use it automatically. Acquisition is
**idempotent**: never re-fetch or overwrite a paper that already has a local PDF, so
a manually supplied file is always respected.

Never imply that a source was full-text checked when only the abstract was
available.

## Step 3: Build Artifacts

Create durable outputs rather than only a narrative answer. Always build
`search_log.md`; then build the artifacts your task needs and skip the ones it
does not. Typical artifacts:

- `search_log.md`: databases, exact queries, counts, date.
- `source_inventory.csv`: papers or datasets, access status, saved-file path, notes.
- `to_download_manually.md`: sources with no legal free full text — DOI link plus the
  exact save-as filename for the researcher to supply (the human hand-off list).
- `evidence_table.csv`: extracted fields and source links.
- `claim_matrix.csv`: claim, evidence, source, caveat, confidence.
- `dataset_inventory.csv`: accession, model, assay, controls, raw-data status.
- `audit_log.csv`: one row per audited unit — auditor, finding, severity, source-check, decision.
- `draft.md`: prose generated only from the artifact set.

Prefer tables with stable IDs so later audits can cite exact rows.

## Step 4: Independent Audit (fan out)

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

Audit checks should ask:

- Is every claim or row supported by the cited source?
- Were full-text and abstract-only sources separated?
- Are caveats and model limitations preserved?
- Are weak, ambiguous, or inaccessible items flagged?
- Would the artifact mislead a reader if used as-is?

## Step 5: Researcher Decision Output

End with a decision package:

- What is ready to use.
- What needs manual download or human confirmation.
- What should be excluded.
- What can be used only as weak background.
- What needs another audit or reanalysis before use.

## Minimal Output

For a small project, collapse the loop to five files: a search log, an
acquisition inventory, one main artifact (the table or draft for your task), one
audit table, and the researcher decision summary.
