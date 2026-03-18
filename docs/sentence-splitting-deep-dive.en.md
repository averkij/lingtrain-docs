# Sentence Splitting: A Deep Dive {#sentence-splitting}

Sentence splitting (also called sentence segmentation or sentence boundary detection) is the first step in any text alignment pipeline. Before sentences can be aligned across languages, they must first be identified within each text. This seemingly simple task conceals surprising complexity, especially when working across languages with different writing systems, punctuation conventions, and syntactic structures.

## Why sentence splitting matters for alignment {#why-it-matters}

The quality of sentence splitting directly determines the quality of downstream alignment. If a sentence boundary is missed, two sentences will be merged into one, producing a longer segment that may not have a clean match in the other language. If a boundary is incorrectly inserted (for example, after an abbreviation period), a sentence will be split into fragments that individually make no sense, leading to spurious alignment matches.

In alignment, these errors compound. A missed boundary in the source text can cascade through the alignment, pushing subsequent sentence pairs out of sync. The aligner may partially recover using its windowed search and conflict resolution mechanisms, but prevention is always better than cure.

## Rule-based approaches {#rule-based}

The oldest and still most common approach to sentence splitting is rule-based: define a set of patterns that identify sentence boundaries and a set of exceptions that override them.

### Basic rules {#basic-rules}

At the simplest level, sentence boundaries occur at periods (`.`), exclamation marks (`!`), and question marks (`?`), followed by whitespace and an uppercase letter. This trivial rule handles most cases in languages that use Latin, Cyrillic, or Greek scripts.

However, even for English, this baseline rule fails on many common constructions:

- **Abbreviations**: "Dr. Smith went to Washington." — the period after "Dr" is not a sentence boundary
- **Initials**: "J. K. Rowling wrote..." — multiple periods that are not boundaries
- **Ellipsis**: "He waited... and waited." — three dots that are not three sentence endings
- **Decimal numbers**: "The price is 3.50 dollars." — a period inside a number
- **URLs and emails**: "Visit example.com for details." — periods in domains
- **Quotations**: "'Stop!' she said." — the exclamation mark is inside a quotation, and the sentence continues

### Abbreviation lists {#abbreviation-lists}

Most rule-based splitters maintain curated lists of known abbreviations for each language. When a period follows a known abbreviation, it is not treated as a sentence boundary. These lists must be carefully maintained and differ significantly across languages:

- **English**: Dr., Mr., Mrs., Ms., Prof., Inc., Corp., etc., vs., e.g., i.e., ...
- **German**: Dr., Prof., Str., Nr., Abs., z.B., d.h., u.a., ...
- **French**: M., Mme, Mlle, Dr., av., bd., ...
- **Russian**: г., гг., т.д., т.п., т.е., др., пр., ...

The abbreviation problem is inherently ambiguous: "He went to St. Petersburg." could end at "St." (if St. is an abbreviation for "Saint" and the sentence continues) or not. Context-dependent disambiguation is one area where ML approaches have an advantage.

### Quotation and bracket handling {#quotations}

Sentences containing quotations pose special challenges. Consider:

> She said, "This is amazing! I can't believe it!" Then she left.

A naive splitter would break this at each exclamation mark, producing three fragments. Correct handling requires tracking quotation marks (and their nesting level) to recognize that the exclamation marks are within a quoted passage.

Different languages use different quotation conventions:
- English: "double" and 'single'
- French: « guillemets »
- German: „Anführungszeichen" or »Guillemets«
- Russian: «ёлочки» and „лапки"
- Japanese: 「brackets」 and 『double brackets』

A robust splitter must recognize each language's quotation marks and track their pairing correctly.

## ML-based approaches {#ml-based}

Machine learning approaches treat sentence boundary detection as a classification problem: for each potential boundary position (typically after a period, exclamation mark, or question mark), a classifier decides whether it is a true sentence boundary.

### Feature-based classifiers {#feature-based}

Early ML approaches used hand-engineered features with classifiers like maximum entropy (logistic regression) or decision trees. Widely used systems include:

- **Punkt** (Kiss and Strunk, 2006) — an unsupervised approach that automatically learns abbreviations and sentence boundaries from unlabeled text, used in NLTK
- **OpenNLP Sentence Detector** — a maximum entropy classifier trained on annotated data

Features typically include:
- The word before the potential boundary and its case
- The word after the potential boundary and its case
- Whether the potential boundary word appears in an abbreviation list
- The length of the word before the boundary
- Whether the word contains internal periods

### Neural approaches {#neural-approaches}

Modern sentence segmentation increasingly uses neural networks. Models like:

