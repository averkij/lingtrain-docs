# Comparing Embedding Models {#embedding-models-comparison}

Lingtrain Aligner supports multiple embedding models for computing sentence similarity across languages. Each model has different strengths, language coverage, and performance characteristics. This page helps you choose the right model for your alignment task.

## Overview {#overview}

The embedding model determines how sentences are converted into vectors for comparison. The choice of model affects alignment quality, processing speed, and language coverage. You select the model in the **Controls** panel of the alignment detail page before running your first batch.

![Controls panel with model selection](assets/screenshots/aligner-controls-panel.png)

## Available models {#available-models}

### BGE-M3 {#bge-m3}

| Property | Value |
|----------|-------|
| **Full name** | BAAI General Embedding M3 |
| **Developer** | BAAI (Beijing Academy of Artificial Intelligence) |
| **Architecture** | XLM-RoBERTa based, fine-tuned for retrieval |
| **Dimensions** | 1024 |
| **Languages** | 100+ |
| **Access** | Via API (OpenRouter) |

**BGE-M3** is a versatile embedding model designed for multilingual, multi-functionality, and multi-granularity retrieval. It supports dense retrieval, sparse retrieval, and multi-vector retrieval in a single model.

**Strengths:**

- Excellent quality across major European and Asian languages
- Strong performance on retrieval benchmarks (MTEB)
- Handles both short sentences and longer passages well
- Good balance of quality and speed

**Weaknesses:**

- Performance drops for truly low-resource languages not well represented in training data
- Requires API access (not available for fully offline use)

**Best for:** General-purpose alignment of major world languages. A solid default choice when you do not have specific requirements.

### LaBSE (via HuggingFace API) {#labse}

| Property | Value |
|----------|-------|
| **Full name** | Language-Agnostic BERT Sentence Embeddings |
| **Developer** | Google Research |
| **Architecture** | Dual-encoder BERT |
| **Dimensions** | 768 |
| **Languages** | 109 |
| **Access** | Via HuggingFace Inference API |

**LaBSE** was specifically designed for cross-lingual sentence retrieval and similarity. It was trained on bilingual sentence pairs mined from the web, giving it broad and robust language coverage.

**Strengths:**

- Purpose-built for sentence similarity across languages
- Strong coverage of medium-resource and some low-resource languages
- Trained on mined parallel data (not just Wikipedia), providing more diverse language exposure
- Well-tested for alignment and bitext mining tasks

**Weaknesses:**

- Older architecture compared to newer models
- 768-dimensional embeddings may capture less nuance than larger models
- Requires API access to HuggingFace

**Best for:** Language pairs involving medium-resource or low-resource languages. Particularly recommended for languages like Armenian, Bashkir, and other languages that larger general-purpose models may underserve.

### Multilingual E5 Large {#e5-large}

| Property | Value |
|----------|-------|
| **Full name** | Multilingual E5 Large |
| **Developer** | Microsoft |
| **Architecture** | XLM-RoBERTa Large, fine-tuned with contrastive learning |
| **Dimensions** | 1024 |
| **Languages** | 100+ |
| **Access** | Via API |

**Multilingual E5 Large** is a high-quality multilingual embedding model from Microsoft, trained with a sophisticated multi-stage process including contrastive pre-training on large-scale text pairs and fine-tuning on labeled datasets.

**Strengths:**

- Among the highest-quality multilingual embeddings available
- Excellent performance on semantic similarity benchmarks
- Strong cross-lingual capabilities due to XLM-R base
- 1024 dimensions capture fine-grained semantic differences

**Weaknesses:**

- Larger model means slower inference
- May be overkill for simple alignment tasks with high-resource languages
- Requires API access

**Best for:** When you need the highest quality embeddings for challenging alignments — texts with significant structural differences, literary works with creative translations, or academic/specialized content.

### OpenAI Text Embedding 3 Small {#openai}

| Property | Value |
|----------|-------|
| **Full name** | text-embedding-3-small |
| **Developer** | OpenAI |
| **Architecture** | Proprietary transformer |
| **Dimensions** | 1536 |
| **Languages** | 100+ (exact coverage not published) |
| **Access** | Via OpenAI API (requires API key) |

**OpenAI's text-embedding-3-small** is a commercial embedding model with strong multilingual capabilities and high dimensionality.

**Strengths:**

- High-quality embeddings with 1536 dimensions
- Good multilingual coverage for major languages
- Fast API response times
- Well-maintained and regularly updated

**Weaknesses:**

- Commercial API — costs money per request
- Proprietary — model details not fully published
- Language coverage details not transparent (hard to know if a specific low-resource language is supported)
- Requires network access and API key

**Best for:** Users who already have an OpenAI API key and want high-quality embeddings without worrying about model hosting. Good for production workflows where API reliability matters.

### Qwen3 Embedding 4B {#qwen3}

