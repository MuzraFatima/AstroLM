# Dataset Specification — AstroLM v1

## 1. Principles

The corpus must be legally reviewable, reproducible from manifests, clean enough for a small model, and split without duplicate leakage. Corpus size is an output of collection—not a target to be filled with lower-quality text.

The project must set a UTC temporal cutoff before production collection: `TBD-CUTOFF`. Documents or revisions after the cutoff are excluded from training, validation, test, and tokenizer training. They may be used only in a separately labeled post-cutoff challenge set.

## 2. Source policy

| Source | Initial status | Requirement |
|---|---|---|
| OpenStax Astronomy 2e | allowed after license record | preserve edition, attribution, canonical URL, and section identity |
| English Wikipedia astronomy pages | allowed through a dated dump | preserve page/revision IDs, source URL, attribution path, and applicable license notices |
| NASA/APOD and NASA outreach | conditional | accept only records whose text provenance and third-party rights flags have been reviewed |
| ESA outreach | excluded by default | enable only after exact page/material terms are recorded as compatible with training and intended release |
| Astronomy Stack Exchange | excluded from baseline | optional only after quality value is demonstrated and revision-date license versions and attribution are captured |

Collection must honor access terms, robots directives where applicable, and rate limits. “Available online” is not a license.

## 3. Document schema

Each logical document is one JSONL record with at least:

```json
{
  "document_id": "stable-project-id",
  "source": "source-name",
  "source_record_id": "page-or-post-id",
  "title": "source title",
  "canonical_url": "https://...",
  "revision_id": "source revision or edition",
  "published_at": "ISO-8601-or-null",
  "retrieved_at": "ISO-8601",
  "author_attribution": ["..."],
  "license_id": "SPDX-or-project-license-id",
  "license_url": "https://...",
  "rights_notes": "third-party or redistribution notes",
  "raw_sha256": "...",
  "clean_sha256": "...",
  "pipeline_version": "git-commit-or-release",
  "language_score": 0.0,
  "quality_flags": [],
  "dedup_cluster_id": "...",
  "split": "train|validation|test|excluded"
}
```

Raw and cleaned text may live in separate restricted artifacts joined by `document_id`. Manifests must not contain the full copyrighted text.

## 4. Pipeline stages

1. **Acquire:** store an immutable raw snapshot or a reproducible source pointer; compute checksum.
2. **Parse:** extract prose while retaining headings needed for context.
3. **Normalize:** decode HTML entities, normalize newlines/whitespace, and apply Unicode NFC. Preserve case, punctuation, Greek letters, mathematical signs, scientific notation, coordinates, units, and catalog identifiers.
4. **Remove boilerplate:** use source-specific selectors and template fingerprints; do not use one global regex as the primary parser.
5. **Quality filter:** apply versioned, measurable rules and record every rejection reason.
6. **Deduplicate:** exact normalization hash, paragraph-level near-duplicate detection, then document-level near-duplicate clustering across all sources.
7. **Split:** assign entire deduplication clusters, never individual paragraphs, using the frozen algorithm in section 7.
8. **Tokenize and pack:** only after the split and tokenizer are frozen.

## 5. Quality rules

Threshold values are `TBD-CALIBRATE` on a labeled sample before full processing. The production report must include thresholds and acceptance/rejection counts for:

- detected language confidence
- prose character and paragraph counts
- alphabetic, markup, replacement-character, and repeated-line ratios
- parser success/failure
- reference/navigation/template contamination
- excessive lists, tables, fragments, or citation-only text
- source-specific metadata completeness

At least 50 randomly selected accepted and 50 rejected records per enabled source must be manually audited where source volume permits. The sample seed, labels, and disagreement resolution must be retained.

Regression fixtures must cover units, equations, Unicode symbols, negative values, dates, coordinates, object designations, headings, captions, and tables.

## 6. Deduplication

- Exact duplicates: hash canonicalized paragraph and document text.
- Near duplicates: use a documented MinHash/LSH or equivalent method over word n-grams.
- Calibrate similarity thresholds on labeled duplicate/non-duplicate pairs; do not select them solely to maximize removal.
- Build transitive duplicate clusters across sources.
- Prefer the best-provenanced/highest-quality representative; retain all source attributions in the cluster record.
- After splitting, run an independent cross-split overlap audit and fail the pipeline if exact duplicates remain.

Semantic paraphrases are not guaranteed to be removed. The residual risk must be quantified using nearest-overlap examples.

## 7. Split policy

- Target proportions: 94% train, 3% validation, 3% test by eligible document clusters, stratified by source.
- Use a versioned keyed hash of `dedup_cluster_id` and a recorded split seed.
- Report both document and token proportions; do not force exact percentages by moving individual documents after inspection.
- No test text may be used for tokenizer training, threshold tuning, prompt creation, hyperparameter selection, or checkpoint selection.
- Validation and test evaluation windows are generated once and hashed.
- A separate entity/time challenge set may be created from explicitly excluded material and must never be merged into headline in-distribution test results.

If a source is too small for meaningful per-source test reporting, combine it only under a preregistered rule or report the metric as underpowered rather than silently reallocating documents.

## 8. Mixture and repetition

Natural source proportions are the default. Any capping, oversampling, or weighting must be defined in a data configuration and treated as an experiment. The corpus report must show documents, bytes, words, selected-tokenizer tokens, and repeated training exposure per source.

Repeated passes over the corpus are not counted as new data.

## 9. Release requirements

Publish the collection/processing code, source manifest where permitted, document IDs, split assignments, checksums, statistics, attribution file, and dataset card. Do not publish raw or cleaned source text unless the manifest proves redistribution permission for every included record.

The licensing audit is a release gate. Unresolved records are excluded, not assumed safe.
