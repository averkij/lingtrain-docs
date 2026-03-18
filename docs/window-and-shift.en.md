# Window and Shift Parameters Explained {#window-and-shift}

The **window** and **shift** parameters are the two most important alignment settings in Lingtrain Aligner. They control where the algorithm searches for matches in the target text, directly affecting alignment accuracy. Understanding these parameters is essential for getting good results, especially on texts that deviate from a simple one-to-one sentence correspondence.

## Mathematical definition {#math}

For each source sentence at index `i` in a batch, the aligner searches for the best matching target sentence within a range defined by window and shift:

```
target_range = [i + shift - window, i + shift + window]
```

Where:
- `i` is the source sentence index (within the current batch)
- `shift` determines the center of the search range relative to `i`
- `window` determines the half-width of the search range

For example, with `window = 4` and `shift = 0`, source sentence 10 will be compared against target sentences 6 through 14 (10 - 4 = 6, 10 + 4 = 14). The aligner computes the cosine similarity between the source sentence embedding and each target sentence embedding in this range, then selects the best match.

## The window parameter {#window}

### What it does {#window-what}

The window defines how far from the expected position the aligner will search. A window of `n` means the aligner looks at `2n + 1` target sentences for each source sentence (n before and n after the expected position, plus the expected position itself).

### Default value {#window-default}

The default window size is typically 4-6, which works well for most well-structured parallel texts. This means the aligner searches across approximately 9-13 target sentences for each source sentence.

### When to increase the window {#window-increase}

Increase the window when:

- **Translation has structural differences**: The translator significantly restructured the text, splitting, merging, and rearranging sentences. A larger window gives the aligner more room to find the correct match even when sentences are displaced.
- **Sentence counts differ significantly**: If the source has 100 sentences but the target has 130, the offset accumulates and sentences near the end of a batch may be outside the default window.
- **Free or literary translation**: Literary translators often take liberties with sentence order. A larger window accommodates this.
- **Visualization shows drift**: If the scatter plot shows points drifting away from the diagonal, the window may be too small to capture the correct matches.

### When to decrease the window {#window-decrease}

Decrease the window when:

- **Texts are tightly structured**: Technical documentation, legal texts, and subtitles typically have very close structural correspondence. A smaller window reduces the chance of false positive matches.
- **Speed matters**: A smaller window means fewer similarity computations per sentence, making alignment faster.
- **False matches appear**: If the aligner is matching sentences that are clearly wrong (e.g., a sentence about topic A matching a sentence about topic B that happens to be nearby), reducing the window can help by limiting the search space.

### Trade-offs {#window-tradeoffs}

| Window size | Pros | Cons |
|-------------|------|------|
| Small (2-3) | Fast; fewer false matches | May miss valid matches if text structure differs |
| Medium (4-6) | Good balance for most texts | Default; works for typical translations |
| Large (8-15) | Handles structural differences well | Slower; more false match risk; higher memory usage |
| Very large (20+) | For extreme structural differences | Significantly slower; high false match risk |

## The shift parameter {#shift}

### What it does {#shift-what}

The shift parameter offsets the center of the search window. With `shift = 0`, the aligner expects source sentence `i` to match target sentence `i` (one-to-one correspondence). With `shift = 5`, it expects source sentence `i` to match target sentence `i + 5` (the target text has approximately 5 extra sentences before the current position).

### Why shift is needed {#shift-why}

In a perfect world, parallel texts would have the same number of sentences. In practice, they almost never do:

- Translators add explanatory sentences
- Translators condense or merge sentences
- Different sentence splitting rules produce different counts
- One text may have prefatory material (epigraphs, dedications) that the other lacks

When the sentence counts differ, a progressive offset develops. If the target has more sentences than the source, each successive source sentence corresponds to a target sentence at a higher-than-expected index. Without shift correction, the target range eventually falls behind the actual position, and the aligner starts producing poor matches.

### Computing the ideal shift {#shift-computing}

A rough estimate of the needed shift can be computed from sentence counts:

```
estimated_shift ≈ (target_count - source_count) / num_batches
```

For example, if the source text has 1000 sentences, the target has 1100 sentences, and you are using 10 batches of 100 sentences each, the shift per batch would be approximately:

```
(1100 - 1000) / 10 = 10
```

This means by the end of each batch, the offset has grown by about 10 sentences, so a shift of around 5-10 would be appropriate (centering the window on the expected midpoint offset).

In practice, the offset is rarely uniform — some sections may be translated more freely than others. Lingtrain's auto-optimize mode can calculate per-batch shifts automatically.

### Positive vs. negative shift {#shift-direction}

- **Positive shift**: Target text has more sentences than source. The aligner looks "ahead" in the target for matches. Common when the translator expanded the text or when the target language tends to use more sentences for the same content.
- **Negative shift**: Target text has fewer sentences than source. The aligner looks "behind" in the target. Common when the translator condensed the text.
- **Zero shift**: Source and target have approximately the same number of sentences. The default for most well-matched texts.

