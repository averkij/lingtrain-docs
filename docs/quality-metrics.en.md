# Alignment Quality Metrics {#quality-metrics}

Measuring alignment quality is essential for knowing when to trust automatic results, when to intervene manually, and how to compare different alignment runs. This page explains the metrics available in Lingtrain Aligner and general concepts for evaluating alignment quality.

## Chain score {#chain-score}

The **chain score** is the primary quality metric in Lingtrain. For each alignment chain, the chain score is the cosine similarity between the aggregated source embedding and the aggregated target embedding.

### How it is computed {#chain-score-computation}

For a 1:1 chain (one source sentence, one target sentence):

```
chain_score = cosine_similarity(embedding_source, embedding_target)
```

For multi-sentence chains (e.g., 2:1), the source embeddings are aggregated (using the configured aggregation method — weighted average, max pooling, or log scaling) before computing similarity:

```
chain_score = cosine_similarity(aggregate(embeddings_source), aggregate(embeddings_target))
```

### Interpreting chain scores {#interpreting-scores}

Chain scores range from -1 to 1, but in practice almost all scores fall between 0.3 and 1.0:

| Score range | Interpretation |
|-------------|---------------|
| 0.85 - 1.0 | Excellent match — high confidence the sentences are translations of each other |
| 0.70 - 0.85 | Good match — likely correct, may involve some paraphrasing |
| 0.55 - 0.70 | Moderate match — possible but uncertain; worth manual review |
| 0.40 - 0.55 | Weak match — likely incorrect or very free translation |
| Below 0.40 | Poor match — almost certainly wrong alignment |

These thresholds are approximate and depend on the embedding model and language pair. Some language pairs consistently produce lower scores even for correct alignments (e.g., Chinese-English scores tend to be lower than French-English scores for the same quality of alignment).

### Score distribution {#score-distribution}

For a well-aligned text, the score distribution should be:
- **Right-skewed**: Most scores are high (0.7+), with a long tail of lower scores
- **Unimodal**: A single peak in the 0.75-0.90 range
- **Few outliers**: Only a small percentage of chains below 0.5

A bimodal distribution (two peaks — one high, one low) suggests systematic problems: some sections are aligning well while others are failing completely.

## Aggregate metrics {#aggregate-metrics}

Beyond individual chain scores, Lingtrain provides several aggregate metrics:

### Average chain score {#average-score}

The mean chain score across all chains in a batch or the entire alignment. This provides a quick summary of overall quality:

- **Above 0.80**: Excellent alignment
- **0.70 - 0.80**: Good alignment, some sections may need review
- **0.60 - 0.70**: Moderate alignment, significant manual review likely needed
- **Below 0.60**: Poor alignment, consider adjusting parameters or using a different approach

### Conflict count {#conflict-count}

The number of conflicts detected during alignment processing. A high conflict count relative to the total number of chains indicates structural mismatch:

- **0-5% conflicts**: Well-matched texts
- **5-15% conflicts**: Moderate structural differences (normal for literary translation)
- **15-30% conflicts**: Significant structural differences, may need parameter adjustment
- **Above 30%**: Severe mismatch, investigate texts and parameters

### Chain type distribution {#chain-distribution}

The proportion of chains by type (1:1, 2:1, 1:2, 2:2, etc.) reveals translation patterns:

- **70-90% type 1:1**: Typical for well-matched texts
- **High proportion of 2:1 or 1:2**: Translator frequently merged or split sentences
- **Many 3:1 or larger**: Significant structural restructuring; may indicate alignment problems
- **Nearly 100% type 1:1**: Unusually close correspondence (typical of subtitles or technical docs)

## Precision and recall concepts {#precision-recall}

In information retrieval and classification, precision and recall are fundamental metrics. These concepts apply naturally to alignment evaluation:

### Precision {#precision}

**Precision** measures how many of the proposed alignments are correct:

```
precision = correct_chains / total_proposed_chains
```

A system with high precision makes few false matches — when it says two sentences are aligned, they usually are. A system with low precision produces many incorrect pairings.

### Recall {#recall}

**Recall** measures how many of the true alignments were found:

```
recall = correct_chains / total_true_chains
```

A system with high recall finds most of the genuine correspondences. A system with low recall misses many valid alignments (leaving sentences unaligned or paired incorrectly).

### F1 score {#f1}

The F1 score is the harmonic mean of precision and recall:

```
F1 = 2 * (precision * recall) / (precision + recall)
```

It provides a single number that balances both aspects. An F1 score of 1.0 means perfect alignment.

### Applying to Lingtrain {#applying-pr}

In practice, computing precision and recall for alignment requires a gold-standard reference — a manually aligned version of the same texts. This is rarely available for real-world alignment tasks.

However, the concepts are still useful for reasoning about alignment quality:
- High chain scores suggest high precision (matched sentences are genuinely translations)
- Low conflict count and few unaligned sentences suggest high recall (most correspondences were found)
- The combination of both suggests the overall alignment is reliable

