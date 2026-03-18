# Tutorial: Using the Alignment Editor {#editor-guide}

The alignment editor is a powerful tool for reviewing and fine-tuning aligned sentence pairs. This tutorial covers every feature available in the editor, from navigation and pagination to editing cells, using candidates, and managing rows.

## Accessing the Editor {#accessing}

The editor is located in the **Editor** section on the alignment detail page. It becomes available after at least one batch has been processed. You can also jump directly to a specific location from the Conflicts section or from a visualization card by clicking **"Open in editor"**.

## Editor Layout {#layout}

The editor displays aligned sentence pairs in a two-column table:

![Alignment editor](assets/screenshots/aligner-alignment-editor.png)

Each row represents one aligned pair and contains:

- **Row number** — sequential numbering (#1, #2, #3, ...).
- **Source line IDs** — the original line numbers from both texts, shown in small text. These IDs are preserved through all editing operations, maintaining the link to the source documents.
- **Source text** (left column) — a sentence from the source ("from") language.
- **Target text** (right column) — the corresponding sentence from the target ("to") language.

![Editor row detail](assets/screenshots/aligner-editor-row.png)

Empty cells may appear where the alignment has gaps — for instance, when a sentence in one language has no counterpart in the other.

## Navigation and Pagination {#navigation}

### Page Controls {#page-controls}

The editor uses pagination to handle large alignments. The controls are located at the top and bottom of the editor section:

- **Per page** selector — choose how many rows to display per page: 10, 20, or 50. Fewer rows per page makes detailed review easier; more rows gives better context.
- **Page indicator** — shows the current page and total pages (e.g., "Page 3 of 15").
- **Prev / Next** buttons — navigate between pages.
- **Go to page** — enter a specific page number and click **"Go"** to jump directly to it.

### Tips for Navigation {#navigation-tips}

- When reviewing the full alignment, start with 20 or 50 lines per page to scan quickly for obvious issues.
- Switch to 10 lines per page when working on detailed edits.
- Use the "Go to page" feature to jump to specific areas identified by the conflict viewer or visualization.
- The editor remembers your pagination preferences for each alignment.

## Cell Actions {#cell-actions}

Hover over any cell (source or target) to reveal action buttons. These are the tools you use to edit the alignment.

### Edit {#action-edit}

Click the **Edit** button (pencil icon) to enter edit mode for that cell. The cell becomes a text input field where you can modify the content.

Use cases:

- **Fix typos or OCR errors** in the source text.
- **Combine text** from adjacent cells by copying content from one cell and pasting it into another.
- **Clean up** unwanted characters or formatting artifacts.

After editing, save your changes. The original line IDs are preserved — only the text content changes.

### Candidates {#action-candidates}

Click the **Candidates** button to browse alternative sentence matches for a cell. The system retrieves sentences from the same text that could potentially be placed in this cell, ranked by semantic similarity to the sentence in the opposite column.

Use the **Previous** and **Next** buttons to cycle through candidates. When you find the correct match, confirm the selection.

This feature is particularly useful for:

- **Fixing mismatches** — when the wrong sentence ended up in a cell.
- **Resolving conflicts** — finding the correct partner for a sentence among several candidates.
- **Verifying alignment** — checking that the current match is indeed the best one.

### Delete {#action-delete}

Click **Delete** to remove a sentence from the cell. The cell becomes empty, but the row itself remains.

Use cases:

- **Remove unmatched content** — translator's notes, footnotes, or extra sentences that have no counterpart.
- **Clear a misplaced sentence** — when a sentence ended up in the wrong position and needs to be placed elsewhere.

Deletion does not remove the sentence from the source document — it only removes it from the alignment result.

### Add Empty Row {#action-add-row}

Two buttons allow you to insert an empty row:

- **Add empty row above** — inserts a new, empty row before the current one.
- **Add empty row below** — inserts a new, empty row after the current one.

Empty rows are useful for:

- **Creating space** for manual adjustments when you need to shift sentences up or down.
- **Separating incorrectly merged content** — add an empty row, then move sentences into it.
- **Handling structural differences** — when one text has extra content that needs a placeholder in the other.

### Split Sentence {#action-split}

The **Split sentence** action allows you to split the content of a cell at a specific character position. When activated, you specify the split position, and the text is divided into two parts: the first part stays in the current cell, and the second part moves to a new row below.

This is useful when two sentences were incorrectly joined into one cell — you can split them and align each part separately.

## Working with Proxy Annotations {#proxy-annotations}

When proxy documents are loaded for the alignment, the editor can display proxy translations as subscript annotations below each sentence.

Toggle proxy visibility using the **"Show subscriptions"** switch in the Conflicts panel header.

Proxy annotations are helpful for:

- **Verifying alignment of unfamiliar languages** — if you do not read one of the languages, the proxy translation (in a language you know) helps you confirm that the pairs are correct.
- **Checking proxy quality** — comparing the proxy translation against the original to spot any issues.

## Editor Workflow Recommendations {#workflow}

### For Quick Reviews {#quick-review}

1. Set lines per page to 50.
2. Scan through pages, looking for obviously mismatched pairs.
3. When you spot an issue, switch to detailed editing mode.

### For Detailed Editing {#detailed-editing}

1. Set lines per page to 10 or 20.
2. Read each pair carefully.
3. Use Candidates to verify uncertain matches.
4. Use Edit and Delete to fix issues.

### For Conflict Resolution {#conflict-workflow}

1. Navigate to the conflict using "Open in editor" from the Conflicts section.
2. Examine the surrounding context — look at 5-10 rows before and after the conflict.
3. Identify the pattern (split, merge, missing content, or swap).
4. Apply the appropriate fix using the editor tools.
5. Move to the next conflict.

### Edge Review {#edge-review}

The system warns: "Pay attention to the beginning and the end of alignment. Some undetected conflicts can still be there."

Always review:

- **The first 10-20 rows** — sentence splitting at the beginning of the text can sometimes produce unexpected results.
- **The last 10-20 rows** — the end of the text is where batch boundary artifacts are most likely to appear.
- **Batch boundaries** — if you aligned multiple batches, check the rows around batch transitions.

## Pagination Settings Persistence {#persistence}

The editor saves your per-page setting and current page position for each alignment. When you leave and return to the editor, your position is restored. This is stored in your browser's local storage.

## Common Editor Scenarios {#scenarios}

### Scenario 1: Wrong Sentence in a Cell {#scenario-wrong}

You notice that a source sentence is paired with the wrong target sentence.

1. Click **Candidates** on the target cell.
2. Browse through candidates using Previous/Next.
3. Find the correct sentence and confirm.

### Scenario 2: Two Sentences in One Cell {#scenario-combined}

A cell contains two sentences that should be separate.

1. Click **Split sentence** on the cell.
2. Specify the split position (the character index where the second sentence starts).
3. Click **Split** — the text is divided into two rows.

### Scenario 3: Extra Sentence with No Counterpart {#scenario-extra}

One side has a sentence (e.g., a translator's footnote) that does not exist in the other text.

1. Click **Delete** on the unmatched cell.
2. The cell becomes empty. The surrounding alignment adjusts naturally.

### Scenario 4: Missing Row {#scenario-missing}

A sentence exists in one text but has no row in the alignment.

1. Click **Add empty row above** or **below** at the nearest position.
2. Use **Candidates** on the new empty cell to find and insert the missing sentence.

## Next Steps {#next-steps}

- [Resolving Alignment Conflicts](tutorial-conflict-resolution.en.md) — conflict-specific resolution strategies.
- [Checking Alignment Quality](tutorial-quality-check.en.md) — verifying that your edits produced good results.
- [Exporting Your Results](tutorial-export-formats.en.md) — exporting the finished alignment.
