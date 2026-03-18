# Export Formats Reference {#export-formats-reference}

This page provides detailed technical specifications for each export format supported by Lingtrain Aligner. For a general overview of the export workflow, see the [Export page](export.en.md).

## Export Settings {#export-settings}

Before exporting, configure the output in the **Create** tab:

- **Paragraph structure** (`par_direction`) — determines which text's paragraph markers are used to group sentences into paragraphs. Values: `from` (source text) or `to` (target text).
- **Language order** (`left_lang`) — which language appears first (left side) in parallel output. Values: `from` or `to`.
- **Highlight style** (`style`) — visual styling for the HTML book. Values: `none` (Simple), `pastel_fill` (Pastel Fill), `pastel_start` (Pastel Start).

## HTML Book {#html-book}

**File extension:** `.html`

**Description:** A self-contained HTML file that renders a parallel bilingual book in any web browser. The book displays aligned sentence pairs in a structured layout with optional styling.

**Structure:**

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title>Book Title — Author Name</title>
  <style>
    /* Embedded CSS for styling */
  </style>
</head>
<body>
  <!-- Title page (from author/title markup tags) -->
  <div class="title-page">
    <div class="author">Author Name</div>
    <div class="title">Book Title</div>
    <div class="translator">Translator Credit</div>
  </div>

  <!-- Epigraph (from qtext/qname markup tags) -->
  <div class="epigraph">
    <div class="quote-text">Quote text</div>
    <div class="quote-name">Attribution</div>
  </div>

  <!-- Chapter heading (from h1-h5 markup tags) -->
  <h1>Chapter Title (source) / Chapter Title (target)</h1>

  <!-- Aligned paragraphs -->
  <div class="paragraph">
    <span style="...">Source sentence 1. Source sentence 2.</span>
    <span style="...">Target sentence 1. Target sentence 2.</span>
  </div>

  <!-- Section divider (from divider markup tag) -->
  <hr class="divider">
</body>
</html>
```

**Highlight styles:**

| Style | Visual Effect |
|-------|--------------|
| Simple (`none`) | No background colors; clean text on white background |
| Pastel Fill (`pastel_fill`) | Solid pastel backgrounds — green for source, pink for target (with border-radius) |
| Pastel Start (`pastel_start`) | Gradient backgrounds that fade from color to transparent |

**Customization:** The generated HTML uses inline styles. You can modify the CSS after download to customize colors, fonts, spacing, and layout.

**Use cases:** Reading bilingual books in a browser, printing, sharing with language learners, publishing on websites.

## TMX (Translation Memory eXchange) {#tmx}

**File extension:** `.tmx`

**Description:** An XML-based standard format for storing translation memory data. TMX files are compatible with all major CAT (Computer-Assisted Translation) tools.

**Version:** TMX 1.4

**Structure:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tmx SYSTEM "tmx14.dtd">
<tmx version="1.4">
  <header
    creationtool="lingtrain-aligner"
    creationtoolversion="1.0"
    datatype="plaintext"
    segtype="sentence"
    adminlang="en"
    srclang="en"
    o-tmf="lingtrain"
  />
  <body>
    <tu>
      <tuv xml:lang="en">
        <seg>Source sentence text.</seg>
      </tuv>
      <tuv xml:lang="ru">
        <seg>Target sentence text.</seg>
      </tuv>
    </tu>
    <!-- Additional translation units -->
  </body>
</tmx>
```

**Key elements:**

| Element | Description |
|---------|-------------|
| `<tmx>` | Root element with version attribute |
| `<header>` | Metadata about the creation tool and language configuration |
| `<tu>` | Translation Unit — one aligned sentence pair |
| `<tuv>` | Translation Unit Variant — text in a specific language |
| `<seg>` | Segment — the actual sentence text |

**Language codes:** Uses the language codes as configured in the alignment (e.g., `en`, `ru`, `de`, `zh`).

**Compatible CAT tools:** SDL Trados Studio, memoQ, Memsource (Phrase), OmegaT, Wordfast, CafeTran, Smartcat, MateCat, and others.

**Notes:**
- Markup-tagged lines (author, title, headings, etc.) are excluded from TMX output
- Multi-sentence alignment pairs are concatenated into a single segment
- The TMX file is UTF-8 encoded

## Sentence Corpora {#sentence-corpora}

**File extension:** `.txt`

**Description:** Plain text files with one aligned sentence per line. Two separate files are generated — one for the source language and one for the target language. Line `N` in the source file corresponds to line `N` in the target file.

**Format:**

Source file (`sentences_en.txt`):
```
When I was down here last year, said Sir Henry Clithering, and stopped.
His hostess, Mrs Bantry, looked at him curiously.
The Ex-Commissioner of Scotland Yard was staying with old friends.
```

Target file (`sentences_ru.txt`):
```
Когда я был здесь в прошлом году… — начал сэр Генри Клитеринг и умолк.
Хозяйка дома, миссис Бантри, посмотрела на него с любопытством.
Экс-комиссар Скотленд-Ярда гостил у старых друзей.
```

**Encoding:** UTF-8

**Line endings:** Platform-dependent (typically LF on Linux, CRLF on Windows)

**Notes:**
- Markup-tagged lines are excluded
- Each line corresponds to one alignment pair (which may contain merged sentences)
- Empty lines indicate alignment pairs where one side had no content

**Use cases:** NLP research, machine translation training data, corpus linguistics, statistical analysis.

## Paragraph Corpora {#paragraph-corpora}

**File extension:** `.txt`

**Description:** Plain text files grouped by paragraphs. Sentences within a paragraph are concatenated with spaces. Paragraphs are separated by blank lines. The paragraph structure is determined by the `par_direction` setting.

