# Interlinear translation {#proxy}

Interlinear documents improve alignment quality for language pairs where the embedding model has limited training data.

## What are interlinear documents? {#what-is-proxy}

An interlinear document is a **machine translation** of a split text that serves as an intermediary during alignment. Interlinear translations are useful when the embedding model has limited training data for a given language pair — for example, when aligning Bashkir, Chuvash, or other low-resource languages.

Instead of comparing original sentences directly, the aligner compares the interlinear translations, which are typically in a well-supported language like English or Russian. The original texts are preserved in the final result — the interlinear translation is only used during the matching step.

## When to use interlinear translation {#when-to-use}

Use interlinear documents when:

- One or both languages are **low-resource** (limited representation in the embedding model)
- Direct alignment produces **poor quality** — many conflicts, broken diagonal on visualization
- The language pair is **distant** (e.g., Japanese–Finnish) and the model struggles with cross-lingual similarity

You do not need interlinear translation for well-supported language pairs like English–Russian, English–German, or English–French — the default embedding models handle these well.

## Creating an interlinear document {#creating-proxy}

1. **Upload and split** your source text in the [Documents tab](uploading.en.md)
2. **Download the split text** — click the download button in the sentence preview panel. This produces a `.txt` file with one sentence per line
3. **Translate it** using any machine translation service (Google Translate, DeepL, Yandex Translate, etc.)
4. **Upload the translation** as an interlinear document in the Interlinear section on the alignment detail page

![Interlinear documents section](img/proxy-documents.en.png)

The interlinear document must contain **exactly the same number of lines** as the original split text. Each line in the interlinear corresponds to the sentence at the same position in the original.

## Uploading interlinear translation {#uploading-proxy}

On the alignment detail page, the **Interlinear** section shows upload areas for both source ("from") and target ("to") languages.

![Interlinear section overview](img/proxy-empty.en.png)

For each language:

- **Drag and drop** a `.txt` file into the upload area, or click to browse
- After upload, the status changes to **"Loaded"** with the filename displayed
- Use the **download** button to retrieve the uploaded interlinear translation
- Use the **remove** button to delete it
- Use the **open** button to view the interlinear text in a new tab

## Using interlinear translation during alignment {#using-proxy}

Once interlinear documents are uploaded, enable them in the [alignment settings](alignment.en.md#settings):

- **Use interlinear (from)** — use the interlinear translation for the source language
- **Use interlinear (to)** — use the interlinear translation for the target language

You can enable interlinear translation for one or both sides. When enabled, the aligner computes embeddings from the interlinear text instead of the original, then applies the resulting alignment mapping to the original sentences.

## Interlinear in the editor {#proxy-in-editor}

When interlinear documents are loaded, the editor can display interlinear translations as **subscript annotations** below each sentence. This helps verify alignment quality even when you don't read one of the languages.

![Editor with interlinear annotations](img/editor-subscriptions.en.png)

Toggle interlinear visibility using the **"Show interlinear"** switch in the Conflicts panel header.
