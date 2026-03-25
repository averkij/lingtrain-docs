# Python library {#python-library}

The `lingtrain-aligner` Python package provides the core alignment engine used by the Lingtrain web application. You can use it directly in your scripts, notebooks, or processing pipelines.

## Installation {#installation}

```bash
pip install lingtrain-aligner
```

The package requires Python 3.6+ and installs SentenceTransformers as a dependency. For GPU acceleration, install PyTorch with CUDA support beforehand.

## Quick start {#quick-start}

```python
from lingtrain_aligner import aligner, resolver, reader, saver

# 1. Prepare texts (one sentence per line)
with open("source_en.txt") as f:
    lines_en = f.read().splitlines()
with open("target_ru.txt") as f:
    lines_ru = f.read().splitlines()

# 2. Create alignment database
db_path = "my_alignment.db"
aligner.fill_db(
    db_path,
    lang_from="en",
    lang_to="ru",
    splitted_from=lines_en,
    splitted_to=lines_ru,
    name="My Alignment"
)

# 3. Run alignment
aligner.align_db(
    db_path,
    model_name="sentence_transformer_multilingual",
    batch_size=200,
    window=40
)

# 4. Resolve conflicts
resolver.resolve_all_conflicts(db_path)

# 5. Export results
saver.save_tmx(db_path, "output.tmx", lang_from="en", lang_to="ru")
```

## Core functions {#core-functions}

### aligner.fill_db() {#fill-db}

Creates and populates the SQLite alignment database with source and target sentences.

```python
aligner.fill_db(
    db_path,          # Path to SQLite database (created if not exists)
    lang_from,        # Source language code ("en", "ru", "de", etc.)
    lang_to,          # Target language code
    splitted_from,    # List of source sentences
    splitted_to,      # List of target sentences
    proxy_from=[],    # Optional: interlinear text for source
    proxy_to=[],      # Optional: interlinear text for target
    name=""           # Alignment project name
)
```

### aligner.align_db() {#align-db}

Runs the main alignment process on a populated database.

```python
aligner.align_db(
    db_path,                      # Path to alignment database
    model_name,                   # Embedding model identifier
    batch_size=200,               # Sentences per batch
    window=40,                    # Context window size
    batch_ids=[],                 # Specific batches to process (empty = all)
    shift=0,                      # Manual offset correction
    embed_batch_size=10,          # Sentences per embedding batch
    normalize_embeddings=True,    # L2-normalize embeddings
    save_pic=False,               # Save visualization images
    img_path="",                  # Path for visualization images
    use_proxy_from=False,         # Use interlinear for source
    use_proxy_to=False,           # Use interlinear for target
    store_embeddings=False,       # Persist embeddings in database
    use_api=False,                # Use external API for embeddings
    api=None,                     # API provider ("hf-inference" or "openai")
    model_api=None,               # Model ID for API
    api_key=None                  # API key
)
```

### resolver.resolve_all_conflicts() {#resolve-conflicts}

Automatically resolves alignment conflicts using semantic similarity scoring.

```python
resolver.resolve_all_conflicts(
    db_path,              # Path to alignment database
    model_name=None,      # Model for re-embedding (optional)
    show_info=False       # Print progress information
)
```

### resolver.prepare_index() {#prepare-index}

Extracts the alignment index for inspection or custom processing.

```python
index, total_batches = resolver.prepare_index(
    db_path,          # Path to alignment database
    batch_id=-1       # Specific batch (-1 = all)
)
```

## Export functions {#export}

### saver.save_tmx() {#save-tmx}

Exports aligned pairs in TMX (Translation Memory eXchange) format.

```python
saver.save_tmx(
    db_path,          # Path to alignment database
    output_path,      # Output TMX file path
    lang_from,        # Source language code
    lang_to           # Target language code
)
```

### saver.save_plain_text() {#save-plain}

Exports aligned sentences as plain text (one sentence per line).

