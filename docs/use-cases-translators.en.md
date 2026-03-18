# Lingtrain for Professional Translators {#use-cases-translators}

Professional translators work with translation memories (TMs) — databases of previously translated segments that help maintain consistency and speed up future translations. Lingtrain Aligner lets translators build translation memories from existing translated texts, even when those translations were created without any CAT tool.

This page explains how translators can leverage Lingtrain to build TMs, integrate with CAT tools, extract terminology, and perform quality assurance on translations.

## Building translation memories from existing translations {#building-tms}

### The legacy translation problem {#legacy-problem}

Every experienced translator has a body of past work — books, documents, articles — that were translated without a CAT tool. These translations contain valuable translation decisions, consistent terminology choices, and domain-specific phrasing that took years to develop.

Without alignment, this knowledge is locked in unstructured text files. You know that somewhere in your translation of that 300-page technical manual, you established how to translate "throttle body assembly" — but finding it means searching through the entire document manually.

Lingtrain solves this by aligning your source and target texts at the sentence level, creating a structured database of translation pairs that you can export as a TMX file and import into any CAT tool.

### Workflow for TM building {#tm-workflow}

1. **Gather your materials.** Collect the source text and your translation in digital form (plain text files). If you only have printed or PDF versions, you will need to OCR them first.

2. **Upload and align.** Upload both texts to Lingtrain, create an alignment, and run the batch processing. For professional translations, alignment quality is typically high because professional translations follow the source structure closely.

3. **Review carefully.** Even professional translations have sentence splits and merges. Use the conflict resolver, then review in the editor. For TM purposes, accuracy matters more than for casual reading — a wrong alignment becomes a wrong TM entry.

4. **Export as TMX.** Download the alignment in TMX format. This file can be imported into any CAT tool (SDL Trados, memoQ, OmegaT, Memsource, Smartcat, and others).

5. **Import and use.** Import the TMX into your CAT tool. Your legacy translations are now available as a translation memory, providing fuzzy and exact matches for future projects.

### Tips for TM quality {#tm-quality-tips}

- **Clean your source texts thoroughly.** Remove page numbers, headers, footnotes, and any non-translatable content before uploading. These elements create noise in the TM.

- **Use markup tags for structure.** Tag chapter headings with `%%%%%h1.` and other structural elements. These are excluded from alignment, preventing false matches.

- **Check sentence splitting.** Before running alignment, review the sentence preview in the Documents tab. If the splitter is cutting sentences at wrong points, consider adjusting the text (e.g., removing abbreviation periods that confuse the splitter).

- **Focus on accuracy, not completeness.** It is better to delete a few uncertain alignment pairs in the editor than to include wrong ones in your TM. Wrong TM matches are worse than no matches at all.

## CAT tool integration {#cat-integration}

### TMX format compatibility {#tmx-compatibility}

Lingtrain exports TMX 1.4, which is supported by all major CAT tools:

| CAT Tool | TMX Import | Notes |
|----------|-----------|-------|
| SDL Trados Studio | Yes | Import via Translation Memories > Import |
| memoQ | Yes | Import via Resource Console > TMs |
| OmegaT | Yes | Place TMX in /tm folder of project |
| Memsource (Phrase) | Yes | Import via Translation Memories |
| Smartcat | Yes | Upload as TM resource |
| CafeTran | Yes | Import via Memory > Import TMX |
| Wordfast | Yes | Import via TM Management |

### Building domain-specific TMs {#domain-tms}

Translators specializing in specific domains (legal, medical, technical, literary) can build dedicated TMs from their past work:

1. **Legal TM** — align contracts, legislation, court decisions you have translated
2. **Medical TM** — align clinical trials, patient information leaflets, medical articles
3. **Technical TM** — align user manuals, specifications, technical documentation
4. **Literary TM** — align novels, short stories, poetry translations (useful for series where terminology and names must remain consistent)

Each domain TM becomes more valuable over time as you add more aligned translations.

## Terminology extraction {#terminology}

### From aligned corpus to term base {#term-extraction}

A sentence-aligned corpus is a starting point for terminology extraction:

1. **Export** the alignment as a sentence corpus (parallel text files)
2. **Identify repeated terms** — words or phrases that appear consistently in the same translation across multiple sentences
3. **Build a term base** — create a glossary of source-target term pairs with context examples

While Lingtrain does not perform automatic terminology extraction itself, the aligned corpus it produces is the essential input for terminology tools and manual glossary building.

### Consistency checking {#consistency}

A well-aligned corpus lets you check whether you have translated the same term consistently:

- Search the parallel text for a source term and review all its translations
- Identify inconsistencies where the same source term was translated differently without good reason
- Update your term base to reflect preferred translations

## Translation quality assurance {#quality-assurance}

### Checking existing translations {#checking-translations}

Lingtrain can serve as a QA tool for checking translation completeness and accuracy:

- **Missing content:** If the alignment visualization shows a large gap (the diagonal jumps), content may have been accidentally omitted from the translation
- **Added content:** Extra sentences in the translation that do not align to any source sentence may indicate translator additions that were not requested
- **Structural issues:** A noisy visualization may indicate that the translation significantly restructured the source, which may or may not be appropriate

### Reviewing another translator's work {#reviewing}

When reviewing someone else's translation:

1. Align the source text with the translation
2. Use the editor to browse aligned sentence pairs
3. The side-by-side view makes it easy to spot omissions, additions, and mistranslations
4. Export the alignment for detailed review notes

## Working with literary translations {#literary}

### Series and recurring characters {#series}

For translators working on book series, consistency across volumes is critical. Lingtrain helps by:

- Building a TM from the first book before starting the second
- Ensuring character names, place names, and recurring phrases are translated consistently
- Creating a reference corpus that can be searched during translation

### Multiple editions and revisions {#revisions}

When revising a translation or comparing editions:

1. Align the original source with the first translation
2. Align the same source with the revised translation
3. Export both as sentence corpora
4. Compare the two translations sentence by sentence to identify changes

## Practical considerations {#practical}

### Text preparation for translators {#text-prep}

Translators' texts often have specific characteristics:

- **Footnotes and endnotes** — remove or handle separately, as they disrupt sentence alignment
- **Translator's notes** — remove before alignment or mark with special tags
- **Different paragraph structure** — the translation may have different paragraph breaks than the source; Lingtrain handles this through its paragraph structure options during export
- **Formatting tags** — if the source had formatting (bold, italic), this is usually lost in plain text; focus on content alignment

### Time investment {#time-investment}

Building a TM from an existing translation with Lingtrain typically takes:

- **Short text (10-50 pages):** 15-30 minutes including review
- **Medium text (50-200 pages):** 1-2 hours including review
- **Long text (200+ pages):** 2-4 hours including review

This is a one-time investment that pays off every time the TM matches in future projects.

## Further reading {#further-reading}

- [Overview](index.en.md) — getting started with Lingtrain
- [Alignment process](alignment.en.md) — the full alignment workflow
- [Export](export.en.md) — export formats including TMX
- [How Translators Work](translation-process.en.md) — understanding the translation decisions that affect alignment
- [Lingtrain for Publishers and Educators](use-cases-publishers.en.md) — creating bilingual publications
