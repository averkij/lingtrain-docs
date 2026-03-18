# Sentence Embeddings Explained {#sentence-embeddings}

Sentence embeddings are the core technology behind Lingtrain Aligner's ability to match sentences across languages. This page explains how they work — from the basic concept of representing meaning as numbers, through the transformer architecture that makes it possible, to the practical details of how Lingtrain uses embeddings for alignment.

## From words to vectors {#words-to-vectors}

### The basic idea {#basic-idea}

Computers work with numbers, not words. To process natural language, we need a way to convert text into numerical form. The simplest approach — assigning each word an arbitrary ID number — captures no meaning. The word "dog" might be ID 4,271 and "puppy" might be ID 12,089, but these numbers tell us nothing about the relationship between the concepts.

**Word embeddings** solve this by mapping each word to a point in a high-dimensional space where semantically similar words are close together. In this space:

- "dog" and "puppy" are close neighbors
- "dog" and "cat" are nearby (both animals)
- "dog" and "refrigerator" are far apart

This was the key insight of Word2Vec (Mikolov et al., 2013) and GloVe (Pennington et al., 2014): you can learn these vector representations from large text corpora by observing which words appear in similar contexts.

### From word embeddings to sentence embeddings {#word-to-sentence}

Word embeddings represent individual words, but alignment operates on sentences. We need a way to represent entire sentences as single vectors. Early approaches simply averaged the word embeddings:

```
sentence_vector = average(word_1_vector, word_2_vector, ..., word_n_vector)
```

This captures something about meaning but loses crucial information about word order and grammatical structure. "The dog bit the man" and "The man bit the dog" would get the same vector, despite meaning very different things.

Modern sentence embeddings, built on the transformer architecture, solve this problem by encoding the full sentence — word order, grammatical relationships, and contextual meaning — into a single dense vector.

## The transformer architecture {#transformers}

### Attention is all you need {#attention}

The transformer, introduced by Vaswani et al. in 2017, revolutionized NLP. At its core is the **self-attention mechanism**, which allows each word in a sentence to "attend to" every other word, capturing long-range dependencies and contextual meaning.

Consider the word "bank" in two sentences:

- "I deposited money at the **bank**." (financial institution)
- "We sat on the river **bank**." (edge of water)

A static word embedding gives "bank" the same vector in both cases. A transformer computes different representations based on context — the full sentence determines what "bank" means in each case.

### Encoder models for embeddings {#encoder-models}

For sentence embeddings, we use the **encoder** part of the transformer. The process:

1. **Tokenization** — the sentence is split into subword tokens (pieces smaller than words, handling rare words and morphology)
2. **Token embeddings** — each token gets an initial vector from a learned vocabulary
3. **Positional encoding** — information about token position is added (so the model knows word order)
4. **Self-attention layers** — multiple layers of attention transform the token representations, incorporating contextual information
5. **Pooling** — the final token representations are combined into a single vector (typically by averaging or taking the [CLS] token representation)

The result is a fixed-size vector (typically 384 to 1024 dimensions) that represents the meaning of the entire sentence.

### The embedding process (simplified) {#embedding-process}

```
Input: "The old man sat by the window."
  |
  v
[Tokenizer] --> ["The", "old", "man", "sat", "by", "the", "window", "."]
  |
  v
[Token Embeddings] --> [v1, v2, v3, v4, v5, v6, v7, v8]  (initial vectors)
  |
  v
[Transformer Layers (x12)] --> [h1, h2, h3, h4, h5, h6, h7, h8]  (contextualized)
  |
  v
[Pooling (mean)] --> [0.12, -0.45, 0.78, ..., 0.33]  (single 768-dim vector)
  |
  v
[L2 Normalization] --> [0.04, -0.15, 0.26, ..., 0.11]  (unit length)
```

## Multilingual embeddings {#multilingual}

### The cross-lingual challenge {#cross-lingual-challenge}

For text alignment, we need embeddings that work across languages. The sentence "The old man sat by the window" and its Russian translation "Старик сидел у окна" must map to nearby points in the embedding space, despite using completely different words and even different scripts.

This is a remarkable capability: the model must learn an abstract representation of meaning that transcends language-specific surface forms.

### Key multilingual models {#key-models}

Several models have been developed to create language-independent sentence embeddings:

**mBERT (Multilingual BERT)** — trained on Wikipedia text in 104 languages. It learns shared representations across languages through shared vocabulary and training data. However, it was not explicitly trained for sentence similarity, so its sentence embeddings are not optimal without fine-tuning.

**XLM-R (XLM-RoBERTa)** — trained on 2.5 TB of CommonCrawl data in 100 languages. Better cross-lingual representations than mBERT due to larger training data and improved training procedure. Models like `multilingual-e5-large` build on XLM-R with additional sentence similarity training.

**LaBSE (Language-Agnostic BERT Sentence Embeddings)** — specifically designed for cross-lingual sentence retrieval. Trained with a dual-encoder architecture on bilingual sentence pairs from the web, covering 109 languages. Particularly strong for low-resource languages because it was trained on mined parallel data rather than relying solely on Wikipedia.

