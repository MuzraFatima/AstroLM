# Tokenizer Specification — NebulaLM v1

## 1. Required design

NebulaLM uses a lossless byte-level BPE tokenizer implemented with Hugging Face `tokenizers`. The tokenizer is a preprocessing dependency; model architecture and weights remain project implementations trained from scratch.

The tokenizer trainer may read **training-split text only**. Validation, test, and challenge material are forbidden inputs to vocabulary or merge learning.

## 2. Pipeline definition

- Normalizer: Unicode NFC only; no lowercasing, accent stripping, or lossy replacement.
- Pre-tokenizer: byte-level with the full 256-byte initial alphabet and `add_prefix_space=false`.
- Model: BPE learned from training documents.
- Decoder: matching byte-level decoder.
- Post-processor: no implicit BOS/EOS insertion; the sequence packer controls document boundaries.
- Unknown handling: no `<unk>` token. Every byte sequence must be representable.

Exact library arguments and versions must be saved with the tokenizer bundle.

## 3. Special tokens

| Token | Required behavior |
|---|---|
| `<eos>` | appended exactly once after each logical training document; may be a prediction target |
| `<pad>` | reserved for batches that require padding; masked from attention and loss |

No BOS token is inserted during packed pretraining. Additional special tokens require a versioned tokenizer change and invalidate previously encoded shards.

Special-token IDs are stored in configuration and verified on load. Literal occurrences of special-token strings in source text must not be interpreted as control tokens unless inserted by the pipeline.

## 4. Vocabulary candidates

Train 4,000- and 8,000-token candidates on the identical frozen training sample and seed. The initial model target assumes 8,000, but selection occurs before production sharding.

A 12,000-token candidate is allowed only if the 8,000-token tokenizer exhibits a documented tail-fragmentation problem on astronomy fixtures. A larger vocabulary is not automatically better because it increases the tied embedding/output matrix and changes model size.

## 5. Evaluation

Each candidate is evaluated on disjoint samples from training and validation documents. Validation text is used only to evaluate a completed tokenizer, never to train or modify it.

Required metrics:

- unknown rate: exactly zero
- exact encode/decode round-trip rate: 100% for the required fixture and audit suites
- tokens per UTF-8 byte
- tokens per whitespace-delimited word, including median, p95, and p99
- distribution by source
- fragmentation of object names, units, numbers, scientific notation, coordinates, Unicode symbols, hyphenated terms, and ordinary English
- vocabulary occupancy/frequency distribution
- estimated model parameter count for each vocabulary
- encoding throughput

Cross-tokenizer model comparisons use bits-per-byte; raw token perplexity is not comparable across tokenizers.

## 6. Selection rule

Reject any candidate that fails zero-unknown or exact round-trip requirements. Among valid candidates, select the smallest vocabulary on the Pareto frontier of validation compression, astronomy-tail fragmentation, encoding speed, and model parameter cost. The decision, plots, sample, and hashes must be entered in `EXPERIMENTS.md` before any production shard is created.

If 4k and 8k trade off without a clear dominant choice, run an identical bounded tiny-model proxy for each and select by validation bits-per-byte and measured tokens/second. The proxy protocol must be registered before viewing results.

## 7. Frozen artifact

The released tokenizer bundle contains:

- tokenizer JSON, vocabulary, and merges
- special-token map
- normalizer/pre-tokenizer/decoder configuration
- trainer configuration and seed
- training manifest hash and sample-selection hash
- library version
- evaluation report
- SHA-256 hash of every artifact

Loading a tokenized shard with a mismatched tokenizer hash must fail.
