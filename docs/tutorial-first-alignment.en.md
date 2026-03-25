# Tutorial: Your First Alignment {#first-alignment}

This step-by-step tutorial walks you through the entire process of creating your first text alignment with Lingtrain Aligner — from creating an account to exporting a finished parallel book. By the end, you will have a bilingual HTML book ready to read in any browser.

## Prerequisites {#prerequisites}

Before you begin, you will need:

- Two text files in different languages — for example, a short story in English and its Russian translation.
- The texts should be in plain text format (`.txt`), saved with UTF-8 encoding.
- Each text should be a few pages long (a short story or a single chapter works well for a first try).

If you do not have texts ready, see [Preparing Texts for Alignment](tutorial-prepare-texts.en.md) for guidance on finding and formatting source materials.

## Step 1: Create an Account {#step-create-account}

Open the Lingtrain Aligner website. You will see the landing page with a brief overview of the platform's capabilities.

![Home page — guest view](assets/screenshots/home-page-guest.png)

Click **"Sign in"** in the top-right corner of the page. The sign-in dialog will appear.

![Sign-in dialog](assets/screenshots/sign-in-dialog.png)

If you already have an account, enter your email and password and click **"Sign in"**. Otherwise, click **"Create account"** to register:

1. Enter your **email address**.
2. Choose a **password** (at least 6 characters).
3. Confirm the password.
4. Agree to the terms of use and personal data processing policy.
5. Click **"Register"**.

You will receive a verification code at the email address you provided. Enter the six-digit code on the verification screen to confirm your account. After verification, you will be prompted to enter your display name — this is how the app will greet you on the home page.

Alternatively, you can sign in using **Google**, **Yandex**, or **VK** OAuth providers if they are available in your region.

## Step 2: Navigate to the Aligner {#step-navigate}

After signing in, you land on the home page. The home page shows your workspace with available applications and recent activity.

![Home page — authenticated](assets/screenshots/home-page-authenticated.png)

Click on the **"Aligner"** card to open the text alignment tool. The Aligner has three main tabs:

- **Documents** — upload and manage source text files.
- **Alignments** — create and run alignment projects.
- **Create** — preview and export aligned results.

## Step 3: Upload Your Texts {#step-upload}

You start on the **Documents** tab. This is where you upload the texts you want to align.

### Select Languages {#select-languages}

At the top of the Documents page, you will see two language selectors labeled **"From"** and **"To"**. Select the language of your source text on the left and the language of the target text on the right. For this tutorial, we will use **English** as the source and **Russian** as the target.

![Documents page — empty state](assets/screenshots/aligner-documents-empty-en.png)

The language selection determines which sentence-splitting rules are applied when your text is processed.

### Upload the Source Text {#upload-source}

In the left panel (the "From" language), you will see a drag-and-drop upload area. Either drag your English text file onto it or click to open a file picker.

The **"Clean text"** checkbox applies basic text normalization during upload — it removes extra whitespace and fixes common encoding issues. Enable it if your text has formatting artifacts from copy-pasting.

After upload, your document appears in the list below the upload area.

![Documents page — source uploaded](assets/screenshots/aligner-documents-en-uploaded.png)

### Upload the Target Text {#upload-target}

Repeat the same process in the right panel (the "To" language) with your Russian text file.

![Documents page — both texts uploaded](assets/screenshots/aligner-documents-both-uploaded.png)

### Preview the Split Result {#preview-split}

Click on a document in the list to see how it was split into sentences. The **Sentence preview** panel shows numbered lines, each representing one sentence that the alignment algorithm will work with.

Lines with markup tags (author, title, headings) display colored badges. The `paragraph` badge marks the last sentence in each paragraph — this information is used later to reconstruct paragraph structure in the exported book.

You can adjust the number of lines per page (10, 20, or 50), navigate between pages, and download the split text as a file.

![Documents page — full view with preview](assets/screenshots/aligner-documents-full-page.png)

Take a moment to scan through the sentences and verify that the splitting looks reasonable. If sentences are cut in the wrong places, your source text may need formatting adjustments (see [Preparing Texts](tutorial-prepare-texts.en.md)).

## Step 4: Create an Alignment {#step-create-alignment}

