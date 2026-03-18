# Appendix: Where to Find Texts for Alignment {#text-sources}

Finding suitable parallel texts is the first step in any alignment project. This appendix provides a curated list of sources for obtaining texts in multiple languages, organized by type, with notes on quality, licensing, and practical considerations.

## Public domain text archives {#public-domain}

### Project Gutenberg {#gutenberg}

**URL**: [gutenberg.org](https://www.gutenberg.org)

The largest and oldest digital library of public domain books. Over 70,000 free ebooks, primarily in English but with growing collections in other languages.

**Languages**: English (dominant), French, German, Portuguese, Finnish, Dutch, Chinese, and many others.

**Strengths**:
- Reliable, well-proofread texts
- Plain text format available (ideal for alignment)
- Clear public domain status
- Multiple translations of classic works often available

**Tips for alignment**:
- Search for the same work in different languages (e.g., search for "Les Misérables" to find both the French original and English translations)
- Check the "Also available in" links on individual book pages
- Project Gutenberg Australia and Project Gutenberg Canada have additional titles not in the US collection (different copyright terms)

### Lib.ru (Библиотека Максима Мошкова) {#libru}

**URL**: [lib.ru](http://lib.ru)

The oldest and largest Russian-language online library. Contains a vast collection of Russian literature, translations, and original works.

**Languages**: Primarily Russian, with some texts in other languages.

**Strengths**:
- Enormous collection of Russian literature (classical and modern)
- Translations of foreign literature into Russian
- Plain text format
- Well-established and widely known in the Russian-speaking community

**Tips for alignment**:
- Pair Russian translations from Lib.ru with English originals from Project Gutenberg
- Check translator names to find specific translation editions
- Some texts may have OCR artifacts — verify quality before alignment

### Wikisource {#wikisource}

**URL**: [wikisource.org](https://wikisource.org)

A Wikimedia project providing free source texts in many languages. Texts are proofread collaboratively by the community.

**Languages**: 70+ languages with dedicated Wikisource editions.

**Strengths**:
- Collaborative proofreading ensures reasonable quality
- Structured by language with clear categorization
- Many texts available in multiple languages
- Creative Commons licensing

**Tips for alignment**:
- Use the interlanguage links to find the same work in different languages
- Quality varies — proofread texts (marked with quality indicators) are more reliable
- Good source for governmental, legal, and historical documents

### Cervantes Virtual Library {#cervantes}

**URL**: [cervantesvirtual.com](https://www.cervantesvirtual.com)

A digital library focused on Spanish and Latin American literature.

**Languages**: Primarily Spanish, with some bilingual editions.

**Strengths**: Authoritative texts for Spanish literature, including rare editions and manuscripts.

### Gallica (BnF) {#gallica}

**URL**: [gallica.bnf.fr](https://gallica.bnf.fr)

The digital library of the Bibliothèque nationale de France.

**Languages**: Primarily French, but includes multilingual historical documents.

**Strengths**: High-quality scans and OCR of French literary and historical texts.

### Aozora Bunko {#aozora}

**URL**: [aozora.gr.jp](https://www.aozora.gr.jp)

A Japanese digital library of public domain and freely available Japanese literature.

**Languages**: Japanese.

**Strengths**: Carefully proofread texts of classic Japanese literature, available in plain text and XHTML.

## Parallel corpus collections {#parallel-corpora}

### OPUS {#opus}

**URL**: [opus.nlpl.eu](https://opus.nlpl.eu)

The largest publicly available collection of parallel corpora, aggregating data from many sources.

**Languages**: 400+ languages in various pair combinations.

**Available corpora include**:
- OpenSubtitles (movie subtitles)
- Europarl (European Parliament proceedings)
- UN Parallel Corpus
- WikiMatrix (parallel sentences from Wikipedia)
- ParaCrawl (web-crawled parallel data)
- JW300 (religious texts in 300+ languages)
- TED Talks (transcripts in many languages)
- GNOME/KDE/Ubuntu (software localization)

**Strengths**:
- Massive scale and language coverage
- Standardized format
- Free to download
- Pre-aligned at the sentence level (though quality varies)

**Tips**:
- Quality varies significantly between corpora — OpenSubtitles is noisy, Europarl is clean
- Some corpora are already aligned but may benefit from re-alignment with better models

### EuroParl {#europarl}

**URL**: [statmt.org/europarl](https://www.statmt.org/europarl/)

Proceedings of the European Parliament, aligned at the sentence level.

**Languages**: 21 official EU languages.

**Strengths**:
- Very clean, formal text
- Professional translations
- Large volume (approximately 2 million sentences per language pair)
- Standard benchmark in MT research

**Considerations**:
- Domain is limited to parliamentary proceedings
- Language is formal and political — not representative of general text

### UN Parallel Corpus {#un-corpus}

**URL**: Available through various NLP resource sites

**Languages**: The six official UN languages: Arabic, Chinese, English, French, Russian, Spanish.

**Strengths**:
- Very high-quality professional translations
- Large volume (approximately 11 million sentence pairs for some language combinations)
- Diverse topics (though centered on international affairs)

### CCAligned {#ccaligned}

**URL**: [statmt.org](https://www.statmt.org)

Web-crawled parallel text from Common Crawl snapshots.

**Languages**: 137 languages paired with English.

**Strengths**: Enormous scale — over 392 million sentence pairs total. Covers many low-resource languages.

**Caution**: Web-crawled data is inherently noisy. Quality filtering is essential before using as training data.

## Literary text sources {#literary}

### Standard Ebooks {#standard-ebooks}

**URL**: [standardebooks.org](https://standardebooks.org)

Carefully formatted and proofread public domain ebooks.

**Languages**: Primarily English.

**Strengths**: Exceptionally high quality — every text is manually proofread and formatted. Better source than raw Project Gutenberg for English texts.

### ManyBooks {#manybooks}

**URL**: [manybooks.net](https://manybooks.net)

Free ebooks in multiple formats, sourced primarily from Project Gutenberg and other public domain collections.

**Languages**: Multiple, though English dominates.

### Feedbooks Public Domain {#feedbooks}

**URL**: [feedbooks.com/publicdomain](https://www.feedbooks.com/publicdomain)

Public domain books in well-formatted EPUB.

**Languages**: English, French, German, Spanish, Italian.

## Specialized sources by language {#by-language}

### Russian {#russian-sources}

- **Lib.ru** (lib.ru) — Largest Russian online library
- **Flibusta** — Large Russian ebook library (access may be restricted)
- **Russian Virtual Library** (rvb.ru) — Academic editions of Russian classics
- **Fundamental Digital Library** (feb-web.ru) — Scholarly editions and reference works

### Chinese {#chinese-sources}

- **Chinese Text Project** (ctext.org) — Classical Chinese texts with English translations
- **Project Gutenberg Chinese** — Chinese-language section of Project Gutenberg
- **WikiSource Chinese** (zh.wikisource.org) — Chinese texts on Wikisource

### Japanese {#japanese-sources}

- **Aozora Bunko** (aozora.gr.jp) — Public domain Japanese literature
- **National Diet Library Digital Collections** (dl.ndl.go.jp) — Japan's national library digital archive

### German {#german-sources}

- **Projekt Gutenberg-DE** (projekt-gutenberg.org) — German-language Project Gutenberg
- **Zeno.org** — German full-text library
- **DTA (Deutsches Textarchiv)** (deutschestextarchiv.de) — Historical German texts

### French {#french-sources}

- **Gallica** (gallica.bnf.fr) — French national library digital collection
- **ABU (Association des Bibliophiles Universels)** — French electronic texts
- **Ebooks libres et gratuits** (ebooksgratuits.com) — Free French ebooks

### Spanish {#spanish-sources}

- **Cervantes Virtual** (cervantesvirtual.com) — Spanish and Latin American literature
- **Project Gutenberg Spanish** — Spanish section of Project Gutenberg

## Subtitle corpora {#subtitles}

### OpenSubtitles {#opensubtitles}

**URL**: [opensubtitles.org](https://www.opensubtitles.org)

The largest collection of movie and TV show subtitles.

**Languages**: 60+ languages.

**Strengths**:
- Informal, spoken-language text (complementary to formal corpora)
- Time-aligned (useful for audio-text alignment research)
- Very large volume for many language pairs

**Considerations**:
- Quality varies (user-contributed translations)
- May contain timing information that needs to be stripped
- Copyright status can be ambiguous

## Religious and multilingual reference texts {#religious}

Religious texts, particularly the Bible, are available in more languages than any other single work:

- **Bible Gateway** (biblegateway.com) — Bible in 70+ languages and many translations per language
- **JW Library** — Watchtower texts in 300+ languages (basis for the JW300 corpus)
- **Quran translations** — Available in many languages through various Islamic text repositories

These texts are useful for:
- Creating alignment benchmarks for low-resource languages
- Testing alignment tools across maximum language diversity
- Building initial bilingual lexicons for rare languages

## Government and institutional sources {#government}

- **EUR-Lex** (eur-lex.europa.eu) — EU legislation in all official EU languages
- **WIPO** (wipo.int) — Patent documents in multiple languages
- **WTO** (wto.org) — Trade documents in English, French, Spanish
- **Canadian Parliament (Hansard)** — Parliamentary proceedings in English and French

## How to check copyright {#copyright}

Before using any text for alignment, verify its copyright status:

1. **Check the publication date**: In most countries, works published before 1928 are in the public domain. The exact rules vary by country.
2. **Check the author's death date**: In the EU, copyright expires 70 years after the author's death. In the US, the rules are more complex and depend on publication date.
3. **Check the license**: Some modern texts are released under Creative Commons or other open licenses that permit free use.
4. **Check the translation**: Even if the original is in the public domain, the translation may be under copyright. A 19th-century novel is public domain, but a 2020 translation of it is not.
5. **Fair use / fair dealing**: In many jurisdictions, using short excerpts for research or education is permitted under fair use / fair dealing provisions. Full-text alignment of copyrighted works typically requires permission.

### Safe choices {#safe-choices}

- Texts from Project Gutenberg (explicitly public domain)
- Texts under Creative Commons licenses (CC0, CC-BY, CC-BY-SA)
- Government documents (often public domain by law)
- Texts you created yourself
- Texts with explicit permission from the copyright holder

## Practical tips for sourcing texts {#practical-tips}

1. **Match editions carefully**: Different editions of the same work may have different chapter structures, introductions, or appendices. These differences will cause alignment problems.

2. **Prefer plain text**: PDF and EPUB files require conversion to plain text, which can introduce errors (broken paragraphs, header/footer remnants, page numbers inline). Start with plain text or HTML when possible.

3. **Check encoding**: Ensure files are in UTF-8. Legacy encodings (Windows-1251 for Russian, GB2312 for Chinese) can cause character corruption.

4. **Verify completeness**: Make sure both texts are complete — no missing chapters, no truncated endings. Incomplete texts will cause alignment to fail catastrophically.

5. **Remove non-text content**: Strip tables of contents, indices, footnote numbers, page headers, and other non-narrative content before alignment.

6. **Note the translator**: If multiple translations exist, choose the one closest in style and structure to the original. Very free or adapted translations are harder to align.
