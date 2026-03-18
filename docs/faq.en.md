# Frequently Asked Questions {#faq}

This page answers the most common questions about Lingtrain Aligner. If your question is not listed here, check the [Troubleshooting Guide](troubleshooting.en.md) or reach out through the feedback form in the application.

---

## General {#general}

### What is Lingtrain Aligner? {#what-is-lingtrain}

Lingtrain Aligner is an ML-powered web application for creating parallel corpora and bilingual books. It takes two texts in different languages (for example, a novel and its translation) and automatically aligns them at the sentence level. The result is a structured dataset of sentence pairs that can be exported as parallel books, translation memories, corpora, and other formats.

### Who is it for? {#who-is-it-for}

Lingtrain is designed for a broad audience:

- **Language learners** who want to read bilingual books with side-by-side translations
- **Translators** who need to build translation memories (TMX files) from existing translations
- **NLP researchers** who require parallel corpora for machine translation training or linguistic analysis
- **Educators** preparing bilingual reading materials for students
- **Publishers** producing bilingual editions

### Is Lingtrain free? {#is-it-free}

Lingtrain offers a free tier for individual users. Usage is metered by the number of lines processed. Each account has a quota that refreshes periodically. Paid plans are available for higher volumes and concurrent task limits. See your account settings for current plan details.

### Do I need to install anything? {#installation}

No. Lingtrain Aligner is a web application that runs entirely in your browser. You only need a modern browser (Chrome, Firefox, Edge, or Safari) and an internet connection. There is nothing to download or install.

---

## File Formats and Upload {#files}

### What file format should my texts be in? {#file-format}

Lingtrain accepts **plain text files** (`.txt`) encoded in UTF-8. Rich formats like PDF, DOCX, EPUB, or HTML are not supported directly. Convert your texts to plain text before uploading.

### What is the maximum file size? {#max-file-size}

The default maximum upload size for a single document is **4 MB**. This is sufficient for most full-length novels. If your text exceeds this limit, consider splitting it into parts.

### Can I upload multiple files at once? {#multiple-files}

Each upload slot accepts one file at a time. You can upload multiple documents for the same language sequentially. Each uploaded document appears in the document list and can be used in different alignments.

### What encoding should my text files use? {#encoding}

Files must be encoded in **UTF-8**. If your file uses a different encoding (like Windows-1251 or ISO-8859-1), convert it to UTF-8 before uploading. Most text editors (Notepad++, VS Code, Sublime Text) can re-save files in UTF-8.

### What does the "Clean text" option do? {#clean-text}

The "Clean text" checkbox applies basic text normalization during upload: removing extra whitespace, fixing common encoding artifacts, and standardizing line breaks. Enable it if your text was extracted from a PDF or has formatting issues.

---

## Languages {#languages}

### How many languages does Lingtrain support? {#language-count}

Lingtrain explicitly supports **22 languages** with dedicated sentence-splitting rules: Russian, English, German, French, Spanish, Italian, Portuguese, Turkish, Polish, Hungarian, Czech, Chinese, Japanese, Korean, Swedish, Dutch, Ukrainian, Belarusian, Bashkir, Chuvash, Armenian, and a "General" mode.

The "General" mode allows alignment of **200+ additional languages** supported by the underlying embedding models (LaBSE, SONAR). The sentence splitting in General mode uses a universal rule-based tokenizer that works for most Latin-script and Cyrillic-script languages.

### Can I align two texts in languages not on the explicit list? {#unlisted-languages}

Yes. Select **"General"** for both languages. The embedding model still produces meaningful sentence vectors for most languages, even without language-specific splitting rules. Quality depends on how well the language is represented in the model's training data.

### Which language pairs work best? {#best-pairs}

Language pairs with strong representation in the embedding model produce the best results. Examples include English-Russian, English-German, English-French, English-Spanish, English-Chinese, and most combinations of major European languages. For less common pairs, consider using [proxy texts](proxy.en.md).

### Does Lingtrain support right-to-left languages like Arabic or Hebrew? {#rtl-languages}