**LASER (Language-Agnostic SEntence Representations)** — Meta's encoder trained on parallel data for 93 languages. Uses a single shared encoder for all languages, producing embeddings in a common space.

**SONAR** — Meta's successor to LASER, covering 200+ languages with improved quality. Uses language-specific encoders feeding into a shared representation space.

For a detailed comparison of the models available in Lingtrain, see [Comparing Embedding Models](embedding-models-comparison.en.md).

## Cosine similarity {#cosine-similarity}

### Measuring closeness {#measuring-closeness}

Once we have sentence embeddings, we need a way to measure how similar two sentences are. **Cosine similarity** is the standard metric. It measures the cosine of the angle between two vectors:

```
cosine_similarity(A, B) = (A . B) / (||A|| * ||B||)
```

Where `A . B` is the dot product and `||A||` is the vector's magnitude (length).

The result ranges from -1 (opposite directions) through 0 (perpendicular / unrelated) to 1 (same direction / identical meaning).

**Why cosine and not Euclidean distance?** Cosine similarity measures direction, not magnitude. Two sentences with the same meaning but different lengths will point in the same direction even if their raw embedding magnitudes differ. Cosine similarity captures the semantic angle, ignoring superficial differences.

For a deeper mathematical explanation, see [Understanding Cosine Similarity](cosine-similarity.en.md).

### L2 normalization and the dot product shortcut {#normalization}

**L2 normalization** scales a vector to unit length (magnitude = 1):

```
normalized_v = v / ||v||
```

When vectors are L2-normalized, cosine similarity simplifies to a plain dot product:

```
cosine_similarity(A_norm, B_norm) = A_norm . B_norm
```

This matters for performance: computing dot products is extremely fast and can be parallelized on GPUs. Lingtrain normalizes all embeddings before storage, so the similarity matrix can be computed as a single matrix multiplication:

```
similarity_matrix = S_normalized @ T_normalized.T
```

Where `S_normalized` is the matrix of source sentence embeddings and `T_normalized` is the matrix of target sentence embeddings. This operation processes an entire batch in milliseconds.

## How Lingtrain uses embeddings {#lingtrain-usage}

### The alignment pipeline {#alignment-pipeline}

1. **Sentence splitting** — both texts are split into sentences using language-aware rules
2. **Embedding computation** — each sentence is encoded into a vector using the selected multilingual model
3. **L2 normalization** — all embeddings are normalized to unit length
4. **Similarity matrix** — dot product between source and target embedding matrices
5. **Window masking** — similarities outside a diagonal band are suppressed
6. **Best match selection** — for each source sentence, select the target sentence with highest similarity
7. **Conflict detection** — identify alignment breaks (non-monotonic mappings)
8. **Conflict resolution** — group conflicting sentences and find the best grouping using combined embeddings

### Embedding dimensions and storage {#dimensions}

Typical embedding sizes:

| Model | Dimensions | Vector size (float32) |
|-------|-----------|----------------------|
| distiluse-base-multilingual-cased-v2 | 512 | 2 KB |
| LaBSE | 768 | 3 KB |
| multilingual-e5-large | 1024 | 4 KB |
| text-embedding-3-small (OpenAI) | 1536 | 6 KB |

For a 1,000-sentence text, embeddings occupy 2-6 MB — easily fitting in memory for batch processing.

### Quality of embeddings by language {#quality-by-language}

Embedding quality varies significantly by language:

- **High-resource languages** (English, French, German, Chinese, Russian) — all models produce good embeddings, alignment quality is consistently high
- **Medium-resource languages** (Turkish, Korean, Hungarian, Czech) — most models work well, some may require a specific model choice
- **Low-resource languages** (Bashkir, Chuvash, Komi) — only certain models (LaBSE, SONAR) provide adequate coverage, and proxy texts may be needed for best results

See [Working with Low-Resource Languages](low-resource-languages.en.md) for guidance on handling challenging language pairs.

## Visualization: what the embedding space looks like {#visualization}

High-dimensional embedding spaces cannot be directly visualized, but dimension reduction techniques (t-SNE, UMAP) can project them into 2D for inspection. In such projections:

- Sentences with similar meanings cluster together regardless of language
- The English sentence "I love this book" and the Russian "Мне нравится эта книга" appear as nearby points
- Semantically different sentences are far apart

Lingtrain's batch visualization (scatter plots) shows a simpler but related view: each dot represents an aligned pair, with source position on one axis and target position on the other. A clean diagonal indicates that the embedding-based matching is working correctly — semantically corresponding sentences are being identified across the two texts.

## Key takeaways {#takeaways}

1. **Sentence embeddings convert text to numbers** — dense vectors that capture meaning, not just surface form
2. **Transformers provide context** — the same word gets different representations depending on surrounding text
3. **Multilingual models share a semantic space** — translations of the same sentence end up near each other in vector space
4. **Cosine similarity measures semantic closeness** — normalized dot products make this computation efficient
5. **Quality depends on the model and language** — choose the right model for your language pair, and consider proxy texts for low-resource languages

For practical guidance on choosing a model, see [Comparing Embedding Models](embedding-models-comparison.en.md). For the mathematical details of cosine similarity, see [Understanding Cosine Similarity](cosine-similarity.en.md).
