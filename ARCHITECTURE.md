# Architecture

This document describes the design of the AI case summarization pipeline (Part 1 of the project). For a shorter overview, see the [README](./README.md#architecture-overview).

## Design Principle

The LLM selects, orders, and phrases facts. It does not originate a value, date, or diagnosis. Every claim in a summary should trace back to a record value, a deterministic rule output, or a cited guideline.

This is why the pipeline uses multiple stages instead of one AI model handling everything. A single model can substitute a qualitative label for a real value, miss a critical finding, or phrase a change the record doesn't actually support, none of which is acceptable in a clinical handover tool.

## Pipeline

![Six phase pipeline architecture](/assets/architecture-diagram.png)

### Phase 1: Intake

Pulls the latest patient record and the previous summary, and derives values needed downstream, such as gestational age.

### Phase 2: Deterministic Detection

The clinical safety layer, no AI involved. Patient data is checked against WHO Labour Care Guide thresholds to flag danger signs and classify the patient as Normal or High-risk. This sets the summary's level of detail and refresh rate (concise, about every 4 hours for Normal; full, about every 2 hours for High-risk). Alerts from this phase go straight into the summary without passing through the LLM.

### Phase 3: AI Background Drafting

An LLM writes the mother's clinical background (prior obstetric history, existing conditions, antenatal complications) from free text notes. This runs once, not every cycle, and is stored and reused across summaries.

### Phase 4: Merger LLM

Assembles the final summary from verified data: current situation, what changed, and active alerts, plus full background for High-risk patients. The Recommendation is always left blank for the clinician.

### Phase 5: Verify (governance)

A deterministic check confirms every number, alert, and change in the summary matches the source data before it reaches a clinician. On failure, the summary is regenerated (up to 2 attempts), then falls back to a rules-only summary with no AI narrative.

### Phase 6: Deliver

The clinician reviews the summary, writes the Recommendation, and signs off. Diagnosis and treatment planning stay with the clinician at every point.

## Why the Summary Format Looks Like This

The structure draws on SBAR, I-PASS, and SOAP, but doesn't fully adopt any of them, since each includes a part (Recommendation, Action List, Assessment/Plan) that requires clinical judgment and isn't appropriate for AI to generate. The result keeps the parts that are safe to automate (situation, background, objective findings, changes, alerts) and leaves diagnosis, planning, and recommendation to the clinician.

## See Also

- [QA_PROCESS.md](./QA_PROCESS.md) for how summaries are evaluated and verified
- [docs/model-setup.md](./docs/model-setup.md) for model options
