---
title: Model Setup
---

# Model Setup

This solution does not require one specific way of running a model. A set of open models is being evaluated and will be published as recommended, tested defaults, but a deployer can also plug in an API based model instead if that fits their setup better.

## Recommended: Local Models

For hospitals and deployments that want to keep patient data on their own infrastructure, the solution recommends running an open model locally. No model weights are committed to this repository, see `.gitignore`. Recommended models are downloaded from the Hugging Face Hub at setup time.

Example pattern, exact instructions will be published alongside the pipeline code:

```sh
huggingface-cli download <org>/<model-name> --local-dir ./models/<model-name>
```

The pipeline reads the model location from a config value, a local path or a Hugging Face repo id, so switching models will not require code changes.

Prototyping so far has used [vLLM](https://github.com/vllm-project/vllm) for local serving. This is not locked in as the final choice, it is what has been used to validate the pipeline design so far.

## Alternative: API Based Models

Deployments that prefer not to host a model themselves will be able to point the pipeline at an API based model instead. This is planned as a configuration option rather than a separate codebase, so switching between a local model and an API should be a config change, not a rewrite. Exact setup instructions for this path will be published alongside the pipeline code and the API integration guide.

## Models Under Evaluation

No model has been finalized yet. Models currently being compared for clinical summarization quality include:

- OpenBioLLM-8B, used for the initial pipeline prototype and prompt iteration testing
- MedGemma
- Qwen 3
- Llama 4

This list covers local, open weight models. API based options are being considered separately and will be added here once decided.

Evaluation follows the approach in [QA_PROCESS.md](../QA_PROCESS.md). Factual grounding (Tiers 1 to 3) is weighted more heavily than narrative fluency (Tier 4) when comparing candidates.

## What's Next

Once a model approach is finalized and the pipeline code is published, this page will be updated with exact setup commands for both the local and API paths, plus a guide for connecting a chosen model to the pipeline's API and summary endpoints.

[Back to docs home](./index.md)
