# Tutorial: Checking Alignment Quality {#quality-check}

After aligning your texts, it is important to verify that the results are correct before exporting. This tutorial explains how to assess alignment quality using the visualization tools, chain score metrics, editor spot-checks, and how to decide between manual editing and re-alignment.

## Why Quality Matters {#why-quality-matters}

The quality of your alignment directly affects the usefulness of the output:

- A parallel book with misaligned sentences frustrates readers and undermines language learning.
- A TMX file with wrong pairs introduces errors into future translations.
- A parallel corpus with noisy data degrades machine learning model performance.

Investing 10-15 minutes in quality verification can save hours of problems downstream.

## Step 1: Read the Visualization {#visualization}

The visualization is your first and most powerful quality indicator. After processing all batches, review the scatter plots in the **Visualization** section.

![Batch card](assets/screenshots/aligner-batch-card.png)

### What Good Alignment Looks Like {#good-alignment}

A high-quality alignment shows:

- **A tight diagonal line** from bottom-left to top-right in each batch.
- **Consistent slope** — the diagonal angle is roughly the same across batches (proportional to the sentence count ratio).
- **Few scattered dots** — most dots are on or very near the diagonal.
- **No abrupt breaks** — the line continues smoothly without large gaps or jumps.

### Warning Signs {#warning-signs}

Watch for these patterns:

- **Flat segments** — a horizontal cluster of dots means multiple source sentences mapped to the same target sentence. This can happen legitimately (merged translations) but many flat segments indicate a problem.
- **Vertical segments** — multiple target sentences mapped to the same source sentence. Same logic.
- **Diagonal breaks** — the line suddenly shifts up or down. This indicates a structural mismatch: content that exists in one text but not the other.
- **Wide scatter** — dots spread far from the diagonal. The model is uncertain about matches. Consider proxy alignment or better text preparation.
- **Off-center diagonal** — the line is not centered in the plot. This indicates drift that could be fixed with shift adjustment.

### Comparing Batches {#comparing-batches}

If you processed multiple batches, compare their visualizations:

- All batches should show a similar pattern quality.
- If early batches look good but later ones deteriorate, drift is likely the cause.
- If one batch in the middle looks bad but others are fine, that batch may cover a section with structural differences (e.g., a chapter that was heavily rewritten in translation).

## Step 2: Check the Chain Score {#chain-score}

The chain score is a numerical quality metric shown in the visualization summary: "Aligned N batches, overall chain score: X.XX".

### Interpreting the Score {#interpreting-score}

| Score range | Quality | Action |
|---|---|---|
| **0.97 - 1.00** | Excellent | Proceed to export. Spot-check a few pairs in the editor. |
| **0.93 - 0.97** | Good | Review conflicts and editor. Fix remaining issues. |
| **0.88 - 0.93** | Acceptable | Thorough editor review needed. Consider re-aligning problematic batches. |
| **0.80 - 0.88** | Poor | Significant issues. Re-align with adjusted parameters or proxy. |
| **Below 0.80** | Unusable | Text preparation issues or unsupported language pair. Clean texts, use proxy, or try a different model. |

### Score Breakdown {#score-breakdown}

The chain score is calculated as:

```
score = 1 - (breaks / total_lines)
```

Where a "break" is any point where the alignment sequence is not monotonically increasing. A score of 0.95 with 200 lines means approximately 10 breaks — 10 places where the alignment jumped backward or skipped a position.

## Step 3: Spot-Check in the Editor {#spot-check}

Even with a good chain score, always verify a sample of pairs in the editor.

### Quick Spot-Check Method {#quick-check}

1. Open the editor with 50 lines per page.
2. Check the **first page** — the beginning of the alignment often has edge effects.
3. Check the **last page** — same logic for the end.
4. Check **2-3 random pages** in the middle.
5. If you processed multiple batches, check one page near each **batch boundary** (look at the line ranges shown on the visualization cards to find the boundaries).

For each page, scan the pairs and verify:

- Do the source and target sentences talk about the same thing?
- Are there any obviously empty cells that should not be empty?
- Are there any cells with text that clearly does not belong?

### Thorough Review Method {#thorough-check}

