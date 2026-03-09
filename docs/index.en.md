# Lingtrain Aligner {#overview}

Lingtrain Aligner is an ML-powered web application for creating parallel corpora and bilingual books from raw texts in different languages. It uses state-of-the-art multilingual embedding models to automatically align sentences across translations, supporting 50-200+ languages.

![Home page](img/apps-page.en.png)

## What is text alignment? {#what-is-alignment}

When you have a book and its translation, the sentences don't match one-to-one. A translator may split one sentence into two, merge several into one, or rearrange the structure. **Text alignment** is the process of finding which sentences in the original correspond to which sentences in the translation.

Lingtrain Aligner automates this process using neural language models that understand meaning across languages. It converts each sentence into a vector (embedding) and finds the best semantic matches between the source and target texts.

## Key features {#features}

- **Automatic sentence-level alignment** using multilingual embedding models (LaBSE, sentence-transformers)
- **50-200+ languages** supported, including major European, Asian, and low-resource languages
- **Interactive conflict resolution** — review and fix mismatches where the algorithm was uncertain
- **Batch processing** with configurable parameters (batch size, window, shift)
- **Proxy text support** — use machine-translated intermediaries for under-resourced language pairs
- **Multiple export formats** — HTML parallel books, TMX corpora, sentence/paragraph corpora, structured data
- **Visualization** of alignment quality per batch
- **Full editor** for manual adjustments at the sentence level

## Workflow overview {#workflow}

The alignment process consists of three main steps:

### 1. Upload texts {#step-upload}

Upload your source and target texts in the **Documents** tab. Select the languages, drag and drop your text files, then review the sentence preview and markup tags.

![Documents page with uploaded texts](img/documents-uploaded.en.png)

[Learn more about uploading texts](uploading.en.md)

### 2. Align {#step-align}

Create an alignment in the **Alignments** tab by selecting source and target documents. Configure batch parameters and click **Align next** to start processing.

![Alignment detail page](img/alignment-detail.en.png)

The system processes texts in batches (default: 200 sentences per batch). After each batch, it detects **conflicts** — places where the algorithm couldn't determine the correct match. You can resolve these automatically or open them in the editor for manual review.

![Conflicts and visualization](img/alignment-conflicts.en.png)

The **Visualization** panel shows alignment quality as a scatter plot. A clean diagonal line indicates good alignment; scattered dots indicate areas that may need attention.

The **Editor** displays aligned sentence pairs side by side. Each row shows the source sentence on the left and its translation on the right. You can edit, split, delete, or reassign sentences.

![Editor view](img/alignment-editor.en.png)

### 3. Export {#step-export}

Once alignment is complete (or partially complete), go to the **Create** tab to preview and export results.

![Create page with book preview](img/create-preview.en.png)

Configure the output:

- **Paragraph structure** — use paragraph breaks from source or target text
- **Language order** — which language appears first
- **Highlight style** — Simple, Pastel Fill, or Pastel Start

Download in any of the available formats:

![Download formats](img/create-download.en.png)

| Format | Description |
|--------|-------------|
| **HTML book** | Parallel book with side-by-side text, styled and ready to read |
| **TMX corpora** | Translation Memory eXchange format for CAT tools |
| **Sentence corpora** | Plain text files with one aligned sentence per line |
| **Paragraph corpora** | Plain text paragraphs based on paragraph structure |
| **Structured formats** | XML and JSON for processing pipelines |
| **Alignment database** | Lingtrain `.lt` format for backup and re-import |

## Supported languages {#languages}

The Aligner supports the following languages with dedicated preprocessing:

Russian, English, German, French, Spanish, Italian, Portuguese, Turkish, Polish, Hungarian, Czech, Chinese, Japanese, Korean, Swedish, Dutch, Ukrainian, Belarusian, Bashkir, Chuvash, Armenian.

For any other language, use the **General** option — the embedding model covers 100+ languages even without language-specific sentence splitting.

## Use cases {#use-cases}

- **Language learners** — create parallel books from your favorite novels to study vocabulary in context
- **Translators** — build translation memories (TMX) from existing translations
- **Researchers** — extract parallel corpora for NLP and machine translation research
- **Educators** — prepare bilingual reading materials for students
- **Publishers** — produce bilingual editions with professional formatting
