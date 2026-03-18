# Understanding Cosine Similarity {#cosine-similarity}

Cosine similarity is the mathematical measure at the heart of Lingtrain Aligner's sentence matching. It quantifies how similar two sentences are by comparing the directions of their embedding vectors. This page explains the concept from first principles, making it accessible to readers without a math background while providing the precise formulas for those who want them.

## Vectors: the basic idea {#vectors-basic}

### What is a vector? {#what-is-vector}

A **vector** is simply a list of numbers that represents a point or direction in space. In everyday life:

- A location on a map can be described by two numbers: latitude and longitude. That is a 2D vector.
- A color on a screen is described by three numbers: red, green, blue (RGB). That is a 3D vector.
- A sentence embedding is described by hundreds of numbers (768, 1024, or 1536 depending on the model). That is a high-dimensional vector.

Each number in the vector is called a **component** or **dimension**.

### Vectors in 2D {#vectors-2d}

Let us start with simple 2D vectors to build intuition. Consider two vectors:

- **A** = (3, 4) — points 3 units right and 4 units up
- **B** = (6, 8) — points 6 units right and 8 units up

These two vectors point in the **same direction** — B is just A scaled by a factor of 2. Despite having different magnitudes (lengths), they share the same direction.

Now consider:

- **C** = (4, -3) — points 4 units right and 3 units down

Vector C points in a very different direction from A. It is nearly perpendicular.

The key insight: **direction captures meaning, magnitude does not.** Two sentences that express the same idea should point in the same direction in embedding space, even if one is longer or more detailed than the other.

### Vectors in 3D {#vectors-3d}

In 3D, vectors have three components: (x, y, z). You can visualize them as arrows in three-dimensional space. The same principles apply — direction matters more than length for measuring similarity.

```
        z
        |    B
        |   /
        |  /
        | /
        |/_______ y
       /
      /  A
     /
    x

    Vectors A and B point in similar directions
    = high cosine similarity
```

### High-dimensional vectors {#high-dimensional}

Sentence embeddings live in spaces with hundreds of dimensions. While we cannot visualize 768 dimensions, the math is the same. Two vectors in 768-dimensional space can be:

- **Parallel** (same direction) — cosine similarity close to 1
- **Perpendicular** (unrelated) — cosine similarity close to 0
- **Opposite** (opposite meaning) — cosine similarity close to -1

In practice, sentence embeddings almost always have positive cosine similarity (between 0 and 1), because the embedding models are trained to produce vectors in a positive cone of the space.

## The cosine formula {#formula}

### Angle between vectors {#angle}

The **cosine similarity** between two vectors A and B is the cosine of the angle between them:

```
cosine_similarity(A, B) = cos(theta)
```

Where theta is the angle between vectors A and B.

- When theta = 0 degrees (same direction): cos(0) = 1
- When theta = 90 degrees (perpendicular): cos(90) = 0
- When theta = 180 degrees (opposite): cos(180) = -1

### Computing with dot products {#dot-product}

To compute the cosine without first computing the angle, we use the **dot product** formula:

```
cosine_similarity(A, B) = (A . B) / (||A|| * ||B||)
```

Where:

**Dot product** (A . B): multiply corresponding components and sum:
```
A . B = a1*b1 + a2*b2 + ... + an*bn
```

**Magnitude** ||A||: the length of vector A:
```
||A|| = sqrt(a1^2 + a2^2 + ... + an^2)
```

### Worked example in 2D {#example-2d}

Let us compute the cosine similarity between A = (3, 4) and B = (6, 8):

**Dot product:**
```
A . B = 3*6 + 4*8 = 18 + 32 = 50
```

**Magnitudes:**
```
||A|| = sqrt(3^2 + 4^2) = sqrt(9 + 16) = sqrt(25) = 5
||B|| = sqrt(6^2 + 8^2) = sqrt(36 + 64) = sqrt(100) = 10
```

**Cosine similarity:**
```
cos_sim = 50 / (5 * 10) = 50 / 50 = 1.0
```

A cosine similarity of 1.0 means the vectors point in exactly the same direction — which makes sense, since B = 2*A.

Now let us try A = (3, 4) and C = (4, -3):

**Dot product:**
```
A . C = 3*4 + 4*(-3) = 12 - 12 = 0
```

**Cosine similarity:**
```
cos_sim = 0 / (5 * 5) = 0.0
```

A cosine similarity of 0 means the vectors are perpendicular — completely unrelated directions.

