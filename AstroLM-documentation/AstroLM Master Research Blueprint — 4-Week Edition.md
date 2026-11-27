# AstroLM Master Research Blueprint — 4-Week Edition

Oct 29, 2026 · @Farhan Aaqil

A 28-day, laptop-feasible plan that turns AstroLM from a model-building exercise into a pre-registered study of what a small astronomy language model learns, stores and leaks.

## Executive summary

The supplied AstroLM plan builds a good small astronomy model but asks no research question. This blueprint keeps its model and discipline and adds one: **how much does a 4.5M–29M model learn, store and leak as it trains on repeated text, and how far is its test score inflated by near-duplicates?** It answers with eleven pre-registered hypotheses over a 28-day schedule (Mon 2 Nov to Sun 29 Nov 2026).

**Three things are new**

1. **A paired leak probe.** Each probe document is scored by a model trained without its siblings and by one trained with them, across exact copies, four levels of edited copies and natural paraphrases. The ratio of the benefit to the exact-copy benefit shows how fast leakage fades with overlap.
2. **A leak-adjusted test score (LA-BPB).** The measured benefit curve converts the test set's similarity to the training set into a bias bound on the reported number, with calibration criteria fixed in advance.
3. **An epoch-by-size surface from one run per size.** A warmup-stable-decay trunk with cooldown branches at 1, 2, 4 and 8 passes, plus equal-compute repeated-versus-unique runs, reads Learn, Store and Leak at every point.

**What changed from the supplied plan.** OpenStax and arXiv are dropped for licence reasons; the checkpoint grid moves from cosine to a trunk-and-branch design; a 4.5M model and three seeds are added; the trainer is built before the tokenizer is chosen; and every compute and power figure is labelled as an assumption until the Day 2 and Day 12 measurements replace it (the findings table lists all eight changes).

**What it costs.** About 200 hours of work plus 25 contingency, and about 25 GPU-hours at mid throughput for a 30M-token corpus (17 to 38 across the assumed band). A team of three fits comfortably. One person doing everything has a marginal schedule and should expect to cut the P2 and P3 items.

**How good is it.** My own rubric gives a mean of 8.5 out of 10 (6.4 for the supplied spec), every criterion at 8.0 or above; it is a self-assessment, and nothing here has been run.

**Check before Day 1.** The OpenStax terms, the `wikimedia/wikipedia` dataset configurations, per-title Gutenberg status, and the 2026 preprints cited in the literature table.

## Scorecard

On my rubric the blueprint scores a mean of 8.5 out of 10 against 6.4 for the supplied spec, with every criterion at 8.0 or above. These are self-assessed scores, not an external review, and schedule feasibility falls to about 7.5 if one person does all the work.

&#91;embedded content: self-assessed against the rubric below · 10 criteria, supplied spec vs this blueprint\]

**Rubric.** 5 = adequate draft. 7 = sound plan with known gaps. 8 = a reviewer would accept it as a solid workshop-level design, with gaps named. 9 = few gaps and evidence in hand. 10 is not awarded before any result exists, so 9 is the ceiling here.

| Criterion | Evidence in this blueprint | What would make it 9 or more |
| --- | --- | --- |
| Novelty | Paired M0/M1 probe with overlap-typed transfer ratio, LA-BPB, and control-calibrated readouts on one checkpoint grid; no reviewed paper combines them | A systematic search that finds no prior pairing; a replicated H3a |
| Falsifiable questions | Eleven hypotheses with numeric thresholds, a Holm-corrected confirmatory family, a failure commitment | Thresholds checked against a pilot on validation data |
| Literature coverage | Twenty sources; eleven closest works each with what they leave open; search protocol recorded | A systematic search; reading every 2026 preprint in full |
| Statistical rigor | Cluster bootstrap, simulated power for the paired design, seeds, multiplicity rules | Power re-simulated with per-document SD measured on Day 14 |
| Compute feasibility | FLOPs, memory and GPU-hour scenarios; D\_cap rule | Measured throughput on the actual laptop in place of the assumed bands |
| Schedule feasibility | Gates, cut-lines, tiers, 25 hours of contingency; team of three comfortable, solo marginal | A second person, or measured solo velocity after Week 1 |
| Reproducibility | Hash chain, frozen bundle, manifests, reproduction check on a 10M run | An independent reproduction |
| Evaluation validity | Cluster splits, sealed test, leak-adjusted BPB, negative controls (group Z, control canaries, post-cutoff cloze) | LA-BPB passing its calibration test (H3c) |
| Impact and reuse | Audit kit, licence-clean recipe, manifests, spec-defect log | Use by a second group or model family |
| Risk, ethics, licensing | Fourteen-risk register; OpenStax and arXiv excluded; no personal data in canaries | Legal review of the release plan |

### Iteration ledger

| Version | Change | Main effect |
| --- | --- | --- |
| v0 | Supplied spec scored as written | Strong engineering and evaluation discipline; weak literature and licence handling |
| v1 | Reframed from model-building to a Learn / Store / Leak audit | Gives the project a research question and a gap |
| v2 | Licence-driven data redesign: OpenStax and arXiv out, Simple Wikipedia used only as probe siblings | Removes a blocker; adds the paraphrase group |
| v3 | Power simulation showed unpaired leak probes could only detect 0.109 BPB; moved to paired M0/M1 difference in differences (0.013) and added LA-BPB | Makes the leak study detectable |
| v4 | Schedule hardening: tiers, gates, cut-lines, D\_cap, WSD trunk with branches | Makes the 28 days credible and the epoch grid affordable |
| v5 | Red-team pass with twelve objections; compute figures rechecked against the planning script and four corrected | Internal consistency |

## Findings that changed the plan

Reading the supplied specs against primary sources surfaced one blocking defect and seven weaker points; each is closed by an amendment (A1–A7) that this blueprint builds in.

| # | Finding | Evidence (opened 29 Oct 2026) | Severity | Amendment |
| --- | --- | --- | --- | --- |
| F1 | OpenStax Astronomy 2e is CC BY-NC-SA 4.0 and states it "may not be used in the training of large language models or otherwise be ingested" without written permission. The supplied plan (Days 1 and 4) treats it as an approved CC BY 4.0 source. | OpenStax preface page, fetched twice, same wording both times | Blocking | A1: excluded from training, tokenizer, validation, test and cloze items. A permission request is optional and never on the critical path. |
| F2 | arXiv abstracts look like an easy extra source, but arXiv's bulk-data and metadata pages grant no CC0 licence and say most papers carry the default arXiv licence. | arXiv bulk-data and metadata-policy pages | High | A1: excluded. The spec's own rule applies: unresolved licence means excluded. |
| F3 | One cosine run evaluated "at 1, 2, 4, 8 passes": mid-run cosine checkpoints sit at a high learning rate, so they are not equivalent to finished runs of that length, and cross-size comparisons mix LR phases. | Hägele et al. 2024; Hu et al. 2024 | High | A2: warmup-stable-decay trunk per size with cooldown branches at each budget. |
| F4 | One smaller baseline gives a two-point size comparison, no slope and no seed variance. | spec ARCHITECTURE §8 | Medium | A3: add a 4.5M model; three seeds for the 10M and 29M models at 1 and 4 passes. |
| F5 | The learning rate is piloted on the 29M model only and reused for the baseline, so the baseline may be under-tuned. | plan Day 21 | Medium | A4: per-size LR sweep over 1e-4, 3e-4, 1e-3. |
| F6 | Dedup removes near-copies, not paraphrases. The spec asks to "quantify residual risk" but gives no method. | DATASET §6 | Medium | A5: leak probes with a paired before/after design and a leak-adjusted test BPB. |
| F7 | Tokenizer choice is scheduled on static metrics (Day 11) while the trainer only exists from Day 15, so the spec's own proxy-run rule cannot run. | plan Days 11 and 15 | Medium | A6: trainer and tests finish before the tokenizer decision. |
| F8 | Blinded rating of every output from three models by two raters is about 360 items per rater. | plan Day 28 | Low | A7: rate 120 sampled outputs from the two transformers; double-rate 40. |

