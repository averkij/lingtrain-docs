# User Story: Creating a Parallel Book {#creating-parallel-book}

Maria is learning French and loves reading classic literature. She has been studying for two years and can read simple texts, but novels in the original are still challenging. She wants to create a parallel French-English book of Antoine de Saint-Exupery's "The Little Prince" — a relatively short novel with simple language, perfect for an intermediate learner.

This is her story, step by step.

## Finding the Texts {#finding-texts}

Maria starts by searching for the texts online. "The Little Prince" was published in 1943, and while its copyright status varies by country, she finds both the French original and an English translation available for personal educational use.

She downloads both texts and opens them in a plain text editor. The French version has some OCR artifacts — ligatures rendered incorrectly and a few garbled accents. The English version is cleaner but includes a publisher's note at the beginning that does not exist in the French original.

Maria spends about 15 minutes cleaning both files:

- She removes the publisher's note from the English file.
- She fixes the garbled accents in the French file (replacing `Ã©` with `e` and similar artifacts).
- She ensures both files are saved as UTF-8 `.txt` files.
- She removes page numbers that were scattered throughout the French text.
- She joins hard-wrapped lines so each paragraph is a single long line separated by blank lines.

## Adding Markup Tags {#adding-tags}

Maria knows that markup tags will make the exported book look professional. She adds tags to both files:

**French file:**
```
Antoine de Saint-Exupéry%%%%%author.
Le Petit Prince%%%%%title.

Chapitre I%%%%%h1.

Lorsque j'avais six ans j'ai vu, une fois, une magnifique image...
```

**English file:**
```
Antoine de Saint-Exupéry%%%%%author.
The Little Prince%%%%%title.

Chapter I%%%%%h1.

Once when I was six years old I saw a magnificent picture...
```

She adds `%%%%%h1.` tags for all 27 chapters in both files, making sure the count matches — 27 headings in French and 27 in English.

## Uploading to Lingtrain {#uploading}

Maria opens Lingtrain Aligner and navigates to the Documents tab.

She sets the "From" language to **French** and the "To" language to **English**. She uploads the French file on the left and the English file on the right, with the "Clean text" checkbox enabled for both.

After uploading, she clicks on each document to check the sentence preview. The French text was split into 562 sentences, and the English text into 547 sentences. The difference in sentence count is normal — translators often split or merge sentences.

She scrolls through the Marks panel at the bottom of the page. Both documents show:
- 1 `author` tag each
- 1 `title` tag each
- 27 `h1` tags each

The counts match perfectly. She is ready to create the alignment.

## Creating and Running the Alignment {#alignment}

On the Alignments tab, Maria clicks "+ Create alignment" and names it "Le Petit Prince FR-EN". She selects the French document as the source and the English document as the target.

The system creates the alignment with 3 batches (batch size 200). Maria keeps the default settings — window 40, shift 0 — and clicks "Align next" to process the first batch.

After about a minute, the first batch completes. She checks the visualization: a clean diagonal line with only minor deviations. Encouraged, she clicks "Align all" to process the remaining batches.

When all three batches are done, the Conflicts section shows **18 conflicts found**. Maria clicks "Resolve all" and watches as the automatic resolver processes them. After a few seconds, the count drops to **2 conflicts** remaining.

## Resolving the Last Two Conflicts {#resolving}

Maria clicks "Next" to examine the first remaining conflict. The conflict viewer shows:

- **From:** 3 French sentences about the narrator drawing a boa constrictor.
- **To:** 2 English sentences covering the same content.

The translator had merged two short French sentences into one English sentence. Maria clicks "Open in editor" and navigates to the conflict location. She uses the Edit action to combine the two source sentences into one cell, matching the structure of the English translation. The conflict is resolved.

The second conflict is similar — a case where a French paragraph was restructured in translation. Maria resolves it the same way.

The conflict counter now shows "All conflicts resolved."

## Reviewing the Result {#reviewing}

Maria sets the editor to 50 lines per page and scans through the entire alignment. Most pairs look correct. She notices one pair where a French idiomatic expression was translated very freely, but the pair is still semantically valid — no action needed.

She pays extra attention to the beginning (chapter 1) and end (chapter 27). Everything looks clean.

## Configuring the Export {#configuring-export}

Maria switches to the Create tab and selects her alignment. She configures the export:

- **Paragraph structure:** From (French) — she prefers the original's paragraph rhythm.
- **Language order:** From — French on the left, English on the right. She wants to try reading French first and glancing at the English when stuck.
- **Highlight style:** Pastel — the gentle background colors make it easy to distinguish languages.
- **Reading density:** Comfortable — she will read this on her tablet during commutes.

She clicks "Generate preview" and sees a beautiful parallel book with a title page showing "Antoine de Saint-Exupery / Le Petit Prince / The Little Prince", followed by chapter headings and neatly formatted sentence pairs.

## Downloading the Book {#downloading}

Satisfied with the preview, Maria clicks the download button next to "HTML book". The browser downloads `Le_Petit_Prince_FR-EN.html`.

She opens the file on her tablet. The responsive layout adjusts perfectly to the screen. Each sentence pair is clearly separated, with the French in a light blue background and the English in a light cream background.

She also downloads the `.lt` backup file — just in case she wants to re-export with different settings later.

## Reading the Book {#reading}

Over the next few weeks, Maria reads the parallel book during her daily commute. Her workflow:

1. Read the French sentence first.
2. Try to understand it from context.
3. If stuck, glance at the English translation on the right.
4. Move to the next pair.

She notices her reading speed improving. By chapter 10, she only needs to check the English for about one sentence in five. By chapter 20, she is reading the French almost independently, only occasionally checking the English for unfamiliar vocabulary.

The parallel format is much more efficient than switching between two separate books or looking up words in a dictionary. The context of the translation immediately clarifies meaning, and she picks up not just vocabulary but also French sentence structure and idiomatic patterns.

## Sharing with Friends {#sharing}

Maria's study group is impressed with the book. Two friends ask her to create parallel books for them — one wants Spanish-English, the other wants Italian-English. Maria walks them through the process, and they create their own books in about 30 minutes each.

The group starts a "parallel book club" where they read the same work in different language pairs and discuss the translation choices they notice. The alignment makes translation differences visible at the sentence level — something they never noticed reading regular translations.

## Lessons Learned {#lessons-learned}

From her experience, Maria offers these tips to fellow language learners:

1. **Start with short, simple texts.** "The Little Prince" at ~15,000 words was perfect for a first project. A 500-page novel would be overwhelming.
2. **Spend time on text preparation.** The 15 minutes she spent cleaning files saved hours of conflict resolution later.
3. **Markup tags matter.** Chapter headings made the exported book feel professional and easy to navigate.
4. **The Pastel highlight style is best for on-screen reading.** It provides clear visual separation without being distracting.
5. **Download the .lt backup.** She later re-exported with "Gradient" style for printing and was glad she had the backup.
6. **Read actively.** The parallel book is a tool, not a magic solution. Active reading — trying to understand French before checking English — is what makes the learning happen.

## Next Steps {#next-steps}

Ready to create your own parallel book? Start here:

- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — step-by-step technical instructions.
- [Tutorial: Preparing Texts](tutorial-prepare-texts.en.md) — how to find and format source texts.
- [Tutorial: Exporting Your Results](tutorial-export-formats.en.md) — all export options explained.
