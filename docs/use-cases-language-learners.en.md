# Lingtrain for Language Learners {#use-cases-language-learners}

Parallel books — texts displayed with the original and translation side by side — are one of the most effective tools for language acquisition. Lingtrain Aligner lets you create personalized bilingual reading materials from any text you choose, in any language pair from 50-200+ supported languages.

This page explains the theory behind parallel reading, how to choose texts, and how to create your first parallel book step by step.

## Why parallel reading works {#why-it-works}

### Comprehensible input theory {#comprehensible-input}

Linguist Stephen Krashen's **Comprehensible Input Hypothesis** (1982) argues that language acquisition occurs when learners are exposed to language input that is slightly above their current level — what Krashen calls "i+1" (current level plus one step). The input must be understandable in context, even if every word is not known.

Parallel books are an ideal implementation of this theory because:

- The translation provides **context** for understanding unfamiliar words and structures
- The learner encounters language in its **natural form** — real sentences, real grammar, real style — rather than simplified textbook examples
- The reading is **self-paced** — the learner can check the translation when needed and skip it when confidence is high
- **Extended exposure** to authentic text builds intuition for word order, collocations, and idiomatic usage

### Extensive reading method {#extensive-reading}

The **Extensive Reading** approach advocates reading large amounts of text at a comfortable difficulty level, prioritizing enjoyment and comprehension over study. Key principles:

- Read for pleasure, not for study
- Choose materials you genuinely want to read
- Read a lot — volume matters for acquisition
- Tolerate some unknown words without stopping to look up each one
- Focus on overall meaning, not word-by-word translation

Parallel books support extensive reading by lowering the barrier: when you encounter a passage that is too difficult, you can glance at the translation and continue reading, maintaining flow and engagement.

### Vocabulary acquisition through context {#vocabulary-acquisition}

Research consistently shows that vocabulary is acquired most effectively **in context** rather than from isolated word lists. When you encounter a word in a meaningful sentence, within a story you care about, you form stronger memory associations:

- The word is connected to a scene, character, or emotion
- You see how the word functions grammatically (noun? verb? adjective?)
- You observe which words typically surround it (collocations)
- Encountering the same word in different contexts reinforces learning

A 200-page novel exposes you to thousands of unique words in hundreds of different contexts — far more effective than memorizing the same number of words from flashcards.

## How to choose texts {#choosing-texts}

### Match your level {#match-level}

The best text is one where you understand **70-90%** without looking at the translation. If you understand less than 70%, you will spend too much time checking the translation and lose the flow. If you understand more than 90%, you are not learning much new.

**Beginner (A1-A2):** Choose children's books, simple fairy tales, or young adult fiction. Short sentences, common vocabulary, straightforward plots.

**Intermediate (B1-B2):** Choose popular fiction, mystery novels, or non-fiction on topics you know. The Agatha Christie texts commonly used in Lingtrain demonstrations are ideal at this level — clear prose, dialogue-heavy, manageable vocabulary.

**Advanced (C1-C2):** Choose literary fiction, essays, journalism, or specialized non-fiction. Complex sentence structures and rich vocabulary will challenge you without overwhelming.

### Choose texts you enjoy {#choose-enjoyment}

This is the most important criterion. If you find the text boring, you will not read enough for acquisition to occur. Choose:

- A genre you love (mystery, science fiction, romance, biography)
- An author you admire
- A topic that genuinely interests you
- A text you have already read in your native language and want to re-experience in the target language

### Practical considerations {#practical-considerations}

- **Availability of translations:** You need the text in both languages. Classics and popular fiction are widely translated; niche works may not be available.
- **Translation quality:** A good, professional translation makes a better parallel book than a machine translation. Look for published translations by recognized translators.
- **Text length:** Start with shorter works (short stories, novellas) before attempting full novels. A 50-page short story is perfect for your first parallel book.
- **Digital format:** You need the text in digital form (plain text file). E-books can often be converted; scanned books may need OCR.

## Creating your first parallel book {#step-by-step}

