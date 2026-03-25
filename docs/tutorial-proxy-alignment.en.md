# Tutorial: Aligning Texts with Interlinear Translation {#proxy-alignment}

Not all language pairs work equally well with the default embedding models. When aligning texts in low-resource or distant languages, you may find that direct alignment produces many conflicts, a broken diagonal on the visualization, or simply poor quality matches. The interlinear translation feature solves this problem by using machine-translated intermediaries. This tutorial walks you through a real-world scenario.

## When to Use Interlinear Alignment {#when-to-use}

Use interlinear alignment when:

- **One or both languages are low-resource** — languages with limited representation in the embedding model's training data. Examples include Bashkir, Chuvash, Tatar, Yakut, Buryat, or many African and Southeast Asian languages.
- **Direct alignment produces poor results** — many conflicts, scattered dots in the visualization instead of a clean diagonal, or obviously wrong sentence matches.
- **The language pair is distant** — the embedding model may struggle with very different language families (e.g., Japanese-Finnish, Korean-Swahili).
- **You are working with a non-standard script** — some models handle certain scripts better than others.

You generally do **not** need interlinear translation for well-supported pairs like English-Russian, English-German, English-French, English-Spanish, English-Chinese, or other major language combinations.

## Real-World Scenario: Aligning Chuvash-Russian {#scenario}

Let us walk through a practical example. Imagine you are working on a digital preservation project for the Chuvash language and you have:

- A short story in **Chuvash** (original).
- The same story translated into **Russian**.

You want to create a parallel Chuvash-Russian book. However, because Chuvash is a low-resource language, the default embedding model has limited training data for it, and direct alignment may produce poor results.

The solution: translate the split Chuvash text into Russian using a machine translation service, then use this translation as an interlinear document during alignment.

## Step 1: Upload and Split Texts {#step-upload}

Start by uploading both texts on the **Documents** tab as you would for a regular alignment:

1. Set the **From** language to Chuvash and the **To** language to Russian.
2. Upload the Chuvash text file on the left panel.
3. Upload the Russian text file on the right panel.
4. Add markup tags (`%%%%%author.`, `%%%%%title.`) to both files before uploading.

Review the sentence preview for both documents. Verify that the splitting looks correct and that markup tags match between the two texts.

## Step 2: Create the Alignment {#step-create}

Go to the **Alignments** tab and create a new alignment:

1. Click **"+ Create alignment"**.
2. Name it descriptively (e.g., "Chuvash Story CV-RU").
3. Select the Chuvash document as the source and the Russian document as the target.
4. Click **"Create alignment"**.

Open the alignment detail page. Do **not** run the alignment yet — we need to prepare the interlinear translation first.

## Step 3: Download the Split Text {#step-download-split}

Before creating the interlinear document, you need the split version of the text. The split version has one sentence per line, exactly as the alignment algorithm will process it.

Go back to the **Documents** tab:

1. Click on the Chuvash document to open its sentence preview.
2. Click the **download** button in the sentence preview panel.
3. Save the downloaded file — it contains one sentence per line, with markup tags preserved.

This is the file you will translate to create the interlinear document.

## Step 4: Translate the Split Text {#step-translate}

Translate the downloaded split text using any machine translation service. The key requirement is that **the translation must have exactly the same number of lines** as the original split text. Each line in the interlinear document must correspond to the sentence at the same position in the original.

### Using Google Translate {#google-translate}

1. Open Google Translate in your browser.
2. Set the source language to Chuvash (if available) or auto-detect.
3. Set the target language to Russian.
4. Paste the content of the split text file.
5. Copy the translated output.
6. Save it as a new `.txt` file (e.g., `chuvash_interlinear_ru.txt`).

### Using DeepL {#deepl}

1. Open DeepL Translator.
2. Upload the split text file or paste its content.
3. Select Chuvash (if supported) as the source and Russian as the target.
4. Download or copy the translated text.
5. Save as a `.txt` file.

### Using Yandex Translate {#yandex}

Yandex Translate has good coverage of Turkic languages, including Chuvash, Bashkir, and Tatar, making it a particularly good choice for these language pairs.

### Important: Verify Line Count {#verify-lines}

After translation, verify that the interlinear file has **exactly the same number of lines** as the original split text. Open both files and compare line counts. If the translator merged or split lines, you need to fix the interlinear document to restore one-to-one line correspondence.

Common issues:

