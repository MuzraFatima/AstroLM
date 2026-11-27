# Correctness and Preflight Testing — NebulaLM v1

## 1. Purpose

Training loss decreasing does not prove that a from-scratch language model is correct. These tests are release gates, not optional cleanup.

Tests are classified as **critical**, **integration**, or **benchmark**. Every critical test must pass before a paid or multi-hour training run.

## 2. Data tests

### Critical

- Every accepted record validates against the document schema.
- Stable IDs and raw/clean checksums are unique where required.
- All included records satisfy the temporal cutoff and license allowlist.
- No exact duplicate crosses a split.
- Every near-duplicate cluster belongs to exactly one split.
- Split assignment is deterministic from manifest, algorithm version, and seed.
- Test IDs never appear in tokenizer input, training shards, prompt derivation, or pilot data.
- Shard token counts and source counts reconcile with manifests.
- EOS is appended exactly once per document.
- PAD, if present, has zero loss weight.

### Integration

- Source parser fixtures preserve astronomy symbols, units, equations, dates, and identifiers.
- Re-running preprocessing produces identical clean hashes.
- A corrupted or mismatched manifest/shard is rejected.

## 3. Tokenizer tests

### Critical

- Arbitrary byte sequences and the Unicode fixture suite encode without unknowns.
- Encode/decode is exact for all required strings.
- Special tokens have stable unique IDs.
- Literal `<eos>` and `<pad>` text does not become control tokens unintentionally.
- The tokenizer’s training-manifest hash equals the frozen training split hash.
- Validation/test IDs are absent from the trainer input log.
- A shard with a mismatched tokenizer hash fails to load.

## 4. Model tests

### Critical

- Forward shapes match `[batch, time, vocabulary]` for boundary batch/time sizes.
- Changing a future token cannot change logits at earlier positions.
- Targets are shifted by exactly one token.
- Masked/PAD positions contribute zero loss and zero valid-token count.
- Token embedding and LM head share the same parameter object/storage.
- Every trainable parameter appears in exactly one optimizer group.
- Dropout is active only in training mode.
- Context overflow produces the documented error.
- Generated parameter count matches the resolved configuration report.

### Reference equivalence

On fixed small tensors and copied weights, the project attention/block output and gradients must match a trusted PyTorch reference within registered FP32 tolerances. Optimized attention paths require the same comparison.

## 5. Training-loop tests

### Critical

- A tiny model overfits a tiny deterministic batch to the registered low-loss threshold.
- Gradient accumulation matches a single equivalent batch within tolerance.
- AMP clipping occurs only after gradients are unscaled.
- Scheduler advances once per optimizer update, not per microbatch.
- Skipped AMP steps do not advance scheduler or token counters incorrectly.
- Token-weighted evaluation matches a hand-computed example.
- Train/eval modes are restored correctly around validation and sampling.
- NaN/Inf detection stops safely and preserves the latest good checkpoint.

## 6. Checkpoint tests

### Critical

- Interrupted and uninterrupted runs produce the same next-step parameters and optimizer state within registered tolerance.
- Model, optimizer, scheduler, scaler, RNG, sampler, counters, and artifact identities restore.
- Resume begins at the correct shard/document and never repeats or skips an optimizer step.
- Truncated or checksum-invalid checkpoints are rejected.
- Atomic write leaves the previous known-good checkpoint intact on simulated failure.
- Identity mismatch fails closed; migration mode creates a new run.

## 7. Evaluation tests

- Fixed windows and prompts reproduce from their bundle hash.
- Overall NLL equals the sum of per-example NLL divided by total valid tokens.
- Per-source totals reconcile with the overall total.
- BPB uses original UTF-8 byte counts under the documented boundary convention.
- Test evaluation requires an explicit final-evaluation flag and records the invocation.
- Baseline and NebulaLM evaluators consume identical eligible windows.
- Memorization metrics pass synthetic exact-copy and non-copy fixtures.

## 8. Hardware preflight

The benchmark gate must exercise the actual planned sequence length and optimizer step, not only inference. Record peak allocated and reserved VRAM after warmup, steady-state tokens/second, checkpoint write time/size, and whether the laptop throttles during the observation window.

If standard AdamW fits reliably, it remains the baseline. Memory-saving techniques are enabled one at a time and re-tested.

## 9. Gate artifact

Before expensive training, publish one machine-readable preflight report containing test command, timestamp, git commit, environment ID, artifact hashes, pass/fail counts, benchmark measurements, and approved exceptions. Critical-test exceptions are not allowed.
