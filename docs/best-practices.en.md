# Best Practices for Quality Alignment {#best-practices}

This guide collects expert tips and strategies for achieving the best possible alignment quality with Lingtrain Aligner. Whether you are aligning a novel, a technical manual, or a collection of short texts, these practices will help you avoid common pitfalls and produce clean, publication-ready results.

## Text Preparation {#text-preparation}

Good alignment starts with good input. The time you invest in text preparation pays off directly in alignment quality.

### Cleaning Your Texts {#cleaning}

Before uploading, remove content that does not have a counterpart in the other language:

- **Page numbers** — always remove these; they appear in only one text's structure
- **Running headers and footers** — repetitive text from page layouts
- **Footnote markers** — inline superscript numbers that reference footnotes
- **Footnotes themselves** — unless both texts have matching footnotes
- **Table of contents** — usually structured differently between editions
- **Publisher information** — copyright pages, ISBN, edition details
- **Translator's prefaces** — unless you specifically want them aligned

### Formatting Best Practices {#formatting}

- **Separate paragraphs with blank lines.** The sentence splitter uses paragraph boundaries to improve segmentation. A text that is one continuous block without blank lines will produce worse splitting.
- **Use consistent punctuation.** Mixed punctuation styles (e.g., alternating between "..." and "...") can confuse the splitter.
- **Remove extra whitespace.** Multiple consecutive spaces and tabs should be normalized to single spaces.
- **Fix encoding issues before uploading.** Common problems include mojibake (garbled characters from encoding mismatches), smart quotes vs. straight quotes, and invisible Unicode characters.

### Adding Markup Tags {#adding-tags}

Always add at minimum:

1. **`%%%%%author.`** — the author name in both texts
2. **`%%%%%title.`** — the work title in both texts
3. **`%%%%%h1.`** — chapter headings in both texts

These tags produce a proper title page and chapter structure in the HTML book export. Without them, the book is a flat sequence of sentences without visual structure.

**Verify tag consistency** between texts:
- Same number of each tag type in both source and target
- Tags appear in the same order
- Use the Marks panel in the Documents tab to verify

### Handling Different Editions {#different-editions}

If your source and target texts are from different editions:

- **Abridged editions:** Remove sections from the full text that are absent in the abridged version. Aligning texts of drastically different lengths leads to many conflicts.
- **Different paragraph structure:** This is fine — the aligner handles different paragraph structures. Just ensure the sentence content matches.
- **Translator additions/omissions:** If the translator added explanatory notes or omitted passages, remove these from one side or accept that the corresponding section will have conflicts.

## Choosing the Right Model {#choosing-model}

The embedding model is the core of alignment quality. Choose based on your language pair:

| Scenario | Recommended Model |
|----------|-------------------|
| Major European language pairs (EN-FR, EN-DE, EN-ES, etc.) | Default model or LaBSE |
| Russian + any major language | LaBSE or default |
| Chinese or Japanese + any language | LaBSE |
| Low-resource languages (Bashkir, Chuvash, etc.) | LaBSE + proxy text |
| Very low-resource languages | SONAR + proxy text |
| Russian-specific task (RU-UK, RU-BE) | LaBSE or rubert-tiny2 |
| Maximum language coverage needed | SONAR |

**When in doubt, use LaBSE.** It provides the best balance of quality and language coverage for most use cases.

The model is locked after the first alignment run. If you want to try a different model, use the "Erase" function to reset the alignment, then select a new model before re-aligning.

## Batch Size and Window Optimization {#batch-optimization}

### Batch Size {#batch-size}

The default batch size of **200** works well for most texts. Consider adjusting in these cases:

- **Very short texts** (under 100 sentences) — the text will fit in a single batch. No adjustment needed.
- **Very long texts** (10,000+ sentences) — the default 200 is fine. It creates many batches but each processes quickly.
- **Texts with uniform structure** — 200 is ideal.
- **Texts with wildly varying density** — consider using a smaller window with careful shift adjustments rather than changing batch size.

