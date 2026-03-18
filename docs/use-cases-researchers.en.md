# Lingtrain for NLP Researchers {#use-cases-researchers}

Parallel corpora are a foundational resource for computational linguistics and natural language processing. Lingtrain Aligner provides researchers with a fast, interactive pipeline for creating high-quality sentence-aligned corpora from raw bilingual texts, with export in formats ready for research workflows.

This page describes how researchers can use Lingtrain for machine translation, cross-lingual NLP, translation studies, digital humanities, and more.

## Building MT training data {#mt-training-data}

### Why custom parallel corpora matter {#why-custom}

Standard parallel corpora (Europarl, UN Corpus, OPUS) cover many language pairs but are limited in domain, register, and style. A parliamentary corpus will not help you build a medical translation system; a subtitle corpus will not cover legal terminology.

Lingtrain allows researchers to create **domain-specific parallel corpora** from any available translations:

- Align bilingual textbooks to create a corpus for educational domain MT
- Align parallel news articles for journalism-domain translation
- Align literary translations for literary MT research
- Align technical documentation for specialized MT systems

### Workflow for MT researchers {#mt-workflow}

1. **Collect** source and target texts in your domain
2. **Upload** to Lingtrain and run alignment
3. **Review and correct** using the conflict resolver and editor — MT training data quality directly affects model quality
4. **Export** as sentence corpus (two parallel plain text files) or TMX
5. **Post-process** — filter by alignment confidence, deduplicate, and clean
6. **Train** your MT model on the resulting parallel data

The sentence corpus export produces two files with one sentence per line, aligned by line number — the standard input format for tools like OpenNMT, Marian NMT, and fairseq.

## Cross-lingual NLP {#cross-lingual}

### Annotation projection {#annotation-projection}

Parallel corpora enable **annotation projection** — transferring linguistic annotations from a resource-rich language to a resource-poor one:

1. Annotate the resource-rich side (e.g., English) with NER tags, POS tags, or dependency parses using existing tools
2. Use the sentence alignment to project annotations to the resource-poor side
3. Train models for the resource-poor language using projected annotations

Lingtrain's sentence-level alignment provides the mapping needed for this projection. Export as JSON or XML format to preserve alignment IDs alongside the text.

### Cross-lingual transfer learning {#transfer-learning}

Parallel corpora are used to evaluate and improve cross-lingual models:

- **Evaluation** — test whether a model trained on language A performs well on language B by evaluating on aligned sentence pairs
- **Fine-tuning** — use aligned sentence pairs to fine-tune multilingual models for specific language pairs
- **Contrastive learning** — train models using aligned and unaligned sentence pairs as positive and negative examples

### Bilingual lexicon extraction {#lexicon-extraction}

From a sentence-aligned corpus, researchers can extract bilingual word and phrase correspondences:

1. Create a sentence-aligned corpus in Lingtrain
2. Export as sentence corpus
3. Apply word alignment tools (eflomal, fast_align, SimAlign) to extract word-level correspondences
4. Aggregate word alignments to build bilingual lexicons

This pipeline is especially valuable for low-resource languages where existing dictionaries are limited. See [Working with Low-Resource Languages](low-resource-languages.en.md).

## Translation studies {#translation-studies}

### Quantitative translation analysis {#quantitative-analysis}

Parallel corpora enable data-driven translation research:

- **Translation shift analysis** — measure how much translators deviate from source structure (sentence splits, merges, reorderings)
- **Explicitation studies** — quantify how often translators add explicit information not present in the source
- **Simplification analysis** — measure whether translations use simpler vocabulary and shorter sentences than originals
- **Translator style comparison** — compare multiple translations of the same source to identify individual translator preferences

Lingtrain's alignment metadata (line IDs, groupings, conflict data) provides rich information for these analyses. The alignment database (`.lt` format) preserves the full alignment history.

### Corpus-based translation teaching {#translation-teaching}

Translation instructors can use Lingtrain to:

- Create aligned corpora of student translations for error analysis
- Compare student translations with professional translations
- Build reference corpora for specific translation problems (idioms, cultural references, technical terminology)

## Digital humanities {#digital-humanities}

### Literary parallel corpora {#literary-corpora}

Digital humanities researchers use parallel corpora of literary texts for:

- **Comparative literature** — study how works are adapted across languages and cultures
- **Translation history** — compare translations from different eras to trace changing translation norms
- **Stylistics** — analyze how literary style is preserved or transformed in translation
- **Intertextuality** — track cross-lingual references and influences

Lingtrain's support for markup tags (`%%%%%h1.`, `%%%%%author.`) preserves structural information (chapters, sections) in the aligned corpus, enabling structural analysis.

### Endangered language documentation {#endangered-languages}

For languages with limited digital resources, aligning existing bilingual texts (folklore collections, religious texts, educational materials) creates structured parallel data that can be used for:

- Building basic NLP tools (tokenizers, POS taggers)
- Training machine translation systems
- Creating bilingual learning materials
- Preserving translation knowledge

## Export formats for research {#export-formats}

Lingtrain supports several export formats suited to different research needs:

### Sentence corpus {#sentence-corpus}

Two plain text files with aligned sentences, one per line. Lines correspond by number.

**Use case:** MT training, statistical analysis, any tool expecting line-aligned parallel text.

**Format:**
```
File 1 (source.txt):          File 2 (target.txt):
The old man sat by the window. Старик сидел у окна.
He watched the rain.           Он смотрел на дождь.
```

### TMX (Translation Memory eXchange) {#tmx}

XML format standard for translation memories. Each translation unit contains the source and target segments with language tags.

**Use case:** CAT tool integration, translation studies, any XML-based processing pipeline.

### JSON and XML {#json-xml}

Structured formats preserving alignment metadata (IDs, line mappings, groupings).

**Use case:** Custom processing pipelines, web applications, database import, detailed analysis.

### Alignment database (.lt) {#lt-format}

Lingtrain's native SQLite-based format containing the complete alignment data: sentence texts, embeddings, alignment mappings, conflict history, and metadata.

**Use case:** Backup, re-import into Lingtrain, direct SQL queries for research analysis.

## Integration with Python {#python-integration}

The `lingtrain-aligner` Python library allows researchers to use the alignment algorithm programmatically:

- Integrate alignment into data processing pipelines
- Batch-process multiple text pairs without the web interface
- Access low-level alignment data (similarity matrices, conflict details)
- Customize alignment parameters for specific research needs

See [lingtrain-aligner Python library](python-library.en.md) for documentation.

## Tips for research use {#tips}

1. **Prioritize alignment quality over speed.** For research corpora, take the time to review conflicts and fix alignment errors. Noisy alignments propagate errors into downstream models.

2. **Document your process.** Record which model, parameters, and post-processing steps you used. This ensures reproducibility.

3. **Export the alignment database.** The `.lt` file preserves everything — you can always re-export in different formats later or re-import for further editing.

4. **Use the visualization for quality assessment.** The scatter plots provide a quick visual check of alignment quality across the entire text, highlighting problem areas.

5. **Consider multiple models.** For research, it may be valuable to align the same text pair with different embedding models and compare results. Create separate alignments for each model.

## Further reading {#further-reading}

- [What is a Parallel Corpus?](what-is-parallel-corpus.en.md) — background on parallel corpora
- [Text alignment: theory and methods](text-alignment-theory.en.md) — alignment algorithms and literature
- [Alignment algorithm](algorithm.en.md) — Lingtrain's specific algorithm
- [Comparing Embedding Models](embedding-models-comparison.en.md) — choosing the right model
- [lingtrain-aligner Python library](python-library.en.md) — programmatic access
