# Proxy texts {#proxy}

Proxy documents improve alignment quality for language pairs where the embedding model has limited training data.

## What are proxy documents? {#what-is-proxy}

A proxy document is a **machine translation** of a split text that serves as an intermediary during alignment. Proxy texts are useful when the embedding model has limited training data for a given language pair — for example, when aligning Bashkir, Chuvash, or other low-resource languages.

Instead of comparing original sentences directly, the aligner compares the proxy translations, which are typically in a well-supported language like English or Russian. The original texts are preserved in the final result — the proxy is only used during the matching step.

## When to use proxy {#when-to-use}

Use proxy documents when:

- One or both languages are **low-resource** (limited representation in the embedding model)
- Direct alignment produces **poor quality** — many conflicts, broken diagonal on visualization
- The language pair is **distant** (e.g., Japanese–Finnish) and the model struggles with cross-lingual similarity

You do not need proxy for well-supported language pairs like English–Russian, English–German, or English–French — the default embedding models handle these well.

## Creating a proxy document {#creating-proxy}

1. **Upload and split** your source text in the [Documents tab](uploading.en.md)
2. **Download the split text** — click the download button in the sentence preview panel. This produces a `.txt` file with one sentence per line
3. **Translate it** using any machine translation service (Google Translate, DeepL, Yandex Translate, etc.)
4. **Upload the translation** as a proxy document in the Proxy section on the alignment detail page

![Proxy documents section](img/proxy-documents.en.png)

The proxy must contain **exactly the same number of lines** as the original split text. Each line in the proxy corresponds to the sentence at the same position in the original.

## Uploading proxy {#uploading-proxy}

On the alignment detail page, the **Proxy** section shows upload areas for both source ("from") and target ("to") languages.

![Proxy section overview](img/proxy-empty.en.png)

For each language:

- **Drag and drop** a `.txt` file into the upload area, or click to browse
- After upload, the status changes to **"Loaded"** with the filename displayed
- Use the **download** button to retrieve the uploaded proxy
- Use the **remove** button to delete it
- Use the **open** button to view the proxy text in a new tab

## Using proxy during alignment {#using-proxy}

Once proxy documents are uploaded, enable them in the [alignment settings](alignment.en.md#settings):

- **Use proxy (from)** — use the proxy for the source language
- **Use proxy (to)** — use the proxy for the target language

You can enable proxy for one or both sides. When enabled, the aligner computes embeddings from the proxy text instead of the original, then applies the resulting alignment mapping to the original sentences.

## Proxy in the editor {#proxy-in-editor}

When proxy documents are loaded, the editor can display proxy translations as **subscript annotations** below each sentence. This helps verify alignment quality even when you don't read one of the languages.

![Editor with proxy annotations](img/editor-subscriptions.en.png)

Toggle proxy visibility using the **"Show subscriptions"** switch in the Conflicts panel header.