**Kept unchanged:** the research question and its acceptance criteria, the 29.45M architecture, the tokenizer rules, test sealing, the "no estimate presented as a result" rule, and the claims policy. Everything else in this document adds to them.

**One more open item you must close yourself on Day 1:** the HF `wikimedia/wikipedia` dataset card could be opened only in older snapshots that do not list the `20231101.en` and `20231101.simple` configs. Confirm both exist before Day 3; the fallback chain is in Data and benchmark design.

## Literature landscape

Nineteen papers and five official pages were opened for this blueprint; the nearest work studies repetition, memorization or contamination at larger scale or in other domains, and the reviewed papers do not audit all three on one set of checkpoints.

### Closest related work

| Work | Setting | Finding used here | Leaves open for this project |
| --- | --- | --- | --- |
| [Muennighoff et al. 2023](https://arxiv.org/abs/2305.16264) | about 400 runs, up to 9B parameters and 900B tokens | Up to about 4 epochs of repeated data change loss negligibly versus unique data; returns then decay to zero. Basis of H2a. | No tiny models, no memorization readout, web text only. |
| [Hägele et al. 2024](https://arxiv.org/html/2405.18392v3) and [Hu et al. 2024](https://arxiv.org/abs/2404.06395) | constant LR plus a cooldown of about 20% of steps (WSD) | Scales like cosine, and one run can supply many budget points by cooling down from any checkpoint. Basis of A2. | Not yet used to map memorization against epochs. |
| [Carlini et al. 2022](https://arxiv.org/abs/2202.07646v3) | LMs of increasing size | Memorization rises log-linearly with capacity, duplication and prompt context. Direction of H2b and H2c. | No link from memorization to evaluation validity. |
| [Tirumala et al. 2022](https://arxiv.org/abs/2205.10770) and [Biderman et al. 2023](https://www.arxiv.org/abs/2304.11158) | causal and masked LMs; Pythia suite | Larger models memorize faster and forget less; memorization is partly predictable from cheaper runs. | Not tied to a leak-adjusted test number. |
| [Morris et al. 2025](https://arxiv.org/abs/2505.24832) | hundreds of models, 500K to 1.5B parameters | About 3.6 bits per parameter of capacity; memorization saturates, then generalization takes over. Scale anchor for canaries and for stretch item S1. | Natural domain text at 4–30M parameters under multi-epoch repetition. |
| [Carlini et al. 2019](https://ar5iv.arxiv.org/html/1802.08232) | canaries in sequence models | Compare inserted random canaries with equivalent random sequences never inserted. Our control-canary calibration follows this. | Used for privacy, not for evaluation bias. |
| [Lee et al. 2022](https://arxiv.org/abs/2107.06499) | C4 and other LM datasets | Deduplication cuts memorized output about tenfold; train–validation overlap affects over 4% of standard validation sets. | Dedup thresholds are set by inspection, not by their effect on the metric. |
| [Hernandez et al. 2022](https://arxiv.org/abs/2205.10487v1) | up to 800M parameters | Repeating 0.1% of the data 100 times can cut an 800M model to roughly the quality of a 400M one. | Exact repeats only. |
| [Chudnovsky et al. 2026](https://arxiv.org/html/2606.24998v1) | 34M–344M Qwen3-style models, FineWeb-Edu-Dedup | Replaying 10% of tokens as exact documents hurts most at an intermediate repeat count, and the worst count shrinks as models grow. Closest in scale. | Near-duplicate and paraphrase overlap, domain text, a bias bound on the test number. |
| [Jiang et al. 2024](https://arxiv.org/abs/2401.06059) and [Yang et al. 2023](https://arxiv.org/abs/2311.04850) | GPT-2 models from scratch; rephrased benchmark samples | Pretraining contamination changes downstream results; n-gram matching misses rephrased copies. | Benchmark accuracy, not paired likelihood; no overlap-typed dose-response. |
| [Zuo et al. 2026](https://arxiv.org/abs/2607.04969) | preliminary, scale not stated in the abstract | Reuse can keep helping beyond four epochs; a scheduler is future work. A counterpoint to H2a. | Evidence is preliminary. |
| [Nemecek et al. 2026](https://arxiv.org/abs/2603.08913) | genomic language models | Combines perplexity, canary and membership-inference audits; repetition and capacity matter; no single attack suffices. | Privacy framing; no epoch-by-size grid; no leak link. |

### Context for choices

- Small models are productive under fixed data budgets: [TinyStories](https://arxiv.org/abs/2305.07759) shows coherent text below 10M parameters on curated data, and the [BabyLM findings](https://arxiv.org/abs/2504.08165) report that data-limited training can beat far larger corpora.
- [Tao et al. 2024](https://arxiv.org/abs/2407.13623v3) argue the best vocabulary grows with compute. That is why the tokenizer sweep covers 4k, 8k and 12k.
- [AstroSage-Llama-3.1-8B](https://arxiv.org/abs/2411.09012) shows what a large model adapted on astronomy papers can do. AstroLM asks a different question: what a 4–30M model trained from scratch learns, stores and leaks.
- Simple English and English Wikipedia are published as aligned article and sentence pairs ([Kauchak simplification datasets](https://cs.pomona.edu/~dkauchak/simplification/), 137K aligned sentence pairs in v1.0). That is the basis for using Simple pages as natural paraphrase siblings.

### Search protocol and limits

Queries were run on 29 Oct 2026 for: memorization capacity, data-constrained scaling, warmup-stable-decay, deduplication, contamination, canaries, Simple/English Wikipedia alignment, and 2026 work on repetition and memorization. A work was included only if its page was opened. This is a one-session scan, not a systematic review; repeat it on arXiv and Google Scholar before any submission.

## The research gap

Repetition, memorization and contamination have each been studied, but not together, not at 4–30M parameters, and not as an effect on the number a small-model report actually prints.

**Identified gap.** Among the works reviewed, repetition (Muennighoff, Hernandez, Chudnovsky), memorization (Carlini, Tirumala, Morris) and contamination (Lee, Jiang, Yang) are studied separately, mostly at 100M parameters and above or on general web text. None puts the three on one set of checkpoints, measures edit-level and paraphrase-level leakage as a paired before/after change in held-out bits-per-byte, and converts that change into a bias bound on the test number a small domain model reports.

**Why it is worth closing**

- A small domain model is judged by one test bits-per-byte figure. If paraphrase leakage inflates it, a standard dedup report would not show that.
- With scarce data, epoch count is the main lever, and the literature is split on how far it can go: about four epochs in Muennighoff et al., beyond four in Zuo et al.
- It is buildable in 28 days: one licence-audited corpus, three model sizes, two training-data variants, one audit kit.
- It is falsifiable: every hypothesis in the next section has a numeric threshold and a rule for rejecting it.

**Out of scope:** claims about large models, privacy guarantees, new architectures, instruction tuning, retrieval, and any factual-authority claim.

## Research questions and hypotheses

Five questions, eleven hypotheses; four hypotheses are confirmatory and carry the headline claims, the rest are labelled exploratory before any data exists.

| ID | Question | Pillar | Status | Tier |
| --- | --- | --- | --- | --- |
| RQ1 | Under the same tokenizer, data, context and token budget, does the 29.4M model reach lower test bits-per-byte than the 10.3M model and than a 5-gram model? (the supplied spec's primary question) | Learn | Confirmatory | P0 |
| RQ2 | How do validation BPB and memorization change with passes E ∈ {1,2,4,8} and model size, and what does equal-compute repetition cost versus unique data? | Learn and Store | Confirmatory | P1 |
| RQ3 | What fraction of an exact-duplicate benefit do edited and paraphrased siblings transfer, and can that become a validated bias bound on reported test BPB? | Leak | Confirmatory | P1 |
| RQ4 | Which span types (prose, numerals, units, designations, proper nouns) carry the gain over the 5-gram model and between sizes? | Learn | Exploratory | P2 |
| RQ5 | Do minimal-pair items separate stored facts from facts the model cannot know? | Store | Exploratory | P2 |

### Pre-registered hypotheses

`E*` is the pass count chosen on validation by one rule: the smallest E whose next doubling improves validation BPB by less than 1%. All comparisons are paired, with cluster bootstrap intervals (Statistical plan).

| ID | Prediction | Decision rule |
| --- | --- | --- |
| H1a | At E\*, test BPB of the 29.4M model is at least 2% lower than the 10.3M model's, averaged over three seeds. | Relative-difference interval excludes 0 and the point estimate is at least 2%. |
| H1b | The 10.3M model is at least 10% lower than the registered 5-gram model. | Same rule with a 10% threshold. |
| H1c | Spec acceptance, descriptive: at least 70% of sampled 29.4M outputs rated coherent and on-domain; fewer than 10% show a repeated 4-gram loop. | Reported as met or not met; not part of the confirmatory family. |
| H2a | Equal compute, validation BPB after 4 passes over D/4 unique tokens is within 3% of 1 pass over D for the 10.3M and 29.4M models; 8 passes over D/8 is more than 3% worse for the 29.4M model. | Both intervals on the relative difference fall on the predicted side of 3%. |
| H2b | Membership-inference AUC (train versus validation windows) rises with E for every size, and at E = 8 the 29.4M AUC exceeds the 4.5M AUC by at least 0.05. | Monotone in E for all sizes, and the gap interval excludes 0.05 from below. |
| H2c | Canary bits memorized rise with multiplicity and size: Spearman ρ ≥ 0.8 across the 3×3 cells at E = 4. | Point estimate ≥ 0.8 with a permutation p < 0.05. |
| H3a | For the 29.4M model at E = 4, the transfer ratio of edited copies at Jaccard 0.90 is at least 0.6 and does not increase as Jaccard falls. | Lower bound of the interval ≥ 0.6, and monotone across the four levels. |
| H3b | At E = 4, paraphrase transfer τ\_para is larger for the 29.4M model than for the 10.3M model, and the 10.3M value is at most 0.25. | Paired difference interval above 0, and the 10.3M upper bound ≤ 0.25. |
| H3c | Leak-adjusted BPB is calibrated: on held-out probe documents its predicted leak benefit has mean absolute error ≤ 0.01 BPB and a regression slope in \[0.7, 1.3\]. | Both criteria met under 5-fold cross-validation over probe documents. |
| H4 | Exploratory: at least 60% of the 5-gram-minus-29.4M BPB gap sits on lowercase prose spans; numerals plus designations account for under 15%. | Reported with intervals; no pass/fail claim. |
| H5 | Exploratory: minimal-pair accuracy ranks pool K\_train > pool K\_excl ≥ pool K\_post, and K\_post lies inside the 95% interval around 50%. | Reported with intervals; K\_post away from 50% flags a flawed item set. |

**Multiplicity.** The confirmatory family is {H1a, H1b, H2a, H3a}, controlled with Holm at α = 0.05. Everything else is exploratory, controlled with Benjamini–Hochberg at q = 0.10 and described as such.

**Commitment on failure.** A rejected hypothesis is reported as rejected. Thresholds are not changed after data exists; any protocol change is a dated, signed decision record listed in the report.

## Proposed study: Learn / Store / Leak

AstroLM is audited on one set of checkpoints along three axes: what it learns (held-out bits-per-byte), what it stores (memorization readouts), and what its own evaluation leaks (paired probe documents).

&#91;embedded content: Learn / Store / Leak design - three lanes, two training variants\]

Each lane reads one pool through the trained models. Validation (3% of clusters) selects checkpoints and a 1% cloze reserve feeds the minimal-pair items; neither appears in the picture. M0 is trained on the clean training split plus canaries. M1 is identical except that it also trains on siblings of the probe documents.

| Pillar | Question | Instrument | Output |
| --- | --- | --- | --- |
| Learn | What generalizes to unseen astronomy text? | Token-weighted NLL divided by UTF-8 bytes on sealed test windows, split by span type | BPB with paired cluster-bootstrap interval |
| Store | What does the model retain from its training set? | Loss-threshold membership AUC; 64-token prefix then 32-token greedy extraction; inserted random canaries against never-inserted control canaries | Three numbers per checkpoint |
| Leak | How much do near-copies and paraphrases inflate a held-out score? | Probe documents compared under M0 and M1: untouched controls, exact copies, edited copies at Jaccard 0.90, 0.80, 0.65, 0.50, and natural paraphrase siblings from Simple English Wikipedia | Transfer ratio τ per overlap type; leak-adjusted test BPB |

The pillars share checkpoints, so every Learn number can be read next to the Store and Leak numbers of the very same model.

## Novelty and contribution claims

Four methodological contributions and one resource, each set against a named prior work; none is a new architecture, and the novelty is in the measurement design.

| ID | Contribution | Why it is new | Closest prior work |
| --- | --- | --- | --- |
| C1 | Paired leak probe | Every probe document is scored by a model without its siblings (M0) and one with them (M1). The change is divided by the exact-duplicate change to give a transfer ratio τ per overlap type: exact, edited at four Jaccard levels, natural paraphrase. | Jiang 2024 and Yang 2023 measure benchmark-score effects; Lee 2022 counts overlap but not its effect on likelihood. |
| C2 | Leak-adjusted BPB (LA-BPB) | The real test set's nearest-neighbour similarity profile is passed through the probe dose-response curve to give a bias bound on the reported test BPB, with calibration criteria fixed in advance (H3c). | No counterpart found in the reviewed papers. |
| C3 | Epoch-by-size surface from one trunk per size | A warmup-stable-decay trunk per model size with cooldown branches at 1, 2, 4 and 8 passes, plus equal-compute repeated-versus-unique arms, at 4.5M–29M parameters on a narrow domain. | Muennighoff 2023 (up to 9B); Hägele 2024 (reuse of runs); Chudnovsky 2026 (exact replay, 34M–344M). |
| C4 | Control-calibrated memorization readouts on the same checkpoints | Extractability, membership AUC and canary bits measured for every size and pass, with never-inserted control canaries as the baseline, so no copyrighted text needs to be shown. | Carlini 2019 and 2022; Morris 2025; Nemecek 2026. |
| C5 | Resource | A licence-audited Wikipedia-astronomy recipe (manifests, not text), a Simple-Wikipedia paraphrase probe set, a public-domain era-shift challenge set, and the log of spec defects including the OpenStax finding. | No direct counterpart; the value is reuse. |

### How this differs from the three closest works

**Chudnovsky et al. 2026.** Same scale band and the same worry about repeated text, but they replay exact documents inside a web corpus and read evaluation loss. AstroLM varies the overlap type, reads paired per-document changes, and uses them to correct the test number.

**Morris et al. 2025.** They measure how much a model can memorize, near 3.6 bits per parameter. AstroLM uses canaries as a calibrated readout inside a natural-text run and treats the capacity curve only as a stretch item (S1) with a pre-stated expectation.

**Nemecek et al. 2026.** A multi-readout memorization audit already exists for genomic models, framed as privacy risk. AstroLM adopts the multi-readout idea for astronomy text and ties it to evaluation validity, epochs and model size.

**Not claimed:** a new scaling law; results that transfer to large models; privacy guarantees; that Simple-Wikipedia paraphrases represent all paraphrase leakage; or that WSD matches cosine everywhere (a 10.3M cosine check, EXP-SCHED, tests it here).

## System architecture

The project is a chain of frozen artefacts. Each stage reads only the hashed output of the stage before it, so any number in the report can be traced back to a data manifest, a config and a commit.

| Stage | Component | Input → output | Frozen by | Gate |
| --- | --- | --- | --- | --- |
| 1 | Corpus builder | Wikipedia dump (cutoff 2023-11-01), category tree → cleaned articles, manifest of titles, revision IDs and licences | Manifest SHA-256 | G1 (Day 7) |
| 2 | Splitter | Cleaned articles → clusters (shared lead entity, near-duplicate hashing) → train 91 / val 3 / test 3 / probe 2 / cloze-reserve 1 | Split file + seed | G1 |
| 3 | Probe and canary builder | Probe docs, Simple-Wikipedia siblings, edited copies at Jaccard 0.90 / 0.80 / 0.65 / 0.50, random-token canaries and controls → M0 and M1 training additions | Probe manifest | G1 |
| 4 | Tokenizer | Train split only → byte-level BPE (4k / 8k / 12k proxies, then one frozen choice) | Vocab hash | Day 12 |
| 5 | Trainer | Token shards → checkpoints, logs. Atomic resume, WSD schedule, branch rule | Config hash + git commit | G2 (Day 14) |
| 6 | Baselines | 5-gram Kneser-Ney (order sweep) on the same tokens | Table of BPB | G2 |
| 7 | Evaluation bundle | Val, test, cloze, probes, canaries, rating prompts → one frozen folder | Bundle hash, sealed test | Day 13 |
| 8 | Test campaign | All registered checkpoints evaluated once against the sealed test | Campaign log | G3 (Day 21) |
| 9 | Analysis | Paired cluster bootstrap, DiD, τ, LA-BPB, memorization readouts → figures, tables, claims ledger | Notebook + data hash | G4 (Day 28) |
| 10 | Release | Manifests, configs, code, report, audit kit | Release tag | G4 |

### Design rules

- **One-way flow.** Nothing downstream edits anything upstream. A defect found late is logged and handled by a documented rerun, not a quiet fix.
- **Hash chain.** Every run log stores the hash of its data manifest, tokenizer, config and code commit. The analysis refuses to load a run whose chain is broken.
- **Sealed test.** The test split is read only by the single campaign in Week 4. Validation drives every earlier decision.
- **Two training sets.** M0 is the clean corpus plus canaries. M1 is M0 plus the probe siblings. They share the seed, the order of shared documents and the schedule, so differences are attributable to the added text.
- **Proxy first.** The trainer is finished before the tokenizer is chosen (amendment A6), so a tokenizer decision never blocks code testing.

## Technical formulation

Every reported quantity has one definition, fixed before any model is trained. Symbols: x is a document, N\_b(x) its UTF-8 byte count, θ a model, c(x) the cluster of x.

**Learn: bits per byte.** Per-document and corpus BPB; the corpus value pools bytes, and uncertainty comes from resampling clusters, never documents.

```latex
\mathrm{BPB}_\theta(x)=\frac{-\sum_{t}\log_2 p_\theta(w_t\mid w_{<t})}{N_b(x)},\qquad \mathrm{BPB}_\theta(\mathcal D)=\frac{\sum_{x\in\mathcal D}N_b(x)\,\mathrm{BPB}_\theta(x)}{\sum_{x\in\mathcal D}N_b(x)}
```

**Schedule: warmup-stable-decay with branches.** The trunk follows the first line to the stable plateau. A branch for E passes starts its decay at 0.8E passes and ends at E, so every branch is a complete run of its own length.

```latex
\eta(s)=\eta_{\max}\cdot\begin{cases}s/s_w & s<s_w\\ 1 & s_w\le s<s_d\\ 0.1+0.9\bigl(1-\sqrt{(s-s_d)/(S-s_d)}\bigr) & s\ge s_d\end{cases},\qquad s_d=0.8\,S_E
```

**Paired comparison.** For two models A and B on the same clusters, the effect is the mean per-cluster BPB difference. Confidence limits use 10,000 cluster resamples, with Holm correction for the confirmatory family.

```latex
\Delta_{AB}=\frac1K\sum_{k=1}^{K}\bigl[\mathrm{BPB}_A(\mathcal D_k)-\mathrm{BPB}_B(\mathcal D_k)\bigr]
```

**Store: three readouts.** Membership AUC scores training documents against matched held-out ones by loss. Extractability is the fraction of training documents whose next 32 tokens are reproduced greedily from a 64-token prefix. Canary exposure in bits compares a canary's loss with the losses of never-inserted controls.

```latex
\mathrm{Ext}_\theta=\frac{1}{|\mathcal T|}\sum_{x\in\mathcal T}\mathbb 1\!\left[\mathrm{greedy}_\theta(x_{1:64})_{1:32}=x_{65:96}\right],\qquad \mathrm{bits}(c)=\log_2\frac{|\mathcal C_{\mathrm{ctrl}}|+1}{1+\#\{c'\in\mathcal C_{\mathrm{ctrl}}:\ \ell_\theta(c')\le \ell_\theta(c)\}}
```

**Leak: difference in differences and transfer ratio.** For a probe document x in group g, the benefit is how much its BPB falls when its siblings join training. Groups are Z (untouched), C (exact copy), B\_j (edited copies at Jaccard level j) and A (natural paraphrase).

```latex
b_g=\mathbb E_{x\in g}\bigl[\mathrm{BPB}_{M_0}(x)-\mathrm{BPB}_{M_1}(x)\bigr]-b_Z,\qquad \tau_g=\frac{b_g}{b_C}
```

**Leak-adjusted test BPB.** Each test document gets a nearest-neighbour similarity s(x) to the training set. The dose-response f̂ maps similarity to benefit, and the adjusted score removes the expected benefit. The 5-fold calibration holds out probe groups and must reach MAE ≤ 0.01 BPB with a slope in \[0.7, 1.3\] (H3c).

```latex
\mathrm{LA\text{-}BPB}_\theta=\mathrm{BPB}_\theta(\mathcal D_{\mathrm{test}})+\frac{\sum_{x}N_b(x)\,\hat f\bigl(s(x)\bigr)}{\sum_{x}N_b(x)}
```

**Capacity check (stretch S1).** Morris et al. report about 3.6 bits per parameter. The test compares total canary bits recovered with that line for each size.

```latex
\text{expected capacity}\approx 3.6\,P\ \text{bits}\ \Rightarrow\ 4.5\text{M}:\ 2.0\ \text{MB},\ 10\text{M}:\ 4.5\ \text{MB},\ 29\text{M}:\ 13\ \text{MB}
```

Multiplicity: confirmatory family H1a, H1b, H2a, H3a under Holm at α = 0.05; every other test under Benjamini–Hochberg at q = 0.10 and labelled exploratory.

## Data and benchmark design

The corpus is licence-clean by construction: only Wikipedia text (CC BY-SA) and public-domain books enter training. Corpus size is not yet measured, because the sandbox could not reach the Wikipedia API; Day 4 measures it, and the plan is written for three scenarios.

### Sources

| Source | Use | Licence status | Fallback |
| --- | --- | --- | --- |
| English Wikipedia, astronomy category tree, dump dated 2023-11-01 | Main training, validation, test, probes | CC BY-SA 4.0; manifests carry titles and revision IDs, text is not redistributed | Wikimedia dump, then API with cutoff set to the retrieval date |
| Simple English Wikipedia | Paraphrase siblings for group A only. Never in M0 | CC BY-SA 4.0 | Drop group A; keep exact and edited groups |
| Project Gutenberg astronomy books, authors who died before 1955 | Era-shift challenge set and tokenizer-independent dev corpus | Public domain in the US | Fewer titles; challenge set shrinks |
| OpenStax, arXiv, ESA, NASA, Astronomy Stack Exchange | Excluded (amendment A1) | OpenStax terms restrict use for model training; arXiv licences are mixed per paper; the others are unaudited | NASA text may enter later as optional P3 after an audit |

### Splits and pools

Documents are grouped into clusters (shared lead entity, near-duplicate hashing), and whole clusters are assigned: **train 91%, validation 3%, test 3%, probe 2%, cloze-reserve 1%**. The probe pool supplies the leak groups; the reserve feeds the cloze benchmark.

| Pool | Content | Size target | Purpose |
| --- | --- | --- | --- |
| Probe groups Z, C, B(0.90 / 0.80 / 0.65 / 0.50), A | 100 documents per group (n=100 gives MDE 0.013 BPB at per-document SD 0.03) | 7 groups × 100 | H3a, H3b |
| Canaries | 100 random-token sequences of 32 tokens at multiplicity 1, 4, 16, plus 100 never-inserted controls | 1,200 inserted, 100 controls | H2c, S1 |
| Cloze K\_train, K\_excl, K\_post | Minimal-pair fact cloze from articles in training, excluded, and created after the cutoff (negative control) | 150 per pool | H5 |
| Gutenberg challenge | Era-shifted prose, 40 passages | Fixed list | Domain-shift reading |
| Rating set | 120 sampled generations, 40 double-rated | 120 | H1c |

### Scenarios for corpus size D (unique training tokens)

| Scenario | D | Consequence |
| --- | --- | --- |
| S | 8M | Epoch study still runs; 16,384-token batches; some groups shrink |
| M | 30M | Planned case |
| L | 100M | Compute cap binds; D is subsampled to D\_cap (72M / 107M / 157M for 90 GPU-hours at low / mid / high throughput) |

### Cleaning and the frozen evaluation bundle

Cleaning keeps article body text, drops tables, references, infobox markup and templates, normalises whitespace and keeps equations as plain text. Every step writes a counts line (documents in, documents out, bytes in, bytes out). The evaluation bundle on Day 13 holds validation, test (sealed), probes, canary lists, cloze items, rating prompts and a README with hashes; nothing in it changes afterwards.

## Baselines and experiment matrix

Eleven experiments, each tied to a hypothesis and a priority tier. P0 must finish for the study to exist; P1 carries the novelty; P2 and P3 are cut first.

| ID | Experiment | Models and settings | Tests | Tier |
| --- | --- | --- | --- | --- |
| EXP-TOK | Tokenizer proxies | 10M, one pass, 4k / 8k / 12k / 8k with split digits | Tokenizer choice (Day 12) | P0 |
| EXP-LR | Learning-rate sweep | Per size, {1e-4, 3e-4, 1e-3} | Peak LR (A4) | P0 |
| EXP-GRID | Epoch grid | 10M and 29M, WSD trunk to 6.4 passes, branches at 1, 2, 4, 8 | H1a, H1b, H2a | P0 |
| 5-GRAM | Baseline | Kneser–Ney, order sweep 3–6 | H1b | P0 |
| EXP-SEED | Seeds | 3 seeds for 10M and 29M at 1 and 4 passes | Noise floor, H1a | P1 |
| EXP-RVU | Equal-compute repeated vs unique | E passes over D/E tokens, 10M and 29M | H2a | P1 |
| EXP-LEAK | M1 with probe siblings | 10M and 29M, full grid | H3a, H3b, H3c | P1 |
| 4.5M arm | Small model | Same grid as 10M | H2b | P1 |
| EXP-SCHED | Cosine vs WSD | 10M, 4 passes | Branch equivalence | P2 |
| Span-type, cloze, Gutenberg | Evaluation slices | All checkpoints | H4, H5 | P2 |
| S1, naive-split, Pythia reference | Capacity curve, inflation under naive split, contaminated reference model | Selected | Stretch | P3 |

### Baselines and controls

- **5-gram Kneser–Ney** on the same token stream gives the floor that H1b (10M at least 10% lower BPB) must beat.
- **4.5M model** is the small end of the size axis; without it H2b (memorization rises with size) has two points.
- **Seeds** set the noise floor: a difference counts only if it exceeds the seed spread and the bootstrap interval.
- **Z group and control canaries** are the in-run negative controls for the leak and memorization readouts.
- **Branch-equivalence test**: a branch from the trunk must match a standalone run of the same length within seed spread, or the grid design is revised.

### Run order

Trainer and proxy runs first, then LR sweeps, then the 29M trunk (longest job), with 10M, 4.5M, seeds, RvU and M1 queued around it. All checkpoints are registered before the single test campaign.

## Statistical plan

The design is sized so that the confirmatory effects are detectable and the exploratory ones are labelled as such. Power figures come from simulations in the planning script (assumption-labelled: per-cluster BPB difference SD 0.05; per-document leak-difference SD 0.03 to 0.06).

### Power

| Comparison | Design | Sample | Detectable effect (80% power) |
| --- | --- | --- | --- |
| Model A vs B on test (H1a, H1b) | Paired cluster bootstrap | 400 test clusters, SD 0.05 | about 0.007 BPB (simulated power 0.45 at 0.005, 0.97 at 0.01); the 2% target is about 0.02 BPB if the base is near 1.0, well above it |
| Leak group vs untouched group, unpaired | Raw per-document BPB | 100 per arm, SD 0.25 | 0.109 BPB, too coarse |
| Leak group vs untouched group, paired DiD (H3a, H3b) | M0 vs M1 change per document | 100 per group, SD 0.03 | **0.013 BPB**; 0.026 if SD is 0.06 |
| Rating agreement (H1c) | 40 double-rated items, observed agreement 0.85, chance 0.5 | 40 | kappa 0.70, standard error about 0.11 |

The paired design is the reason the leak study is feasible: it shrinks the minimum detectable effect from 0.109 to 0.013 BPB by cancelling document difficulty.

### Rules fixed before training

1. **Confirmatory family**: H1a, H1b, H2a, H3a. Holm correction at α = 0.05. Everything else is exploratory with Benjamini–Hochberg at q = 0.10 and is reported as such.
2. **Uncertainty**: 10,000 cluster-bootstrap resamples for every BPB difference; seeds are reported as a separate spread, not merged into the interval.
3. **Seeds**: three for 10M and 29M at 1 and 4 passes; single seed elsewhere, which limits claims for those cells to descriptive.
4. **One test pass**: every registered checkpoint is evaluated once in the Week 4 campaign. No model selection on test; validation chooses learning rate and tokenizer.
5. **Failure commitment**: a hypothesis that fails is reported as failed, with its interval, in the same table as the ones that pass.
6. **Stopping rule**: a run that diverges is repeated once at half the learning rate and logged as a deviation; no run is dropped silently.
7. **Rater protocol**: two raters (the author and one volunteer) for 40 of the 120 outputs; disagreement is reported through kappa, and the single-rater remainder is flagged as such.

### Calibration of LA-BPB (H3c)

Five-fold cross-validation holds out one probe group at a time, fits the dose-response f̂ on the rest and predicts the held-out benefit. Success is mean absolute error ≤ 0.01 BPB and a predicted-vs-observed slope in \[0.7, 1.3\]. If it fails, LA-BPB is reported only as a descriptive bound and the claim C2 is withdrawn from the abstract.

## Compute and feasibility

The compute fits one RTX 3050 laptop only if the corpus stays at or below about 107M tokens (mid throughput) in a 90 GPU-hour budget; the plan defines that cap and a rule for hitting it. Throughput figures are assumptions until the Day 2 and Day 12 benchmarks replace them.

### Models

| Model | Layers / width / heads | Parameters at V = 8,000 | FLOPs per token (fwd+bwd) | Assumed throughput (low / mid / high) |
| --- | --- | --- | --- | --- |
| 4.5M | 3 / 256 / 4 | about 4.5M | about 29 MFLOPs | about 69k / 90k / 90k tokens/s (launch-bound cap) |
| 10M | 4 / 384 / 6 | about 10M | about 66 MFLOPs | about 30k / 46k / 68k tokens/s |
| 29M | 8 / 512 / 8 | about 29M | about 190 MFLOPs | about 11k / 16k / 24k tokens/s |

Estimated memory at micro-batch 16×256 is about 1.8 GB for 29M (2.8 GB at 32×256) including logits and the CUDA context, inside 4 GB with margin; the Day 2 benchmark confirms it.

### GPU-hours by corpus scenario (mid throughput band)

Training tokens are counted as multiples of D per model: 29M needs 31.7 D (grid 9.4, extra seeds 8.4, RvU 3.0, leak 9.4, LR sweep 1.5); 10M needs 41.7 D (the same plus tokenizer proxies 4.0, naive-split 4.0 and cosine check 2.0); 4.5M needs 10.9 D (grid and LR sweep).

| D | Low / mid / high throughput | Verdict |
| --- | --- | --- |
| 8M | 10.0 / 6.7 / 4.6 GPU-h | Comfortable; room for every P2 and P3 item |
| 30M | 37.6 / 25.2 / 17.1 GPU-h | Planned; about 25 GPU-hours at mid throughput, 17 to 38 across the band |
| 100M | 125.4 / 84.1 / 57.2 GPU-h | Only at mid or high throughput; otherwise subsample to D\_cap |

**D\_cap rule.** Set D = min(corpus, D\_cap) with D\_cap = 72M, 107M or 157M tokens for low, mid or high measured throughput at a 90 GPU-hour budget. If the corpus is larger, take a random sample of clusters; splits are drawn after capping.

### Human effort

| Tier | Hours | Contents |
| --- | --- | --- |
| P0 | about 110 | Corpus, tokenizer, model and trainer, 5-gram, 10M and 29M grid, test campaign, report |
| P1 | about 68 | M1 leak runs, canaries, readouts, 4.5M, RvU, seeds, DiD and LA-BPB |
| P2 + P3 | about 22 | Span-type slices, cloze pools, Gutenberg, digit tokenizer, cosine check, stretch items |
| Contingency | 25 | Debugging, reruns, licence and data surprises |

Total about 200 hours plus 25 contingency over 28 days. **Solo** at about 7 hours a day the core (P0 + P1, about 178 hours) fits only marginally; a team of three fits comfortably. A solo run should plan to cut P2 and P3 on Day 14 if the G2 gate is late.

### What would break feasibility

- Thermal throttling drops throughput below the low band: the D\_cap rule absorbs it.
- The Wikipedia pull is blocked: the fallback chain in the data section takes over, at a cost of 1 to 2 days.
- Resume bugs corrupt a multi-day trunk: atomic checkpoints and the Day 10 resume test are gate items.

## Four-week plan

The plan freezes all inputs by Day 14 (Sun 15 Nov), so Weeks 3 and 4 only train, evaluate once and write. Week 1 builds the data and registers the study; Week 2 builds and tests the machinery; Week 3 runs the queue; Week 4 opens the test split once.

&#91;embedded content: four-week plan · 4 weeks, 7 gates\]

Days 7, 14, 21 and 28 are gate and buffer days: they hold no new scope. The day-by-day tab lists tasks, night jobs and exit checks for each of the 28 days.

Day-by-day tasks, night jobs and exit checks: Day-by-day plan

### Gates

| Gate | Date | Pass criteria | If missed |
| --- | --- | --- | --- |
| G0 | Day 1, Mon 2 Nov | Hypotheses, analysis rules and failure commitment committed; licence status of every source logged | No source is downloaded until the licence list is fixed |
| G1 | Day 7, Sun 8 Nov | Dataset v1.0.0 frozen: manifest, splits, probe and canary manifests hashed; cleaning counts table; corpus size measured and scenario S, M or L chosen | Wikipedia-only corpus; drop paraphrase group A and the Gutenberg challenge |
| Tokenizer | Day 12, Fri 13 Nov | Proxy results on validation; vocabulary hash frozen | Use 8k |
| Eval bundle | Day 13, Sat 14 Nov | Validation, sealed test, probes, canaries, cloze and rating prompts hashed | Slip cloze and rating items to P2 |
| G2 | Day 14, Sun 15 Nov | Shape, overfit-one-batch, bit-exact resume and branch-equivalence tests pass; 5-gram done; throughput measured; D\_cap set; run queue written | Cut P3 and the cosine check; start the queue anyway |
| G3 | Day 21, Sun 22 Nov | Every P0 and P1 run finished or declared cut; checkpoints registered; test still sealed | Open the campaign with the registered subset |
| G4 | Day 28, Sun 29 Nov | Test campaign done; claims ledger complete; reproduction from manifests passes on a 10M run; release tagged | Publish validation results and state that the test was not opened |

### Cut-lines, in order

1. Gutenberg challenge and paraphrase group A (if G1 slips).
2. P3: capacity curve, naive-split neural run, Pythia reference; and the cosine check (if G2 slips).
3. Third seed for 29M, then M1 at 29M restricted to the 1-pass and 4-pass branches (if G3 is at risk).
4. P2 slices: span-type BPB, cloze pools, digit tokenizer.

P0 and the paired leak study for 10M are never cut: without them there is no study.

## Risk register

The five highest risks are schedule, a blocked or thin corpus, a leak design that shows nothing, a resume or divergence bug in a long run, and licence error. Likelihood and impact are my estimates (L low, M medium, H high), not measured.

| # | Risk | Likelihood | Impact | Early signal | Response |
| --- | --- | --- | --- | --- | --- |
| R1 | Solo schedule overruns (core about 178 h in 28 days) | H | H | G1 or G2 slips by more than a day | Cut-lines in order; P3 and P2 go first; pair with a second person for evaluation work if possible |
| R2 | Wikipedia pull blocked or corpus below 8M tokens | M | H | Day 3 count under target | Fallback chain (dump, API); scenario S settings; widen category depth |
| R3 | Leak shows no transfer at any overlap level | M | M | Day 18 probe readout | A null result is reported with its interval; H3a stays in the confirmatory family; LA-BPB reduces to a bound |
| R4 | Probe groups too small after splitting (2% probe share) | M | M | Fewer than 100 probe docs per group | Reduce to 60 per group (MDE rises to about 0.017 BPB at SD 0.03) and state it |
| R5 | Resume bug corrupts the 29M trunk | M | H | Day 10 bit-exact resume test fails | Atomic checkpoint writes; fix before G2; never start the trunk without the test |
| R6 | Fp16 loss spikes or divergence | M | M | Loss spike over 3× baseline | GradScaler logging; one rerun at half LR; if spikes recur, switch to bf16 (supported on Ampere GPUs such as the RTX 3050) or fp32 for the smallest models |
| R7 | WSD branches differ from standalone runs | M | H | Branch-equivalence test exceeds seed spread | Switch to separate cosine runs at the grid points; the grid shrinks to three pass counts (1, 2, 4) |
| R8 | Thermal throttling cuts throughput | H | M | Tokens per second under the low band | D\_cap rule; run at night; cooling pad |
| R9 | Licence or terms error (Wikipedia attribution, Gutenberg boundaries) | L | H | Any source with unclear terms | Release manifests, not text; retain attribution file; verify Gutenberg per-title |
| R10 | Test leakage through repeated peeking | L | H | Any test read before G3 | Single campaign; test path outside the working tree until Day 22 |
| R11 | Simple-Wikipedia pairs are not true paraphrases of the English article | M | M | Low similarity on audit sample | Audit 30 pairs by hand; keep only pairs above an overlap threshold; label group A as "Simple-English siblings" |
| R12 | Rater disagreement makes H1c unreadable | M | L | Kappa under 0.4 | Report as exploratory; use a rubric with three anchors |
| R13 | HF dataset configuration differs from the one assumed | M | M | Streaming filter returns no astronomy articles | Day 1 check; dump fallback |
| R14 | Scope creep from stretch items | M | M | Any P3 item started before G3 | Tier rules; P3 only after G3 |

### Ethics and licensing

The study trains a small model on public text and publishes no model weights trained on restricted data. Random-token canaries contain no personal data. Wikipedia text is CC BY-SA, so any released derivative text keeps attribution and share-alike; the plan releases manifests and code, not the corpus.

## Contributions and deliverables

The project ends with a tagged release, a report and an audit kit, each checked against the list below on Day 28.

- [ ] **Data recipe**: manifests of titles, revision IDs and licences; split file; probe, canary and cloze manifests; cleaning counts.
- [ ] **Code**: tokenizer, trainer with atomic resume and WSD branches, 5-gram baseline, evaluation harness, leak and memorization probes, analysis notebook.
- [ ] **Models**: 4.5M, 10M and 29M checkpoints with config, log and hash (weights released only if the licence review allows it).
- [ ] **Learn / Store / Leak audit kit**: one command that, given a checkpoint and the frozen bundle, outputs BPB, membership AUC, extractability, canary bits and the leak transfer ratios.
- [ ] **Report**: pre-registered hypotheses with outcomes (pass, fail, exploratory), figures, claims ledger linking every number to a run and a hash.
- [ ] **Limitations note**: what the study cannot say (large models, other domains, other paraphrase types).
- [ ] **Reproduction check**: a fresh clone reproduces the 10M validation BPB from manifests within seed spread.

### Preprint outline

1. Introduction: why small domain models need a leak-aware evaluation.
2. Related work: the closest eleven works and the gap.
3. Study design: M0 and M1, probe groups, readouts.
4. Setup: corpus, tokenizer, models, schedule.
5. Results: Learn (H1), Store (H2), Leak (H3), exploratory (H4, H5).
6. LA-BPB: calibration and the corrected test numbers.
7. Limitations and ethics.
8. Resource description and reproduction.

## Red-team review

Twelve objections a sceptical reviewer would raise, with the plan's answer and what it still leaves open.

| # | Objection | Answer in the plan | Still open |
| --- | --- | --- | --- |
| 1 | "A 29M model on astronomy text says nothing about real LLMs." | The claims are scoped to small domain models; the audit kit is the transferable part | Scale transfer is untested |
| 2 | "Simple Wikipedia pairs are not typical paraphrases." | Labelled as one natural paraphrase type; edited copies span four overlap levels | Other paraphrase styles unmeasured |
| 3 | "Wikipedia is in every pre-training corpus, so contamination is trivial." | The study trains from scratch; contamination is the object of measurement, not a defect | Applies to models trained here only |
| 4 | "WSD branches are not independent runs." | Branch-equivalence test (H2 design check) and seed spread decide it | Shared trunk correlates errors across E |
| 5 | "Only one seed for most cells." | Three seeds at the two key epochs; other cells are descriptive | Interaction effects between seeds and size |
| 6 | "LA-BPB looks circular: it uses the probes it later validates." | Cross-validation holds out whole probe groups; success criteria fixed in advance | Probe-to-test similarity shift |
| 7 | "Extractability at 64→32 tokens is arbitrary." | Canaries and membership AUC triangulate it; sensitivity check at 32→16 | Other prefix lengths |
| 8 | "Test BPB is contaminated by near-duplicates inside Wikipedia." | Cluster-level splits; test similarity profile reported; LA-BPB bounds the bias | Residual semantic overlap |
| 9 | "28 days is too short for this scope." | Tiers and cut-lines; solo core is marginal and stated as such | Solo completion of P1 is not guaranteed |
| 10 | "Self-assigned ratings prove nothing." | The scorecard is a rubric-based self-assessment with named evidence; external review is a stated next step | No independent rater yet |
| 11 | "Fp16 on a laptop GPU is unstable." | GradScaler, divergence rule, logged loss scale | Rare late spikes |
| 12 | "Hypotheses were fit after seeing data." | Pre-registration on Day 1; all deviations logged; exploratory results labelled | Informal knowledge of related results |

The review adds three items to the plan: a sensitivity check for extractability (32→16 tokens), a probe-to-test similarity report, and explicit labelling of group A.

## Glossary

| Term | Meaning here |
| --- | --- |
| BPB | Bits per byte: negative log2 likelihood divided by the UTF-8 byte count; comparable across tokenizers |
| Cluster | A group of documents sharing a lead entity or near-duplicate hash; splits and bootstrap resampling use clusters, never single documents |
| WSD | Warmup-stable-decay learning-rate schedule: ramp, constant plateau, short decay |
| Branch | A copy of the trunk taken at 0.8E passes and decayed to finish at E passes |
| M0 / M1 | The clean training set with canaries / that set plus the probe siblings |
| Probe group | Z untouched, C exact copy, B edited copy at Jaccard 0.90, 0.80, 0.65 or 0.50, A natural paraphrase sibling |
| DiD | Difference in differences: the M0-to-M1 change of a probe group minus that of group Z |
| τ (transfer ratio) | A group's DiD benefit divided by the exact-copy benefit |
| LA-BPB | Leak-adjusted test BPB, the test BPB corrected by the expected benefit from similar training text |
| Canary | A random-token sequence inserted into training to measure memorization; control canaries are never inserted |
| Membership AUC | Area under the curve for telling training documents from held-out ones by loss alone |
| Extractability | Share of training documents whose continuation the model reproduces greedily from a prefix |
| Jaccard | Overlap of two token sets divided by their union |
| D, D\_cap | Unique training tokens; the cap set by the GPU-hour budget |
| P0–P3 | Priority tiers: must finish, carries novelty, desirable, stretch |
| Gate | A dated checkpoint with pass criteria and a fallback |
| MDE | Minimum detectable effect at 80% power |

## References

### Sources consulted while preparing this blueprint

- [Muennighoff et al. 2023, Scaling data-constrained language models](https://arxiv.org/abs/2305.16264)
- [Hägele et al. 2024, Scaling laws and compute-optimal training beyond fixed durations](https://arxiv.org/html/2405.18392v3)
- [Hu et al. 2024, MiniCPM (WSD schedule)](https://arxiv.org/abs/2404.06395)
- [Carlini et al. 2022, Quantifying memorization across neural language models](https://arxiv.org/abs/2202.07646v3)
- [Carlini et al. 2019, The Secret Sharer](https://ar5iv.arxiv.org/html/1802.08232)
- [Tirumala et al. 2022, Memorization without overfitting](https://arxiv.org/abs/2205.10770)
- [Biderman et al. 2023, Pythia](https://www.arxiv.org/abs/2304.11158)
- [Morris et al. 2025, How much do language models memorize?](https://arxiv.org/abs/2505.24832)
- [Lee et al. 2022, Deduplicating training data makes language models better](https://arxiv.org/abs/2107.06499)
- [Hernandez et al. 2022, Scaling laws and interpretability of learning from repeated data](https://arxiv.org/abs/2205.10487v1)
- [Chudnovsky et al. 2026](https://arxiv.org/html/2606.24998v1)
- [Jiang et al. 2024, Investigating data contamination for pre-training language models](https://arxiv.org/abs/2401.06059)
- [Yang et al. 2023, Rethinking benchmark and contamination](https://arxiv.org/abs/2311.04850)
- [Zuo et al. 2026](https://arxiv.org/abs/2607.04969)
- [Nemecek et al. 2026](https://arxiv.org/abs/2603.08913)
- [Eldan and Li 2023, TinyStories](https://arxiv.org/abs/2305.07759)
- [BabyLM findings](https://arxiv.org/abs/2504.08165)
- [Tao et al. 2024, Scaling laws with vocabulary](https://arxiv.org/abs/2407.13623v3)
- [AstroSage-Llama-3.1-8B](https://arxiv.org/abs/2411.09012)
- [Kauchak simplification datasets](https://cs.pomona.edu/~dkauchak/simplification/)

Findings are cited as the abstracts or pages stated them at the time of search; the short descriptive titles after the author names are paraphrases and may differ from the published titles, so check each against its page before citing. The 2026 works are recent and some are preliminary.

### Background, not opened in this session

Verify these before citing: Kaplan et al. 2020 (scaling laws); Hoffmann et al. 2022 (compute-optimal training); Radford et al. 2019 (GPT-2); Sennrich et al. 2016 (byte-pair encoding); Kneser and Ney 1995 (smoothing); Loshchilov and Hutter 2019 (AdamW). Also verify before Day 1: OpenStax terms of use, the `wikimedia/wikipedia` dataset configurations, and per-title public-domain status on Project Gutenberg.

## Attribution and originality

The model, corpus plan and 30-day schedule come from the supplied AstroLM documentation set; this blueprint keeps its architecture, evaluation discipline and "no estimate presented as result" rule, and changes what the study is about, how data are sourced and how the 28 days are used (see the findings table). The structure follows the supplied GateOrchestra blueprint. The Learn / Store / Leak framing, the paired M0/M1 leak probe, LA-BPB and the WSD branch grid are proposed here as a design; none has been run, and every number in the compute and power sections is an estimate or simulation under stated assumptions. Ratings in the scorecard are my own rubric-based self-assessment, not an external review. Wikipedia text is CC BY-SA 4.0; the plan releases manifests, code and results, not the corpus.
