# Alignment algorithm {#algorithm}

This page explains how Lingtrain Aligner works under the hood — the math, the models, and the processing pipeline that turns two raw texts into aligned sentence pairs.

## Overview {#overview}

The alignment pipeline consists of four stages:

1. **Preprocessing** — split texts into sentences, extract metadata
2. **Embedding** — convert sentences into vector representations using ML models
3. **Matching** — build a similarity matrix and select best matches
4. **Conflict resolution** — fix mismatches where one-to-one mapping failed

## Sentence splitting {#splitting}

Before alignment, each text is split into individual sentences. Lingtrain uses language-aware splitting rules:

- **Russian, Ukrainian, Belarusian** — the `razdel` library, a rule-based splitter designed for Cyrillic texts
- **Chinese** — regex matching on sentence-ending punctuation (`。！？`)
- **Japanese** — similar pattern matching with Japanese punctuation
- **Other languages** — standard tokenizer with language-specific rules

During splitting, markup tags (`%%%%%author.`, `%%%%%h1.`, etc.) are extracted and stored as metadata. Tagged lines are excluded from the alignment process — they are used only for formatting during export.

Paragraph boundaries are preserved as markers on the last sentence of each paragraph. These markers are later used to reconstruct paragraph structure in exported books.

## Sentence embeddings {#embeddings}

Each sentence is converted into a dense vector (embedding) that captures its semantic meaning. Lingtrain supports several multilingual embedding models:

| Model | Languages | Description |
|-------|-----------|-------------|
| **distiluse-base-multilingual-cased-v2** | 50+ | Default model. Fast, good quality for major language pairs. Based on distilled Universal Sentence Encoder. |
| **LaBSE** | 100+ | Language-agnostic BERT Sentence Embeddings. Best for rare and low-resource languages. |
| **XLM-R-100langs** | 100+ | XLM-RoBERTa-based model with broad multilingual coverage. |
| **rubert-tiny2** | Russian-focused | Lightweight Russian BERT model. Fast inference for Russian-centric tasks. |
| **SONAR** | 200+ | Meta's massively multilingual encoder. Widest language coverage. |

Embeddings are computed in batches (default: 10 sentences per batch) and L2-normalized before storage. Normalized embeddings allow cosine similarity to be computed as a simple dot product.

### External API support {#api-embeddings}

In addition to local models, Lingtrain supports computing embeddings via external APIs:

- **Hugging Face Inference API** — any SentenceTransformers model hosted on HF
- **OpenAI Embeddings API** — models like `text-embedding-3-small`

API-based inference is useful when you need a specific model that's too large to run locally or when GPU resources are limited.

## Batch processing {#batches}

Large texts are split into batches for processing. This keeps memory usage manageable and allows incremental alignment with progress tracking.

### How batches work {#how-batches-work}

Given a source text of `N` sentences and a configured batch size of `B`:

1. The source text is divided into batches of `B` sentences each
2. For each source batch, the corresponding target window is calculated **proportionally**:
   - Proportionality ratio: `k = len(target) / len(source)`
   - Target start: `source_start × k - window + shift`
   - Target end: `source_end × k + window + shift`
3. The **window** parameter adds extra sentences on each side to prevent edge-cutting errors
4. The **shift** parameter allows manual offset correction when sentence counts differ significantly

### Example {#batch-example}

For a text with 600 source sentences, 700 target sentences, batch size 200, and window 40:

- **Batch 1**: source [1–200], target [1–200 × 700/600 + 40] ≈ [1–273]
- **Batch 2**: source [201–400], target [201 × 700/600 - 40 ... 400 × 700/600 + 40] ≈ [195–507]
- **Batch 3**: source [401–600], target [401 × 700/600 - 40 ... 700] ≈ [428–700]

Notice the overlap between batches — this is intentional. The window overlap ensures that sentences near batch boundaries are not missed.

## Similarity matrix {#similarity-matrix}

For each batch, the aligner builds a **cosine similarity matrix** between source and target sentence embeddings.

### Computation {#computation}

Given source embeddings matrix `S` (dimensions: source_batch × embedding_dim) and target embeddings matrix `T` (dimensions: target_window × embedding_dim):

```
similarity = S × T^T
```

This is a single matrix multiplication (BLAS-optimized), producing a matrix where `similarity[i][j]` is the cosine similarity between source sentence `i` and target sentence `j`.

### Window masking {#masking}

To prevent false matches from distant parts of the text, a **window mask** is applied:

