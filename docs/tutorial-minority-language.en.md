# User Story: Preserving a Minority Language with Parallel Texts {#minority-language}

Aidar is a linguist and cultural activist from Bashkortostan. He is passionate about preserving the Bashkir language, a Turkic language spoken by about 1.2 million people. While Bashkir has official status in the Republic of Bashkortostan (Russia), digital resources for the language are scarce. Aidar wants to create a collection of parallel Bashkir-Russian texts that can serve as educational materials for language learners and as a digital resource for computational linguistics research.

## The Problem {#problem}

Minority languages face a digital divide. While major languages have vast online corpora, translation memories, and parallel text collections, languages like Bashkir have very little:

- Few digitized literary works.
- Almost no parallel corpora.
- Limited support in NLP tools and language models.
- Growing concern about language attrition among younger generations.

Aidar believes that creating accessible parallel texts can help on multiple fronts: language learners can use them to study Bashkir, researchers can use them to improve NLP tools, and the texts themselves serve as a form of digital preservation.

## Finding Source Materials {#finding-materials}

Aidar identifies several texts that exist in both Bashkir and Russian:

1. **Mustai Karim's stories** — the renowned Bashkir writer whose works have been widely translated into Russian. Some are in the public domain.
2. **Bashkir folk tales** — traditional stories compiled in bilingual editions.
3. **Government and educational materials** — official documents that exist in both languages by law.

He starts with a collection of short Bashkir folk tales that was published in a bilingual Bashkir-Russian edition. He has access to both texts in digital form.

## The Proxy Challenge {#proxy-challenge}

Bashkir is a low-resource language for most embedding models. When Aidar tries direct alignment (Bashkir as source, Russian as target), the visualization shows a scattered pattern — the model cannot reliably match Bashkir sentences to their Russian translations.

This is where proxy alignment becomes essential.

## Creating the Proxy {#creating-proxy}

Aidar's approach:

1. **Upload the Bashkir text** to the Documents tab and review the sentence split. The Bashkir text splits into 186 sentences.
2. **Download the split Bashkir text** — one sentence per line.
3. **Translate into Russian** using Yandex Translate, which has reasonable Bashkir support as a Turkic language. Yandex is a better choice than Google Translate for this pair because of its stronger coverage of languages from the former Soviet Union.
4. **Verify the line count** — the proxy has exactly 186 lines, matching the original.
5. **Spot-check the quality** — Aidar reads Bashkir natively and checks a random sample of 20 proxy translations. About 15 are good, 3 are acceptable, and 2 are poor (very short sentences that were mistranslated). The overall quality is sufficient for alignment purposes.

He uploads the proxy on the "from" side (Bashkir) of the alignment detail page.

## Running the Alignment {#running}

With the proxy enabled, Aidar runs the alignment:

1. The system computes embeddings from the Russian proxy (instead of the original Bashkir text) and compares them against the Russian target text.
2. Since both sides are now in Russian, the embedding model works well.
3. The visualization shows a clean diagonal — a dramatic improvement over the direct attempt.

The system finds 12 conflicts, and automatic resolution handles all of them. Aidar reviews the result in the editor with "Show subscriptions" enabled, so he can see the proxy translations below each Bashkir sentence. The pairs look correct.

## Quality Review {#quality-review}

Because he reads both Bashkir and Russian, Aidar can verify every pair directly. He goes through the 186 pairs and finds:

- 178 pairs are perfectly aligned.
- 5 pairs have minor issues (a sentence boundary slightly off) but are semantically correct.
- 3 pairs need manual correction — two cases where a short Bashkir proverb was matched to the wrong Russian sentence.

He fixes the 3 problematic pairs using the editor's Candidates feature and is satisfied with the result.

## Exporting and Sharing {#exporting}

Aidar exports the alignment in multiple formats:

- **HTML book** — a beautiful parallel Bashkir-Russian book that language learners can read in their browser. He chooses the Pastel highlight style to make the two languages visually distinct.
- **Sentence corpora** — for computational linguistics researchers who want to train or evaluate models on this language pair.
- **TMX** — for translators working with Bashkir who want to build up translation memory.
- **Alignment database (.lt)** — for archival and for anyone who wants to review or extend the alignment.

He publishes the HTML book on a Bashkir language learning website he runs, and uploads the corpora to a linguistic data repository.

## Scaling the Project {#scaling}

Encouraged by the success, Aidar expands the project:

1. He processes more folk tales — another 500 sentence pairs.
2. He adds Mustai Karim's short stories — approximately 2,000 sentence pairs.
3. He recruits two volunteers (a Bashkir teacher and a university student) to help with quality review.

Over six months, the project produces a parallel corpus of approximately 4,000 Bashkir-Russian sentence pairs — one of the largest openly available resources for this language pair.

## Impact {#impact}

The parallel texts have several effects:

- **Language learners** use the HTML books to study Bashkir vocabulary and sentence structure in context.
- **A university research group** uses the sentence corpora to fine-tune a multilingual model for Bashkir, improving its performance on downstream tasks.
- **Local translators** import the TMX into their CAT tools, helping them maintain consistent terminology.
- **The Bashkir Wikipedia community** uses the aligned texts as reference material for translation work.

## Challenges and Lessons {#challenges}

Aidar reflects on the challenges he encountered:

1. **Proxy quality varies.** Yandex Translate handles Bashkir reasonably well for common vocabulary but struggles with archaic or dialectal forms in folk tales. For these, he sometimes creates the proxy manually — slower but more accurate.
2. **Sentence splitting for Bashkir is imperfect.** The "General" language option works but occasionally misses sentence boundaries specific to Bashkir punctuation conventions. He pre-processes texts to ensure clean paragraph breaks.
3. **Cultural context matters.** Some Bashkir expressions have no direct Russian equivalent. The alignment correctly pairs the sentences, but the translation relationship is more of an adaptation than a literal translation.
4. **Community involvement is key.** Quality review by native speakers is essential for a trustworthy resource.
5. **Start small, grow organically.** Beginning with 186 sentences was the right approach — it validated the workflow before committing to larger texts.

## Guidance for Other Language Activists {#guidance}

Based on his experience, Aidar offers advice to people working on other minority languages:

1. **Use proxy alignment.** It is the single most important feature for low-resource languages. Without it, direct alignment may be unusable.
2. **Choose the best translation service for your language.** Test multiple services. For Turkic languages, Yandex often outperforms Google. For African languages, Google may be better. Check.
3. **Verify proxy quality before processing.** A poor proxy will produce a poor alignment. Spot-check at least 10% of the proxy translations.
4. **Export in multiple formats.** Different audiences need different formats. Learners want HTML books, researchers want corpora, translators want TMX.
5. **Share openly.** The value of parallel texts multiplies when they are freely available. Publish under a permissive license if possible.
6. **Document your methodology.** Future contributors need to know how the corpus was built to extend it consistently.

## Next Steps {#next-steps}

- [Tutorial: Aligning Texts with Proxy](tutorial-proxy-alignment.en.md) — detailed proxy alignment instructions.
- [Tutorial: Your First Alignment](tutorial-first-alignment.en.md) — basic alignment walkthrough.
- [Tutorial: Checking Alignment Quality](tutorial-quality-check.en.md) — quality verification methods.