```python
saver.save_plain_text(
    db_path,          # Path to alignment database
    output_path,      # Output text file path
    side,             # "from" or "to"
    batch_ids=[]      # Specific batches (empty = all)
)
```

### saver.save_json() / saver.save_xml() {#save-structured}

Exports aligned book structure in JSON or XML format.

```python
saver.save_json(db_path, output_path, lang_order=["en", "ru"], direction="to")
saver.save_xml(db_path, output_path, lang_order=["en", "ru"], direction="to")
```

### reader.get_aligned_pair_chains() {#get-pairs}

Extracts valid aligned sentence pair chains from the database.

```python
pairs = reader.get_aligned_pair_chains(
    db_path,          # Path to alignment database
    min_len=2         # Minimum chain length
)
# Returns: list of [text_from, text_to] pairs
```

## Embedding models {#models}

Available model identifiers for `model_name`:

| Identifier | Model | Languages |
|------------|-------|-----------|
| `sentence_transformer_multilingual` | distiluse-base-multilingual-cased-v2 | 50+ |
| `sentence_transformer_multilingual_xlm_100` | XLM-RoBERTa 100 languages | 100+ |
| `sentence_transformer_multilingual_labse` | LaBSE | 100+ |
| `rubert_tiny` | rubert-tiny2 | Russian-focused |
| `sonar` | Meta SONAR | 200+ |

You can also pass any Hugging Face SentenceTransformers model name directly.

## Using interlinear translations {#proxy}

Interlinear translations are machine translations used as intermediaries for low-resource language pairs. The interlinear document must have the same number of lines as the original split text.

```python
# Load interlinear translations
with open("source_ba_proxy_en.txt") as f:
    proxy_from = f.read().splitlines()

# Create database with interlinear
aligner.fill_db(
    db_path,
    lang_from="ba",
    lang_to="ru",
    splitted_from=lines_ba,
    splitted_to=lines_ru,
    proxy_from=proxy_from
)

# Align using interlinear
aligner.align_db(
    db_path,
    model_name="sentence_transformer_multilingual_labse",
    batch_size=200,
    window=40,
    use_proxy_from=True
)
```

## Using external APIs {#api}

### Hugging Face Inference API {#hf-api}

```python
aligner.align_db(
    db_path,
    model_name="",
    batch_size=200,
    window=40,
    use_api=True,
    api="hf-inference",
    model_api="sentence-transformers/LaBSE",
    api_key="hf_..."
)
```

### OpenAI Embeddings API {#openai-api}

```python
aligner.align_db(
    db_path,
    model_name="",
    batch_size=200,
    window=40,
    use_api=True,
    api="openai",
    model_api="text-embedding-3-small",
    api_key="sk-..."
)
```

## Supported languages {#languages}

Language codes for `lang_from` / `lang_to`:

| Code | Language | Code | Language |
|------|----------|------|----------|
| `en` | English | `pl` | Polish |
| `ru` | Russian | `pt` | Portuguese |
| `de` | German | `hu` | Hungarian |
| `fr` | French | `cs` | Czech |
| `es` | Spanish | `ja` | Japanese |
| `it` | Italian | `ko` | Korean |
| `zh` | Chinese | `sv` | Swedish |
| `tr` | Turkish | `nl` | Dutch |
| `uk` | Ukrainian | `be` | Belarusian |
| `ba` | Bashkir | `cv` | Chuvash |
| `hy` | Armenian | `xx` | General (any language) |

Use `xx` (General) for languages not in the list — the embedding model covers 100+ languages even without language-specific sentence splitting.

## Database format {#database}

The alignment database is a SQLite file with the following key tables:

- **splitted_from / splitted_to** — source and target sentences with metadata
- **processing_from / processing_to** — aligned sentence pairs per batch
- **doc_index** — alignment mapping (batch_id → sentence pairs)
- **meta** — document structure markers (headings, paragraphs)
- **batches** — batch processing metadata (timestamp, shift, window)
- **languages** — source and target language codes

The `.lt` format used by the Lingtrain web app is the same SQLite database with a different extension.
