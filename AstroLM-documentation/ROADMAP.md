# Implementation Roadmap and Team Interfaces — NebulaLM v1

## 1. Workstream contracts

### Data / Dataset Pipeline

Owns source approvals, collection, parsing, quality filters, provenance, licensing, deduplication, split manifests, packing inputs, and corpus reports.

Publishes:

- document schema and source adapters
- source/license manifest
- cleaned-document manifest
- dedup clusters and frozen split manifest
- train-text iterator for tokenizer training
- tokenized shard request/acceptance contract
- dataset card and quality report

### Model / Tokenizer / Training

Owns tokenizer candidates, model implementation, runtime configuration, optimization, GPU benchmark, checkpoints, sampling, and training logs.

Publishes:

- frozen tokenizer bundle and hash
- shard encoder/packer implementation conforming to the Data manifest
- model/config schema and parameter report
- verified trainer and checkpoint loader
- checkpoints, metrics, and generation artifacts

### Evaluation / Experiments / Reproducibility

Owns experiment registration, test gates, evaluation bundles, baselines, controlled generation, factuality/cloze, memorization measurement, environment capture, and final reporting.

Publishes:

- frozen validation/test window IDs
- baseline implementations/protocols
- prompt, domain-evaluation, and safety bundles
- preflight report and run registry
- evaluation outputs and final report

## 2. Interface rules

- Data publishes stable document IDs; all later artifacts reference IDs rather than paths.
- Tokenizer publishes a content hash; shards and configs must embed it.
- Data publishes split and shard hashes; training refuses mismatches.
- Evaluation publishes a bundle hash before full training; test content remains access-controlled.
- Training publishes immutable run IDs and resolved configs; evaluation never infers settings from filenames.
- Upstream interface changes invalidate downstream artifacts and require new versions.

## 3. Phases and dependencies

### Phase 0 — Specification freeze

**Parallel work**

- Data: propose cutoff, source allowlist, manifest schema, and license-audit procedure.
- Model: create repository/config skeleton and environment probe.
- Evaluation: implement experiment registry schema and draft frozen evaluation protocols.

**Joint gate**

- Name, research question, success criteria, cutoff, schemas, source-of-truth policy, and change-control rules approved.

### Phase 1 — Small-sample vertical slice

- Data collects a small, legally cleared sample from each candidate source and implements parsers/fixtures.
- Model implements both tokenizer candidates against training-only sample input and creates tokenizer tests.
- Evaluation implements data leakage checks, quality audit sampling, and baseline/evaluator skeletons.

**Gate:** one sample document can be traced from provenance through cleaning, split, tokenization, packing, model loss, and evaluation with matching hashes.

### Phase 2 — Dataset production and split freeze

- Data runs collection, cleaning, quality filtering, and global deduplication; calibrates thresholds with Evaluation.
- Evaluation audits accepted/rejected samples, cross-split overlap, and test isolation.
- Model consumes only the published training iterator and prepares tokenizer comparison tooling.

**Gate:** corpus report, license audit, dedup report, and immutable 94/3/3 split manifests approved. Actual size and composition are known.

### Phase 3 — Tokenizer and evaluation freeze

- Model trains/evaluates 4k and 8k candidates and publishes the selected bundle.
- Data encodes and packs shards only after tokenizer freeze.
- Evaluation finalizes validation windows, test controls, prompts, domain items, safety items, memorization samples, and 5-gram protocol.

**Gate:** tokenizer decision recorded; shard reconciliation passes; evaluation bundle hash frozen.

### Phase 4 — Model correctness

- Model implements the reference transformer and trainer.
- Evaluation implements the smaller transformer/5-gram baselines and independent metric checks.
- Data validates shard provenance and supplies tiny deterministic fixtures.

**Gate:** all critical tests in `TESTING.md`, tiny-batch overfit, and exact resume pass on CPU/small GPU runs.

### Phase 5 — Hardware preflight and pilots

- Model runs the standard-AdamW hardware benchmark and enables memory options only if required.
- Evaluation verifies benchmark/run records and executes the registered LR pilot analysis.
- Data confirms source exposure counts and repeat accounting.

**Gate:** measured feasible batch/accumulation, runtime cap, peak LR, dropout decision, and maximum-pass protocol recorded.

### Phase 6 — Baseline and full training

- Train the 5-gram and smaller-transformer baselines under registered rules.
- Train NebulaLM, evaluating fixed validation windows at registered token intervals.
- Evaluation monitors protocol compliance; Data monitors source/shard accounting.

**Gate:** best checkpoint selected from validation only; terminal status and all deviations recorded.

### Phase 7 — Final evaluation and release

- Freeze selected checkpoint.
- Run the one-time test evaluation, controlled generations, domain/factuality, safety, and memorization analysis.
- Complete model/dataset cards, attribution, reproduction guide, raw result tables, and honest conclusion.

**Gate:** every public claim traces to a run/result artifact and every released file passes rights review.

## 4. Coordination cadence

- Short interface review whenever a manifest, tokenizer, shard, config, checkpoint, or evaluation schema changes.
- One joint gate review at each phase boundary.
- Experiment protocols registered before runs; results reviewed only after artifact validation.
- Blockers and rejected alternatives entered in the decision log, not left in chat or personal notes.

## 5. Explicitly deferred

Instruction tuning, chat UI, RAG, automated fact checking, larger model tiers, distributed/cloud support, RoPE/ALiBi migration, custom Flash Attention, KV cache work, broad crawling, and extensive generic benchmarks are not v1 dependencies.

## 6. Definition of implementation-ready

Coding can begin after Phase 0. Expensive training cannot begin until Phase 5 is complete. A polished README or successful sample generation cannot waive a failed gate.
