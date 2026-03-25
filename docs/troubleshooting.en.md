# Troubleshooting Guide {#troubleshooting}

This guide covers common problems you may encounter while using Lingtrain Aligner and how to resolve them.

---

## Upload Issues {#upload-issues}

### File upload fails with "Invalid file format" {#invalid-format}

**Cause:** Lingtrain only accepts `.txt` files. Attempting to upload PDF, DOCX, EPUB, HTML, or other formats will be rejected.

**Solution:**
1. Convert your file to plain text (`.txt`) using any text editor or conversion tool
2. Make sure the file extension is exactly `.txt` (not `.txt.bak` or `.text`)
3. Save in UTF-8 encoding

### File upload fails with "File too large" {#file-too-large}

**Cause:** The uploaded file exceeds the maximum size limit (4 MB by default).

**Solution:**
1. Split your text into smaller files (e.g., by chapter or section)
2. Remove unnecessary content: tables of contents, indices, footnotes, page numbers
3. Compress whitespace and remove blank lines that are not paragraph separators

### File upload fails with "File already exists" {#file-exists}

**Cause:** A document with the same filename has already been uploaded for the same language.

**Solution:**
1. Rename your file to a unique name before uploading
2. Or delete the existing document first, then re-upload

### Uploaded text has garbled characters {#garbled-text}

**Cause:** The file is not encoded in UTF-8, or contains binary data.

**Solution:**
1. Open the file in a text editor (Notepad++, VS Code, Sublime Text)
2. Check the current encoding (usually shown in the status bar)
3. Re-save the file as "UTF-8" or "UTF-8 without BOM"
4. If the file was extracted from a PDF, use a higher-quality PDF extraction tool

---

## Sentence Splitting Issues {#splitting-issues}

### Sentences are split incorrectly {#bad-splitting}

**Cause:** The sentence splitter may mishandle abbreviations, titles (Mr., Dr., etc.), or unusual punctuation.

**Solution:**
1. Check the sentence preview after uploading — review the first few pages for obvious errors
2. For problematic abbreviations, consider preprocessing the text to remove periods from abbreviations (e.g., replace "Mr." with "Mr" before uploading)
3. If using the "General" language mode, try selecting the specific language if available — language-specific rules handle edge cases better
4. For Chinese or Japanese texts, ensure proper punctuation is used (full-width sentence-ending marks)

### Too many very short sentences {#short-sentences}

**Cause:** The text contains many line breaks within paragraphs (e.g., from PDF extraction), causing each line to be treated as a separate sentence.

**Solution:**
1. Preprocess the text to merge lines within paragraphs — replace single line breaks with spaces, keeping double line breaks (paragraph separators) intact
2. Enable the "Clean text" option during upload
3. Use a text editor's find-and-replace to normalize line breaks

### Markup tags not recognized {#tags-not-recognized}

**Cause:** Tags are not formatted correctly. The `%%%%%` prefix must be placed at the end of the line with no trailing whitespace or extra characters.

**Solution:**
1. Verify the tag format: `Text content%%%%%tag.` (the period at the end is required)
2. Remove any trailing spaces or tabs after the tag
3. Ensure there are no invisible characters (zero-width spaces, BOM markers) in the tag line
4. Check the [Markup Reference](markup-reference.en.md) for correct syntax

---

## Alignment Issues {#alignment-issues}

### Alignment produces many conflicts {#many-conflicts}

**Cause:** Multiple possible reasons:
- The texts have significant structural differences (different editions, abridged vs. full)
- The window parameter is too small for the degree of sentence count difference
- The embedding model is not well-suited for this language pair

