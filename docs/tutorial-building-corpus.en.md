# User Story: Building a Parallel Corpus for Research {#building-corpus}

Dr. Alexei Petrov is a computational linguistics researcher at a university. He is working on a paper comparing sentence embedding models for the English-Russian language pair and needs a high-quality parallel corpus for evaluation. He decides to build one from classic literature using Lingtrain Aligner.

## Research Requirements {#requirements}

Alexei needs:

- At least 5,000 aligned sentence pairs for statistical significance.
- Sentence-level alignment (not paragraph-level).
- Clean, verified pairs with minimal noise.
- Multiple source texts to avoid domain bias.
- Export in plain text format for easy processing with Python scripts.

He selects five public domain works with well-known Russian translations:

1. Arthur Conan Doyle — "A Study in Scarlet" / "Этюд в багровых тонах"
2. Jack London — "White Fang" / "Белый клык"
3. O. Henry — "The Gift of the Magi" (short stories collection) / "Дары волхвов"
4. Mark Twain — "The Adventures of Tom Sawyer" / "Приключения Тома Сойера"
5. H.G. Wells — "The Time Machine" / "Машина времени"

## Text Preparation {#preparation}

Alexei downloads the English texts from Project Gutenberg and the Russian translations from Lib.ru. He writes a simple Python script to automate the cleaning:

```python
import re

def clean_text(text):
    # Remove Project Gutenberg header/footer
    start = text.find("*** START OF")
    end = text.find("*** END OF")
    if start > 0 and end > 0:
        text = text[text.find("\n", start) + 1:end]

    # Remove page numbers (standalone digits on their own line)
    text = re.sub(r'\n\d+\n', '\n', text)

    # Join hard-wrapped lines within paragraphs
    text = re.sub(r'(?<!\n)\n(?!\n)', ' ', text)

    return text.strip()
```

For each text pair, he:

1. Cleans and normalizes the text.
2. Adds `%%%%%author.` and `%%%%%title.` tags.
3. Adds `%%%%%h1.` tags for chapter headings.
4. Saves both files as UTF-8 `.txt`.
5. Verifies that markup tag counts match between the two files.

The preparation takes about an hour for all five texts.

## Batch Processing in Lingtrain {#batch-processing}

Alexei uploads all ten files to the Documents tab — five English and five Russian. He creates five separate alignments, one per book.

For each alignment, he uses the same settings:

- **Batch size:** 200 (default)
- **Window:** 40 (default)
- **Batch count:** all (to process everything at once)

He starts with "A Study in Scarlet" — clicks "Align all" and waits. The text has about 800 sentences, producing 4 batches. Processing takes a few minutes.

After all batches complete, he checks the visualization. The diagonal is clean for batches 1-3, but batch 4 shows some drift — the Russian translation of the second part of the novel was restructured. He adjusts the shift to +5 for batch 4, erases it, and re-aligns. The diagonal improves.

He resolves conflicts ("Resolve all"), then does a quick scan through the editor. The alignment looks solid.

Alexei repeats this for all five books. The whole process — upload, align, resolve, review — takes about 2 hours for all five texts.

## Quality Control {#quality-control}

For his research, alignment quality is critical. Alexei implements a sampling strategy:

1. For each book, he exports the **similarity corpora** to get confidence scores for each pair.
2. He randomly samples 50 pairs from each book (250 total) and manually verifies them.
3. He calculates alignment accuracy: 243 out of 250 pairs are correct (97.2% accuracy).

The 7 incorrect pairs are mostly very short sentences ("Yes.", "No.", "Indeed.") that were matched to the wrong instance. For his research purposes, this noise level is acceptable — but he notes it in his paper's methodology section.

He also checks the overall chain score reported by each alignment's visualization summary. All five alignments score above 0.96, confirming good quality.

## Exporting the Corpus {#exporting}

For each alignment, Alexei goes to the Create tab and downloads:

- **Sentence corpora** (source and target) — plain text files with one sentence per line, strictly aligned by position.
- **TMX corpora** — as an additional format for sharing with the translation studies department.
- **Alignment database (.lt)** — for archival and reproducibility.

He now has 10 text files (5 source + 5 target) that he combines into a single corpus:

```python
import os

all_en = []
all_ru = []

for book in books:
    with open(f"{book}_en.txt") as f:
        all_en.extend(f.readlines())
    with open(f"{book}_ru.txt") as f:
        all_ru.extend(f.readlines())

# Write combined corpus
with open("corpus_en.txt", "w") as f:
    f.writelines(all_en)
with open("corpus_ru.txt", "w") as f:
    f.writelines(all_ru)

print(f"Total pairs: {len(all_en)}")
# Output: Total pairs: 6,847
```

The combined corpus has 6,847 aligned sentence pairs — well above his 5,000 minimum.

## Using the Python Library {#python-library}

For larger projects, Alexei notes that the `lingtrain-aligner` Python package can automate the entire pipeline programmatically — loading texts, running alignment, resolving conflicts, and exporting results — without the web interface. This would be useful for processing dozens of texts in batch.

The web interface, however, is invaluable for the quality review step: the visual editor and visualization tools make it easy to spot issues that would be invisible in raw data.

## Analysis and Results {#analysis}

With the corpus in hand, Alexei runs his embedding model comparison:

1. He splits the corpus into train (80%) and test (20%) sets.
2. He computes embeddings with each model under evaluation.
3. He measures alignment quality metrics: precision, recall, and F1 at the sentence level.
4. He analyzes error patterns — which types of sentences are hardest for each model.

The parallel corpus serves as ground truth for his evaluation. Because Lingtrain's alignment was verified by the automatic resolver and manually reviewed, it provides a reliable gold standard.

His paper is published six months later, citing the corpus construction methodology and the Lingtrain Aligner tool.

## Sharing the Corpus {#sharing}

Alexei makes the corpus publicly available (since the source texts are in the public domain). He shares:

- The raw sentence corpora files.
- The TMX files for the translation community.
- The `.lt` database files for anyone who wants to review or modify the alignment.
- A metadata file documenting the source texts, alignment parameters, and quality metrics.

Other researchers use his corpus for their own experiments, and several cite his paper's methodology.

## Lessons for Researchers {#lessons}

1. **Multiple source texts reduce domain bias.** A corpus from a single novel will be biased toward that author's style and vocabulary.
2. **Quality control is essential.** Random sampling with manual verification gives a reliable accuracy estimate.
3. **Chain score is a useful proxy metric.** Scores above 0.95 generally indicate good alignment quality.
4. **Export multiple formats.** Sentence corpora for computation, TMX for the translation community, .lt for reproducibility.
5. **Document your methodology.** Record the alignment parameters, conflict resolution steps, and quality metrics for reproducibility.
6. **The web editor is irreplaceable for review.** Programmatic pipelines are fast but miss issues that are obvious visually.

## Next Steps {#next-steps}

- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — basic alignment walkthrough.
- [Tutorial: Checking Alignment Quality](tutorial-quality-check.en.md) — quality verification techniques.
- [Tutorial: Exporting Your Results](tutorial-export-formats.en.md) — all export format details.
- [Tutorial: Working with Multiple Batches](tutorial-multiple-batches.en.md) — managing large texts.