**Format:**

```
When I was down here last year, said Sir Henry Clithering, and stopped. His hostess, Mrs Bantry, looked at him curiously.

The Ex-Commissioner of Scotland Yard was staying with old friends of his, Colonel and Mrs Bantry, who lived near St Mary Mead.
```

**Notes:**
- Paragraph boundaries come from the text specified by `par_direction` (source or target)
- Markup-tagged lines are excluded
- Useful for comparing paragraph-level translation decisions

## XML {#xml}

**File extension:** `.xml`

**Description:** A structured XML representation of the aligned book, including metadata, structure, and all aligned sentence pairs.

**Structure:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<book>
  <head>
    <author from="Author Name" to="Имя Автора"/>
    <title from="Book Title" to="Название Книги"/>
  </head>
  <contents>
    <item>
      <h1 from="Chapter One" to="Глава первая"/>
    </item>
    <item>
      <from>Source sentence text.</from>
      <to>Target sentence text.</to>
    </item>
    <item>
      <from>Another source sentence.</from>
      <to>Ещё одно исходное предложение.</to>
    </item>
    <!-- Additional items -->
    <item>
      <divider/>
    </item>
  </contents>
</book>
```

**Key elements:**

| Element | Description |
|---------|-------------|
| `<book>` | Root element |
| `<head>` | Metadata (author, title, translator) |
| `<contents>` | List of content items |
| `<item>` | A single content element (sentence pair, heading, or divider) |
| `<from>`, `<to>` | Source and target text within a sentence pair |
| `<h1>`–`<h5>` | Heading elements with `from` and `to` attributes |
| `<divider>` | Section divider |

**Use cases:** Custom processing pipelines, data transformation, programmatic analysis.

## JSON {#json}

**File extension:** `.json`

**Description:** A structured JSON representation of the aligned book, containing the same data as the XML format in a JSON-native structure.

**Structure:**

```json
{
  "head": {
    "author": {
      "from": "Author Name",
      "to": "Имя Автора"
    },
    "title": {
      "from": "Book Title",
      "to": "Название Книги"
    }
  },
  "contents": [
    {
      "type": "h1",
      "from": "Chapter One",
      "to": "Глава первая"
    },
    {
      "type": "text",
      "from": "Source sentence text.",
      "to": "Target sentence text."
    },
    {
      "type": "divider"
    }
  ]
}
```

**Encoding:** UTF-8, with `ensure_ascii=False` for proper Unicode representation.

**Use cases:** Web applications, JavaScript processing, API integrations, data analysis with Python/R.

## Alignment Database (.lt) {#lt-format}

**File extension:** `.lt`

**Description:** The native Lingtrain alignment format. This is a SQLite database file containing the complete alignment state. It can be re-imported into Lingtrain to restore an alignment.

**Database tables:**

| Table | Description |
|-------|-------------|
| `splitted_from` | Source text sentences (one row per sentence) |
| `splitted_to` | Target text sentences (one row per sentence) |
| `doc_index` | The aligned document index — mapping between source and target sentence IDs |
| `batches` | Batch metadata (batch_id, shift, window) |
| `marks` | Markup tags (type, text, line index, language direction) |
| `proxy_from` | Proxy text sentences for source language (if uploaded) |
| `proxy_to` | Proxy text sentences for target language (if uploaded) |
| `embeddings_from` | Cached source embeddings (if STORE_EMBEDDINGS is enabled) |
| `embeddings_to` | Cached target embeddings (if STORE_EMBEDDINGS is enabled) |
| `metadata` | Key-value metadata (language codes, embedding model info, batch size) |

**Key columns in `doc_index`:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Row index in the alignment |
| `text_from` | TEXT | Concatenated source sentence text |
| `from_ids` | TEXT | JSON array of source line IDs (e.g., `[0, 1]`) |
| `text_to` | TEXT | Concatenated target sentence text |
| `to_ids` | TEXT | JSON array of target line IDs |
| `batch_id` | INTEGER | Which batch this pair belongs to |

**Key columns in `splitted_from` / `splitted_to`:**

| Column | Type | Description |
|--------|------|-------------|
| `id` | INTEGER | Line index (0-based) |
| `text` | TEXT | Sentence text |
| `paragraph` | INTEGER | Paragraph marker flag |
| `excluded` | INTEGER | Whether this line is excluded from alignment |

**Notes:**
- When downloading, embedding blobs may be stripped to reduce file size (configurable via `STRIP_EMBEDDINGS_ON_EXPORT`)
- The `.lt` file is a standard SQLite3 database that can be opened with any SQLite client (DB Browser for SQLite, `sqlite3` CLI, Python's `sqlite3` module)
- Re-importing a `.lt` file creates a new alignment with the same data but a new GUID

**Use cases:** Backup, migration between Lingtrain instances, offline analysis, custom tooling.

## Format Comparison {#comparison}

| Feature | HTML | TMX | Sentence | Paragraph | XML | JSON | .lt |
|---------|------|-----|----------|-----------|-----|------|-----|
| Human-readable | Yes | Yes | Yes | Yes | Yes | Yes | No (binary) |
| Preserves markup | Yes | No | No | No | Yes | Yes | Yes |
| Preserves line IDs | No | No | No | No | No | No | Yes |
| Preserves embeddings | No | No | No | No | No | No | Optional |
| Re-importable | No | No | No | No | No | No | Yes |
| CAT-tool compatible | No | Yes | No | No | No | No | No |
| Suitable for NLP | No | Partially | Yes | Yes | Yes | Yes | Yes |
| Styled output | Yes | No | No | No | No | No | No |
