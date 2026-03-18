# Understanding Alignment Visualization {#visualization}

Lingtrain Aligner provides visual representations of alignment quality for each processed batch. These visualizations — primarily scatter plots — give immediate insight into how well the alignment performed and where problems may exist. This page explains how to read and interpret these plots.

## The alignment scatter plot {#scatter-plot}

The primary visualization is a scatter plot where:

- The **x-axis** represents source sentence indices (position in the source text)
- The **y-axis** represents target sentence indices (position in the target text)
- Each **point** represents an alignment chain — a connection between one or more source sentences and one or more target sentences

In a perfect alignment, where sentence `i` in the source maps to sentence `i` in the target, all points would fall exactly on the diagonal line from bottom-left to top-right. Real alignments deviate from this ideal in characteristic ways that reveal information about the texts and the alignment quality.

## What good alignment looks like {#good-alignment}

A high-quality alignment produces a scatter plot with the following characteristics:

- **Points follow the diagonal**: The overall trend is a clear line from the origin to the upper-right corner. This confirms that the source and target texts follow the same narrative order.
- **Points are tightly clustered around the diagonal**: Small deviations from the diagonal are normal (due to sentence splits, merges, and minor reordering), but the points should not scatter widely.
- **No large gaps**: The points are distributed relatively evenly along the diagonal. Large gaps indicate passages where alignment failed or where significant content was added or omitted in the translation.
- **Consistent density**: The spacing between points is roughly uniform. Variable density can indicate regions of different translation fidelity.

A clean diagonal with tight clustering is the hallmark of a well-aligned text pair with a faithful, well-structured translation.

## Common patterns {#common-patterns}

### Clean diagonal {#clean-diagonal}

**Appearance**: Points form a straight, tight line along the diagonal.

**What it means**: The source and target texts are closely matched in structure. Sentences correspond nearly one-to-one, with few splits, merges, or omissions. This pattern is typical of:

- Technical or legal translations (where precision requires close structural correspondence)
- Subtitles (constrained by timing to follow source structure)
- Parallel texts from international organizations (parliamentary proceedings, UN documents)
- Translations between closely related languages

**Action needed**: None — this is the ideal result.

### Stepped pattern {#stepped}

**Appearance**: The plot follows the diagonal but with visible "steps" — horizontal or vertical segments where multiple source sentences map to the same target sentence (horizontal step) or one source sentence maps to multiple target sentences (vertical step).

**What it means**: The translator merged or split sentences. This is extremely common in literary translation, where translators routinely adjust sentence boundaries for stylistic reasons. A stepped pattern is perfectly normal and does not indicate an alignment problem, as long as:

- The steps are small (2-3 sentences)
- The overall trend remains diagonal
- The steps don't pile up in one region

**Action needed**: Usually none. The conflict resolution algorithm handles these cases automatically by creating multi-sentence chains (2:1, 1:2, etc.).

### Drift pattern {#drift}

**Appearance**: The line of points gradually shifts away from the true diagonal, becoming offset either above or below.

**What it means**: The source and target texts differ in total sentence count, causing a progressive offset. This happens when:

- The translator added explanatory content (extra sentences in the target)
- The translator condensed passages (fewer sentences in the target)
- Sentence splitting produced different counts in each language

Drift is problematic if it exceeds the alignment window, because the aligner will start looking for matches in the wrong region of the target text.

**Action needed**: Adjust the **shift** parameter to compensate for the offset, or increase the **window** parameter to cover a wider search range. For severe drift, consider splitting the text into smaller sections and aligning them separately.

### Scattered pattern {#scattered}

**Appearance**: Points are spread widely around the diagonal, with many points far from the expected position.

**What it means**: The alignment is struggling. Possible causes:

- **Poor embedding model fit**: The chosen model does not handle this language pair well
- **Free translation**: The translator departed significantly from the source structure
- **Wrong texts**: The texts may not actually be translations of each other (or are different editions/versions)
- **Bad sentence splitting**: Splitting errors have fragmented sentences, producing poor embeddings
- **Batch size too large**: Very large batches can accumulate errors

**Action needed**: This pattern requires investigation. Check the sentence splitting output, try a different embedding model, reduce batch size, or try using a proxy text. Scattered alignments will have many conflicts and low chain scores.

### Parallel lines {#parallel-lines}

**Appearance**: Two or more distinct diagonal lines running in parallel.

