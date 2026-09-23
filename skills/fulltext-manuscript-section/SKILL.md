---
name: fulltext-manuscript-section
description: Full-text-backed manuscript writing workflow for any section the user names (Introduction, Discussion, Results narrative, and so on). Use when the user wants agents to search literature, acquire legally available full texts, mark unavailable papers for manual download, have independent agents read full text, build that section's narrative from supported claims, and audit the final prose.
---

# Full-Text Manuscript Section

> **Install.** Codex: drop this folder in `~/.codex/skills/`, invoke `$fulltext-manuscript-section`. Claude Code: drop it in `~/.claude/skills/` (or a project's `.claude/skills/`), invoke `/fulltext-manuscript-section` or let the description auto-trigger it. (`agents/openai.yaml` is Codex-only; Claude Code ignores it.)

Use this skill to draft or revise any manuscript section the user names —
Introduction, Discussion, Results narrative, a rebuttal, and so on — from
acquired literature, not from unscreened search snippets. The user states which
section and what it must accomplish; the workflow supplies the evidence and the
guardrails.

## Required Workflow

1. Confirm the target section and its goal with the user.
2. Run literature search.
3. Acquire full text where legally available.
4. Build claim artifacts from acquired sources.
5. Run independent full-text reading and claim audit.
6. Draft or revise the section.

## Search

Start with a manuscript-facing question:

- What is the biological or technical context?
- What evidence does this section need?
- What must this section establish — gap, rationale, interpretation,
  comparison, or limitation?

Save:

- databases searched
- exact queries
- counts
- inclusion/exclusion criteria
- ranked candidate records

## Acquire Full Text

For each relevant record, attempt legal acquisition:

- PMCID full text
- publisher open-access HTML or PDF
- repository-hosted accepted manuscript
- user-provided PDF

Label each item with its access status — one of:

- `full text acquired`
- `abstract only`
- `not accessible`
- `excluded`

and add the `manual download needed` flag to any item the researcher should obtain in
full — it co-occurs with an access status (e.g. `abstract only` + `manual download needed`).

Do not use paywall workarounds. Do not present abstract-only claims as if they
were verified in the full text.

**Save every acquired full text to a local `fulltext/` folder** (one file per
source, named by ID, with a header carrying title/authors/DOI/access label) and
point the `fulltext_inventory.csv` `file_path` column at the real saved file. The independent readers below read these
local files; the researcher keeps the folder to read the sources directly during
the final human review. The acquire agent may write the file, but the harness owns
the guarantee — it verifies each file exists on disk and builds `fulltext_inventory.csv`,
never trusting a self-reported "saved" — so nothing is silently skipped and the corpus
is reproducible. Paywalled items are saved as abstract-only with a `manual download
needed` flag.

## Artifacts

This skill produces four artifacts, each filled at its own stage (not all up
front):

- `fulltext_inventory.csv` (after acquisition): PMID/DOI, title, access label,
  file path, notes.
- `claim_matrix.csv` (filled during independent reading): claim, source, source
  section, evidence type, caveat.
- `section_outline.md` (before drafting): what this section must establish, in
  order, with the claims that support each point.
- `draft_section.md` (the drafting step): prose with citation markers.

Each claim should be traceable to a source and section.

## Independent Reading (fan out)

Fan out readers as widely as the platform allows: ideally one independent reader
per acquired full text, in parallel, so no single reader colors the whole corpus.
Give each reader only its source(s) and the extraction task, never the intended
conclusion. On Claude Code use parallel Task/Agent subagents (an Ultracode
dynamic Workflow when the corpus is large); on Codex use parallel subagents or
independent passes; with no subagents, read each source in its own explicit pass.

Reader extraction fields:

- background claim
- mechanistic claim
- methodological limitation
- model or species limitation
- wording caveat
- exact source location
- confidence: high, medium, low

Compare independent reader outputs before drafting. Resolve conflicts in the
claim matrix rather than hiding them in prose.

## Drafting Rules

Draft only from the claim matrix and outline.

Use cautious wording when:

- evidence is preclinical
- methods, materials, or models differ across papers
- only abstracts are available
- papers support plausibility but not efficacy

Avoid novelty, efficacy, safety, or clinical claims unless the full text
directly supports them.

When the section needs a fact that no acquired source supports, insert a
`[CITATION NEEDED]` placeholder for the author to supply — never invent a citation.

## Independent Prose Audit (fan out)

Audit the finished prose with independent auditors, fanned out as widely as the
platform allows (parallel subagents on Claude Code or Codex; an explicit
adversarial pass if none are available). The audit should:

- Map each sentence to a claim row or source.
- Flag unsupported, overstated, vague, or abstract-only statements.
- Check whether caveats survived the drafting step.
- Mark required manual checks.

Final deliverable: `draft_section.md` plus an audit table (e.g. one row per
sentence: mapped claim/source, verdict, caveat preserved?, manual check needed?),
shipped alongside `fulltext_inventory.csv`, `claim_matrix.csv`, and
`section_outline.md`.
