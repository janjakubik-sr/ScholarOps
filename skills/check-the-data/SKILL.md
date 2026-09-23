---
name: check-the-data
description: Audit whether manuscript data were handled appropriately, including experimental units, sample size, technical versus biological replication, independent experiments, animal numbers, exclusions, preprocessing, and statistical analysis. Use for peer review, methods/results audit, or data-validity checks.
---

# Check the Data

## Workflow

1. Identify the experimental unit for every analysis. Distinguish technical replicates, biological replicates, independent experiments, animals, litters, cultures, fields, cells, and repeated measurements. Flag pseudoreplication and unclear nesting.
2. Build a compact design inventory for each figure, table, or principal endpoint: groups, unit of analysis, stated `n`, what `n` represents, independent repeats, exclusions, missing data, and statistical test.
3. Check data handling: acquisition, normalization, transformations, outlier rules, blinding, randomization, batch handling, repeated-measures structure, aggregation, and selective exclusions. Separate missing reporting from demonstrated error.
4. Assess whether sample size is justified and adequate for the claim. Look for power calculations or precision targets, plausible effect sizes, attrition, imbalance, and unusually small `n`. Do not invent a universal minimum; judge adequacy from design, variability, endpoint, model, and claim strength.
5. For animal studies, check numbers of animals and independent cohorts, sex and relevant biological variables, randomization, blinding, exclusions, experimental unit, welfare-related attrition, and whether multiple samples from one animal were wrongly treated as independent.
6. Audit statistics against the design and data type: assumptions, paired versus unpaired structure, repeated measures, nesting, distribution and scale, multiplicity, post-hoc tests, covariates, effect sizes, confidence intervals, exact `n`, exact tests, and consistency between text, legends, tables, and plots.
7. Check whether conclusions match the analysis. Statistical significance alone does not establish biological importance, equivalence, absence of effect, mechanism, or causality.
8. Mark each issue as `critical`, `major`, `minor`, or `reporting clarification`. Give the evidence location and the smallest corrective action or information needed.
9. If raw data, code, protocols, or key metadata are unavailable, state what cannot be verified. Never infer that statistics are correct merely because a test name is present.

## Output Contract

- **Design inventory:** one row per key experiment or endpoint.
- **Data-handling findings:** issue, location, severity, rationale, required correction.
- **Statistical findings:** test/design match, assumptions, multiplicity, reporting, effect-size interpretation.
- **Adequacy decision:** adequate, potentially adequate with clarification, requires reanalysis, or not supportable from supplied material.
- **Reviewer-ready comments:** concise, actionable wording that does not overstate certainty.

## Boundaries

- Do not fabricate sample sizes, power, assumptions, or analysis details.
- Do not demand a specific statistical method when several valid approaches exist; explain the design requirement the method must satisfy.
- Treat unclear reporting as unclear reporting unless the artifact demonstrates incorrect handling.