## Why cosine similarity for semantic comparison {#why-cosine}

### Direction vs. magnitude {#direction-vs-magnitude}

Consider two sentences:

- "The cat sat on the mat."
- "The cat sat on the mat in the corner of the room near the old fireplace."

The second sentence says more, but the core meaning is similar. In embedding space, the second sentence's vector may have a larger magnitude (more content = more "energy" in the vector), but it should point in approximately the same direction.

Cosine similarity ignores magnitude differences and focuses on direction, making it robust to sentence length variations. This is exactly what we want for alignment — a short sentence should match its translation regardless of whether the translation is longer or shorter.

### Why not Euclidean distance? {#why-not-euclidean}

**Euclidean distance** measures the straight-line distance between two points:

```
distance = sqrt((a1-b1)^2 + (a2-b2)^2 + ... + (an-bn)^2)
```

The problem: two vectors pointing in the same direction but with different magnitudes will have a large Euclidean distance, even though they represent similar meanings. Cosine similarity correctly identifies them as similar.

That said, when vectors are **L2-normalized** (scaled to unit length), Euclidean distance and cosine similarity become equivalent — both rank pairs in the same order. This is why Lingtrain normalizes embeddings: it allows using efficient dot product computation while preserving the semantic ranking.

## L2 normalization {#l2-normalization}

### What it does {#what-l2-does}

**L2 normalization** scales a vector so its magnitude becomes exactly 1:

```
A_normalized = A / ||A||
```

For A = (3, 4) with ||A|| = 5:
```
A_normalized = (3/5, 4/5) = (0.6, 0.8)
```

Check: ||A_normalized|| = sqrt(0.6^2 + 0.8^2) = sqrt(0.36 + 0.64) = sqrt(1.0) = 1.0

### The dot product shortcut {#dot-product-shortcut}

When both vectors are normalized, the cosine similarity formula simplifies dramatically:

```
cosine_similarity(A_norm, B_norm) = A_norm . B_norm
```

The denominator (||A|| * ||B||) becomes 1 * 1 = 1, so it disappears. This means computing cosine similarity between normalized vectors is just a dot product — the simplest and fastest vector operation.

### Why this matters for performance {#performance}

In Lingtrain, the alignment algorithm computes similarity between every source sentence and every target sentence in a batch. For a batch of 200 source sentences and 250 target sentences, that is 50,000 similarity computations.

With normalized vectors, all 50,000 computations happen in a single matrix multiplication:

```
similarity_matrix = source_embeddings @ target_embeddings.T
```

This operation is highly optimized in numerical libraries (NumPy, BLAS) and completes in milliseconds. Without normalization, each computation would require additional magnitude calculations, slowing the process significantly.

## Practical interpretation of similarity scores {#interpretation}

In the context of sentence alignment, cosine similarity scores typically fall in these ranges:

| Score range | Interpretation |
|-------------|---------------|
| 0.85 - 1.0 | Very high similarity — almost certainly a translation pair |
| 0.70 - 0.85 | High similarity — likely a translation, possibly with some restructuring |
| 0.50 - 0.70 | Moderate similarity — may be related but not a direct translation |
| 0.30 - 0.50 | Low similarity — probably different sentences on a related topic |
| 0.0 - 0.30 | Very low similarity — unrelated sentences |

These ranges are approximate and depend on the embedding model, language pair, and text type. The alignment algorithm does not use fixed thresholds — it selects the best match for each source sentence, whatever the score.

## Connection to the alignment algorithm {#connection-to-alignment}

In Lingtrain's alignment pipeline, cosine similarity is used in three places:

1. **Similarity matrix** — the core step where each source sentence is compared to each candidate target sentence. The highest-scoring pair becomes the alignment match.

2. **Conflict resolution** — when resolving grouping ambiguities (e.g., should source sentences 5-6 match target sentences 7-8-9 as a 2:3 group?), combined embeddings are compared using cosine similarity to score each possible grouping.

3. **Window masking** — the similarity matrix is masked so that only scores within a diagonal band are considered. This prevents distant sentences from matching incorrectly, even if they happen to have high cosine similarity.

For full details on the algorithm, see [Alignment algorithm](algorithm.en.md).

## Further reading {#further-reading}

- [Sentence Embeddings Explained](sentence-embeddings.en.md) — how sentence vectors are created
- [Alignment algorithm](algorithm.en.md) — how similarity scores are used for alignment
- [Batch Processing in Detail](batch-processing-explained.en.md) — how the similarity matrix is computed in batches
