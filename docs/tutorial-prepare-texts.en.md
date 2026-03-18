# Tutorial: Preparing Texts for Alignment {#prepare-texts}

Good alignment starts with well-prepared texts. This tutorial explains how to find source materials, clean and format them, add markup tags, and avoid common pitfalls. Investing time in text preparation will dramatically improve alignment quality and reduce the number of conflicts you need to resolve.

## Finding Source Texts {#finding-texts}

Before you can align, you need a text and its translation. Here are some reliable sources:

### English Texts {#english-sources}

- **Project Gutenberg** (gutenberg.org) — over 70,000 free public domain ebooks. Excellent source for classic literature. Download the "Plain Text UTF-8" version of any book.
- **Standard Ebooks** (standardebooks.org) — curated, well-formatted public domain books. Higher quality than raw Gutenberg texts, but fewer titles available.
- **Wikisource** (en.wikisource.org) — collaboratively edited source texts, including many historical documents and literary works.

### Russian Texts {#russian-sources}

- **Lib.ru** (lib.ru) — one of the oldest Russian digital libraries with an extensive collection of literary works. Texts are typically in KOI-8 or Windows-1251 encoding, so you may need to convert them to UTF-8.
- **RoyalLib / Litmir** — large collections of Russian fiction and translations.
- **Flibusta** (flibusta.is) — community-maintained library with a vast catalogue.
- **Wikisource** (ru.wikisource.org) — Russian-language original texts and some translations.

### Other Languages {#other-sources}

- **Wikisource** has editions in dozens of languages.
- **Project Gutenberg** has a limited selection in German, French, and other languages.
- Many countries maintain national digital library projects — search for "[language] digital library" or "[language] free ebooks".

### Important Note on Copyright {#copyright}

Only use texts that are in the public domain or for which you have the right to use. Copyright laws vary by country. In most jurisdictions, works by authors who died more than 70 years ago are in the public domain.

## Text Format Requirements {#format-requirements}

Lingtrain Aligner works with **plain text files** (`.txt`). It does not process PDF, DOCX, EPUB, or other rich formats directly. If your source is in one of these formats, you will need to extract the text first.

### Converting from Other Formats {#converting}

- **PDF** — use a tool like `pdftotext` (from poppler-utils) or copy-paste the text. Be aware that PDF extraction often introduces artifacts: broken lines, missing spaces, garbled characters. Review the result carefully.
- **DOCX** — open in a word processor and save as plain text (`.txt`). Or use `pandoc` on the command line: `pandoc input.docx -t plain -o output.txt`.
- **EPUB** — use `calibre` to convert to plain text, or extract the HTML files from the EPUB archive and convert them.
- **HTML** — copy the text content from a browser, or use tools like `html2text`.

### Encoding {#encoding}

All text files must be in **UTF-8** encoding. This is the default encoding for modern systems, but older texts (especially Russian) may be in:

- **Windows-1251** (CP1251) — common for older Russian texts.
- **KOI8-R** — another legacy Russian encoding.
- **Latin-1** (ISO-8859-1) — common for older Western European texts.

If your text looks garbled after opening, the encoding is likely wrong. Use a text editor like Notepad++, VS Code, or Sublime Text that can detect and convert encodings:

1. Open the file.
2. Check the detected encoding (usually shown in the status bar).
3. If it is not UTF-8, convert it: in Notepad++, go to Encoding > Convert to UTF-8.
4. Save the file.

## Cleaning the Text {#cleaning}

Raw texts from digital libraries often contain extraneous content that will interfere with alignment. Remove the following before uploading:

### Remove Headers and Footers {#headers-footers}

Project Gutenberg texts, for example, include a lengthy license header and footer. Delete everything before the actual text begins and after it ends:

```
*** START OF THE PROJECT GUTENBERG EBOOK THE BLUE GERANIUM ***

(remove everything above this line)

The actual text starts here...

(remove everything below this line)

*** END OF THE PROJECT GUTENBERG EBOOK THE BLUE GERANIUM ***
```

