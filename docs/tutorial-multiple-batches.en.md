# Tutorial: Working with Multiple Batches {#multiple-batches}

When you align a long text, the system divides it into batches for processing. Understanding how batches work, how to adjust parameters between batches, and how to handle drift is essential for getting good results on longer texts (novels, manuals, collections of stories). This tutorial covers the batch workflow in detail.

## How Batches Work {#how-batches-work}

The alignment system divides the source text into fixed-size segments called batches. Each batch processes a portion of the source text against a corresponding window of the target text.

Given a source text of `N` sentences and a configured batch size of `B`:

- **Batch 1** processes source sentences 1 through B.
- **Batch 2** processes source sentences B+1 through 2B.
- And so on until all source sentences are covered.

For each source batch, the target window is calculated proportionally:

- The proportionality ratio `k = len(target) / len(source)` determines the approximate position.
- The **window** parameter adds extra sentences on each side of the proportional target range.
- The **shift** parameter applies a manual offset to the target window.

The total number of batches is shown in the progress indicator (e.g., "0/5" means 5 batches, none processed yet).

## Understanding Batch Progress {#batch-progress}

The progress indicator on the alignment detail page shows the current state:

- **0/3** — 3 batches total, none processed. Status: "Init".
- **1/3** — first batch processed. Status: "Waiting" (waiting for user to continue or review).
- **2/3** — two batches processed.
- **3/3** — all batches processed. Status: "Done" (if conflicts are resolved) or "Waiting" (if conflicts remain).

## Batch-by-Batch vs. Align All {#batch-vs-all}

You have two options for processing:

### Batch-by-Batch (Recommended for First Use) {#batch-by-batch}

1. Set **Batch count** to 1 in Settings.
2. Click **"Align next"** to process one batch.
3. Check the visualization.
4. If the diagonal looks good, click "Align next" again.
5. If the diagonal is off, adjust **shift** and re-align.

This approach gives you control and lets you catch problems early before they compound across batches.

### Align All {#align-all}

1. Set **Batch count** to a high number or click **"Align all"**.
2. All remaining batches process sequentially.
3. Review the visualization for all batches when done.

This is faster and works well when you are confident the texts are well-prepared and the language pair is well-supported.

## The Visualization Cards {#visualization-cards}

After processing, each batch gets a visualization card in the **Visualization** section.

![Batch card](assets/screenshots/aligner-batch-card.png)

Each card shows:

- **Batch number** (e.g., "Batch 1", "Batch 2").
- **Window (w) and shift (s) parameters** used for that batch.
- **Source and target line ranges** (e.g., "en 1-199, ru 1-242").
- **A scatter plot** showing aligned sentence positions.
- **"Open in editor"** button to jump to that batch's sentences in the editor.

### Reading the Scatter Plot {#scatter-plot}

Each dot represents one aligned pair. The X-axis is the source sentence position, and the Y-axis is the matched target position.

- **Clean diagonal line** — excellent alignment. The model found correct matches throughout.
- **Diagonal with minor wiggles** — good alignment. Small deviations are normal (sentence splits/merges).
- **Broken diagonal** — the alignment drifted. Some sentences were matched far from their expected position. Adjust shift and re-align.
- **Scattered dots** — poor alignment. The model could not find reliable matches. Consider using proxy, adjusting window, or improving text preparation.

### Overall Chain Score {#chain-score}

The visualization summary shows the overall chain score across all processed batches (e.g., "Aligned 3 batches, overall chain score: 0.97").

The chain score measures alignment continuity:

```
score = 1 - (breaks / total_lines)
```

- **1.0** — perfect alignment, no breaks in the sequence.
- **0.95-0.99** — good alignment with minor issues.
- **0.90-0.95** — acceptable but review recommended.
- **Below 0.90** — significant issues, manual review or re-alignment needed.

## Adjusting Shift Between Batches {#adjusting-shift}

The **shift** parameter is the most important adjustment you will make when working with multiple batches. It compensates for differences in sentence count between the source and target texts.

### When to Adjust Shift {#when-to-adjust}

- The visualization shows the diagonal **drifting** — starting aligned but gradually moving away from the expected position.
- The dots are **clustering** near one edge of the plot rather than following the diagonal.
- Conflicts are concentrated **at batch boundaries**.
- The source and target texts have **significantly different sentence counts** (e.g., 800 source vs. 1,100 target).

### How to Adjust {#how-to-adjust}