**Solution:**
1. **Increase the window** parameter (try 60 or 80 instead of the default 40)
2. **Use interlinear translations** for low-resource languages
3. **Clean your texts** — remove content that exists in only one version (translator's notes, footnotes without counterparts)
4. **Check batch visualization** — if the diagonal is offset, adjust the shift parameter
5. **Try a different embedding model** if available

### Visualization shows broken diagonal {#broken-diagonal}

**Cause:** The sentence flow between source and target texts is out of sync, often because one text has significantly more sentences in a region.

**Solution:**
1. **Adjust the shift parameter.** If the diagonal drifts upward, use a positive shift. If it drifts downward, use a negative shift
2. **Re-align the affected batches** with the corrected shift
3. **Check for missing content** — one text may have sections not present in the other
4. If the problem persists across all batches, verify that the two texts are actually translations of each other

### "Concurrent task limit reached" error {#concurrent-limit}

**Cause:** You have reached the maximum number of simultaneous tasks allowed by your plan.

**Solution:**
1. Wait for your current running task(s) to complete
2. Check the task status indicator in the alignment page header
3. If a task appears stuck, use the "Stop" button to cancel it
4. Consider upgrading your plan for higher concurrency limits

### Alignment stuck in "Queued" state {#stuck-queued}

**Cause:** The task is waiting in the processing queue. During high-demand periods, queue wait times may be longer.

**Solution:**
1. The queue position is displayed on the alignment page — check how many tasks are ahead
2. Wait for the queue to process. Tasks are handled in priority order
3. If the task has been queued for an unusually long time (more than 30 minutes), try stopping it and re-submitting

### Alignment shows "Error" state {#error-state}

**Cause:** An error occurred during processing. The error message is displayed on the alignment page.

**Solution:**
1. Read the error message for specific guidance
2. Common errors and their fixes:
   - **"Unsupported embedding model"** — the model is no longer available. Erase the alignment and re-create with an available model
   - **Service unavailable** — a temporary infrastructure issue. Retry after a few minutes
   - **Worker timeout** — the batch was too large or the server was overloaded. Try with a smaller batch or fewer batches at a time
3. If the error persists, try erasing the alignment data and starting fresh

---

## Conflict Resolution Issues {#conflict-resolution}

### Automatic resolution does not fix all conflicts {#remaining-conflicts}

**Cause:** Large or complex conflicts (involving many sentences) cannot be resolved automatically with confidence. The resolver handles small conflicts (up to about 6x6) but skips larger ones.

**Solution:**
1. Open remaining conflicts in the **Editor** using the "Open in editor" button
2. Manually adjust sentence pairs: use candidates, split, merge, or delete operations
3. For very large conflicts, consider whether the texts genuinely correspond in that section

### Resolved conflicts reappear after re-alignment {#conflicts-reappear}

**Cause:** Re-aligning batches (especially with "Align all") overwrites all previous results, including manual edits and resolved conflicts.

**Solution:**
1. Only re-align specific batches that need correction, not all batches
2. After re-alignment, run conflict resolution again
3. Export a backup (`.lt` file) before making significant changes

---

## Editor Issues {#editor-issues}

### Editor shows empty page {#empty-editor}

**Cause:** No batches have been processed yet, or the alignment database is corrupted.

**Solution:**
1. Verify that at least one batch has been processed (check the progress indicator)
2. Try refreshing the page
3. If the problem persists, re-align the first batch

### Cannot edit alignment while processing {#editing-locked}

**Cause:** The alignment is currently being processed. Editing is disabled during processing to prevent data corruption.

**Solution:**
1. Wait for the current processing task to complete
2. Or stop the current task using the "Stop" button
3. Once the status changes to "Waiting" or "Done," editing will be re-enabled

### Candidates list shows unexpected sentences {#wrong-candidates}

**Cause:** The candidate browser shows sentences from the original split text within a configurable range. If the alignment drifted, nearby candidates may not be the expected ones.

**Solution:**
1. Use the navigation buttons (Previous / Next) to browse through more candidates
2. Adjust the candidate search range if needed
3. If the correct sentence is far from the expected position, the alignment may need shift adjustment for that batch

---

## Export Issues {#export-issues}

### HTML book has missing chapters or headings {#missing-headings}

**Cause:** Markup tags (`%%%%%h1.`, `%%%%%author.`, etc.) are missing from one or both texts, or they do not match between source and target.

**Solution:**
1. Go back to the Documents tab and check the Marks panel
2. Verify that both texts have the same number of each tag type
3. Add missing tags to the source text files and re-upload

### Export generates empty file {#empty-export}

**Cause:** No batches have been processed, or the alignment database is missing.

**Solution:**
1. Verify that at least one batch has been aligned
2. Try a different export format to isolate the issue
3. Check the alignment page for error messages

### TMX file not recognized by CAT tool {#tmx-not-recognized}

**Cause:** Some CAT tools are strict about TMX formatting or language codes.

**Solution:**
1. Verify that the TMX file opens in a text editor and contains valid XML
2. Check that the language codes in the TMX match what your CAT tool expects (e.g., "en" vs. "en-US")
3. Try importing into a different CAT tool to determine if the issue is tool-specific

### "Missing metadata mark" error during book export {#missing-mark}

**Cause:** The book export requires matching structural marks (headings, dividers) in both texts. If one text has a mark that the other lacks, export fails.

**Solution:**
1. Check the Marks panel in the Documents tab — both texts should have the same mark types
2. Add the missing marks to the text that lacks them
3. Alternatively, remove the extra marks from the text that has them

---

## Browser and Performance Issues {#browser-issues}

### Page loads slowly or becomes unresponsive {#slow-page}

**Cause:** Large alignments with thousands of sentences can require significant browser resources, especially in the editor view.

**Solution:**
1. Use a smaller "per page" setting (10 or 20 instead of 50)
2. Close other browser tabs to free memory
3. Use Chrome or Edge (based on Chromium) for best performance
4. Clear browser cache if the issue persists

### Interface elements overlap or display incorrectly {#display-issues}

**Cause:** Browser zoom level, very narrow window, or an unsupported browser version.

**Solution:**
1. Reset browser zoom to 100% (Ctrl+0 or Cmd+0)
2. Ensure your browser window is at least 1200px wide
3. Update your browser to the latest version
4. Try a different browser (Chrome, Firefox, or Edge are recommended)

### Session expired unexpectedly {#session-expired}

**Cause:** JWT tokens expire after a set period (30 days by default). Browser cookie or storage clearing can also remove the token.

**Solution:**
1. Sign in again using your credentials
2. If using browser privacy modes or extensions that clear storage, be aware they may remove your session
3. Consider using a standard browser profile for Lingtrain

---

## Getting Further Help {#further-help}

If your issue is not covered here:

1. Check the [FAQ](faq.en.md) for additional answers
2. Review the [Algorithm page](algorithm.en.md) for understanding how alignment works
3. Use the feedback form in the application to report issues
4. Include the alignment GUID, error message, and browser version in your report for faster diagnosis