For high-stakes outputs (TMX for professional translation, corpora for published research):

1. Set lines per page to 20.
2. Read every pair on every page.
3. Mark issues as you go (use the editor's tools to fix them immediately or note page numbers for later).
4. Pay special attention to short sentences — these are most prone to mismatches.

## Step 4: Identify Common Quality Issues {#common-issues}

### Issue 1: Systematic Offset {#systematic-offset}

**Symptom:** All pairs are shifted by 1-2 positions — source sentence N is paired with target sentence N+2 instead of N.

**Cause:** Unmatched content at the beginning of one text (e.g., a dedication or epigraph in one language only).

**Fix:** Remove the unmatched content from the source text, re-upload, and re-align. Or, use the editor to delete the unmatched sentences and manually adjust the first few pairs.

### Issue 2: Localized Mess, Good Elsewhere {#localized-mess}

**Symptom:** Most of the alignment is clean, but one section (e.g., 10-20 consecutive pairs) is clearly wrong.

**Cause:** A section of one text was rewritten, expanded, or abridged in translation. Or there is unmatched content (footnotes, translator's notes) in that section.

**Fix:** Focus on that section in the editor. Delete unmatched content, use Candidates to find correct matches, and edit cells to restore order.

### Issue 3: Gradual Drift {#gradual-drift}

**Symptom:** The alignment is good at the start but progressively worsens.

**Cause:** The source and target have significantly different sentence counts, and the proportional window calculation drifts over time.

**Fix:** Adjust the shift parameter for affected batches and re-align them. See [Working with Multiple Batches](tutorial-multiple-batches.en.md).

### Issue 4: Many Short-Sentence Mismatches {#short-sentence}

**Symptom:** Long, content-rich sentences are correctly aligned, but short sentences ("Yes.", "He nodded.", "She laughed.") are paired with the wrong counterpart.

**Cause:** Short sentences provide weak semantic signals. The embedding model cannot distinguish between multiple similar short sentences.

**Fix:** If the mismatches are few, fix them manually in the editor. If there are many, this is inherent to the content (dialogue-heavy text) and may require more aggressive conflict resolution or manual review.

### Issue 5: Poor Quality Throughout {#poor-throughout}

**Symptom:** The chain score is below 0.85, the visualization shows scattered dots, and many pairs are wrong.

**Cause:** Either the texts are poorly prepared (different editions, mismatched content) or the language pair is not well-supported by the embedding model.

**Fix:**
1. Verify that both texts are the same work, same edition.
2. Clean the texts more thoroughly (remove all unmatched content).
3. Try a different embedding model.
4. Use proxy alignment for low-resource language pairs.

## When to Re-Align vs. Manual Edit {#re-align-vs-edit}

| Situation | Recommended action |
|---|---|
| Chain score > 0.95, a few wrong pairs | Manual edit in the editor |
| Chain score 0.90-0.95, scattered issues | Resolve conflicts, then manual edit |
| One batch is bad, others are good | Re-align the bad batch with adjusted shift/window |
| Chain score < 0.90 overall | Re-align entirely (possibly with proxy or different model) |
| Many short-sentence mismatches | Manual edit (re-alignment will produce similar results) |
| Systematic offset | Fix the source texts, re-upload, re-align |

## Quality Checklist {#checklist}

Before exporting, verify:

1. All batches show a clean diagonal in the visualization.
2. The chain score is above 0.93 (above 0.97 for professional use).
3. All conflicts are resolved (counter shows "All conflicts resolved").
4. The first and last 10-20 pairs are correct.
5. At least 3 random pages of the editor have been spot-checked.
6. No obvious empty cells or mismatches are visible.
7. Batch boundaries have been checked for edge artifacts.

If all items pass, your alignment is ready for export.

## Next Steps {#next-steps}

- [Tutorial: Exporting Your Results](tutorial-export-formats.en.md) — export your verified alignment.
- [Tutorial: Using the Alignment Editor](tutorial-editor-guide.en.md) — fix issues found during quality check.
- [Tutorial: Working with Multiple Batches](tutorial-multiple-batches.en.md) — adjust batch parameters.
