# Tutorial: Resolving Alignment Conflicts {#conflict-resolution}

Conflicts are a natural part of the alignment process. This tutorial explains what conflicts are, why they occur, how to resolve them automatically, and how to handle remaining issues manually in the editor.

## What Are Conflicts? {#what-are-conflicts}

A conflict occurs when the alignment algorithm cannot establish a clean, monotonic mapping between source and target sentences. In a perfect alignment, source sentence 1 maps to target sentence 1, source sentence 2 maps to target sentence 2, and so on in strict order. When this sequence breaks — for example, source sentences [1, 2, 3] map to target sentences [5, 3, 8] — the system detects a conflict.

Conflicts appear in the **Conflicts** section on the alignment detail page after at least one batch has been processed.

![Conflicts section](assets/screenshots/aligner-conflicts-section.png)

## Why Conflicts Happen {#why-conflicts-happen}

Conflicts are caused by legitimate differences between the source text and its translation. The most common causes are:

### Sentence Splitting {#splitting}

A translator splits one sentence into two or more. For example, a long English sentence becomes two shorter Russian sentences. The algorithm may match both Russian sentences to the same English sentence or to nearby sentences, breaking the expected sequence.

**Example:**

| Source (EN) | Target (RU) |
|---|---|
| "She entered the room and, noticing the open window, walked across to close it before turning to greet her guests." | "Она вошла в комнату и заметила открытое окно." |
| | "Подойдя, чтобы закрыть его, она повернулась, чтобы поприветствовать гостей." |

One source sentence corresponds to two target sentences.

### Sentence Merging {#merging}

The opposite of splitting: a translator combines multiple short sentences into one. Two English sentences become a single Russian sentence.

**Example:**

| Source (EN) | Target (RU) |
|---|---|
| "It was raining." | "Шёл дождь, и было холодно." |
| "It was cold." | |

Two source sentences map to one target sentence.

### Content Rearrangement {#rearrangement}

A translator reorders sentences or moves a phrase from one sentence to another. The semantic similarity model may then find a better match in a non-adjacent sentence, breaking the expected monotonic sequence.

### Short or Repetitive Sentences {#short-repetitive}

Very short sentences ("Yes.", "No.", "He nodded.") or sentences with repetitive content provide weak semantic signals. The model may confuse them, matching to the wrong instance.

### Different Paragraph Structures {#different-paragraphs}

Different paragraph breaks between source and target can shift sentence positions relative to each other, especially at batch boundaries.

### Missing Content {#missing-content}