```
For each source position i and target position j:
  if j × k is within [i - window, i + window]:
    keep similarity[i][j] (minimum 0.01)
  else:
    set similarity[i][j] = 0
```

This restricts matches to a diagonal band, enforcing the assumption that sentence order is generally preserved across translations. The minimum value of 0.01 prevents zero-similarity entries within the valid window.

## Best match selection {#best-match}

After computing the masked similarity matrix, the aligner selects the **best match** for each source sentence:

```
For each row i (source sentence):
  best_target[i] = argmax(similarity[i])
```

This creates a one-to-one mapping from source to target. However, multiple source sentences can map to the same target sentence — this is expected when a translator merged several sentences into one.

The resulting alignment is stored as a **document index**: a mapping from batch IDs to lists of `(source_ids, target_ids)` pairs.

## Conflict detection {#conflicts}

After alignment, the system scans for **conflicts** — discontinuities in the alignment sequence.

A conflict occurs when the alignment chain breaks: if source sentences [1, 2, 3] map to target sentences [5, 3, 8], the ordering is violated. The system detects these breaks by:

1. Walking through aligned pairs in source order
2. Tracking the expected target sequence (monotonically increasing)
3. Marking any position where the target sequence jumps backward or skips

Consecutive aligned pairs without breaks form **chains** (minimum length: 2–3 pairs). The gaps between chains are the conflicts.

### Conflict types {#conflict-types}

Conflicts are characterized by their size — the number of source and target sentences involved:

- **Small conflicts** (2×2, 2×3, 3×2) — most common, usually caused by sentence splitting/merging
- **Medium conflicts** (up to 6×6) — handled by automatic resolution
- **Large conflicts** (>6×6) — marked for manual review in the editor

## Conflict resolution {#resolution}

The automatic resolver processes conflicts from smallest to largest, using an exhaustive search over possible groupings.

### Resolution algorithm {#resolution-algorithm}

For a conflict with `N` source and `M` target sentences:

1. **Enumerate all groupings** — generate every possible way to split `N` source sentences into groups and `M` target sentences into corresponding groups
2. **Compute group embeddings** — for each grouping variant, compute a combined embedding for each group using weighted averaging (weight by sentence length)
3. **Score variants** — calculate the sum of cosine similarities between paired groups
4. **Select best** — choose the grouping variant with the highest total similarity score
5. **Apply** — merge sentences according to the selected grouping and update the database

### Aggregation methods {#aggregation}

When combining multiple sentences into a group embedding, several methods are available:

- **Weighted average** — weight each sentence embedding by its character length
- **Length scaling** — multiply embedding by length, then average
- **Max pooling** — take the component-wise maximum across sentence embeddings
- **Logarithmic scaling** — weight by `log(length + offset)` for diminishing returns on long sentences

The default is weighted average, which gives more influence to longer (typically more informative) sentences.

### Iterative processing {#iterative}

After resolving a round of conflicts, the system rechecks for remaining issues. Resolution of one conflict can sometimes resolve adjacent conflicts as well, as the alignment chains grow and absorb previously ambiguous regions.

## Quality metrics {#metrics}

The alignment quality is measured by a **chain score**:

```
score = 1 - (breaks / total_lines)
```

Where:
- `breaks` = number of non-consecutive jumps in the alignment sequence
- `total_lines` = total number of aligned lines

A score of **1.0** means a perfect diagonal with no breaks. Scores above **0.95** indicate good alignment quality. Lower scores suggest areas that need manual attention.

## Visualization {#visualization}

Each batch produces a scatter plot where:

- **X-axis** — source sentence position
- **Y-axis** — matched target sentence position

A perfect alignment produces a straight diagonal line. Common patterns:

- **Clean diagonal** — excellent alignment, few or no conflicts
- **Stepped diagonal** — translator added or removed content in blocks
- **Scattered dots** — poor alignment, needs parameter adjustment (larger window, shift correction, or interlinear translations)

The visualization also optionally includes a regression line overlay showing the expected proportional mapping.

## Tips for best results {#tips}

1. **Prepare texts carefully** — remove extraneous content (page numbers, footnotes) that doesn't have a counterpart in the other language
2. **Use markup tags** — proper heading and author tags improve structural alignment
3. **Start with default settings** — batch size 200, window 40 works well for most texts
4. **Check the diagonal early** — after the first batch, verify the visualization. If it's broken, adjust the shift before continuing
5. **Use interlinear translation for rare languages** — machine-translate the split text and upload as an interlinear document
6. **Resolve conflicts before editing** — automatic resolution handles most cases; manual editing is for the remaining few