Switch to the **Alignments** tab. If this is your first alignment, you will see an empty state.

![Alignments tab — empty](assets/screenshots/aligner-alignments-empty.png)

Click **"+ Create alignment"** in the top-right corner. A dialog appears:

![Create alignment dialog](assets/screenshots/aligner-create-alignment-dialog.png)

1. **Name** — give your alignment a descriptive name, for example "The Blue Geranium EN-RU".
2. **Source document** — select the English document you uploaded.
3. **Target document** — select the Russian document.

Click **"Create alignment"** to initialize the project. The system will split your texts into batches and prepare the alignment database.

Your new alignment appears in the list with the state **"Init"** and progress **"0/N"** (where N is the total number of batches).

![Alignment created](assets/screenshots/aligner-alignment-created.png)

Click on the alignment to open its detail page.

## Step 5: Configure Settings {#step-settings}

On the alignment detail page, you will see several panels: **Settings**, **Controls**, **Visualization**, **Conflicts**, and **Editor**.

![Settings panel](assets/screenshots/aligner-settings-panel.png)

The **Settings** panel controls how the alignment runs:

- **Batch size** — number of sentences from the source text per batch (default: 200). This value is fixed after creation.
- **Batch count** — how many batches to process in the next run. Leave it at 1 for your first alignment to check the results batch by batch.
- **Window** — extra sentences added on each side of the target window (default: 40). A larger window catches more matches but takes longer.
- **Batch shift** — manual offset for the target window. Leave at 0 for now.
- **Embedding model** — the neural model used to compute sentence embeddings. The default model works well for most European language pairs.

For your first alignment, the default settings are fine. You can always adjust them later.

## Step 6: Run the Alignment {#step-run}

In the **Controls** panel, click **"Align next"** to start processing the first batch.

![Controls panel](assets/screenshots/aligner-controls-panel.png)

The alignment status changes to **"Queued"** while waiting for a processing slot, then to **"In progress"** during computation.

![Alignment queued](assets/screenshots/aligner-alignment-queued.png)

During processing, the system:

1. Computes sentence embeddings using the configured ML model.
2. Builds a cosine similarity matrix between source and target sentences.
3. Selects the best match for each source sentence.
4. Saves results and generates a visualization.

After the first batch completes, the status changes to **"Waiting"**. The progress indicator updates (e.g., "1/3" means 1 out of 3 batches processed).

### Check the Visualization {#check-visualization}

The **Visualization** section shows a scatter plot for the processed batch. Each dot represents an aligned sentence pair.

A good alignment shows dots forming a **diagonal line** from bottom-left to top-right. If the diagonal is clean, your alignment is working well. If you see scattered dots or a broken diagonal, you may need to adjust the **shift** parameter or prepare your texts more carefully.

Each visualization card shows the batch number, the window and shift parameters used, and the source and target line ranges.

### Continue Processing {#continue-processing}

If the first batch looks good, click **"Align next"** again to process the next batch. Repeat until all batches are processed.

Alternatively, you can click **"Align all"** to process all remaining batches in one go. This is convenient once you are confident the settings are correct.

## Step 7: Resolve Conflicts {#step-conflicts}

After all batches are processed, the **Conflicts** section shows any mismatches that need resolution.

![Conflicts section](assets/screenshots/aligner-conflicts-section.png)

A conflict occurs when the alignment algorithm cannot establish a clean one-to-one mapping between source and target sentences. This is normal and happens because translators routinely split, merge, or rearrange sentences.

The conflict viewer shows:

- The total number of conflicts found.
- Navigation buttons to browse through conflicts one by one.
- The **"From"** and **"To"** panels showing the conflicting sentence groups with their line numbers.

### Automatic Resolution {#auto-resolve}

Click **"Resolve all"** to run the automatic conflict resolution algorithm. The system iteratively resolves conflicts from smallest to largest, picking the best grouping based on semantic similarity scores.

After automatic resolution, the conflict count should drop significantly — often to zero. The remaining conflicts, if any, need manual attention in the editor.

### Manual Resolution {#manual-resolve}

For detailed guidance on resolving conflicts manually, see [Tutorial: Resolving Alignment Conflicts](tutorial-conflict-resolution.en.md).