If one text has content without a counterpart in the other (translator's notes, footnotes, extra explanatory sentences), the unmatched sentences create conflicts.

## Understanding the Conflict Viewer {#conflict-viewer}

The Conflicts section shows:

- **Total conflict count** — displayed as "N conflicts found" (e.g., "31 conflicts found").
- **Navigation** — Previous and Next buttons with a counter (e.g., "Conflict 5 of 31").
- **From panel** — the source sentences involved in the conflict, with their original line numbers.
- **To panel** — the target sentences involved in the conflict, with their line numbers.
- **Handle start** checkbox — include conflicts at the very beginning of the aligned text (first few sentences).
- **Handle finish** checkbox — include conflicts at the very end of the aligned text.
- **Open in editor** button — jump to this conflict's location in the editor for manual review.
- **Resolve all** button — run automatic conflict resolution on all conflicts.

### Conflict Size {#conflict-size}

Conflicts vary in size — the number of source and target sentences involved:

- **Small conflicts** (2x2, 2x3, 3x2) — most common. Usually caused by a single split or merge.
- **Medium conflicts** (up to 6x6) — may involve multiple splits/merges in close proximity.
- **Large conflicts** (more than 6x6) — indicate a significant structural difference or a problem with the source texts.

The automatic resolver handles small and medium conflicts well. Large conflicts typically require manual intervention.

## Automatic Conflict Resolution {#automatic-resolution}

### Running Automatic Resolution {#running-auto}

Click **"Resolve all"** to start automatic resolution. The button changes to **"Resolving..."** while the process runs.

The automatic resolver works as follows:

1. **Sort conflicts by size** — smallest first.
2. **For each conflict**, enumerate all possible ways to group the source and target sentences.
3. **Score each grouping** — compute combined embeddings for each group using weighted averaging (by sentence length), then calculate the sum of cosine similarities between paired groups.
4. **Select the best grouping** — the one with the highest total similarity score.
5. **Apply the grouping** — merge sentences according to the selected arrangement.
6. **Recheck** — after resolving a round, the system checks for remaining conflicts. Resolving one conflict can sometimes fix adjacent ones as well.

### After Automatic Resolution {#after-auto}

After resolution completes, the conflict count updates. In many cases, all conflicts are resolved automatically. If some remain, they appear in the conflict viewer for manual handling.

The remaining conflicts are typically:

- **Large conflicts** where the number of possible groupings is too high for exhaustive search.
- **Ambiguous cases** where multiple groupings score similarly.
- **True mismatches** where the source texts have structural differences that cannot be resolved by regrouping alone.

## Manual Conflict Resolution {#manual-resolution}

For conflicts that the automatic resolver cannot handle, you need to use the editor.

### Navigating to a Conflict {#navigating}

In the Conflicts section, browse through remaining conflicts using Previous and Next buttons. When you find one to resolve, click **"Open in editor"** to jump directly to that location in the editor.

### Understanding the Editor View {#editor-view}

The editor shows aligned sentence pairs side by side. Each row has:

- A **row number**.
- **Source text** (left cell) and **target text** (right cell).
- **Line IDs** — the original line numbers from both texts.

![Alignment editor](assets/screenshots/aligner-alignment-editor.png)

Conflict areas typically show misaligned sentences: the source and target in the same row do not correspond to each other.

### Editor Actions for Conflict Resolution {#editor-actions}

Hover over any cell to reveal action buttons. The key actions for conflict resolution are:

**Candidates** — browse alternative sentence matches. The system shows other sentences from the same text that could be placed in this cell, ranked by similarity score. Use Previous and Next to cycle through candidates.

**Edit** — modify the text content of a cell. Useful for minor corrections or for combining text from adjacent cells.

**Delete** — remove a sentence from the cell. Use this when a sentence does not belong (e.g., a translator's note without a counterpart).

**Add empty row above / below** — insert an empty row. Useful for creating space to move sentences around.

### Common Resolution Patterns {#resolution-patterns}

#### Pattern 1: Split Sentence {#pattern-split}

The source has one sentence where the target has two. The editor shows the source sentence paired with only one of the two target sentences; the other target sentence is paired with the wrong source sentence.

**Solution:** Edit the target cell to combine both target sentences into one cell, or use the Candidates feature to find and place the correct sentences.

#### Pattern 2: Merged Sentences {#pattern-merged}

The target has one sentence where the source has two. Two source sentences point to the same target sentence.

**Solution:** Edit the source cells to combine the two source sentences into one row, or delete the empty/mismatched cell and let the alignment consolidate.

#### Pattern 3: Missing Content {#pattern-missing}

One text has a sentence that does not exist in the other (e.g., a translator's note, a footnote, or an omitted passage).

**Solution:** Delete the unmatched sentence from the cell. This removes it from the alignment without affecting surrounding pairs.

#### Pattern 4: Swapped Sentences {#pattern-swapped}

Two adjacent sentence pairs are in the wrong order — the sentence in row N should be in row N+1 and vice versa.

**Solution:** Use the Candidates feature to swap the sentences, or edit the cells directly.

### Split Conflict Tool {#split-conflict}

For larger conflicts, the editor provides a **"Split conflict"** tool. This allows you to manually select a dividing point within a conflict:

1. Select a line on the left side (source).
2. Select a corresponding line on the right side (target).
3. Click **"Split conflict"** to divide the large conflict into two smaller ones.

Smaller conflicts are easier to resolve, either automatically or manually. After splitting, you can run **"Resolve"** again on the individual parts.

Note: conflicts must be resolved before splitting — if there are unresolved conflicts, the system will prompt you to resolve them first.

## Checking the Results {#checking-results}

After resolving conflicts (both automatically and manually), verify the results:

1. **Check the conflict count** — ideally it should be 0. The section will show "All conflicts resolved."
2. **Browse the editor** — scan through the aligned pairs, especially around the areas where conflicts were. Verify that the sentence pairs make sense.
3. **Pay attention to the beginning and end** — the system warns that undetected conflicts can hide at the edges of the aligned text.
4. **Check the visualization** — a clean diagonal in the scatter plot confirms good alignment. Remaining deviations indicate areas to review.

## Tips for Efficient Conflict Resolution {#tips}

1. **Always run automatic resolution first.** It handles the majority of conflicts correctly and saves significant time.
2. **Resolve conflicts batch by batch.** After aligning each batch, resolve its conflicts before moving on. This prevents conflicts from accumulating and becoming harder to manage.
3. **Use "Handle start" and "Handle finish" checkboxes.** These include edge conflicts that are sometimes excluded by default.
4. **For large conflicts, try splitting first.** Use the Split Conflict tool to break a large conflict into smaller, more manageable pieces.
5. **If many conflicts remain, consider re-aligning.** Poor source text preparation is often the root cause. Clean the texts and try again rather than manually fixing dozens of conflicts.
6. **Use interlinear translations for low-resource languages.** If you see many conflicts with a language pair that the model does not handle well, consider using [interlinear alignment](tutorial-proxy-alignment.en.md).

## Next Steps {#next-steps}

- [Using the Alignment Editor](tutorial-editor-guide.en.md) — detailed editor guide with all available tools.
- [Working with Multiple Batches](tutorial-multiple-batches.en.md) — managing conflicts across batches in long texts.
- [Checking Alignment Quality](tutorial-quality-check.en.md) — how to verify your alignment is correct.