### Step 1: Prepare your texts {#step-1}

Prepare two plain text files — one in the source language, one in the target language. The text should be clean:

- Remove headers, footers, page numbers
- Remove table of contents and indexes
- Keep chapter titles (you can tag them with `%%%%%h1.` markup)
- Ensure consistent encoding (UTF-8 recommended)

### Step 2: Upload to Lingtrain {#step-2}

1. Go to the **Documents** tab in the Aligner
2. Upload your source text — select the correct language
3. Upload your target text — select its language
4. Review the sentence preview to verify splitting looks correct

See [Uploading texts](uploading.en.md) for detailed instructions.

### Step 3: Create and run alignment {#step-3}

1. Go to the **Alignments** tab
2. Click **"+ Create alignment"**
3. Name your alignment (e.g., "Blue Geranium EN-RU")
4. Select source and target documents
5. Keep default settings (batch size 200, window 40)
6. Click **"Align next"** to process the first batch
7. Check the visualization — if the diagonal is clean, continue aligning
8. After all batches: click **"Resolve all"** to fix conflicts automatically
9. Review in the editor for any remaining issues

See [Alignment process](alignment.en.md) for the full workflow.

### Step 4: Export as HTML book {#step-4}

1. Go to the **Create** tab
2. Configure your book:
   - **Paragraph structure** — use the source text's paragraph breaks
   - **Language order** — put the language you are learning first (left side)
   - **Highlight style** — choose your preferred visual style
3. Click **Download** and select **HTML book**

The resulting file opens in any web browser and displays your parallel book with aligned sentences side by side.

### Step 5: Read and learn {#step-5}

Open the HTML file in your browser and start reading. Tips for effective parallel reading:

- **Read the target language first.** Try to understand as much as you can from the foreign text before looking at the translation.
- **Do not look up every word.** If you can understand the general meaning, keep reading. Look up words only when not understanding them blocks comprehension of the whole passage.
- **Read in sessions.** 20-30 minutes per day is more effective than occasional long sessions.
- **Re-read passages.** When you finish a chapter, go back and re-read the target language only. You will be surprised how much more you understand the second time.
- **Track your progress.** Note when passages become easier to read without checking the translation — this is acquisition happening.

## Beyond basic reading {#beyond-reading}

### Vocabulary extraction {#vocab-extraction}

Export your alignment as a **sentence corpus** (two parallel text files). You can then use these files with flashcard tools (like Anki) to create sentence-based flashcards where each card shows a sentence in the target language and the translation on the back.

### TMX for language tools {#tmx-language-tools}

Export as **TMX** format and import into translation memory tools or concordance search tools. This lets you search for how specific words or phrases are used across the entire book.

### Multiple texts for the same language {#multiple-texts}

Create parallel books from multiple works to build a personal library:

- Different genres expose you to different vocabulary domains
- Different authors expose you to different writing styles
- Reading more text in the target language is always beneficial

## Supported languages for learning {#supported-languages}

Lingtrain supports language learning for virtually any language pair. Common learning scenarios:

| Learning | From | Notes |
|----------|------|-------|
| English | Russian | Well-supported, many texts available |
| German | English | Strong alignment quality |
| French | English | Many classic texts available |
| Spanish | English | Large selection of literature |
| Japanese | English | Works well; use Chinese splitting mode for similar CJK |
| Chinese | English | Supported with Chinese-specific splitting |
| Korean | English | Dedicated splitting support |
| Turkish | Russian | Good results with LaBSE model |
| Bashkir | Russian | Use interlinear translation for best results |

For less common language pairs, see [Working with Low-Resource Languages](low-resource-languages.en.md).

## Further reading {#further-reading}

- [Overview](index.en.md) — getting started with Lingtrain
- [Uploading texts](uploading.en.md) — preparing and uploading your texts
- [Alignment process](alignment.en.md) — the full alignment workflow
- [Export](export.en.md) — export formats and customization options
- [What is a Parallel Corpus?](what-is-parallel-corpus.en.md) — background on parallel texts