1. Look at the visualization for the latest batch.
2. If the dots are **above** the diagonal (target matches are at higher positions than expected), the target text is "running ahead" — use a **negative** shift to pull it back.
3. If the dots are **below** the diagonal, the target text is "falling behind" — use a **positive** shift to push it forward.
4. Start with small adjustments (shift = 5 or -5) and increase if needed.

### Per-Batch Adjustment {#per-batch}

Each batch can be individually re-aligned with different parameters. To re-align a specific batch:

1. Click on the batch card in the visualization.
2. In the batch dialog, adjust the **shift** and **window** values.
3. Click **"Recalculate"** to re-align that batch with the new parameters.

This does not affect other batches — only the selected batch is re-processed.

## Handling Drift {#handling-drift}

Drift occurs when the cumulative difference in sentence counts between source and target causes the alignment to gradually lose synchronization. It is the most common challenge with long texts.

### Detecting Drift {#detecting-drift}

- **Batch 1** visualization: clean diagonal.
- **Batch 2** visualization: diagonal slightly offset.
- **Batch 3** visualization: diagonal noticeably off-center or broken.

This pattern indicates drift — the proportional window calculation is no longer accurately predicting where target sentences should be.

### Fixing Drift {#fixing-drift}

1. **Check the current batch's visualization** — note how far the diagonal has shifted.
2. **Adjust shift** for the next batch. A good starting point: if the diagonal shifted by about 20 target sentences, set shift to -20 (or +20, depending on direction).
3. **Re-align the affected batch** if it was already processed.
4. **Consider adjusting window** — a larger window (e.g., 60 instead of 40) gives the algorithm more room to find correct matches even when the proportional estimate is slightly off.

### Auto-Optimize Parameters {#auto-optimize}

The **"Auto-optimize parameters"** feature can automatically determine the best shift and window values for each batch. When enabled, the system analyzes the alignment pattern and adjusts parameters for optimal quality. This is particularly useful when you do not want to manually tune parameters for each batch.

## When to Erase and Restart a Batch {#erase-restart}

Sometimes a batch produces such poor results that manual correction is not worth the effort. In these cases:

1. Go to the batch dialog (click on the batch card).
2. Adjust the shift and window parameters.
3. Click **"Recalculate"** to re-align the batch with new settings.

If the entire alignment needs to start over (e.g., you want to try a different embedding model):

1. Click **"Erase"** in the Controls panel.
2. Confirm the action — this deletes all batch data but preserves the alignment configuration and documents.
3. Select a new embedding model if desired.
4. Click "Align next" to start fresh.

![Settings panel](assets/screenshots/aligner-settings-panel.png)

## Custom Range Alignment {#custom-range}

For advanced use cases, the **Custom range** feature lets you select a specific range of batches and re-align them with custom parameters. This is useful for:

- **Collecting samples** with different shift and window values to find the optimal settings.
- **Re-aligning a problematic section** without affecting the rest.
- **A/B testing** different parameter combinations.

## Managing Long Texts {#managing-long-texts}

For very long texts (1,000+ sentences), keep these practices in mind:

1. **Process batch by batch initially.** Even if you plan to "Align all" later, process the first 2-3 batches manually to verify the settings work.
2. **Check the visualization after each batch.** Catching drift early prevents it from compounding.
3. **Resolve conflicts incrementally.** After each batch (or every 2-3 batches), resolve conflicts. This keeps the conflict list manageable.
4. **Use a consistent shift strategy.** If batch 2 needed shift +5 and batch 3 needed shift +8, batch 4 will likely need shift +10 or +12. The drift tends to be cumulative.
5. **Save .lt backups at milestones.** After successfully processing half the text, download the .lt file. If something goes wrong with later batches, you can restore from the backup.

## Tips {#tips}

1. **Default settings work for most texts.** Batch size 200 and window 40 handle the vast majority of cases well. Only adjust when the visualization shows problems.
2. **Shift is your primary tuning knob.** If the diagonal drifts, adjust shift. If it is clean, leave everything as-is.
3. **Larger window = more tolerance, more computation.** Increasing window from 40 to 60 or 80 helps with drift but slows processing and may introduce more false matches.
4. **Erase is not destructive to documents.** Erasing alignment data does not affect your uploaded documents. You can always re-align.
5. **Use auto-optimize for hands-off processing.** If you do not want to manually check each batch, the auto-optimize feature is a good middle ground.

## Next Steps {#next-steps}

- [Tutorial: Resolving Alignment Conflicts](tutorial-conflict-resolution.en.md) — handling conflicts that arise in multi-batch alignments.
- [Tutorial: Checking Alignment Quality](tutorial-quality-check.en.md) — verifying quality across batches.
- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — basic workflow overview.
