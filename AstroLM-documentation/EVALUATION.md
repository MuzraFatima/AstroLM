# Evaluation Specification — AstroLM v1

## 1. Evaluation layers

Evaluation separates optimization health, final language-model performance, controlled generation quality, domain knowledge, and memorization. No single metric is described as factual intelligence.

Validation supports development and checkpoint selection. The frozen test split is evaluated once after the final checkpoint and protocol are locked. Any later test evaluation is labeled a post-hoc analysis.

## 2. Language-model metrics

Required for validation and test:

- summed negative log-likelihood and valid-token count
- token-weighted mean NLL
- perplexity for the single frozen tokenizer
- bits per UTF-8 byte for model/baseline and tokenizer comparisons
- metrics by source and overall
- document-cluster bootstrap intervals for primary test comparisons

The overall result must state its source weighting. A uniform-logit loss near `ln(vocab_size)` is only a sanity check, not a baseline.

## 3. Registered baselines

### 3.1 Classical baseline

A 5-gram language model over the selected tokenizer IDs, trained on exactly the training split with documented smoothing and boundary handling. It consumes the same eligible token stream and is evaluated on the same windows. Report its test bits-per-byte and storage/runtime characteristics.

### 3.2 Smaller transformer

The configuration in `ARCHITECTURE.md` is trained with the same tokenizer, packing, objective, valid-token budget, and evaluation windows. Optimization settings may be scaled only by a registered rule; post-hoc tuning that disadvantages either model is prohibited.

### 3.3 Additional references

A unigram model and untrained transformer are sanity references. They do not satisfy the primary baseline requirement.

## 4. Fixed-prompt generation suite

Create and freeze at least 60 prefixes before full training, with balanced categories:

- Solar System objects
- stars and stellar evolution
- galaxies and cosmology
- telescopes and observation
- missions and spaceflight
- quantitative/scientific prose

Prompts must not be copied from training document openings. Save prompt provenance, category, expected risks, generation seeds, and exact decoding configuration.

Every final checkpoint produces greedy output and the registered sampled outputs. Publish all outputs, not a hand-selected subset.

The binary rubric records:

- grammatical/coherent through at least one complete sentence
- remains on-domain
- contradicts itself
- degenerates into a repeated 4-gram loop
- contains one or more externally checkable factual claims
- appears truncated by the context limit

At least two independent team ratings are preferred for the final suite; report agreement and resolution procedure. Raters must not know which transformer produced an output during baseline comparison where practical.

## 5. Domain knowledge and factuality

Create a frozen set of at least 60 cloze or multiple-choice items from documents excluded from all corpus splits, including a separately marked post-cutoff subset where possible. Record source, answer, license, retrieval date, and why it is uncontaminated.

Score answers using conditional log-likelihood, which is appropriate for a base LM. Separate:

- stable categorical facts
- dates and mission status
- numeric values and units
- entity-property associations

Report accuracy, bootstrap interval, and error categories. Do not verify headline factuality using the same passages that trained the model. This evaluation is exploratory and must not support a “factual reference” claim.

## 6. Memorization evaluation

### Prefix-continuation test

- Sample at least 50 training prefixes and 50 matched held-out prefixes using a frozen seed.
- Generate continuations under deterministic and registered sampled decoding.
- Measure longest contiguous token match, character-level match length, and rare n-gram overlap against the originating and nearest corpus passages.

### Unprompted-generation audit

For every fixed-prompt generation, search the training corpus for exact and near-exact overlap. Report the fraction with contiguous matches of at least 50 model tokens and show short, rights-respecting examples.

Memorization metrics must accompany qualitative examples. A copied-looking output cannot be presented as evidence of general generation quality.

## 7. Checkpoint selection

Select the model using token-weighted validation NLL/BPB under the frozen evaluator. If checkpoints are statistically indistinguishable under the registered validation comparison, prefer the earlier/lower-compute checkpoint unless the preregistered generation rubric clearly breaks the tie.

The test set never selects a checkpoint.

## 8. Final report table

The final report includes, for all registered models:

| Model | Parameters | Valid training tokens seen | Equivalent passes | Validation BPB | Test BPB | Domain accuracy | Coherent + on-domain | Repetition rate | Memorization indicators |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 5-gram | measured | measured | measured | measured | measured | measured or N/A | N/A | N/A | measured or N/A |
| smaller transformer | measured | measured | measured | measured | measured | measured | measured | measured | measured |
| AstroLM | measured | measured | measured | measured | measured | measured | measured | measured | measured |

No cells may contain estimates presented as results.
