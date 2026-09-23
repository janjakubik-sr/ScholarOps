---
name: independent-evidence-audit
description: Independent audit workflow for research artifacts produced by agents. Use when the user wants to fan out independent auditors (one per claim, row, or dataset where feasible) to verify claims, manuscript prose, literature extraction rows, dataset relevance, source acquisition status, caveats, and whether outputs are safe to use scientifically.
---

# Independent Evidence Audit

> **Install.** Codex: drop this folder in `~/.codex/skills/`, invoke `$independent-evidence-audit`. Claude Code: drop it in `~/.claude/skills/` (or a project's `.claude/skills/`), invoke `/independent-evidence-audit` or let the description auto-trigger it. (`agents/openai.yaml` is Codex-only; Claude Code ignores it.)

Use this skill after an agent has produced a literature, manuscript, database,
or dataset-mining artifact. The auditor should verify, not rewrite.

## Audit Principles

- Treat audit as mandatory before scientific use.
- Do not add new claims unless asked.
- Check source support and caveats.
- Separate full-text evidence from abstract-only evidence.
- Flag uncertainty rather than smoothing it away.
- End with use/revise/reject decisions.

## Fan Out To Independent Auditors

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

Reconcile before deciding: any single decisive signal — a `blocker` severity, or an
`unsupported` / `exclude` / `remove` decision — settles the item, and disagreements
are settled by re-checking the source, not by averaging. Carry every blocker and
unresolved disagreement into the decision.

Give each auditor the artifact (or its slice), the source files or metadata, the
audit questions below, and the expected output table format.

## Claim And Prose Audit

Check each important sentence or paragraph:

- Is it supported by a cited source?
- Is the source full text, abstract only, or inaccessible?
- Does the wording overstate model, efficacy, safety, novelty, or mechanism?
- Are limitations preserved?
- Is the citation attached to the correct claim?

Labels:

- `supported`
- `needs softer wording`
- `needs citation`
- `manual full-text check`
- `unsupported`
- `remove`

## Extraction Row Audit

Check each database row:

- source paper and source location are correct
- field values match the paper
- endpoints are not conflated
- evidence level is appropriate
- caveat is present when needed
- row can stand alone in a database

Labels:

- `pass`
- `minor edit`
- `major revision`
- `manual check`
- `exclude`

## Dataset Relevance Audit

Check each dataset candidate:

- topic match
- organism/model relevance
- assay relevance
- controls and sample groups
- raw or processed data availability
- metadata completeness
- reanalysis value

Labels:

- `ready for reanalysis`
- `manual download`
- `metadata only`
- `low relevance`
- `not usable`

## Source Acquisition Audit

Check each acquired source:

- correct access label (`full text` vs `abstract only` vs `not accessible`)
- the saved file actually exists on disk and matches the record (no fetch-and-discard)
- legitimate access only — no paywall workaround
- abstract-only items are not presented as full-text-verified
- items needing manual retrieval carry a DOI link and a save-as filename

Labels:

- `verified`
- `relabel access`
- `file missing — re-acquire`
- `manual download needed`
- `not accessible`

## Audit Table Format

Use this table unless the user provides another format:

| item_id | artifact_type | source_id | audit_question | finding | severity | decision | required_action |
| --- | --- | --- | --- | --- | --- | --- | --- |

Severity:

- `blocker`: output should not be used.
- `major`: revise before use.
- `minor`: safe after small correction.
- `note`: no action required.

Decision: take the value from the matching audit section's label set above
(`supported`/…, `pass`/…, `ready for reanalysis`/…, `verified`/…) or from the
Decision Summary set below — the row's `artifact_type` says which set applies.

## Decision Summary

End with:

- safe to use now
- safe after revision
- requires manual download or human check
- exclude or remove
- unresolved questions for the researcher
