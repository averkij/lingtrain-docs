# Working with Low-Resource Languages {#low-resource-languages}

One of Lingtrain Aligner's most valuable capabilities is supporting alignment for **low-resource languages** — languages with limited digital text, few NLP tools, and underrepresentation in training data for machine learning models. This page explains the challenges these languages present, how Lingtrain addresses them, and practical strategies for achieving good alignment quality.

## What are low-resource languages? {#definition}

A **low-resource language** is one that lacks the large-scale digital resources that NLP systems typically rely on: large text corpora, parallel datasets, trained models, and standardized tools. The vast majority of the world's 7,000+ languages fall into this category.

Even among written languages with established literary traditions, many are underrepresented in modern NLP because:

- **Limited web presence** — fewer websites, books, and documents available digitally
- **Small speaker population** — smaller communities produce less digital text
- **Script diversity** — non-Latin scripts may have less tool support
- **Dialectal variation** — no single standard form for NLP tools to target
- **Historical exclusion** — large language models are trained primarily on English and a handful of other major languages

## Challenges for alignment {#challenges}

### Embedding quality {#embedding-quality}

Multilingual embedding models learn to represent languages based on the training data they have seen. For languages with little training data, the resulting embeddings are less accurate — sentences that should be similar may not be mapped to nearby points in the vector space.

This manifests in the alignment visualization as a noisy or scattered plot instead of a clean diagonal.

### Sentence splitting {#sentence-splitting}

Sentence boundary detection rules are language-specific. For languages without built-in support in Lingtrain's splitter, the **General** splitting mode is used, which relies on universal punctuation patterns. This works for most languages with standard punctuation (periods, question marks, exclamation marks) but may struggle with:

- Languages that use non-standard sentence-ending markers
- Languages with different quotation conventions
- Languages where abbreviation periods are common and hard to distinguish from sentence boundaries

### Script and encoding {#script-encoding}

Most modern NLP tools handle Unicode well, but some scripts have specific challenges:

- **Right-to-left scripts** (Arabic, Hebrew) — handled by embedding models but may affect text display
- **Complex scripts** (Devanagari, Thai, Myanmar) — tokenization challenges
- **Multiple scripts for one language** (Serbian Cyrillic/Latin, Uzbek) — may affect model coverage

## Languages with dedicated support in Lingtrain {#supported-languages}

Lingtrain provides language-specific sentence splitting and preprocessing for these languages:

- **Russian, Ukrainian, Belarusian** — `razdel` library for Cyrillic text splitting
- **English, German, French, Spanish, Italian, Portuguese, Dutch, Swedish, Polish, Hungarian, Czech, Turkish** — standard European language tokenizers
- **Chinese** — regex-based splitting on Chinese punctuation
- **Japanese** — pattern matching with Japanese sentence-ending markers
- **Korean** — Korean-specific splitting rules
- **Bashkir** — custom rules for Bashkir Cyrillic text
- **Chuvash** — custom rules for Chuvash
- **Armenian** — Armenian-specific punctuation handling

For all other languages, the **General** splitter is available, which handles most punctuation systems correctly.

## The proxy text solution {#proxy-texts}

### How proxy texts work {#how-proxy-works}

The most effective strategy for aligning low-resource language pairs is **proxy text** alignment. Instead of directly comparing embeddings between two low-resource languages, you introduce a high-resource intermediary:

1. **Machine-translate** the low-resource text into a high-resource language (e.g., English or Russian)
2. **Upload** the machine translation as a proxy document in Lingtrain
3. **Align** using the proxy — the system compares embeddings of the proxy (high-resource) with the other text

Because embedding models produce much better vectors for high-resource languages, the alignment quality improves dramatically.

**Example workflow for Bashkir-Russian alignment:**

1. Upload your Bashkir text as the source document
2. Upload your Russian text as the target document
3. Machine-translate the Bashkir text into Russian using an available MT service
4. Upload the Russian translation of the Bashkir text as a **proxy (from)** document
5. Enable "Use proxy (from)" in alignment settings
6. Run alignment — the system compares the Russian proxy with the actual Russian target

The proxy acts as a bridge: the alignment is computed between two Russian texts (which produces high-quality embeddings), and the results are applied to the original Bashkir-Russian pair.

### When to use proxy texts {#when-proxy}

Use proxy texts when:

- The visualization shows scattered or noisy alignment after the first batch
- You are working with a language that has fewer than 10 million speakers
- The language pair does not include English, Russian, Chinese, or another major language
- Direct alignment produces too many conflicts to resolve manually

You may not need proxy texts when:

- Both languages are well-supported by the embedding model (e.g., English-German, Russian-French)
- The visualization shows a clean diagonal
- Conflict counts are manageable (under 5% of total sentences)

For detailed instructions on using proxy texts, see the [Proxy texts guide](proxy.en.md).

## Language-specific guidance {#language-guidance}

### Bashkir (Башҡорт теле) {#bashkir}

Bashkir is a Turkic language spoken by approximately 1.2 million people, primarily in the Republic of Bashkortostan (Russia). It uses a modified Cyrillic script with additional characters.

**Lingtrain support:**

- Dedicated sentence splitter in Lingtrain
- LaBSE provides reasonable embedding quality
- Best results with Russian proxy text

**Recommended approach:**

1. Use the Bashkir language option for sentence splitting
2. Select LaBSE as the embedding model
3. If alignment quality is poor, add a Russian proxy (machine-translate Bashkir to Russian)
4. Set window parameter to 50-60 (slightly larger than default) to handle structural differences

