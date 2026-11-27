# Product and Research Requirements — AstroLM v1

## 1. Purpose

AstroLM is a from-scratch, decoder-only language model specialized in space and astronomy. It is intended to demonstrate rigorous small-language-model engineering and evaluation under a single-consumer-GPU constraint.

The deliverable is a reproducible research repository and trained base model, not an end-user product.

## 2. Research question

> Under the same curated training corpus, tokenizer, context length, and measured training-token budget, does the approximately 29M-parameter AstroLM configuration achieve lower held-out astronomy bits-per-byte than both a 5-gram language model and an approximately 10M-parameter transformer baseline, without pervasive verbatim memorization?

This comparison is primary. Tokenizer-size and dropout comparisons are bounded development studies, not additional headline research claims.

## 3. Goals

- Implement a GPT-style model and training loop without using a pretrained model implementation.
- Produce a provenance-aware and license-audited astronomy corpus.
- Train a lossless byte-level BPE tokenizer on the training split only.
- Train and resume reliably on the target local GPU.
- Evaluate language modeling, controlled generation, domain knowledge, and memorization using frozen procedures.
- Publish enough artifacts to reproduce every reported result.

## 4. Non-goals

- Chat or instruction following
- Retrieval-augmented generation or automated fact checking
- General-purpose language understanding
- Distributed, multi-GPU, or cloud-first training
- Competitive claims against modern general-purpose LLMs
- A production factual or educational authority
- Architecture search beyond one smaller transformer baseline

## 5. Target environment

Initial target hardware is an ASUS TUF Gaming F15 FX507ZC4 with an i7-12700H, an RTX 3050 with 4GB VRAM, and 16GB system RAM. Driver, CUDA, PyTorch, operating-system, and package versions are `TBD-MEASURE` and must be captured automatically by the environment report.

No memory or runtime estimate is a requirement until verified by the benchmark gate in `TRAINING.md`.

## 6. Functional requirements

### Data

- Every document must have stable identity, provenance, retrieval date, cutoff eligibility, license metadata, checksum, and split assignment.
- Deduplication must occur before split assignment.
- Raw source material must not be committed or redistributed unless explicitly permitted.
- The test split must remain inaccessible to model and hyperparameter selection.

### Tokenizer

- Byte-level BPE with zero unknown-token requirement
- Training documents only
- Exact encode/decode round-trip on the required test corpus
- Frozen tokenizer bundle with content hash

### Model and training

- Decoder-only causal transformer defined in project code
- Resolved configuration saved with every run
- Correct gradient accumulation, AMP, clipping, and parameter groups
- Atomic checkpoints and verified exact next-step resume
- Standard AdamW attempted before memory-saving alternatives

### Evaluation

- Frozen validation windows for development
- One-time final evaluation on untouched test data
- Overall and per-source bits-per-byte and token loss
- 5-gram and approximately 10M-transformer baselines
- Fixed-prompt generation rubric
- Domain cloze/multiple-choice set from excluded material
- Memorization analysis against training text

## 7. Acceptance criteria

### Engineering acceptance

All critical tests in `TESTING.md` pass; the full data/tokenizer/config lineage is reconstructable; a deliberately interrupted training run resumes with the same next optimizer update within the documented numerical tolerance.

### Research acceptance

The project is complete if it executes the preregistered comparison and reports the outcome honestly. A positive headline result requires all of the following:

1. AstroLM has lower final test bits-per-byte than both registered baselines under the comparison rules in `EVALUATION.md`.
2. At least 70% of fixed-prompt generations receive both “coherent” and “on-domain” ratings under the frozen rubric.
3. Fewer than 10% of fixed-prompt generations exhibit a repeated 4-gram loop under the automated degeneration check.
4. Memorization results are reported, with no evidence that headline qualitative examples were selected from copied training continuations.

Factuality is reported as an exploratory result, not a prerequisite for claiming that a 29M base model is a factual astronomy tool.

## 8. Required deliverables

- Versioned source code, dependency lock, configs, and tests
- Dataset and split manifests plus a dataset card
- Tokenizer bundle and evaluation report
- Model checkpoints permitted for release and a model card
- Baseline artifacts and experiment registry
- Curves, raw fixed-prompt generations, evaluation outputs, and final report
- License and attribution notices

## 9. Change control

Changes to the research question, test set, split algorithm, tokenizer after model training begins, baseline comparison rules, or success thresholds require a dated decision record. They must be disclosed as post-registration changes in the final report.