## Step 8: Review in the Editor {#step-editor}

The **Editor** section shows aligned sentence pairs in a side-by-side view.

![Alignment editor](assets/screenshots/aligner-alignment-editor.png)

Each row displays:

- A row number.
- The source text (left) and the target text (right).
- Source line IDs — original line numbers from both texts.

![Editor row detail](assets/screenshots/aligner-editor-row.png)

Scroll through the aligned pairs and verify that the sentences match. Pay special attention to the **beginning and end** of the alignment, where undetected conflicts sometimes hide.

If you find mismatches, hover over a cell to reveal action buttons:

- **Edit** — modify the text content.
- **Candidates** — browse alternative sentence matches.
- **Delete** — remove a sentence from the cell.
- **Add empty row** — insert an empty row above or below for manual adjustments.

For a complete guide to the editor, see [Tutorial: Using the Alignment Editor](tutorial-editor-guide.en.md).

## Step 9: Export Your Results {#step-export}

Once you are satisfied with the alignment quality, switch to the **Create** tab.

![Create tab — empty state](assets/screenshots/aligner-create-empty.png)

### Configure Export Settings {#export-settings}

Select your alignment from the dropdown list. Then configure the output:

- **Paragraph structure** — use paragraph breaks from the source ("from") or target ("to") text.
- **Language order** — which language appears on the left side (first) in the parallel view.
- **Highlight style** — visual styling for the parallel book:
  - **Without highlight** — no background colors.
  - **Pastel** — solid pastel-colored backgrounds for each language.
  - **Gradient** — gradient backgrounds that fade out.

### Generate Preview {#generate-preview}

Click **"Generate preview"** to see a live preview of your parallel book. The preview shows how the final HTML book will look with your current settings.

![Alignment result preview](assets/screenshots/aligner-alignment-result.png)

Adjust settings and regenerate the preview until you are satisfied.

### Download the Book {#download-book}

Below the preview, you will find download buttons for all available formats:

- **HTML book** — a styled parallel book ready to read in any browser or on a tablet.
- **TMX corpora** — Translation Memory eXchange format for CAT tools.
- **Sentence corpora** — plain text with one aligned sentence per line.
- **Paragraph corpora** — plain text grouped by paragraphs.
- **Structured formats** — XML and JSON for custom processing.
- **Alignment database** — Lingtrain `.lt` format for backup or re-import.

Click the download button next to **"HTML book"** to save your parallel book.

![Full alignment result](assets/screenshots/aligner-alignment-result-full.png)

## Congratulations! {#congratulations}

You have created your first text alignment and exported it as a parallel book. Open the downloaded HTML file in your browser to read your bilingual text with side-by-side sentences.

## What Next? {#what-next}

Now that you have completed your first alignment, you can explore more advanced features:

- [Preparing Texts for Alignment](tutorial-prepare-texts.en.md) — learn how to format texts with markup tags for better results.
- [Resolving Alignment Conflicts](tutorial-conflict-resolution.en.md) — master the conflict resolution workflow.
- [Using the Alignment Editor](tutorial-editor-guide.en.md) — learn all the editor tools.
- [Working with Multiple Batches](tutorial-multiple-batches.en.md) — manage alignment of long texts.
- [Aligning Texts with Interlinear Translation](tutorial-proxy-alignment.en.md) — use interlinear translations for low-resource languages.
- [Exporting Your Results](tutorial-export-formats.en.md) — explore all export formats and their use cases.

## Tips for Best Results {#tips}

1. **Prepare texts carefully** — remove page numbers, footnotes, and extraneous content before uploading.
2. **Use markup tags** — add `%%%%%author.`, `%%%%%title.`, and `%%%%%h1.` tags to your texts for proper formatting in the exported book.
3. **Start with default settings** — batch size 200 and window 40 work well for most texts.
4. **Check the visualization early** — after the first batch, verify the diagonal. If it is broken, adjust parameters before continuing.
5. **Resolve conflicts automatically first** — the automatic resolver handles most cases. Use the editor for the remaining few.
6. **Save your work** — download the `.lt` alignment database as a backup so you can re-import it later.