Note: Batch size is fixed at alignment creation and cannot be changed later.

### Window Size {#window-size}

The window parameter adds extra sentences on each side of the proportional target range:

- **Default (40-50):** Appropriate for texts with similar sentence counts per section
- **Larger (60-80):** Use when sentence counts differ significantly (e.g., the translator split many sentences, or one text is much more verbose)
- **Very large (100+):** Rarely needed. Increases computation time and risk of false matches. Use only for texts with extreme structural differences

### Shift Parameter {#shift-usage}

The shift parameter is your primary tool for correcting alignment drift:

1. **Start with shift = 0** for the first batch
2. **Check the visualization** after the first batch
3. If the diagonal drifts upward (target sentences are "ahead"), use a **positive shift** to move the target window forward
4. If the diagonal drifts downward, use a **negative shift**
5. Adjust by 10-20 sentences at a time; check the visualization after each adjustment
6. Different batches may need different shifts — the tool allows per-batch shift configuration

## When to Use Proxy Texts {#when-proxy}

Use proxy texts when:

1. **The alignment quality is poor** — many conflicts, broken diagonal on visualization
2. **One language is low-resource** — few training examples in the embedding model
3. **The language pair is distant** — the embedding model struggles with cross-lingual similarity (e.g., Chuvash-Japanese)

**How to prepare a good proxy:**

1. Download the split text from the sentence preview
2. Translate it using the best available MT service for this language
3. Verify the line count matches exactly
4. Upload as proxy on the alignment detail page

**Proxy language choice:** Translate into a language that is well-represented in the embedding model. English and Russian are safe choices for most models. If aligning Language A - Language B, and Language A is low-resource, translate Language A's split text into English and use it as the proxy.

## Handling Different Genres {#genres}

### Fiction (Novels, Short Stories) {#fiction}

- **Expect some conflicts.** Literary translation involves creative choices — merged/split sentences, rearranged clauses. Some conflicts are inevitable.
- **Use markup tags generously.** Add chapter headings, section dividers, and epigraphs.
- **Paragraph structure matters.** Enable paragraph marks for better HTML book output.
- **Dialogue-heavy sections** may produce more short sentences; this is normal.

### Technical Documentation {#technical}

- **Higher consistency.** Technical translations tend to be more literal, producing better alignment.
- **Watch for tables and lists.** These often break sentence splitting. Extract them separately or remove them.
- **Code blocks** should be removed or consistently present in both texts.
- **Version-specific content** (release notes, API references) should match between texts.

### Academic/Scientific Papers {#academic}

- **Remove citations** that appear in only one text's format.
- **Equations and formulas** may cause splitting issues. Place them on separate lines.
- **Abstracts** usually align well because they are translated precisely.
- **Bibliography** should be removed unless both texts have matching references.

### Poetry {#poetry}

- **Poetry is inherently difficult** for sentence-based alignment because line breaks are structural, not syntactic.
- Consider treating each stanza as a unit (separated by blank lines) rather than each line.
- Manual editing will likely be needed.
- The paragraph corpora export may work better than sentence corpora for poetry.

## Long Text Strategy {#long-texts}

For texts with 5,000+ sentences:

1. **Process in stages.** Align 3-5 batches at a time, not all at once.
2. **Check visualization after each stage.** Catch drift early.
3. **Adjust shift progressively.** If the diagonal drifts in batch 3, apply a corrective shift starting from batch 4.
4. **Resolve conflicts after all batches.** Run automatic resolution once when all batches are done.
5. **Do not re-align completed batches** unless there is a clear problem. Re-alignment erases previous results.
6. **Export backups (.lt files)** at key milestones in case you need to revert.

## Quality Verification Workflow {#quality-verification}

After completing alignment and conflict resolution, verify quality:

### Step 1: Check Overall Metrics {#verify-metrics}

- **Chain score** should be above 0.95 for good quality
- **Per-batch quality** should be consistent; a single low-quality batch may indicate a problem in that region

