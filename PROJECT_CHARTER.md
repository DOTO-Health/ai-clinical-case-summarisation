PROJECT CHARTER — AI Case Summarization

What this project is

AI Case Summarization is an open-source tool that turns lengthy maternal clinical records intrapartum case sheets, vitals, labs, CTG/NST readings, medications, and clinician notes into concise, structured summaries (Flash Cards) for clinical review.

The problem it solves: labour-room clinicians routinely lose time piecing together a patient's status from fragmented documentation during shift handovers, consultant rounds, emergency referrals, and routine reassessments. This project automates that synthesis without replacing clinical judgment.

The design is deliberately hybrid, not a single end-to-end LLM call:

- Deterministic clinical rules (based on frameworks such as the WHO Labour Care Guide) classify patient status and raise alerts. These rules are reproducible and auditable by design identical inputs always produce identical outputs.
- AI (LLM) generation is used only to organize and phrase verified information into readable prose for background history and for assembling the final summary. It does not diagnose, predict risk, or recommend treatment.
- Independent verification checks every AI-generated summary against source data before it reaches a clinician; if verification fails, the system falls back to a deterministic, rules-based summary rather than presenting unverified text.

The intent is a documentation assistant, not a decision-maker. Diagnosis, treatment planning, and sign-off remain with the treating clinician.

Why Apache License 2.0

We chose Apache 2.0 for three reasons that matter specifically for a clinical-adjacent tool:

1. Permissive adoption in hospital and health-tech settings: Apache 2.0 lets hospitals, EHR vendors, and health-tech companies including those building proprietary or commercial products adopt, modify, and redistribute the code with minimal legal friction. Clinical tooling only helps patients if it's actually deployed; a copyleft license would deter the commercial and institutional integrations this project depends on for real-world reach.
2. Explicit patent grant: Apache 2.0's built-in patent license protects users and contributors from patent-infringement claims tied to contributed code, and includes a defensive termination clause if a contributor initiates patent litigation. In a healthcare context, where patent risk around clinical algorithms is a real concern, this protection is valuable to both the project and downstream adopters.
3. Clear attribution and modification tracking. Apache 2.0 requires derivative works to state the changes made, which preserves a traceable lineage of the classification and summarization logic important for a tool that touches clinical workflows and may eventually intersect with regulatory scrutiny.

We are not a copyleft (e.g., GPL/AGPL) project because we want hospitals and vendors to be able to embed this into closed clinical systems without being compelled to open-source their surrounding stack a common adoption blocker in health tech.

How decisions get made

- Day-to-day changes (bug fixes, refactors, non-clinical features): standard pull-request review. Requires approval from at least one maintainer before merge.
- Changes to clinical logic (rule thresholds, alert criteria, what appears in a summary, verification logic): require both maintainer code review **and** sign-off from a clinical advisor. This dual-review gate exists because errors here have direct patient-safety implications it is the one place we deliberately slow down.
- Roadmap and larger design decisions (new summary formats, new data sources, architecture changes): proposed as an issue or RFC-style discussion on GitHub, open for community comment, and decided by maintainer consensus. Where consensus can't be reached, the lead maintainer(s) make the final call, documented with reasoning in the issue thread decisions aren't made in private channels.
- Disagreements are resolved through open discussion in the issue/PR; if unresolved, maintainers vote, with ties broken by the project lead.

This project intentionally keeps AI-authored output in a supporting role and keeps the clinical safety layer deterministic and human-reviewed that same philosophy carries into governance: the people closest to patient-safety risk (clinical advisors) have a formal veto on clinical-logic changes, while everything else moves at normal open-source speed.
