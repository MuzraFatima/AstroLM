# Training Specification — AstroLM v1

## 1. Budget principle

AstroLM does not target a Chinchilla-derived number of repeated tokens. Repeated passes over a small corpus have diminishing value and increase memorization risk.

The final training budget is selected from measured learning curves subject to a wall-clock/thermal budget established on the target machine. Unique corpus tokens, total tokens seen, and number of equivalent corpus passes are always reported separately.

## 2. Training gates

Expensive training may begin only after:

1. dataset, split, tokenizer, config, and evaluation artifacts are frozen and hashed;
2. all critical tests in `TESTING.md` pass;
3. a 500–1,000 optimizer-step hardware benchmark records throughput, peak allocated/reserved VRAM, checkpoint size, temperature/throttling observations, and projected wall time;
4. save/resume has reproduced the next optimizer update within the registered tolerance;
5. baseline and pilot protocols are registered.

The benchmark does not count as a result unless its data/config identities are recorded.

## 3. Sequence batches

- Context length: 256.
- Training sequences and loss masks come from the frozen packer in `DATASET.md`.
- Effective batch size is reported in valid prediction tokens per optimizer update, not only sequences.
- Microbatch size and gradient accumulation are measured choices.
- Loss is divided by the number of accumulation steps or, for variable valid-token counts, normalized by the total valid-token count so the accumulated gradient matches the defined large batch.
- Optimizer steps, tokens seen, and equivalent corpus passes are logged independently.

## 4. Optimizer baseline

| Setting | Baseline |
|---|---|
| optimizer | PyTorch AdamW |
| peak learning rate | pilot decision; candidates `1e-4` and `3e-4` |
| betas | `(0.9, 0.95)` |
| weight decay | `0.1` using architecture parameter groups |
| gradient clipping | global norm `1.0`, after AMP unscale |
| precision | FP16 autocast with GradScaler |
| dropout | `0.10`; `0.20` is the only planned alternative |

BF16 is not part of the baseline. It may replace FP16 only after a measured compatibility/performance decision.

Try standard AdamW first. Gradient checkpointing, smaller microbatches, and bitsandbytes 8-bit AdamW are memory options, introduced in that order only when benchmark evidence requires them. The selected option and package build must pass resume and optimizer tests.

## 5. Learning-rate schedule

- One continuous schedule; no cosine restarts.
- Linear warmup for 1% of the registered maximum optimizer steps, with a minimum of one step.
- Cosine decay from the selected peak to 10% of peak at the maximum step.
- The maximum is fixed for a run and restored exactly on resume.

Pilot comparisons use equal valid training tokens, equal evaluation windows, and identical initialization seeds where the compared shape permits.

## 6. Pilot and budget-selection procedure

1. Run the hardware benchmark with the planned 29M configuration.
2. Compare the two registered learning rates over an equal, preregistered pilot-token budget.
3. Continue the better stable learning-rate run with dropout 0.10; compare dropout 0.20 only if train/validation divergence is already material.
4. Record validation metrics at fixed token intervals and at approximately 1, 2, 4, and, only if still justified, 8 equivalent corpus passes.
5. Select a hard maximum of no more than 8 passes for v1 unless a dated change record is approved before the extra run.
6. Use the best validation checkpoint, not automatically the last checkpoint.

Stop early if validation token-weighted NLL worsens at two consecutive full-validation evaluations while training NLL improves, after ruling out evaluator/config changes. Retain the best earlier checkpoint. Qualitative samples never override a clearly worse validation result; they may break a near-tie documented in advance.

## 7. Checkpoints and exact resume

Checkpoints are written only at optimizer-step boundaries, first to a temporary file and then atomically renamed after validation. Keep the latest three valid checkpoints and the best-validation checkpoint; retention must never delete the only known-good checkpoint.

Each checkpoint contains:

- model, optimizer, scheduler, and GradScaler state
- Python, NumPy, Torch CPU, and all CUDA RNG states
- global optimizer step, valid tokens seen, epoch/pass counter, shard/document position
- dataloader/sampler state and permutation seed
- resolved model, data, optimizer, scheduler, and evaluation configuration
- tokenizer, shard, split-manifest, and dataset-manifest hashes
- git commit, dependency-lock hash, environment report ID
- best metric and early-stopping state

On load, identity mismatches fail unless an explicit non-exact migration mode is used. Migration resumes must use a new run ID and cannot be described as exact.

## 8. Evaluation during training

- Fixed validation windows; no resampling between checkpoints
- Token-weighted NLL, bits per byte, and perplexity for the frozen tokenizer
- Per-source metrics and train/validation gap
- Fixed prompt suite using frozen decoding settings and seeds
- Throughput, peak VRAM, gradient norm, LR, scaler value, and skipped-step count

The test split is not evaluated during training.

## 9. Sampling defaults

For qualitative tracking only: temperature 0.8, top-k 40, maximum 150 new tokens, stop at EOS, and reject prompts whose prompt plus requested continuation exceeds context. Greedy output is also saved for deterministic comparison.

Sampling settings are not tuned on the test set.