### Step 2: Sample Check in Editor {#verify-editor}

- Open the editor and navigate to the middle of the text
- Verify 10-20 sentence pairs for correctness
- Check the beginning and end of the text (edge cases)
- Look for any patterns: are mismatches concentrated in one area?

### Step 3: Check Visualization {#verify-visualization}

- All batch scatter plots should show a clean diagonal
- Look for outlier dots far from the diagonal — these indicate specific mismatches
- Stepped diagonals are acceptable (indicate added/removed content blocks)

### Step 4: Preview the Book {#verify-preview}

- Generate a book preview in the Create tab
- Check chapter structure and paragraph breaks
- Verify that markup tags produced correct formatting
- Try both paragraph direction options to see which reads better

## Common Pitfalls {#common-pitfalls}

### Pitfall 1: Not Checking the First Batch {#pitfall-first-batch}

**Problem:** Processing all batches at once without checking the first one.

**Why it matters:** If the first batch has a broken diagonal, all subsequent batches will be progressively worse because the window positions are based on the proportion.

**Solution:** Always align one batch first, check the visualization, adjust parameters if needed, then continue.

### Pitfall 2: Ignoring Mismatched Markup {#pitfall-markup}

**Problem:** One text has markup tags that the other lacks.

**Why it matters:** Mismatched tags cause structural misalignment in the exported book. Missing tags mean no chapter headings or title page.

**Solution:** Always check the Marks panel after uploading. Fix mismatches before creating the alignment.

### Pitfall 3: Using "Align All" Carelessly {#pitfall-align-all}

**Problem:** Using "Align all" after making manual edits.

**Why it matters:** "Align all" erases all previous results, including manual edits, resolved conflicts, and custom shift values.

**Solution:** Use selective re-alignment (specific batch IDs) instead. Export a backup before any major re-alignment.

### Pitfall 4: Skipping Automatic Resolution {#pitfall-skip-resolution}

**Problem:** Trying to fix all conflicts manually in the editor.

**Why it matters:** Automatic resolution handles 70-90% of conflicts correctly and is much faster than manual editing.

**Solution:** Always run automatic resolution first. Only manually fix the conflicts that remain after automatic resolution.

### Pitfall 5: Wrong Text Versions {#pitfall-wrong-version}

**Problem:** Aligning texts that are different editions, abridged versions, or have been significantly modified.

**Why it matters:** If the texts do not actually correspond, no amount of parameter tuning will produce good alignment.

**Solution:** Verify that the texts are genuine translations of each other. Compare word counts — they should be in a reasonable ratio (usually 0.7-1.4 for most language pairs).

### Pitfall 6: Forgetting Proxy for Low-Resource Languages {#pitfall-no-proxy}

**Problem:** Attempting direct alignment of a low-resource language without proxy.

**Why it matters:** The embedding model may produce meaningless vectors for under-represented languages, leading to near-random alignment.

**Solution:** Check the language pair support. If one language is not well-represented, create a proxy text via machine translation.

## Summary Checklist {#checklist}

Before starting alignment:

- [ ] Texts are in plain text (.txt) format, UTF-8 encoded
- [ ] Extraneous content removed (page numbers, footnotes, TOC)
- [ ] Paragraphs separated by blank lines
- [ ] Markup tags added (at minimum: author, title, chapter headings)
- [ ] Marks panel shows matching tag counts between texts
- [ ] Appropriate embedding model selected for the language pair
- [ ] Proxy text prepared if needed for low-resource languages

During alignment:

- [ ] First batch checked via visualization before proceeding
- [ ] Shift adjusted if diagonal drifts
- [ ] Batches processed in manageable groups (3-5 at a time)
- [ ] Automatic conflict resolution run after all batches complete

After alignment:

- [ ] Chain score above 0.95
- [ ] Sample check in editor (beginning, middle, end)
- [ ] Book preview verified for structure and formatting
- [ ] Backup exported (.lt file)