### Chuvash (Чӑвашла) {#chuvash}

Chuvash is a Turkic language spoken by about 1 million people in the Chuvash Republic (Russia). It is the only surviving member of the Oghur branch of Turkic languages.

**Lingtrain support:**

- Dedicated sentence splitter
- LaBSE provides basic coverage
- Proxy texts strongly recommended

**Recommended approach:**

1. Use the Chuvash language option for splitting
2. Use LaBSE as the embedding model
3. Always use a Russian proxy for best results
4. Expect higher conflict rates than with major languages; plan for manual editing

### Komi (Коми кыв) {#komi}

Komi is a Uralic language spoken by approximately 160,000 people in the Komi Republic (Russia). It uses Cyrillic script.

**Lingtrain support:**

- Use the General splitter (Komi uses standard Cyrillic punctuation)
- LaBSE may have limited coverage
- Russian proxy is essential

**Recommended approach:**

1. Use General splitting mode
2. Use LaBSE as the embedding model
3. Always use a Russian proxy
4. Increase the window parameter to 60+ for safety
5. Be prepared for significant manual editing

### Armenian (Հայերեն) {#armenian}

Armenian is an Indo-European language spoken by approximately 6 million people. It uses its own unique script (Armenian alphabet).

**Lingtrain support:**

- Dedicated sentence splitter handling Armenian punctuation (including the Armenian full stop `։`)
- LaBSE provides good embedding quality (Armenian is in the LaBSE training set)
- Direct alignment often works well without proxy

**Recommended approach:**

1. Use the Armenian language option for splitting
2. Use LaBSE as the embedding model
3. Try direct alignment first; add proxy only if the visualization shows issues
4. Default parameters usually work well

### Other Turkic languages {#other-turkic}

Languages like Kazakh, Uzbek, Kyrgyz, Tatar, and Azerbaijani fall into the medium-resource category. They have growing digital presence and are represented (to varying degrees) in multilingual models.

**General approach:**

1. Use the General splitter or Turkish splitter (for Latin-script Turkic languages)
2. Try LaBSE first
3. Add a Russian or English proxy if needed
4. Test with a small batch before committing to full alignment

### Other Uralic languages {#other-uralic}

Languages like Mari, Udmurt, Erzya, and Moksha are genuinely low-resource, with very limited representation in NLP datasets.

**General approach:**

1. Use the General splitter
2. Use LaBSE
3. Always use a proxy (Russian recommended)
4. Expect significant manual work; these are among the most challenging alignment tasks

## The role of LaBSE and SONAR {#labse-sonar}

Two models stand out for low-resource language support:

### LaBSE {#labse-detail}

LaBSE's advantage comes from its training methodology. Unlike models trained primarily on Wikipedia or web crawl data (which skews toward high-resource languages), LaBSE was trained on **bilingual sentence pairs mined from the web**. This mining process found parallel sentences in many language pairs that traditional corpus collection would miss, giving LaBSE exposure to a wider range of languages.

For alignment tasks, LaBSE is the recommended starting point for any language pair involving a low-resource language.

### SONAR {#sonar-detail}

Meta's SONAR model covers **200+ languages**, making it the broadest multilingual model available. It uses language-specific encoders that share a common output space, allowing comparison across any supported language pair.

SONAR's main advantage is coverage: it includes many languages that other models simply do not know. If LaBSE does not produce adequate results for your language, SONAR may be worth trying.

## Connection to language preservation {#preservation}

Parallel corpora are valuable resources for language preservation and revitalization:

- **Educational materials** — bilingual books help learners of endangered languages study with native-language support
- **Translation memory** — building TM databases from existing translations preserves translation knowledge
- **NLP development** — parallel corpora enable training of machine translation and other NLP tools for underresourced languages
- **Cultural heritage** — aligning literary works, folklore collections, and historical texts creates structured bilingual archives

Lingtrain's support for low-resource languages is motivated by these preservation goals. Every parallel corpus created for a minority language is a contribution to its digital future.

## Practical tips for low-resource alignment {#practical-tips}

1. **Start small.** Test with a short excerpt (50-100 sentences) before attempting a full book. This lets you evaluate embedding quality and parameter settings quickly.

2. **Check the visualization early.** After the first batch, inspect the scatter plot. If it does not show a clear diagonal, switch models or add a proxy before proceeding.

3. **Use larger windows.** For low-resource languages, increase the window parameter to 50-80 (default is 40). This compensates for lower embedding precision.

4. **Expect more manual work.** Even with proxy texts, low-resource alignments typically produce more conflicts than high-resource ones. Budget time for manual editing in the alignment editor.

5. **Leverage machine translation for proxy.** Services like Google Translate, Yandex Translate, and DeepL support many languages. Even imperfect machine translation can serve as an effective proxy.

6. **Consider the direction.** If aligning language A (low-resource) with language B (high-resource), the proxy for language A is more important. The embedding model already handles language B well.

7. **Document your settings.** When you find a configuration that works well for a particular language pair, record the model, window, shift, and proxy settings for future reference.

## Further reading {#further-reading}

- [Proxy texts](proxy.en.md) — detailed guide on using proxy texts in Lingtrain
- [Comparing Embedding Models](embedding-models-comparison.en.md) — choosing the right model for your languages
- [Alignment algorithm](algorithm.en.md) — understanding the parameters that affect alignment quality
- [Batch Processing in Detail](batch-processing-explained.en.md) — how window and shift parameters work