The embedding models support Arabic and Hebrew. You can align texts in these languages using the "General" mode. The web interface renders text according to browser defaults, so right-to-left text will display correctly in the editor and exports.

---

## Alignment Process {#alignment-process}

### How does the alignment algorithm work? {#how-it-works}

Lingtrain converts each sentence into a numeric vector (embedding) using a multilingual neural language model. It then computes cosine similarity between all source and target sentence embeddings within a processing window, and selects the best match for each source sentence. For a detailed explanation, see the [Algorithm page](algorithm.en.md).

### What are batches? {#what-are-batches}

Large texts are processed in segments called batches. Each batch covers a fixed number of source sentences (default: 200) and the proportionally corresponding target sentences. Batches allow incremental processing with progress tracking and prevent memory exhaustion on very large texts.

### What is the "window" parameter? {#window-parameter}

The window parameter controls how many extra sentences are included on each side of the proportional target range for a batch. A larger window (e.g., 60) increases the chance of finding correct matches when sentence counts differ significantly, but also increases computation time.

### What is the "shift" parameter? {#shift-parameter}

The shift parameter manually offsets the target window position. Use it when the proportion between source and target sentence counts varies along the text. For example, if the target text has significantly more sentences in early chapters, a positive shift moves the target window forward to compensate.

### What are conflicts? {#what-are-conflicts}

Conflicts are places where the alignment algorithm could not establish a clean sequential mapping. They occur when sentences were split, merged, or reordered by the translator. Conflicts are detected automatically and can be resolved using the built-in resolver or manually in the editor.

### How do I resolve conflicts? {#resolving-conflicts}

Click **"Resolve all"** in the Conflicts section to run automatic resolution. The system evaluates all possible groupings of conflicting sentences, computes combined embeddings for each grouping, and selects the one with the highest similarity score. After automatic resolution, review any remaining conflicts in the editor.

### Can I stop an alignment in progress? {#stopping-alignment}

Yes. Click the **"Stop"** button while an alignment is running. All progress up to the current point is preserved. You can resume by clicking "Align next" again.

### What does "Align all" do? {#align-all}

"Align all" reprocesses all batches from scratch, overwriting previous results. Use it when you want to start over with different settings (e.g., a different shift or window value). Note that this erases all previous alignment data including manual edits.

---

## Proxy Texts {#proxy}

### What is a proxy text? {#what-is-proxy}

A proxy text is a machine translation of your split document that serves as an intermediary during alignment. Instead of comparing the original sentences directly (which may not work well for under-resourced language pairs), the aligner compares the proxy translations while preserving the original texts in the final output.

### When should I use proxy texts? {#when-proxy}

Use proxy when:

- One or both languages are low-resource (e.g., Bashkir, Chuvash, minority languages)
- Direct alignment produces poor quality with many conflicts
- The language pair is very distant (e.g., Japanese-Finnish)

You generally do not need proxy for well-supported pairs like English-Russian, English-German, or English-French.

### How do I create a proxy text? {#create-proxy}

1. Upload and split your source text normally
2. Download the split text using the download button in the sentence preview
3. Translate the downloaded file using any machine translation service (Google Translate, DeepL, etc.)
4. Upload the translated file as a proxy document on the alignment detail page

The proxy must have **exactly the same number of lines** as the original split text.

---

## Export {#export}

### What export formats are available? {#export-formats}

Lingtrain supports the following export formats:

| Format | Extension | Use case |
|--------|-----------|----------|
| HTML book | `.html` | Parallel book for reading in a browser |
| TMX | `.tmx` | Translation memory for CAT tools |
| Sentence corpora | `.txt` | One sentence per line, for NLP research |
| Paragraph corpora | `.txt` | Paragraph-level aligned text |
| XML | `.xml` | Structured data for pipelines |
| JSON | `.json` | Structured data for applications |
| Alignment DB | `.lt` | Full backup for re-import |

