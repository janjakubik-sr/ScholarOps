---
name: internal-logic-control
description: Verify that a manuscript or research artifact is internally consistent and that every substantive claim is supported by the presented data, analysis, and cited references. Use for claim-level logic audit before writing a peer review.
---

# Internal Logic Control

## Workflow

1. Extract the central research question, hypotheses, declared contribution, principal conclusions, and all decision-relevant claims.
2. Build a claim map linking each claim to its supporting figure, table, analysis, method, and reference. Split compound claims into independently testable parts.
3. Test internal consistency across abstract, introduction, methods, results, figures, tables, legends, discussion, limitations, and conclusion. Flag conflicting sample sizes, labels, directions of effect, time points, definitions, endpoints, and interpretations.
4. Classify support for each claim as `supported`, `partially supported`, `unsupported`, `contradicted`, or `not verifiable`.
5. Check inferential validity: association versus causation, direct versus indirect evidence, mechanism versus phenotype, subgroup versus population claims, exploratory versus confirmatory analysis, absence of evidence versus evidence of absence, and statistical versus practical significance.
6. Verify reference support at claim level. Distinguish full-text verification from abstract-only or metadata-only checks. Flag citations that are irrelevant, too weak, mischaracterized, outdated where recency matters, or unable to support the scope of the sentence.
7. Check whether limitations and alternative explanations are acknowledged in proportion to their impact.
8. Prioritize issues that would change the main conclusion, interpretation, novelty, or reader understanding. Do not rewrite the manuscript during the audit.

## Output Contract

- **Claim-support matrix:** claim, location, data support, reference support, verdict, caveat.
- **Contradiction log:** conflicting statements or artifacts with exact locations.
- **Reasoning issues:** overreach, missing premise, alternative explanation, or scope mismatch.
- **Decision summary:** claims safe to retain, claims needing revision, and claims that should be removed unless new evidence is supplied.

## Boundaries

- Preserve uncertainty and legitimate alternative interpretations.
- Never fabricate a citation check or imply full-text verification without full text.
- Keep scientific disagreement separate from demonstrable inconsistency or unsupported inference.