- **Spacy's sentencizer** — uses a dependency parser to identify sentence roots, then derives boundaries
- **SaT (Segment any Text)** — a transformer-based universal sentence segmenter
- **WtP (Where's the Point)** — a pre-trained model specifically for sentence boundary detection

Neural approaches handle ambiguity better because they consider broader context, but they require more computational resources and may introduce latency in processing.

## Language-specific challenges {#language-specific}

### CJK languages: Chinese, Japanese, Korean {#cjk}

Chinese, Japanese, and Korean present unique challenges because they do not use spaces between words (Chinese and Japanese) or use spaces differently (Korean).

**Chinese** uses its own sentence-ending punctuation:
- 。 (full stop / 句号)
- ！ (exclamation mark / 感叹号)
- ？ (question mark / 问号)
- ；(semicolon, sometimes sentence-ending / 分号)

Splitting Chinese text on these characters is relatively straightforward, but complications arise with:
- Mixed Chinese-English text (common in technical writing)
- Classical Chinese, which may not use modern punctuation
- Quotation marks: 「」and 『』 in traditional usage, "" in simplified

**Japanese** combines three writing systems (kanji, hiragana, katakana) and uses both Japanese and Western punctuation:
- 。 (full stop / 句点)
- ！ and ？ (increasingly used in modern informal text)
- The grammatical particle か (ka) at the end of a sentence indicates a question, even without a question mark

Japanese also poses word segmentation challenges upstream of sentence splitting, since there are no spaces between words. Tools like MeCab, Juman++, and SudachiPy perform morphological analysis that implicitly handles sentence boundaries.

**Korean** uses spaces between words (eojeols) but has its own punctuation conventions and agglutinative morphology that can affect boundary detection.

### Arabic and Hebrew {#arabic-hebrew}

Arabic and Hebrew are written right-to-left and present their own challenges:
- Arabic has a complex system of diacritical marks that are often omitted in modern text
- Sentence boundaries in Arabic are sometimes marked by a comma or semicolon rather than a full stop, particularly in classical and formal texts
- Hebrew uses the sof pasuq (׃) in biblical texts and standard punctuation in modern Hebrew

### Languages without standardized punctuation {#no-punctuation}

Some languages, particularly those that have only recently been written or are primarily oral, may not have standardized punctuation conventions. For these languages, sentence splitting may rely on:
- Prosodic cues (if working from transcribed speech)
- Discourse particles that signal sentence boundaries
- Fixed-length chunking as a fallback

## The razdel library for Russian {#razdel}

Lingtrain uses the `razdel` library for segmenting Russian, Ukrainian, and Belarusian texts. Razdel is a rule-based segmenter developed by Natasha project contributors, specifically designed for Cyrillic text processing.

Razdel handles several Russian-specific challenges:

- **Abbreviations**: Russian uses a rich set of abbreviations (т.д., т.п., т.е., пр., др., г., гг., ст., рис., табл., and many more) that must not be treated as sentence boundaries
- **Initials**: Russian names commonly use initials — "А. С. Пушкин" has two periods that are not boundaries
- **Direct speech**: Russian conventions for marking direct speech differ from English (using em-dashes and specific quotation marks)
- **Mixed scripts**: Modern Russian texts often contain Latin-script words, URLs, and abbreviations that interact with boundary detection

Razdel operates without trained models, making it fast, deterministic, and predictable — qualities valued in a pipeline where consistency matters.

## How Lingtrain handles edge cases {#lingtrain-approach}

Lingtrain Aligner incorporates several strategies to handle sentence splitting edge cases:

### Language detection and splitter selection {#splitter-selection}

When a user uploads text and selects the language, Lingtrain routes the text to the appropriate splitter:

- **Russian, Ukrainian, Belarusian** — razdel
- **Chinese** — regex-based splitting on Chinese sentence-ending punctuation (。！？), with special handling for mixed Chinese-English content
- **Japanese** — pattern matching on Japanese punctuation with awareness of quotation brackets
- **Other languages** — a general-purpose splitter that handles Latin-script languages with standard punctuation

### Markup tag preservation {#markup-tags}

Lingtrain supports a markup system where lines beginning with `%%%%%` (e.g., `%%%%%h1.`, `%%%%%author.`) are treated as metadata, not content. These tagged lines are extracted before splitting and reattached during export. This prevents metadata lines from interfering with sentence boundary detection.

### Paragraph boundary tracking {#paragraph-boundaries}

Paragraph breaks in the original text are preserved as markers on the last sentence of each paragraph. This information survives the splitting process and is used during export to reconstruct paragraph structure in the aligned output.

### Preview and manual correction {#preview}

After splitting, Lingtrain displays a preview of the detected sentences, allowing users to verify that splitting was performed correctly before proceeding to alignment. If splitting errors are found, users can adjust the input text (for example, adding or removing line breaks) and re-upload.

## Common splitting problems and solutions {#common-problems}

| Problem | Example | Solution |
|---------|---------|----------|
| Abbreviation treated as boundary | "Dr. Smith" split into "Dr." and "Smith" | Add to abbreviation list; use ML disambiguation |
| Ellipsis splits sentence | "Wait... yes!" becomes three fragments | Treat "..." as a single token, not three periods |
| Dialogue punctuation | "'Go!' she said." split at "!" | Track quotation nesting |
| Missing punctuation | Run-on sentences without periods | May require prosodic or syntactic analysis |
| Mixed-language text | "See section 3.2 for details" split at "3." | Recognize numeric patterns |
| Enumeration periods | "1. First item" split at "1." | Detect list/enumeration patterns |

## Impact on alignment quality {#impact}

In practice, sentence splitting errors are one of the most common causes of alignment problems. When users report alignment issues, the first diagnostic step is to examine the sentence splitting output:

- Are sentences merged that should be separate? This produces overly long segments that may not match any single segment in the other language.
- Are sentences split that should be together? This produces fragments that may match incorrectly or produce very low similarity scores.

The relationship between splitting and alignment is a key reason why Lingtrain provides a sentence preview step — catching splitting errors before alignment is far more efficient than trying to fix them after.

## Future directions {#future}

Sentence splitting continues to evolve:

- **Universal segmenters** trained on diverse multilingual data are reducing the need for language-specific rules
- **Context-aware models** that consider document-level structure can better handle ambiguous cases
- **Joint segmentation and alignment** — performing splitting and alignment simultaneously, allowing the alignment signal to inform boundary decisions
- **User-adaptive splitting** — learning from user corrections to improve splitting for specific text types or domains

For now, the combination of language-specific rule-based splitters (for well-understood languages like Russian) and robust general-purpose splitters (for the long tail of other languages) provides the best balance of accuracy and reliability for alignment workflows.