For detailed specifications of each format, see the [Export Formats Reference](export-formats-reference.en.md).

### Can I customize the HTML book appearance? {#customize-html}

Yes. The export settings allow you to choose between three highlight styles: **Simple** (no background), **Pastel Fill** (solid pastel backgrounds), and **Pastel Start** (gradient backgrounds). You can also control paragraph structure direction and language order.

### What CAT tools can open TMX files? {#tmx-cat-tools}

TMX (Translation Memory eXchange) is a standard format supported by virtually all CAT tools, including SDL Trados Studio, memoQ, Memsource, OmegaT, Wordfast, CafeTran, and Smartcat.

### Can I export partially aligned texts? {#partial-export}

Yes. Export is available as soon as at least one batch has been processed. The export will include only the aligned portions of the text.

---

## Quality {#quality}

### How do I assess alignment quality? {#quality-assessment}

The alignment detail page shows a **chain score** (quality metric) for each batch and overall. A score of 1.0 means perfect sequential alignment. Scores above 0.95 indicate good quality. Additionally, the **visualization** scatter plot should show a clean diagonal line.

### What can I do if alignment quality is poor? {#poor-quality}

Several strategies can improve alignment quality:

1. **Adjust the shift** parameter if the diagonal is offset
2. **Increase the window** parameter to give the algorithm more search space
3. **Use proxy texts** for low-resource languages
4. **Clean your texts** more thoroughly (remove footnotes, page numbers, translator notes)
5. **Ensure matching markup** (both texts should have the same structural tags)
6. **Try a different embedding model** if available for your language pair

### Which embedding model should I choose? {#choosing-model}

The model selector shows available models for your language pair. **LaBSE** is recommended for most language pairs, especially those involving non-European languages. Specialized models may be available for specific language combinations. If you are unsure, the default model is a safe choice.

---

## Account and Data {#account}

### How do I create an account? {#create-account}

Click **"Sign in"** on the home page, then choose **"Create account"**. Enter your email address and password. You will receive a verification code by email. Enter the code to activate your account. You can also sign in using Google, Yandex, or VK OAuth providers.

### Is my data private? {#data-privacy}

Yes. Each user's documents and alignments are isolated. Other users cannot access your files.

### Can I delete my data? {#delete-data}

You can delete individual documents and alignments at any time through the interface. All associated files are removed from the server when you delete them.

### What happens if I lose my alignment? {#backup}

You can export the alignment database (`.lt` format) at any time as a backup. This file contains the complete alignment state including sentence splits, alignment mapping, and edit history. You can re-import `.lt` files later to restore an alignment.

---

## API Access {#api-access}

### Does Lingtrain have an API? {#api}

Yes. Lingtrain exposes a REST API for all operations. You can upload documents, create alignments, start processing, resolve conflicts, and export results programmatically.

### How do I authenticate with the API? {#api-auth}

The API uses JWT (JSON Web Token) bearer authentication. Obtain a token by sending your credentials to the `/api/auth/login` endpoint. Include the token in the `Authorization: Bearer <token>` header of subsequent requests.

---

## Common Errors {#common-errors}

### "File too large" error {#file-too-large}

Your file exceeds the maximum upload size (4 MB by default). Reduce the file size by splitting it into smaller parts or removing unnecessary content.

### "File already exists" error {#file-exists}

A file with the same name has already been uploaded for this language. Either rename your file or delete the existing one first.

### "Concurrent task limit reached" {#concurrent-limit}

You have reached the maximum number of simultaneous processing tasks allowed by your plan. Wait for a running task to complete before submitting another, or upgrade your plan for higher concurrency limits.

### "Alignment uses an unsupported embedding model" {#unsupported-model}

The embedding model used when this alignment was originally created is no longer available on the server. You need to erase the alignment data and select a currently available model to re-align.

### The visualization shows scattered dots instead of a diagonal {#bad-visualization}

This indicates poor alignment quality. See the [quality improvement tips](#poor-quality) above, or consult the [Troubleshooting Guide](troubleshooting.en.md).
