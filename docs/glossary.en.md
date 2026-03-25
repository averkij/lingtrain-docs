# Glossary {#glossary}

This glossary defines key terms and concepts used throughout Lingtrain Aligner and its documentation.

---

### Aggregation {#aggregation}

The process of combining multiple sentence embeddings into a single vector representation when resolving conflicts. Lingtrain supports several aggregation methods including weighted average (default), length scaling, max pooling, and logarithmic scaling. The aggregation method determines how individual sentence vectors are merged when grouping sentences during conflict resolution.

### Alignment {#alignment}

The core process of matching sentences between a source text and a target text. In Lingtrain, an alignment is a project that contains all data related to pairing sentences across two texts: the split texts, computed embeddings, the resulting sentence mapping, conflict resolution history, and manual edits. Each alignment is stored as a `.lt` file (SQLite database) on the server.

### Alignment Database {#alignment-database}

A SQLite file (`.lt` extension) that stores all alignment data for a single project. It contains the split sentences for both languages, computed embeddings, the document index (sentence pair mapping), batch metadata, edit history, and markup tags. This file can be exported for backup and re-imported later.

### Batch {#batch}

A segment of the source text processed as a single unit during alignment. By default, each batch contains 200 source sentences and the proportionally corresponding target sentences (plus the window overlap). Large texts are divided into multiple batches for incremental processing, progress tracking, and memory management.

### Batch Count {#batch-count}

The number of batches to process in a single "Align next" action. Can be set from 1 to 5. Processing multiple batches at once is convenient for large texts where you do not need to inspect each batch individually.

### Batch Processing {#batch-processing}

The approach of dividing a text into fixed-size segments (batches) and aligning them sequentially. This prevents memory exhaustion on large texts, allows incremental progress, and enables per-batch quality inspection.

### Batch Shift {#batch-shift}

