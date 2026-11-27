# Experiment Registry — NebulaLM v1

This file is an append-oriented template. It contains no experimental results.

## 1. Blocking decisions

| Decision | Evidence required | Owner workstream | Status |
|---|---|---|---|
| temporal cutoff | dated team decision before production collection | Data | `TBD` |
| enabled conditional sources | per-source/page license and quality audit | Data | `TBD` |
| dedup thresholds | labeled duplicate/non-duplicate calibration set | Data + Evaluation | `TBD` |
| final corpus composition | post-cleaning/dedup report | Data | `TBD-MEASURE` |
| 4k vs 8k tokenizer | frozen tokenizer evaluation; proxy only if needed | Model/Tokenizer + Evaluation | `TBD-EXPERIMENT` |
| microbatch and memory features | hardware preflight | Model/Training | `TBD-MEASURE` |
| peak LR | equal-token registered pilot | Model/Training | `TBD-EXPERIMENT` |
| dropout 0.10 vs 0.20 | only if pilot shows material divergence | Model/Training | `TBD-CONDITIONAL` |
| final maximum tokens/passes | validation curve, runtime budget, memorization signals | All | `TBD-EXPERIMENT` |
| final checkpoint | frozen validation protocol | Evaluation | `TBD-EXPERIMENT` |

## 2. Protocol registration template

```yaml
experiment_id: EXP-YYYY-NNN
registered_at: TBD
hypothesis: TBD
decision_informed: TBD
primary_metric: TBD
secondary_metrics: []
changed_variables: {}
controlled_variables: {}
dataset_manifest_sha256: TBD
split_manifest_sha256: TBD
tokenizer_sha256: TBD
evaluation_bundle_sha256: TBD
code_commit: TBD
environment_id: TBD
seeds: []
valid_token_budget: TBD
wall_clock_cap: TBD
evaluation_intervals: TBD
stop_rule: TBD
tie_rule: TBD
planned_runs: []
known_deviations: []
status: registered
```

## 3. Run record template

| Field | Value |
|---|---|
| run ID | `TBD` |
| experiment ID | `TBD` |
| start/end | `TBD` |
| terminal status | `TBD` |
| resolved config hash | `TBD` |
| exact parameter count | `TBD-MEASURE` |
| artifact identity chain | `TBD` |
| valid training tokens seen | `TBD-MEASURE` |
| equivalent corpus passes | `TBD-MEASURE` |
| peak/selected validation BPB | `TBD-MEASURE` |
| peak allocated/reserved VRAM | `TBD-MEASURE` |
| steady-state tokens/sec | `TBD-MEASURE` |
| checkpoint selected | `TBD` |
| deviations/failures | `TBD` |

Every run, including crashes and rejected pilots, receives a record.

## 4. Required experiments

### EXP-TOK: tokenizer selection

Compare 4k and 8k byte-level BPE candidates using `TOKENIZER.md`. Add 12k only if its entry condition is met.

### EXP-LR: learning-rate pilot

Compare peak learning rates `1e-4` and `3e-4` under identical valid-token budgets and evaluation windows. Register pilot duration after the hardware benchmark and before either result is viewed.

### EXP-DROP: dropout contingency

Compare 0.10 and 0.20 only if the selected LR pilot shows material train/validation divergence under the preregistered trigger. Otherwise record “not triggered.”

### EXP-SCALE: primary transformer comparison

Train the smaller transformer and NebulaLM with the selected tokenizer and controlled valid-token budget. This supplies the primary neural comparison.

### EXP-BUDGET: pass/checkpoint selection

Evaluate at approximately 1, 2, 4, and conditionally 8 equivalent passes. Select the budget/checkpoint from frozen validation metrics and the registered stop/tie rule.

## 5. Final result registry

Populate the table required by `EVALUATION.md`, attach machine-readable metric files, and link every number to a run ID. Report negative and inconclusive outcomes.

## 6. Decision log

| Date | Decision | Evidence/run IDs | Alternatives rejected | Consequence |
|---|---|---|---|---|
| `TBD` | `TBD` | `TBD` | `TBD` | `TBD` |

Protocol changes are new rows; prior entries are not rewritten.
