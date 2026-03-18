# Lingtrain for Publishers and Educators {#use-cases-publishers}

Lingtrain Aligner enables publishers to create professional bilingual editions and educators to prepare high-quality bilingual teaching materials. The HTML book export provides customizable formatting, paragraph structure options, and multiple highlight styles suited for publication and classroom use.

## For publishers: bilingual editions {#publishers}

### Why bilingual books sell {#why-bilingual}

Bilingual editions serve multiple markets:

- **Language learners** at all levels who want to read in the target language with native-language support
- **Heritage speakers** who want to reconnect with their family's language through literature
- **Comparative literature enthusiasts** who enjoy seeing how translation transforms text
- **Academic readers** who need to reference both original and translation
- **Expatriate communities** who maintain connections to their home culture while living abroad

The market for bilingual books is growing as global mobility increases and language learning becomes more popular. Lingtrain provides the alignment infrastructure that makes bilingual publishing practical at scale.

### Creating a bilingual edition {#creating-bilingual}

The workflow for publishers:

1. **Obtain rights** for both the original text and the translation (or use public domain texts)
2. **Prepare clean text files** in both languages — remove page numbers, running headers, and publisher metadata
3. **Tag structural elements** — use Lingtrain markup for chapter headings (`%%%%%h1.`), author names (`%%%%%author.`), and other structural markers
4. **Upload and align** in Lingtrain following the standard workflow
5. **Review alignment quality** — for publication-quality work, every sentence pair should be verified
6. **Configure export settings** — choose paragraph structure, language order, and highlight style
7. **Export and integrate** — download the HTML book and adapt it for your publishing workflow

### Paragraph structure options {#paragraph-structure}

The paragraph structure setting determines how paragraph breaks appear in the exported book:

- **From source** — paragraph breaks follow the original text's structure. Best when the original's paragraph rhythm is important.
- **From target** — paragraph breaks follow the translation's structure. Useful when the translation restructured paragraphs for better flow in the target language.

Choose the option that produces the most natural reading experience for your primary audience.

### Highlight styles {#highlight-styles}

Lingtrain offers several highlight styles for distinguishing between the two languages in the HTML book:

**Simple** — minimal styling with clean typography. Source and target languages are differentiated by subtle visual cues (indentation, font weight). Best for readers who prefer a clean, book-like appearance.

**Pastel Fill** — sentences in each language have a light background color (e.g., pale blue for source, pale peach for target). Makes it easy to identify which language each sentence belongs to, even when scanning quickly.

**Pastel Start** — only the beginning of each sentence is highlighted with a colored marker. A compromise between Simple and Pastel Fill — provides a visual anchor without coloring entire sentences.

Choose the style that matches your publication's visual identity and your readers' preferences. For serious literary publications, Simple is usually best. For educational materials, Pastel Fill provides the clearest visual distinction.

### Language order {#language-order}

Configure which language appears first (left/top) in the parallel display:

- For **language learners**, put the language being learned first — the reader encounters it first and can check the translation below
- For **reference editions**, put the original language first — this is the traditional convention for scholarly bilingual editions
- For **cultural outreach** editions targeting diaspora communities, put the heritage language first

## For educators: teaching materials {#educators}

### Bilingual reading materials {#reading-materials}

Educators can use Lingtrain to create customized bilingual reading materials:

- **Graded readers** — select texts appropriate for each student level and create parallel versions
- **Literature anthologies** — compile short stories or excerpts in parallel format for literature classes
- **Content-area materials** — create bilingual versions of science, history, or social studies texts for bilingual education programs
- **Cultural studies** — pair original texts with translations for comparative cultural analysis

### Classroom applications {#classroom}

**Translation comparison exercises:** Align the same source text with multiple translations (by different translators). Students compare translation choices and discuss strategies.

**Vocabulary in context:** Create parallel texts focused on specific vocabulary domains. Students study new words in authentic contexts rather than isolated lists.

**Grammar analysis:** Aligned sentences let students observe how grammatical structures are handled differently across languages. A sentence using the subjunctive in French alongside its English equivalent illustrates the concept through real examples.

**Reading comprehension:** Students read the target language version first, then check their understanding against the source. This is more engaging than traditional reading comprehension exercises because they are working with real literature.

### Preparing educational materials {#preparing-materials}

Tips for educators:

1. **Start with short texts.** A single short story or article is enough for a lesson or homework assignment. Full novels are for extended reading programs.

2. **Choose texts with pedagogical value.** Consider not just language level but also thematic content — texts that generate discussion are more valuable in the classroom.

3. **Tag key vocabulary.** While Lingtrain does not have built-in vocabulary annotation, you can post-process the HTML output to highlight target vocabulary.

4. **Create answer keys.** Export the alignment as a sentence corpus and use the parallel text files to create vocabulary lists, comprehension questions, and answer keys.

5. **Consider accessibility.** The HTML format is accessible in any web browser, on any device. Students do not need special software to read the parallel book.

### Assessment and testing {#assessment}

Aligned texts can support assessment:

- Create cloze tests by removing words from the target language side and having students fill in blanks with reference to the source
- Use aligned passages for translation exercises — students translate a passage and compare with the aligned professional translation
- Build reading comprehension tests where students must identify the corresponding passage in the other language

## Customizing HTML output {#customizing-html}

### Post-processing options {#post-processing}

The HTML book exported by Lingtrain can be further customized:

- **CSS styling** — modify colors, fonts, spacing, and layout to match your brand or publication style
- **Print formatting** — add CSS print styles for creating print-ready bilingual books
- **Interactive features** — add JavaScript for click-to-reveal translations, vocabulary highlighting, or audio playback
- **E-book conversion** — convert HTML to EPUB or MOBI for e-reader distribution

### Bulk production {#bulk-production}

For publishers producing multiple bilingual titles:

- Establish a standard Lingtrain workflow with consistent parameters
- Create CSS templates for consistent visual identity across titles
- Use the alignment database (`.lt`) export for backup and re-processing
- Consider the Python library for batch processing of multiple texts

## Further reading {#further-reading}

- [Overview](index.en.md) — getting started with Lingtrain
- [Export](export.en.md) — export formats and options
- [Uploading texts](uploading.en.md) — preparing texts with markup tags
- [Lingtrain for Language Learners](use-cases-language-learners.en.md) — the learner's perspective on parallel books
- [Lingtrain for Professional Translators](use-cases-translators.en.md) — building translation memories