See [Shift](#shift).

### Batch Size {#batch-size}

The number of source sentences in each batch. The default is 200. This value is fixed for the entire alignment once it is created. The corresponding target window is calculated proportionally based on the ratio of target to source sentence counts.

### CAT Tool {#cat-tool}

Computer-Assisted Translation tool. Software used by professional translators that leverages translation memories, glossaries, and other aids. Examples include SDL Trados Studio, memoQ, OmegaT, Memsource, and Wordfast. Lingtrain exports TMX files compatible with these tools.

### Celery {#celery}

A distributed task queue system used by Lingtrain to process alignment jobs asynchronously. When you click "Align next" or "Resolve all," a task is dispatched to a Celery worker queue. This allows the web interface to remain responsive while heavy computation runs in the background.

### Chain {#chain}

A sequence of consecutive aligned sentence pairs where the target sentence positions are monotonically increasing. Chains represent correctly aligned regions. The gaps between chains are conflicts. Longer chains indicate higher alignment quality.

### Chain Score {#chain-score}

A quality metric calculated as `1 - (breaks / total_lines)`, where `breaks` is the number of non-consecutive jumps in the alignment sequence and `total_lines` is the total number of aligned lines. A score of 1.0 indicates perfect sequential alignment. Scores above 0.95 are considered good quality.

### Clean Text {#clean-text}

An upload option that applies basic text normalization: removing extra whitespace, fixing encoding artifacts, and standardizing line breaks. Useful for texts extracted from PDFs or other sources with formatting issues.

### Concurrent Task Limit {#concurrent-task-limit}

The maximum number of alignment or conflict resolution tasks a user can have running simultaneously. This limit is determined by the user's subscription plan and prevents server overload. When the limit is reached, new tasks are rejected until existing ones complete.

### Conflict {#conflict}

A discontinuity in the alignment sequence where the algorithm could not establish a clean one-to-one mapping between source and target sentences. Conflicts occur when translators split, merge, or reorder sentences. They are detected automatically and displayed in the Conflicts section for resolution.

### Conflict Resolution {#conflict-resolution}

The process of fixing alignment conflicts. Lingtrain offers automatic resolution (which evaluates all possible sentence groupings and selects the best based on similarity scores) and manual resolution in the editor. Automatic resolution handles small conflicts (up to approximately 6x6 sentences); larger ones require manual intervention.

### Cosine Similarity {#cosine-similarity}

A mathematical measure of the angle between two vectors, ranging from -1 (opposite) to 1 (identical). In Lingtrain, cosine similarity quantifies how semantically close two sentence embeddings are. Higher values indicate greater semantic similarity between sentences. After L2 normalization, cosine similarity equals the dot product of the two vectors.

### Document {#document}

A text file uploaded to Lingtrain for alignment. Documents are plain text files (`.txt`) associated with a specific language. After upload, the text is split into sentences using language-specific rules and stored in the system for use in alignments.

### Document Index {#document-index}

The internal data structure that records the mapping between source and target sentences. It is organized by batch and contains pairs of `(source_line_ids, target_line_ids)` for each aligned row. The document index is the primary output of the alignment process.

### Embedding {#embedding}

A dense numerical vector (typically 768 dimensions) that represents the semantic meaning of a sentence. Multilingual embedding models encode sentences from different languages into the same vector space, allowing cross-lingual similarity to be computed using cosine similarity or dot product.

### Embedding Model {#embedding-model}

A neural network that converts text into embedding vectors. Lingtrain supports several multilingual models including LaBSE, distiluse-base-multilingual-cased-v2, XLM-R-100langs, rubert-tiny2, and SONAR. Different models vary in language coverage, speed, and accuracy.

### Erase {#erase}

An operation that removes all alignment data (embeddings, batch results, edit history) from an alignment while keeping the alignment record and its source/target document references. After erasing, you can select a different embedding model and re-align from scratch.

### Export {#export}

The process of generating downloadable output files from an alignment. Available formats include HTML parallel books, TMX translation memories, sentence corpora, paragraph corpora, XML, JSON, and the native `.lt` alignment database format.

### GUID {#guid}

Globally Unique Identifier. A random string that uniquely identifies each alignment and document in Lingtrain. GUIDs are used in URLs, API endpoints, and file paths to reference specific resources without exposing sequential numeric IDs.

### Handle Start / Handle Finish {#handle-edges}

Options in conflict resolution that control whether conflicts at the very beginning or very end of the aligned text are included in automatic resolution. By default, edge conflicts are excluded because they often result from different preambles or postscripts between source and target texts.

### HTML Book {#html-book}

An export format that produces a styled parallel book viewable in any web browser. The book displays source and target sentences side by side, with optional chapter headings, author credits, and highlight styling. Three styles are available: Simple, Pastel Fill, and Pastel Start.

### Inference Type {#inference-type}

How embedding computations are performed: either "local" (on the server's own hardware) or "api" (via external APIs like Hugging Face or OpenAI). Local inference offers lower latency and no per-request costs but requires GPU resources. API inference provides access to additional models.

### LaBSE {#labse}

Language-agnostic BERT Sentence Embeddings. A multilingual embedding model developed by Google that supports 100+ languages. LaBSE is particularly effective for rare and low-resource languages, making it a recommended choice when aligning texts in less common language pairs.

### Lingtrain Aligner (Library) {#lingtrain-aligner-library}

The Python library (`lingtrain-aligner`) that implements the core alignment algorithm. It handles sentence splitting, embedding computation, similarity matrix construction, best-match selection, and conflict resolution. The web application wraps this library with a user interface, task queue, and database.

### Magic Alignment {#magic-alignment}

An experimental alignment mode that uses enhanced search density to find matches. When enabled, the aligner applies a denser search pattern across the similarity matrix, which may produce better results for texts with significant structural differences but at the cost of increased computation time.

### Markup Tag {#markup-tag}

A special annotation placed at the end of a text line using the `%%%%%` prefix (e.g., `%%%%%author.`, `%%%%%h1.`). Markup tags identify metadata like author names, titles, headings, quotations, and section dividers. Tagged lines are excluded from alignment and used for formatting in exported books.

### Parallel Corpus {#parallel-corpus}

A dataset consisting of aligned sentence pairs in two languages. Parallel corpora are foundational resources for machine translation training, cross-lingual NLP research, and translation studies. Lingtrain's primary purpose is creating parallel corpora from raw texts.

### Paragraph Corpora {#paragraph-corpora}

An export format that groups aligned sentences into paragraphs based on paragraph markers detected during sentence splitting. Each paragraph in the source text corresponds to a block of aligned sentences in the target text.

### Interlinear Translation (Proxy Text) {#proxy-text}

A machine-translated version of a split text used as an intermediary during alignment. When the embedding model has limited training data for a language, an interlinear translation in a well-supported language (e.g., English) can improve alignment quality. The original texts are preserved in the final output.

### Quality Score {#quality-score}

See [Chain Score](#chain-score).

### Queue {#queue}

The task processing system. When you submit an alignment or conflict resolution task, it enters a queue managed by Celery workers. Tasks are processed in priority order. The queue position is visible on the alignment page while waiting.

### Sentence Corpora {#sentence-corpora}

An export format that produces plain text files with one aligned sentence per line. Two separate files are generated (one for each language). Sentence corpora are widely used in NLP research, machine translation training, and corpus linguistics.

### Sentence Splitting {#sentence-splitting}

The preprocessing step that divides a raw text into individual sentences. Lingtrain uses language-specific splitting rules: the `razdel` library for Russian/Ukrainian/Belarusian, regex patterns for Chinese and Japanese, and a standard tokenizer for other languages. Markup tags are extracted during splitting.

### Shift {#shift}

A parameter that manually offsets the target window position during batch processing. Positive values shift the target window forward (toward later sentences); negative values shift it backward. Use shift to compensate when the proportion of source to target sentences varies along the text, causing the alignment diagonal to drift.

### Similarity Matrix {#similarity-matrix}

A matrix where entry `[i][j]` contains the cosine similarity between source sentence `i` and target sentence `j`. Computed by multiplying the source embedding matrix by the transpose of the target embedding matrix. A window mask is applied to restrict matches to a diagonal band.

### SONAR {#sonar}

A massively multilingual sentence encoder developed by Meta AI. SONAR supports 200+ languages, providing the widest language coverage among the models available in Lingtrain. It is recommended for extremely low-resource languages not well covered by LaBSE.

### Task Record {#task-record}

An internal database record that tracks the lifecycle of a processing task (alignment or conflict resolution). It stores the task status (queued, running, completed, failed, cancelled), timing information, error messages, and the associated alignment.

### TMX {#tmx}

Translation Memory eXchange. An XML-based standard format for storing translation memory data. TMX files contain aligned source-target sentence pairs with language metadata and are compatible with all major CAT tools (SDL Trados, memoQ, OmegaT, etc.).

### Visualization {#visualization}

A scatter plot generated for each processed batch, showing source sentence positions on one axis and matched target positions on the other. A clean diagonal indicates good alignment; scattered points indicate problems. Visualizations help identify batches that need parameter adjustment or manual review.

### Window {#window}

The number of extra sentences added on each side of the proportional target range for a batch. A larger window increases the search space, improving the chance of finding correct matches when sentence counts differ significantly between source and target. The default value is 40-50.

### Window Mask {#window-mask}

A restriction applied to the similarity matrix that sets similarity values to zero outside a diagonal band defined by the window parameter. This enforces the assumption that sentence order is generally preserved across translations and prevents false matches from distant parts of the text.
