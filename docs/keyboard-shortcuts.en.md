# Keyboard Shortcuts and Tips {#keyboard-shortcuts}

This page lists keyboard shortcuts and productivity tips for working efficiently with Lingtrain Aligner.

## Browser Navigation Shortcuts {#browser-shortcuts}

Since Lingtrain is a web application, standard browser shortcuts apply throughout the interface:

| Shortcut | Action |
|----------|--------|
| `Ctrl+R` / `Cmd+R` | Reload the page (useful if the interface seems stuck) |
| `Ctrl+F` / `Cmd+F` | Browser find — search for text on the current page |
| `Ctrl+0` / `Cmd+0` | Reset zoom level to 100% |
| `Ctrl++` / `Cmd++` | Zoom in |
| `Ctrl+-` / `Cmd+-` | Zoom out |
| `Ctrl+Tab` | Switch to the next browser tab |
| `Ctrl+Shift+Tab` | Switch to the previous browser tab |
| `Alt+Left` / `Cmd+Left` | Navigate back in browser history |
| `F11` | Toggle full-screen mode (more space for the editor) |

## Application Navigation {#app-navigation}

### Tab Navigation {#tab-navigation}

The aligner has three main tabs: **Documents**, **Alignments**, and **Create**. Click the tab labels to switch between them. The active tab is highlighted.

**Tip:** Use the Documents tab for text preparation, the Alignments tab for processing, and the Create tab for export. Work through them in order for a natural workflow.

### Sidebar Navigation {#sidebar-navigation}

The left sidebar provides navigation to different sections of the application. Click the aligner icon to go to the aligner workspace.

### Language Selector {#language-selector}

On the Documents tab, the language dropdowns at the top allow quick switching between source and target language contexts. The selected languages persist across sessions.

## Editor Tips {#editor-tips}

### Efficient Page Navigation {#page-navigation}

- Use the **page number input** (between Previous and Next buttons) to jump directly to any page
- Set the **per-page count** (10, 20, or 50) based on your needs: use 10 for detailed review, 50 for quick scanning
- The editor remembers your per-page setting in local storage

### Working with Alignment Pairs {#working-with-pairs}

- **Hover over a cell** to reveal action buttons (edit, candidates, delete, add row)
- **Click the edit button** on a cell to modify its text content — press Enter or click outside to save
- **Use the candidates browser** to find the correct matching sentence when a pair is misaligned
- **Navigate candidates** with the Previous/Next arrows to scan through nearby sentences from the source text
- **Add empty rows** above or below to create space for manual adjustments

### Batch Visualization Navigation {#visualization-navigation}

- Each visualization card has an **"Open in editor"** button that scrolls the editor to the corresponding batch
- Click on batch cards in the visualization section to inspect specific batches
- Quality scores on each card give you an at-a-glance view of which batches need attention

### Conflict Navigation {#conflict-navigation}

- Use the **Previous/Next** buttons in the Conflicts section to step through conflicts one at a time
- The conflict counter (e.g., "3 / 31") shows your position in the conflict list
- Click **"Open in editor"** on any conflict to jump directly to that location in the editor
- Enable **"Handle start"** and **"Handle finish"** checkboxes only when you specifically want to address edge conflicts

## Power User Tips {#power-user-tips}

### Optimal Workflow Order {#workflow-order}

For the fastest alignment workflow:

1. **Prepare texts offline** — clean, format, and add markup tags in your text editor before uploading
2. **Upload both texts** and verify sentence counts are in the expected range
3. **Check the Marks panel** — verify tag counts match between texts before proceeding
4. **Align one batch first** — check the visualization before processing all remaining batches
5. **Adjust shift if needed** — correct the shift parameter based on the first batch's diagonal
6. **Process remaining batches** in groups of 3-5
7. **Resolve conflicts once** after all batches are done — automatic resolution handles most cases
8. **Review in editor** only for remaining conflicts — do not manually review every pair
9. **Export** — generate your desired formats

### Text Preparation Shortcuts {#text-preparation}

Use these text editor techniques before uploading:

- **Find and Replace** to batch-add markup tags (e.g., find chapter headings by pattern and append `%%%%%h1.`)
- **Regular expressions** to normalize whitespace, fix punctuation, or remove page numbers
- **Diff tools** to verify that source and target texts have similar structure

### Efficient Conflict Resolution {#efficient-resolution}

- Always run **automatic resolution first** — it handles 70-90% of conflicts without manual intervention
- After automatic resolution, sort remaining conflicts by reviewing the **smallest ones first** — they are usually quick to fix
- For large conflicts (many sentences), check whether the texts actually correspond in that region before spending time on manual alignment
- Use the **candidate browser** rather than free-text editing when possible — candidates come from the actual source text

### Local Storage Persistence {#local-storage}

Lingtrain stores the following settings in your browser's local storage:

- Selected languages (Documents tab)
- Per-page count preferences (editor)
- UI panel states (expanded/collapsed)

These persist across browser sessions, so you do not need to reconfigure them each time.

### Multiple Browser Tabs {#multiple-tabs}

You can open multiple alignments in separate browser tabs to compare them or work on several projects simultaneously. Each tab maintains its own state independently.

### Using Browser Find for Large Texts {#browser-find}

When working with large alignments in the editor:

1. Set the per-page count to 50 for maximum visible content
2. Use `Ctrl+F` / `Cmd+F` to search for specific text within the current page
3. Navigate pages to find text that is not on the current page

Note: Browser find only searches the currently rendered page, not the entire alignment. For finding a specific sentence across all pages, use the "Go to page" feature combined with your knowledge of the approximate location.

### Export Workflow Tips {#export-tips}

- **Preview before downloading** — use the "Generate preview" button in the Create tab to verify the output looks correct
- **Try different paragraph directions** — switching between source and target paragraph structure can significantly improve readability
- **Test with different styles** — the three highlight styles suit different use cases (Simple for printing, Pastel Fill for screen reading)
- **Download the .lt file** periodically as a backup — it is the only format that preserves the complete alignment state for re-import