- **Merged lines** — the translator combined two short lines into one. Split them back.
- **Extra empty lines** — the translator added blank lines. Remove them.
- **Missing lines** — some lines were dropped during translation. Add placeholder translations.

## Step 5: Upload the Interlinear Translation {#step-upload-proxy}

Return to the alignment detail page. The **Interlinear** section shows upload areas for both source ("from") and target ("to") languages.

### Upload the Interlinear Document {#upload-proxy-doc}

For our scenario, we translated the **Chuvash** (source) text into Russian. So we upload the interlinear document on the **"from"** side:

1. Drag and drop the interlinear file (`chuvash_interlinear_ru.txt`) onto the "Interlinear (from)" upload area.
2. After upload, the status changes to **"Loaded"** with the filename displayed.

You can also:

- Click the **download** button to retrieve the uploaded interlinear translation.
- Click **open** to view the interlinear text in a new tab.
- Click **remove** to delete it and upload a different version.

If you also want to create an interlinear translation for the Russian (target) text — for example, translating it into English — you can upload it on the "Interlinear (to)" side. This is useful when aligning two low-resource languages through a common bridge language.

## Step 6: Enable Interlinear Translation in Settings {#step-enable}

In the **Settings** panel on the alignment detail page:

1. Check the **"Use interlinear (from)"** checkbox to enable the Chuvash interlinear translation.
2. If you also uploaded a target interlinear document, check **"Use interlinear (to)"** as well.

These checkboxes become active only after the corresponding interlinear documents are uploaded.

## Step 7: Run the Alignment {#step-run}

Now run the alignment as usual:

1. Click **"Align next"** to process the first batch.
2. Wait for processing to complete.
3. Check the visualization — you should see a much cleaner diagonal compared to what direct alignment would produce.
4. Continue with remaining batches.

When interlinear translation is enabled, the aligner computes embeddings from the interlinear translations instead of the original texts. But the original texts are preserved in the alignment result — the interlinear translation is only used during the matching step.

## Step 8: Verify Results {#step-verify}

After alignment:

1. **Check the visualization** — the diagonal should be clean. If it is still broken, the interlinear translation quality may be insufficient, or there may be structural differences between the texts.
2. **Resolve conflicts** — click "Resolve all" as usual.
3. **Review in the editor** — enable **"Show interlinear"** to see interlinear annotations below each sentence. This helps verify alignment quality even if you do not read Chuvash.

The interlinear annotations appear as small text beneath each sentence, showing the machine translation that was used for alignment. Compare these with the Russian target text to verify that the pairs are semantically correct.

## Interlinear for Both Sides {#both-sides}

In some cases, you may want to use interlinear translation for both the source and target languages. This is useful when:

- Both languages are low-resource (e.g., Chuvash and Bashkir).
- You have a common bridge language (e.g., Russian) that both can be translated into.

The workflow is the same — download split texts for both documents, translate both into the bridge language, upload both interlinear documents, and enable both interlinear checkboxes.

## Tips for Better Interlinear Alignment {#tips}

1. **Choose the best available translation service** for your language pair. Yandex Translate often works better for Turkic and Slavic languages; Google Translate has broader coverage overall; DeepL excels for European languages.
2. **Verify the line count** of the interlinear document. Mismatched line counts will cause the upload to fail or produce incorrect alignment.
3. **Use a well-supported bridge language.** Russian or English typically work best, depending on which language the source is closer to culturally and linguistically.
4. **Check interlinear quality on a sample.** Before processing the entire text, manually check a few interlinear translations. Poor interlinear quality will lead to poor alignment.
5. **Consider interlinear for only one side.** If one language is well-supported and the other is not, you only need interlinear translation for the low-resource side.
6. **Keep the original interlinear file.** If you need to re-align or adjust, having the interlinear document ready avoids repeating the translation step.

## Limitations {#limitations}

- **Interlinear quality depends on machine translation quality.** If the MT service produces poor translations for your language, the interlinear translation may not help much.
- **Line count must match exactly.** Any mismatch will cause issues.
- **Interlinear translation adds a step** to the workflow but is usually worth it for low-resource languages.
- **The final result contains original texts**, not interlinear translations. The interlinear translation is used only for the matching step.

## Next Steps {#next-steps}

- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — basic alignment workflow without interlinear translation.
- [User Story: Preserving a Minority Language](tutorial-minority-language.en.md) — a deeper narrative on using interlinear translation for language preservation.
- [Checking Alignment Quality](tutorial-quality-check.en.md) — verifying results after interlinear alignment.
