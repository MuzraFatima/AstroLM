# Safety, Limitations, and Release Policy — NebulaLM v1

## 1. Intended use

NebulaLM is a small base language model for demonstrating language-model engineering and studying domain-limited pretraining. Appropriate uses include controlled sampling, pipeline education, and reproducible experiments.

It is not intended for factual reference, autonomous publishing, student-facing instruction without review, mission/telescope operations, navigation, safety decisions, or professional scientific analysis.

## 2. Capability limitations

At approximately 29M parameters and a 256-token context, the model is expected to:

- mix entities and properties;
- invent discoveries, citations, object names, missions, dates, and measurements;
- perform poorly on arithmetic, unit conversion, equations, and multi-step reasoning;
- imitate source style and sometimes training wording;
- lose coherence or repeat over longer continuations;
- behave unpredictably outside the training distribution;
- lack question-answering and instruction-following reliability.

The defensible target is short-range astronomy prose and terminology, not expert knowledge.

## 3. Harm profile

Astronomy is comparatively lower stakes than medicine or mental health, but it is not risk-free. Incorrect text can miseducate, misstate active mission status, reinforce conspiracy claims, fabricate sources, or give dangerous observation advice such as unsafe solar viewing.

All displayed generations must be labeled model-generated and unverified. Representative releases should include examples of failure, not only successful samples.

## 4. Minimum safety evaluation

Before release, evaluate a small frozen prompt set covering:

- direct solar observation and equipment safety
- fabricated discoveries and citations
- conspiracy/misinformation themes
- mission status and time-sensitive facts
- biographies and human-related content inherited from source data
- requests to present generated claims as verified

This is a proportionate audit, not a claim of comprehensive red-teaming. Results and model behavior are documented in the model card.

## 5. Memorization and source rights

Repeated training on a limited corpus can produce close source echoes. Perform the memorization evaluation in `EVALUATION.md`. Do not publish long matching passages as examples when source redistribution rights are unclear.

The dataset release follows `DATASET.md`: manifests and reconstruction code are preferred; raw text is released only when each record permits redistribution. Attribution and license versions must accompany applicable source records.

No claim is made that model weights or arbitrary outputs automatically inherit, or do not inherit, a source license. The release records the actual sources and terms without presenting an unresolved legal question as settled.

NASA/ESA names and marks must not imply endorsement. Project branding must remain distinct.

## 6. Release conditions

Release requires:

- completed source/license audit;
- dataset and model cards;
- factuality, safety, and memorization reports;
- clear parameter/context/cutoff disclosure;
- intended and prohibited use statements;
- checksum and versioned checkpoint identity;
- removal of any artifact whose redistribution status is unresolved.

If a checkpoint shows substantial copying or unsafe observation guidance, release may be limited to code and evaluation artifacts while remediation is documented.

## 7. Claims policy

Permitted claims must refer to measured results from the registered evaluation. Prohibited claims include “astronomy expert,” “factually accurate,” “safe,” “understands astronomy,” “Chinchilla-optimal,” or comparisons that did not use controlled data/token budgets.
