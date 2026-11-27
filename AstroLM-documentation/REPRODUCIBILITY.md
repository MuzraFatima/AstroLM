# Reproducibility Specification — AstroLM v1

## 1. Reproducibility target

The project targets:

- **Artifact reproducibility:** another operator can reconstruct the same eligible document identities, splits, tokenizer, shards, configuration, and evaluation bundle from permitted sources.
- **Run reproducibility:** the same environment and seed reproduce the run within documented deterministic limits.
- **Exact resume:** interruption at an allowed checkpoint boundary produces the same next optimizer update as an uninterrupted run within registered numerical tolerance.

Cross-GPU bitwise identity is not promised. Differences and nondeterministic operations must be disclosed.

## 2. Artifact identity chain

Every run ID links the following immutable hashes:

```text
source manifest
  -> cleaned manifest + pipeline commit
  -> dedup clusters + split manifest
  -> tokenizer training manifest
  -> tokenizer bundle
  -> encoded shards + packer config
  -> evaluation bundle
  -> resolved run config + code commit + environment
  -> checkpoints and result artifacts
```

A changed upstream hash creates a new downstream artifact version. Files must never be silently overwritten under an existing identity.

## 3. Required repository controls

- Dependency lock with exact versions and hashes where supported
- One command to generate an environment report
- Versioned configuration files validated against a schema
- Repository commit and dirty-worktree status saved per run
- Generated artifacts stored outside Git unless small and redistribution-safe
- Checksums for tokenizer, manifests, shards, evaluation bundle, checkpoints, and reports
- CI or local test command that runs the critical CPU test suite

Secrets, raw restricted source data, checkpoints not intended for release, and large shards must be ignored.

## 4. Seed policy

Use named seeds rather than one undocumented global seed:

- data split seed
- dedup calibration/sample seed
- tokenizer trainer/sample seed
- shard packing/permutation seed
- model initialization seed
- training sampler seed
- generation seed set
- bootstrap/statistical seed

The resolved values live in configuration and experiment records. A seed change creates a new run or artifact identity as appropriate.

## 5. Determinism controls

Record PyTorch deterministic-algorithm settings, cuDNN benchmark/deterministic settings, TF32 policy, matmul precision, AMP dtype, CUDA/CUDNN versions, GPU model, driver, operating system, CPU, and thread settings.

If a required operator is nondeterministic, document it, quantify resume/repeat variation on a short test, and compare runs using tolerance-based or statistical criteria rather than claiming bitwise identity.

## 6. Run directory

Each run directory contains:

- immutable `run_id`
- resolved configuration
- command line and start/end timestamps
- code commit and dirty diff indicator
- environment report and dependency-lock hash
- all upstream artifact hashes
- append-only metric JSONL
- event log for resume, skipped AMP steps, exceptions, and manual interventions
- checkpoints with checksums
- evaluation and sampling outputs
- terminal status: completed, early-stopped, failed, or superseded

CSV may be exported for analysis, but JSONL is the canonical metric log.

## 7. Checkpoint identity and resume

Checkpoint fields are defined in `TRAINING.md`. Exact resume is allowed only at an optimizer-step boundary. The loader validates configuration schema version, code compatibility, tokenizer/shard/split identity, optimizer type, and tensor shapes before mutating live state.

An emergency session-end checkpoint follows the same boundary rule. If the process stops mid-accumulation, it resumes from the preceding complete optimizer-step checkpoint; partial gradients are not claimed as exact unless they are explicitly serialized and tested in a later version.

## 8. Experiment registration

Before a comparison begins, register:

- hypothesis and decision it informs
- changed and controlled variables
- artifact identities
- token/step and wall-clock caps
- evaluation checkpoints
- primary metric and tie/stop rule
- seeds and number of repeats
- known deviations

Afterward, append results and interpretation without editing the registered protocol. Corrections are new dated entries.

## 9. Release reproduction packet

The final release includes a concise reproduction guide, supported environment, commands for data reconstruction where lawful, preprocessing, tokenizer creation, baseline training, model training/resume, and evaluation. It must distinguish “reproduce from sources,” “run with provided processed artifacts,” and “evaluate a released checkpoint.”