## Examples with different text ratios {#examples}

### Example 1: Close match (ratio ~1:1) {#example-close}

- Source: 500 sentences (English)
- Target: 510 sentences (French)
- Difference: 10 sentences over 500 = 2% more in target

**Recommended settings**: `window = 4`, `shift = 0`

The difference is minimal (2%). The default window of 4 can absorb this small offset across batches. No shift adjustment is needed.

### Example 2: Moderate difference (ratio ~1:1.15) {#example-moderate}

- Source: 800 sentences (English)
- Target: 920 sentences (Japanese)
- Difference: 120 sentences over 800 = 15% more in target

**Recommended settings**: `window = 6-8`, `shift = 2-3` (per batch of 100)

Japanese translations of English text often produce more sentences due to different sentence structure. The 15% difference requires a moderate window increase and a small positive shift to keep the search centered on the correct region.

### Example 3: Large difference (ratio ~1:1.3) {#example-large}

- Source: 600 sentences (English)
- Target: 780 sentences (Russian)
- Difference: 180 sentences over 600 = 30% more in target

**Recommended settings**: `window = 10-12`, `shift = 4-6` (per batch of 100)

A 30% difference is significant. The window must be large enough to span the growing offset, and the shift should compensate for the systematic difference. Consider using smaller batch sizes (50 instead of 100) to reduce the within-batch offset.

### Example 4: Extreme difference (ratio ~1:1.5+) {#example-extreme}

- Source: 400 sentences (Chinese)
- Target: 650 sentences (English)
- Difference: 250 sentences over 400 = 62% more in target

**Recommended settings**: `window = 15-20`, `shift = 8-12` (per batch of 50-80)

Chinese text is typically much more compact than English. A 60%+ difference requires aggressive parameter adjustment. Use smaller batches (50-80 sentences) to keep the within-batch offset manageable, a large window, and significant positive shift. Auto-optimize mode is strongly recommended for this scenario.

## Auto-optimize mode {#auto-optimize}

Lingtrain offers an auto-optimize mode that automatically calculates the shift parameter based on the sentence count ratio between source and target texts. This mode:

1. Computes the overall sentence count ratio
2. Estimates the expected offset at each batch boundary
3. Sets the shift parameter for each batch independently, adjusting for the cumulative offset

Auto-optimize is recommended for:
- First-time alignment (when you don't know the ideal parameters)
- Texts with significant length differences
- Texts where the offset varies across sections (e.g., some chapters are translated closely, others are paraphrased heavily)

You can still manually override the auto-calculated shift for individual batches if needed.

## Common scenarios requiring manual adjustment {#common-scenarios}

### Scenario: Chapter with long translator's note {#scenario-note}

A translator added a 20-sentence explanatory note in the middle of a chapter. The remaining sentences are offset by 20 positions. **Solution**: Increase shift for batches after the note, or split the alignment at the note boundary.

### Scenario: Condensed translation {#scenario-condensed}

The translator systematically condensed the text, producing about 80% as many sentences as the original. **Solution**: Use negative shift (approximately -20% of batch size) and a slightly larger window.

### Scenario: Different editions {#scenario-editions}

The source and target are from different editions of a book. Some chapters are in a different order. **Solution**: This is beyond what window/shift can solve. Align the matching chapters individually rather than the entire book at once.

### Scenario: Poetry with uneven line counts {#scenario-poetry}

A poetry translation where the translator sometimes uses more or fewer lines per stanza. **Solution**: Use a larger window (10-15) to handle the variability. Consider aligning stanza-by-stanza rather than line-by-line.

### Scenario: Mixed content (narrative + dialogue) {#scenario-mixed}

The text alternates between long narrative passages (translated closely) and dialogue (translated freely). **Solution**: Window of 6-8 handles most cases. If dialogue sections consistently cause problems, consider aligning them separately with a larger window.

## Relationship between window, shift, and batch size {#relationship}

These three parameters interact:

- **Larger batches** accumulate more offset within the batch, requiring larger windows and more precise shifts
- **Smaller batches** have less within-batch offset, allowing smaller windows and less critical shift values
- **Shift is per-batch**: A shift of 5 in a batch of 100 sentences compensates for a 5% difference; the same shift of 5 in a batch of 50 compensates for a 10% difference

A general guideline:
```
window ≥ abs(expected_offset_per_batch) + 3
```

Where `expected_offset_per_batch` is the predicted sentence count difference within a single batch. The `+ 3` provides margin for local variation.

## Troubleshooting {#troubleshooting}

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Good alignment at start of batch, poor at end | Shift too small; offset accumulates | Increase shift or reduce batch size |
| Poor alignment throughout batch | Window too small for the structural difference | Increase window |
| Many false matches (wrong sentence pairs) | Window too large; aligner finds spurious matches | Decrease window |
| Alignment quality varies wildly between batches | Non-uniform text structure | Use auto-optimize; consider per-batch parameter tuning |
| Scatter plot shows diagonal with wrong slope | Systematic sentence count difference | Adjust shift to match the slope |