### Remove Page Numbers {#page-numbers}

Texts extracted from PDFs often have page numbers scattered throughout. Remove them. They typically appear as standalone numbers on their own lines:

```
some text on the page.

47

The next page starts here.
```

Remove the `47` and any similar standalone numbers.

### Remove Running Headers {#running-headers}

PDF extractions may include repeated running headers or footers (book title, author name, chapter title) on every page. Remove these as well.

### Remove Footnotes and Endnotes {#footnotes}

Unless both texts have the same footnotes in the same order, remove them. Footnotes in one language without counterparts in the other will create conflicts during alignment.

If footnotes are important, move them to the end of the chapter where they appear, clearly marked. But for best results, simply remove them.

### Remove Table of Contents {#toc}

Delete the table of contents unless the other text has one too. Same logic as footnotes — unmatched content creates conflicts.

### Remove Translator's Prefaces {#translator-preface}

If only the translation has a translator's preface (and the original does not), remove it. If both texts have prefaces and you want to align them, keep them.

## Paragraph Formatting {#paragraphs}

Proper paragraph formatting is critical for good sentence splitting. The splitter uses **blank lines** to detect paragraph boundaries.

### Correct Formatting {#correct-format}

Each paragraph should be separated by exactly one blank line:

```
Mrs. Bantry looked at him curiously. The Ex-Commissioner
of Scotland Yard was staying with old friends.

"When I was down here last year," said Sir Henry
Clithering, and stopped.

His hostess looked at him with interest.
```

### Problematic Formatting {#bad-format}

**Hard line breaks within paragraphs** — if lines are broken at a fixed width (e.g., 72 characters), the splitter may incorrectly treat each line as a separate paragraph:

```
Mrs. Bantry looked at him curiously. The
Ex-Commissioner of Scotland Yard was staying
with old friends.
```

Fix this by joining lines within each paragraph into a single line (or at least ensure there are no blank lines inside a paragraph). Many text editors have a "join lines" feature.

**No blank lines between paragraphs** — if paragraphs are not separated by blank lines, the splitter cannot detect boundaries:

```
Mrs. Bantry looked at him curiously.
"When I was down here last year," said Sir Henry.
His hostess looked at him with interest.
```

Add blank lines between paragraphs.

**Multiple blank lines** — extra blank lines between paragraphs are fine; the splitter handles them. But they may produce unnecessary empty entries.

## Adding Markup Tags {#markup-tags}

Lingtrain uses special markup tags to identify metadata in your texts. Tags are placed **at the end of a line** using the `%%%%%` prefix.

### Essential Tags {#essential-tags}

At minimum, add these tags to both texts:

**Author tag** — the author's name:

```
Agatha Christie%%%%%author.
```

**Title tag** — the book or story title:

```
The Blue Geranium%%%%%title.
```

These produce the title page in the exported HTML book.

### Structural Tags {#structural-tags}

For longer texts, add heading tags to mark chapter and section boundaries:

```
Chapter One%%%%%h1.
```

Headings help the alignment algorithm by providing structural anchors. They are excluded from the sentence matching process and appear as formatted headings in the exported book.

Available heading levels: `%%%%%h1.` through `%%%%%h5.`

### Other Tags {#other-tags}

| Tag | Purpose | Example |
|-----|---------|---------|
| `%%%%%qtext.` | Quotation text | `To be or not to be%%%%%qtext.` |
| `%%%%%qname.` | Quote attribution | `William Shakespeare%%%%%qname.` |
| `%%%%%translator.` | Translator credit | `Translated by John Doe%%%%%translator.` |
| `%%%%%divider.` | Section divider | `* * *%%%%%divider.` |
| `%%%%%image.` | Image reference | `illustration.jpg%%%%%image.` |

### Tag Matching Rules {#tag-matching}