| Property | Value |
|----------|-------|
| **Full name** | Qwen3 Embedding 4B |
| **Developer** | Alibaba Cloud (Qwen Team) |
| **Architecture** | Qwen3 transformer, 4 billion parameters |
| **Dimensions** | Variable (model-dependent) |
| **Languages** | 100+ with strong CJK support |
| **Access** | Via API |

**Qwen3 Embedding 4B** is a large embedding model from Alibaba's Qwen family, offering high capacity for capturing semantic nuances.

**Strengths:**

- Very large model (4B parameters) captures subtle semantic differences
- Excellent quality for Chinese, Japanese, Korean alongside European languages
- Strong performance on cross-lingual benchmarks
- Good handling of domain-specific and technical content

**Weaknesses:**

- Large model size means slower inference
- Higher computational cost per request
- Relatively newer model with less community testing on alignment tasks specifically

**Best for:** Alignment involving Chinese, Japanese, or Korean texts, or highly specialized/technical content where capturing subtle semantic differences is critical.

## Comparison table {#comparison-table}

| Feature | BGE-M3 | LaBSE | E5 Large | OpenAI | Qwen3 4B |
|---------|--------|-------|----------|--------|----------|
| **Dimensions** | 1024 | 768 | 1024 | 1536 | Variable |
| **Languages** | 100+ | 109 | 100+ | 100+ | 100+ |
| **CJK quality** | Good | Good | Good | Good | Excellent |
| **Low-resource** | Fair | Good | Fair | Unknown | Fair |
| **Speed** | Fast | Fast | Medium | Fast | Slow |
| **Cost** | API | Free API | API | Paid API | API |
| **Offline** | No | No | No | No | No |
| **Best for** | General | Low-resource | High quality | Production | CJK focus |

## How to choose {#how-to-choose}

### Decision flowchart {#flowchart}

1. **Are you working with Chinese, Japanese, or Korean?**
   - Yes: Start with **Qwen3 Embedding 4B** or **BGE-M3**
   - No: Continue to step 2

2. **Are you working with low-resource or minority languages?**
   - Yes: Start with **LaBSE**
   - No: Continue to step 3

3. **Do you need the highest possible quality?**
   - Yes: Use **Multilingual E5 Large**
   - No: Continue to step 4

4. **Do you have an OpenAI API key and prefer a managed service?**
   - Yes: Use **OpenAI text-embedding-3-small**
   - No: Use **BGE-M3** as your default

### Practical tips {#tips}

- **Start with the default.** BGE-M3 works well for most language pairs. Only switch if you see quality issues in the visualization (scattered dots instead of a clean diagonal).

- **Try LaBSE for rare languages.** If your alignment involves languages like Bashkir, Chuvash, Armenian, or Komi, LaBSE's training on mined parallel data gives it an advantage.

- **Check the visualization after the first batch.** The scatter plot is your best indicator of embedding quality. A clean diagonal means the model is working well for your language pair; scattered dots mean you should try a different model.

- **Consider proxy texts as a complement.** If no model produces good direct alignment for your language pair, try using proxy texts (machine translations as intermediaries). See [Working with Low-Resource Languages](low-resource-languages.en.md).

- **Model choice is fixed per alignment.** Once you process the first batch with a model, that model is used for all subsequent batches in the same alignment. To try a different model, create a new alignment.

## Technical details {#technical-details}

### How models are configured {#configuration}

Embedding models are managed through the admin panel. Each model has a **key** (internal identifier used for API routing) and a **public name** (displayed in the UI). Administrators can add, edit, and remove models as needed.

The model key typically follows the format `api:provider:model-name`, for example:

- `api:openrouter:baai/bge-m3`
- `api:huggingface:sentence-transformers/LaBSE`
- `api:openai:text-embedding-3-small`

### Embedding computation flow {#computation-flow}

1. The frontend sends the selected model key with the alignment request
2. The backend resolves the model key to an API endpoint and configuration
3. Sentences are sent to the embedding API in batches (typically 10 sentences per batch)
4. Returned embeddings are L2-normalized and stored in the alignment database
5. The similarity matrix is computed as a dot product of normalized embedding matrices

### Consistency requirements {#consistency}

All sentences in an alignment must use the same embedding model. Mixing models would make similarity scores incomparable, as different models map to different vector spaces. If you want to compare results from different models, create separate alignments for each.

## Further reading {#further-reading}

- [Sentence Embeddings Explained](sentence-embeddings.en.md) — how embeddings work at a conceptual level
- [Understanding Cosine Similarity](cosine-similarity.en.md) — the math behind similarity measurement
- [Working with Low-Resource Languages](low-resource-languages.en.md) — strategies for challenging language pairs
- [Alignment algorithm](algorithm.en.md) — how Lingtrain uses embeddings in the alignment pipeline
