# Contributing

Thanks for your interest in this project. It's an early-stage, pre-alpha repository the
architecture ([ARCHITECTURE.md](./ARCHITECTURE.md)) is settled, but implementation is still in
progress, so please calibrate expectations: there's no functioning pipeline to run yet, and
APIs/interfaces described in the docs may still change.

All contributors are expected to follow the [Code of Conduct](./CODE_OF_CONDUCT.md).

## Before you start

- Read [ARCHITECTURE.md](./ARCHITECTURE.md) in particular the design principle that the LLM
  may only select, order, and phrase facts, and never originate a value, date, or diagnosis.
  Any contribution touching the summarization pipeline needs to preserve this.
- Check [Status & Roadmap in the README](./README.md#status--roadmap) for what's already
  decided vs. still open.
- For anything non-trivial, open an issue first to discuss the approach before submitting a PR.

## Development setup

Pipeline code hasn't been published yet once it lands, this section will cover full local
setup. In the meantime, note the project's model policy: this project **never commits model
weights**. Models are loaded from the Hugging Face Hub at setup time and configured locally.
See [model-setup.md](model-setup.md).

## How to contribute

1. Fork the repository and create a branch off `main`:
   ```sh
   git checkout -b feature/short-description
   ```
2. Make your change. Keep commits scoped and use clear commit messages.
3. If your change touches the summarization pipeline, rules engine, or verification logic,
   include or update tests demonstrating the grounding invariant still holds (see
   [QA_PROCESS.md](./QA_PROCESS.md)).
4. Push your branch and open a Pull Request against `main`.

## Pull request review

Since there's no CI pipeline wired up yet, PR review is manual. A reviewer checks:

- **Grounding:** does this change let the LLM originate a value, date, or diagnosis instead of
  sourcing it from the record, a deterministic rule, or a cited guideline?
- **Scope:** does the change stay within Part 1 (pipeline + API)? Part 2 (Dashboard) is a
  separate, later phase and out of scope for this repository.
- **Clarity:** is the change documented code comments, and a docs update if it affects
  architecture, setup, or the QA process?

Automated CI (linting, tests) is planned as the codebase grows past the current prototyping
stage; until then, please note in your PR description what you tested manually and how.

## Documentation changes

Root-level files (`README.md`, `ARCHITECTURE.md`, `QA_PROCESS.md`, `PROJECT_CHARTER.md`) are
the source of truth. Pages under `docs/` are meant to be short and link back to the root files
rather than duplicate their content please keep that pattern when editing docs.

## Questions

Open an issue, or see the [Project Charter](./PROJECT_CHARTER.md) for project scope and
stakeholders.
