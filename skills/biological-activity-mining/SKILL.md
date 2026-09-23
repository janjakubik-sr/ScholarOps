---
name: biological-activity-mining
description: Use when the task is to find biological activity data for one or more queried compounds, inventory candidate datasets from PubChem or related pharmacology sources, assign reanalysis priority labels with reasons, run an independent dataset audit, and recommend whether to reanalyze, retrieve manually, or exclude each candidate.
---

# Biological Activity Mining

## Purpose

Use this skill for compound-centered biological activity mining tasks where the user wants structured, auditable evidence about affinity, potency, efficacy, bias, or related assay results.

This workflow is for finding candidate activity datasets and deciding which ones are worth reanalysis. It is not a shortcut for making unsupported claims about compound activity. Treat missing metadata, missing files, inaccessible records, or weak assay fit as real blockers.

## When To Use

Use this skill when the request is to:
- search for biological activity evidence for one or more compounds
- mine PubChem, Guide to Pharmacology, GPCRdb, or closely related sources for assay and dataset records
- build an inventory of candidate biological activity datasets
- prioritize candidate datasets for reanalysis versus manual retrieval
- audit whether candidate datasets actually match the biological question

Do not use this skill for manuscript drafting, general literature summaries, omics repository triage, or evidence-table work that is better handled by a different attached workflow.

## Expected Inputs

Start by identifying:
- the queried compound or compound set
- the biological question or target question
- any target, receptor family, organism, assay class, or endpoint constraints
- whether the user wants only public downloadable data or is willing to consider manual retrieval

If the biological question is underspecified, ask the minimum needed to judge relevance, such as the target, organism, or assay context.

## Core Workflow

### 1. Search activity sources

Search for biological activity data for each queried compound.

Use this source order by default:
1. PubChem: `https://pubchem.ncbi.nlm.nih.gov/`
2. Guide to Pharmacology: `https://www.guidetopharmacology.org/`
3. GPCRdb: `https://gpcrdb.org/`

For each candidate record, capture enough source detail to revisit it later. Preserve identifiers, assay names, target names, organism/model context, measurement type, and any obvious file or supplementary-data availability.

Look specifically for evidence about:
- affinity
- potency
- efficacy
- bias
- related biological activity endpoints

Do not overstate what a source contains. Distinguish clearly between:
- a database entry with analyzable data files
- a metadata record that points to an assay or paper
- a source that is informative but not actually reusable for reanalysis

### 2. Acquire dataset metadata and file availability

For each candidate dataset or assay record, collect the metadata needed to decide whether it can support reanalysis.

Capture when available:
- source database and stable identifier
- queried compound name and any synonyms used in the record
- target or biological system
- organism or model
- assay and measurement type
- sample or condition structure, including case/control or treatment/control when relevant
- file availability status: raw data, processed data, supplementary files, metadata only, or inaccessible
- linked paper or methods availability
- any obvious blockers or caveats

If a record requires manual supplement retrieval, note that explicitly instead of pretending the files are already available.

### 3. Build the dataset inventory and assign priority labels

Produce a dataset inventory that covers all credible candidates, not just the easy-to-download ones.

Assign exactly one primary priority label to each candidate:
- `ready for reanalysis`: relevant model, usable controls, sufficient metadata, and raw or processed data available
- `manual download`: relevant but requires manual supplement or file retrieval
- `metadata only`: accession or record exists but data are insufficient for analysis
- `low relevance`: accessible but weakly aligned with the research question
- `not usable`: missing files, missing metadata, inaccessible, or wrong assay

Add a reason for every label.

Do not promote a dataset only because it is easy to download.

### 4. Score relevance against the user question

Evaluate each candidate against the user's biological question using these criteria:
- direct match to the biological topic
- relevant organism or target model
- relevant assay and measurement
- clear case/control or treatment/control structure
- adequate sample metadata
- adequate sample count for intended analysis
- raw data or usable processed matrix available
- linked paper or methods sufficient for interpretation

Use these criteria to justify the priority label and the final recommendation. When a criterion cannot be checked, say so.

### 5. Run an independent dataset audit

Audit is mandatory before any artifact is treated as ready to use.

Run the audit as independent auditors, not one self-check. Fan out as broadly as the platform allows. Ideally, use one independent auditor per unit of work such as a dataset, row, or source slice, in parallel; at minimum, use at least two independent audit passes so findings can cross-check.

Rules for independence:
- give each auditor only its assigned slice and that slice's sources
- do not provide the intended conclusion
- do not provide a shared draft
- treat independence as necessary for meaningful agreement

If true parallel auditors are available, use them. Otherwise, run independent sequential passes that each start fresh from the artifact and source material. If only one pass is possible, make it an explicit adversarial review that re-reads the evidence as a hostile reviewer.

Audit questions for each assigned candidate:
- Does the dataset truly match the research question?
- What kind of evidence is it: molecular profiling, perturbation/response, mechanism, or unrelated?
- Does that evidence type actually bear on the research question?
- Are controls and sample groups interpretable?
- Are raw or processed files available enough for the proposed analysis?
- Would reanalysis actually change a conclusion, or only add a dataset that looks relevant?

Reconcile before deciding:
- any single blocker, unsupported finding, or exclude decision is decisive until re-checked against the source
- resolve disagreements by re-checking the source, not by averaging votes
- carry every blocker and unresolved disagreement into the final decision

### 6. Recommend next action

End with a researcher-facing recommendation that distinguishes:
- reanalyze now
- manual retrieval needed first
- exclude from current scope

Recommend the single best dataset to reanalyze first and explain why it wins over the other candidates.

State clearly when the next step is manual download rather than analysis.

## Output Contract

Produce these artifacts for the run:
- `dataset_search_log.md`
- `dataset_inventory.csv`
- `reanalysis_priority_table.csv`
- `dataset_audit_table.csv`
- a short recommendation naming the single dataset to reanalyze first, and why

Minimum expectations for each artifact:

### `dataset_search_log.md`
Include:
- the queried compounds
- the biological question
- source-by-source search notes
- exact search terms or navigation trail when recoverable
- key inclusion and exclusion observations
- unresolved access or metadata blockers

### `dataset_inventory.csv`
Include one row per candidate with fields covering at least:
- compound
- source database
- source identifier
- target or biological system
- organism/model
- assay type
- measurement type
- file availability status
- linked paper or methods status
- preliminary relevance notes

### `reanalysis_priority_table.csv`
Include one row per candidate with at least:
- source identifier
- priority label
- reason for label
- strongest relevance criteria met
- main blocker or caveat
- recommended next action

### `dataset_audit_table.csv`
Include one row per audited candidate with at least:
- source identifier
- auditor or audit pass label
- audit verdict
- evidence type classification
- controls interpretable yes/no
- data availability sufficient yes/no
- decisive blockers
- disagreements or follow-up checks

## Quality Bar

- Preserve exact source distinctions and do not merge metadata-only records with downloadable datasets.
- Keep unsupported assumptions out of the inventory.
- Label missing files, missing metadata, and manual retrieval needs explicitly.
- Do not let convenience outweigh relevance.
- Do not treat the audit as optional or as a single lightweight self-check.
- Do not present a dataset as ready for reanalysis unless the evidence supports that claim.

## Example Trigger Prompts

- "Find biological activity datasets for compound X at receptor Y and tell me which one to reanalyze first."
- "Mine PubChem and related pharmacology databases for affinity and efficacy datasets on these ligands, then rank the candidates for reanalysis."
- "Build an auditable inventory of public biological activity records for this compound set and exclude anything that is only metadata."