Both texts **must have matching markup**. If the English text has `%%%%%author.` and `%%%%%title.` tags, the Russian text must have them too. The same applies to heading tags — if you mark five chapters with `%%%%%h1.` in English, mark the same five chapters in Russian.

Mismatched tag counts produce warnings in the **Marks** panel on the Documents page and can cause alignment issues, since tags define structural boundaries that the algorithm relies on.

### Complete Example {#complete-example}

**English file (`story_en.txt`):**

```
Agatha Christie%%%%%author.
The Blue Geranium%%%%%title.


'When I was down here last year -' said Sir Henry Clithering, and stopped.

His hostess, Mrs Bantry, looked at him curiously. The Ex-Commissioner of Scotland Yard was staying with old friends of his, Colonel and Mrs Bantry, who lived near St Mary Mead.

Mrs Bantry, a large, comfortable woman, with a talent for gardening, was usually of placid temperament. But lately she had been vexed by a series of small domestic upheavals.
```

**Russian file (`story_ru.txt`):**

```
Кристи Агата%%%%%author.
Синяя герань%%%%%title.


— Когда я был здесь в прошлом году… — начал сэр Генри Клитеринг и умолк.

Хозяйка дома, миссис Бантри, посмотрела на него с любопытством. Экс-комиссар Скотленд-Ярда, их старый приятель, гостил у них с мужем в их имении, расположенном неподалеку от Сент-Мэри-Мид.

Миссис Бантри, крупная добродушная женщина, увлекающаяся садоводством, обычно отличалась спокойным нравом. Но в последнее время её выводила из себя целая серия мелких домашних неурядиц.
```

## Common Mistakes and How to Fix Them {#common-mistakes}

### Mistake 1: Different Editions {#different-editions}

Using texts from different editions or abridged versions will produce poor alignment. The source and translation should correspond as closely as possible. Verify by spot-checking a few passages — do they match in content?

### Mistake 2: Missing or Extra Content {#missing-content}

One text has a foreword and the other does not. Or one version includes illustrations or footnotes that the other lacks. Remove all unmatched content from both sides.

### Mistake 3: Wrong Encoding {#wrong-encoding}

Garbled characters (`Ð¿ÑÐ¸Ð²ÐµÑ` instead of `привет`) indicate encoding problems. Convert both files to UTF-8.

### Mistake 4: Rich Formatting Artifacts {#formatting-artifacts}

Texts copied from web pages or converted from PDF may contain invisible characters, non-breaking spaces, or soft hyphens. Enable the **"Clean text"** checkbox during upload to remove common artifacts, or clean the text manually in a plain text editor.

### Mistake 5: Poetry or Verse {#poetry}

Poetry, song lyrics, and verse are particularly challenging because each line may be a separate "sentence" but is not semantically self-contained. The alignment algorithm may struggle with very short lines. For poetry, consider treating each stanza as a single paragraph rather than splitting line by line.

### Mistake 6: Mixed Languages in Source {#mixed-languages}

If your source text contains significant passages in another language (e.g., French phrases in an English novel), the embedding model will still process them, but alignment quality may be affected. This is usually fine for occasional foreign words but may cause issues for extended foreign-language passages.

## Pre-Upload Checklist {#checklist}

Before uploading your texts, verify:

1. Both files are in `.txt` format with UTF-8 encoding.
2. Headers, footers, page numbers, and running headers are removed.
3. Paragraphs are separated by blank lines.
4. `%%%%%author.` and `%%%%%title.` tags are present in both files.
5. Chapter headings are tagged with `%%%%%h1.` in both files (if applicable).
6. The number and order of markup tags match between both files.
7. Unmatched content (footnotes, prefaces, appendices) is removed from both sides.
8. Hard line breaks within paragraphs are joined.
9. The content of both texts corresponds — they are the same work, same edition.

Following this checklist will give you the best possible starting point for alignment.

## Next Steps {#next-steps}

With your texts prepared, proceed to [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) to upload them and create your first alignment project.
