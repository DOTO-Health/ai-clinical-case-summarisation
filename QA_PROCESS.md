# QA Process

This document describes how AI generated summaries are, and will be, evaluated for clinical safety, factual grounding, and quality. It reflects the intended QA approach for this solution. Parts of it are already in use during prototyping (mentioned below), and the rest will be built out as the pipeline is implemented.



This is a data-to-text generation task, not general purpose summarization: the source is a structured or semi-structured clinical record, and there is a hard constraint that every claim in a summary must trace to a record value, a rule output, or a cited guideline. That framing is why purpose built evaluation metrics fit better here than generic summarization benchmarks, as described in the four tiers below.

## Runtime Verification (Phase 5 of the Pipeline)

Before any AI generated summary reaches a clinician, it passes through a deterministic verification step. This is not another AI model grading the first one; it checks that:

- every numeric value in the summary exactly matches the source record
- every alert the rules engine raised is present in the summary
- identified changes since the previous summary are accurate
- no clinical information appears that cannot be traced to a source

On failure, the summary is regenerated, up to two attempts. If it still fails, the system falls back to a rules only summary with no AI narrative, and the clinician is told the fallback was used. See [ARCHITECTURE.md, Phase 5](./ARCHITECTURE.md#phase-5-verify-governance) for the full description.

Status: implemented. A grounding checker has been built and used across prompt iterations during prototyping (see below).

## Two Tier Evaluation Framework

Used to evaluate the summarization model itself, separate from the runtime verification step above, which checks every summary in production.

| Tier | What it checks | Method |
|---|---|---|
| 1. Deterministic checks | Exact factual correctness | Slot Error Rate, numeric and date exact match scripting, risk stratified completeness ratio |
| 2. Semantic entailment | Whether the summary's meaning follows from the source, even when phrasing differs | Self hosted scorers |
|

Tiers 1 and 2 determine whether a summary is safe to show a clinician. 

Status: framework designed. Tier 1 grounding checks are implemented and in use. Tier 2 planned as the pipeline moves past prototyping

## Manual annotation schema

Human reviewers (inter-rater checks across at least two reviewers) tag each claim in a summary
against source data using a seven-tag schema:

- `grounded-verbatim` — matches the source exactly
- `grounded-paraphrased` — same meaning, different wording
- `distorted` — meaning changed from source
- `fabricated` — not supported by any source
- `omitted-critical` — a clinically important fact is missing
- `omitted-minor` — a non-critical fact is missing
- `qualitative-substitution` — a real value replaced with a label like "stable" or "normal"
- `scope-violation` — the summary includes diagnosis, prediction, or recommendation content
  that should have been left to the clinician


## Test set strategy

Evaluation uses a diverse test set across risk states (Normal / High-risk), visit counts,
obstetric complexity, and multilingual free-text fields — not just "easy" cases — since edge
cases are exactly where grounding failures are most likely and most dangerous.

## What's next

- Wire Tier 2 into the evaluation harness
- Formalize the inter-rater review process into a repeatable checklist
- Publish evaluation results against the finalized model and pipeline once implementation lands
