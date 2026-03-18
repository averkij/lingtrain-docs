# User Story: Building Translation Memory from Legacy Translations {#translation-memory}

Elena is a professional translator who has been working for 15 years. Over the years, she has translated dozens of technical manuals, marketing materials, and literary texts from English to Russian. Most of these translations exist only as finished documents — Word files, PDFs, and printed books. She wants to convert her past work into reusable translation memory (TMX) that she can load into her CAT tool (memoQ) to speed up future projects.

## The Challenge {#challenge}

Elena's situation is common among experienced translators:

- She has **original English texts** and their **Russian translations** as separate documents.
- The texts were translated manually, without a CAT tool — no translation memory exists.
- Her current CAT tool (memoQ) can import TMX files, but creating them manually would take months.
- She wants to reuse her past translations for terminology consistency and leverage in similar projects.

Lingtrain Aligner can solve this: upload both texts, align them at the sentence level, and export as TMX.

## Selecting Texts to Process {#selecting}

Elena starts with her most valuable translations — the ones most likely to contain reusable content:

1. **A software user manual** (120 pages, ~30,000 words) — highly repetitive technical content with consistent terminology. Excellent leverage potential.
2. **A corporate annual report** (40 pages, ~15,000 words) — business language with standard phrases.
3. **A product brochure series** (5 brochures, ~5,000 words each) — marketing language with repeated product descriptions.

She decides to start with the software manual as a pilot project.

## Extracting and Preparing Text {#preparation}

The original English manual is a PDF, and her Russian translation is a DOCX file. She needs plain text from both.

**English (PDF):**
Elena uses `pdftotext` to extract the content. The result has some issues:
- Page numbers on every page.
- Running headers ("Product Name — User Manual") repeated throughout.
- Hyphenated words at line breaks ("config-\nuration" instead of "configuration").
- Table content interspersed with body text.

She cleans the text in a text editor:
- Removes all page numbers and running headers.
- Joins hyphenated words across line breaks.
- Removes table content (it does not align well at the sentence level).
- Ensures paragraphs are separated by blank lines.

**Russian (DOCX):**
She opens the DOCX in her word processor and saves as plain text (UTF-8). The result is cleaner but still needs:
- Removal of the table of contents.
- Removal of figure captions that do not exist in the English version.
- Joining of hard-wrapped lines.

She adds minimal markup to both files:
```
Product Name%%%%%title.
User Manual%%%%%title.
```

And heading tags for major sections:
```
Chapter 1: Installation%%%%%h1.
Chapter 2: Configuration%%%%%h1.
```

## Uploading and Aligning {#aligning}

Elena uploads both texts to Lingtrain. The English text splits into 1,247 sentences, and the Russian into 1,312 sentences — a 5% difference, which is typical for technical translation where Russian tends to be slightly more verbose.

She creates the alignment and processes all batches. The visualization shows a clean diagonal — technical texts with parallel structure align very well.

The system finds 42 conflicts. She runs "Resolve all" and the count drops to 3. These three remaining conflicts are in a section where the Russian translation added explanatory notes that do not exist in English. Elena opens them in the editor and deletes the extra Russian sentences — they are translator's notes, not part of the original content, and should not be in the translation memory.

## Reviewing for Quality {#reviewing}

For a translation memory, quality is paramount. A wrong pair in the TM will surface as a wrong suggestion in future projects, potentially introducing errors.

Elena reviews the alignment carefully:

1. She scans through the editor at 20 lines per page, checking every pair.
2. She pays special attention to:
   - **Terminology consistency** — do the pairs use the same terms she would want to reuse?
   - **Sentence boundaries** — is each pair a clean, self-contained translation unit?
   - **Empty cells** — are there any gaps that indicate missing content?
3. She finds and fixes 8 issues:
   - 3 pairs where the sentence split cut a sentence in the middle of a technical term.
   - 2 pairs where a bulleted list was incorrectly merged into a single sentence.
   - 3 pairs where the Russian included text from a different section.

The review takes about 45 minutes for the 1,247-pair alignment.

## Exporting as TMX {#exporting}

Satisfied with the quality, Elena goes to the Create tab and downloads the TMX file.

The TMX file contains:

```xml
<tmx version="1.4">
  <header srclang="en" adminlang="en" datatype="plaintext"/>
  <body>
    <tu>
      <tuv xml:lang="en">
        <seg>Click the Install button to begin the installation process.</seg>
      </tuv>
      <tuv xml:lang="ru">
        <seg>Нажмите кнопку «Установить», чтобы начать процесс установки.</seg>
      </tuv>
    </tu>
    <!-- ... more translation units ... -->
  </body>
</tmx>
```

## Importing into memoQ {#importing}

Elena imports the TMX file into memoQ:

1. Opens her memoQ project.
2. Goes to Translation memories > Import.
3. Selects the TMX file.
4. memoQ imports all 1,247 translation units.

She immediately tests the memory on a new section of a similar software manual. The results are impressive — memoQ shows fuzzy matches for about 30% of the new sentences, with many exact matches for standard UI phrases like "Click OK to confirm" / "Нажмите ОК для подтверждения".

## Processing the Remaining Texts {#remaining}

Encouraged by the results, Elena processes the annual report and the brochure series. Each follows the same workflow:

1. Extract text from the original format.
2. Clean and add markup tags.
3. Upload, align, resolve, review.
4. Export as TMX.
5. Import into memoQ.

The entire process for all five documents takes one full working day. The result: a translation memory with approximately 4,500 high-quality translation units built from 15 years of work.

## Long-Term Value {#value}

Over the next six months, Elena tracks the impact:

- **Leverage rate** on new technical translations increases by 15-25%.
- **Terminology consistency** improves noticeably — her TM suggests the same terms she used before.
- **Translation speed** increases, as she spends less time looking up past translations manually.
- **Client satisfaction** improves, as clients notice more consistent terminology across projects.

She also shares a subset of the TM with a colleague who works on similar technical content, further multiplying the value.

## Tips for Translation Memory Construction {#tips}

1. **Start with the most repetitive content.** Technical manuals, product documentation, and legal texts have the highest leverage potential.
2. **Clean texts thoroughly.** A TMX with noisy pairs is worse than no TMX at all — it introduces errors into future translations.
3. **Review every pair.** For TM quality, manual review is non-negotiable. Budget time for it.
4. **Remove translator's notes and additions.** Only include content that appears in both the original and the translation.
5. **Export .lt backups.** You may need to re-export with different parameters or make corrections later.
6. **Process in batches.** Do not try to process all your legacy translations at once. Start with the highest-value texts, verify the workflow, then continue.
7. **Keep your TM organized.** In your CAT tool, create separate TMs for different domains (technical, marketing, legal) to improve match relevance.

## Next Steps {#next-steps}

- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — step-by-step alignment guide.
- [Tutorial: Exporting Your Results](tutorial-export-formats.en.md) — detailed TMX export information.
- [Tutorial: Preparing Texts](tutorial-prepare-texts.en.md) — text preparation best practices.