## Comparison with human alignment {#human-comparison}

Human alignment serves as the gold standard for evaluating automatic alignment systems. When professional linguists align texts manually, the results define what "correct" alignment looks like.

### How human alignment differs {#human-differences}

Human aligners consider factors that automatic systems may miss:
- **Pragmatic equivalence**: Two sentences may express the same communicative function even if their literal meanings differ
- **Cultural adaptation**: A translator may replace a cultural reference with a local equivalent, which a human aligner would recognize as corresponding
- **Implicit information**: A human aligner understands when a translator has made explicit what was implicit in the source
- **Context**: Human aligners consider the broader narrative context, not just sentence-level similarity

### Inter-annotator agreement {#inter-annotator}

Even human aligners do not always agree. Studies of inter-annotator agreement for sentence alignment typically report:
- **Agreement on 1:1 chains**: 90-98% (high agreement on straightforward sentence pairs)
- **Agreement on multi-sentence chains**: 70-85% (lower agreement on where to draw boundaries for merges/splits)
- **Agreement on difficult cases**: 50-70% (significant disagreement on heavily restructured passages)

This means that even the "gold standard" has inherent ambiguity. An automatic system that achieves F1 scores in the 0.90-0.95 range is performing at near-human levels for most text types.

### Benchmarking Lingtrain {#benchmarking}

To benchmark alignment quality:

1. **Select representative samples**: Choose 2-3 passages of 50-100 sentences each from different parts of the text
2. **Create manual alignment**: Have a qualified bilingual person align the sample passages manually
3. **Compare**: Check what percentage of automatic chains match the manual alignment exactly
4. **Analyze errors**: Categorize discrepancies as precision errors (wrong matches) or recall errors (missed matches)

This manual spot-checking, while labor-intensive, provides the most reliable quality assessment.

## Measuring improvement {#measuring-improvement}

When adjusting alignment parameters or trying different approaches, you need to measure whether changes actually improve quality.

### A/B comparison {#ab-comparison}

Align the same text with two different parameter sets and compare:

1. **Average chain score**: Did it increase?
2. **Conflict count**: Did it decrease?
3. **Chain type distribution**: Are there more 1:1 chains?
4. **Scatter plot**: Is the diagonal cleaner?
5. **Spot-check**: Are previously incorrect chains now correct?

### Iterative refinement {#iterative-refinement}

The typical workflow for improving alignment quality:

1. Run initial alignment with default parameters
2. Review the scatter plot and aggregate metrics
3. Identify problem areas (drift, scattered regions, high conflict count)
4. Adjust parameters (window, shift, batch size)
5. Re-run alignment
6. Compare metrics with the previous run
7. Repeat until satisfactory

### Parameter sensitivity {#parameter-sensitivity}

Not all parameters affect quality equally:
- **Window**: Strong effect — too small misses matches, too large produces false ones
- **Shift**: Strong effect when sentence counts differ significantly
- **Batch size**: Moderate effect — smaller batches limit error accumulation
- **Embedding model**: Strong effect — different models work better for different language pairs
- **Aggregation method**: Weak effect for most texts; matters mainly for texts with very uneven sentence lengths

## Quality assurance workflow {#qa-workflow}

For production-quality alignment, follow this quality assurance process:

1. **Check sentence splitting**: Preview the split output for both texts. Fix any obvious errors before proceeding.
2. **Review first batch**: After aligning the first batch, check the scatter plot and chain scores. Adjust parameters if needed before continuing.
3. **Monitor aggregate metrics**: Watch for sudden drops in average score or spikes in conflict count, which indicate localized problems.
4. **Resolve conflicts**: Use the interactive conflict resolution interface to fix mismatches flagged by the algorithm.
5. **Spot-check**: Randomly sample 20-30 chains and verify them manually. If more than 5% are incorrect, consider re-aligning with different settings.
6. **Final review**: Browse through the complete aligned output, paying special attention to chapter boundaries, dialogue sections, and any passages where the translator was known to take liberties.

## Limitations of automatic metrics {#limitations}

Automatic metrics have inherent limitations:

- **Semantic similarity is not translation equivalence**: Two sentences can be semantically similar without being translations of each other. Conversely, a very free translation may have low semantic similarity despite being correct.
- **Scores are model-dependent**: Different embedding models produce different score ranges for the same text pair. Scores from different models are not directly comparable.
- **Aggregate metrics hide local problems**: An average score of 0.82 may mask a section with a score of 0.45. Always examine the distribution, not just the mean.
- **Metrics cannot detect certain errors**: If two sentences happen to be about the same topic but are not translations of each other, they may receive a high similarity score. Only human review can catch these false matches.

Despite these limitations, automatic metrics — especially when combined with visualization and strategic spot-checking — provide a practical and effective framework for assessing alignment quality at scale.