**What it means**: A section of text is present in one language but absent in the other. The most common cause is a foreword, introduction, or afterword that exists in the translation but not the original (or vice versa). After the extra section, the alignment "jumps" to a second parallel line.

**Action needed**: Remove the non-matching sections from the input texts before alignment, or align the texts in segments (before the extra section and after it).

### Cluster pattern {#cluster}

**Appearance**: A region where points cluster into a dense blob rather than following the diagonal.

**What it means**: A localized area where the alignment broke down completely. This often happens at chapter boundaries, where both texts have short, similar sentences (chapter titles, section markers) that cross-match with each other. It can also indicate a passage where the translator heavily rearranged content.

**Action needed**: Review the conflicted chains in this region manually. Consider aligning the problematic section with smaller batch sizes or different parameters.

## How window and shift affect the plot {#window-shift-effect}

The **window** parameter determines how far from the expected position the aligner searches for matches. On the scatter plot, this corresponds to the width of the band around the diagonal where matches are considered.

- **Small window**: The band is narrow. Points must be very close to the diagonal. Good for well-structured, closely matching texts. May miss valid matches if there is drift.
- **Large window**: The band is wide. Points can be further from the diagonal. More tolerant of structural differences but increases the chance of false matches.

The **shift** parameter moves the center of the search band up or down relative to the true diagonal:

- **Positive shift**: The aligner looks for matches at higher target indices than would otherwise be expected. Use when the target text has more sentences than the source.
- **Negative shift**: The aligner looks for matches at lower target indices. Use when the target text has fewer sentences than the source.
- **Zero shift**: The aligner expects a roughly 1:1 sentence count ratio.

On the scatter plot, the shift visually moves the expected diagonal up or down.

## Using visualization to diagnose problems {#diagnosis}

When alignment results are unsatisfactory, the scatter plot is the first diagnostic tool:

1. **Check the overall shape**: Is it a clean diagonal, or is it scattered? If scattered, the fundamental alignment parameters or input data need attention.

2. **Look for drift**: Is the line of points gradually shifting away from the diagonal? If yes, adjust shift or window.

3. **Identify gap regions**: Are there sections where no points appear? This may indicate missing or extra content in one of the texts.

4. **Examine step sizes**: Are the steps small (2-3 sentences) or large (5+ sentences)? Large steps suggest structural mismatches that may need manual intervention.

5. **Check consistency across batches**: If some batches look clean and others look scattered, the problem may be localized to specific sections of the text.

## Interpreting batch cards {#batch-cards}

In Lingtrain's interface, each batch is represented by a card that includes the scatter plot along with summary statistics:

- **Batch number and sentence range**: Which portion of the text this batch covers
- **Chain count**: Total number of alignment chains in the batch
- **Conflict count**: How many conflicts were detected and resolved
- **Average chain score**: The mean similarity score across all chains in the batch
- **Chain type breakdown**: The distribution of 1:1, 2:1, 1:2, and larger chain types

A batch with a clean scatter plot, few conflicts, high average score, and predominantly 1:1 chains is well-aligned. A batch with a scattered plot, many conflicts, low scores, and many multi-sentence chains needs attention.

## Visualization limitations {#limitations}

While scatter plots are powerful diagnostic tools, they have limitations:

- **They show structure, not content**: A point on the diagonal tells you that source sentence 5 was matched to target sentence 5, but not whether the match is semantically correct. A completely wrong alignment can still produce a clean diagonal if the texts happen to have similar structure.
- **Batch boundaries**: Each batch is visualized independently. Alignment quality at batch boundaries (where one batch ends and the next begins) is not visible in individual batch plots.
- **Scale**: For very large batches (hundreds of sentences), individual points become hard to distinguish. The overall pattern is still visible, but fine-grained problems may be hidden.

For definitive quality assessment, combine visualization with manual spot-checking of individual chains, especially in regions where the plot shows anomalies.

## Summary of patterns {#summary}

| Pattern | Meaning | Typical cause | Action |
|---------|---------|---------------|--------|
| Clean diagonal | Excellent alignment | Well-matched texts | None needed |
| Stepped diagonal | Normal translation adjustments | Sentence splits/merges | Usually none; auto-resolved |
| Drift | Progressive offset | Different sentence counts | Adjust shift/window |
| Scattered | Poor alignment | Model mismatch, bad splitting, wrong texts | Investigate and re-align |
| Parallel lines | Missing/extra section | Content present in only one text | Remove non-matching sections |
| Dense cluster | Localized breakdown | Heavy restructuring, chapter boundaries | Manual review, smaller batches |
