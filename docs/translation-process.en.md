# How Translators Work: Understanding the Source Material {#translation-process}

To understand why text alignment is a hard problem, it helps to understand how human translation works. Translators do not produce word-for-word copies of the source text — they create new texts that convey the same meaning, tone, and effect in a different language. This creative process is what makes alignment both necessary and challenging.

This page explores translation from the translator's perspective, explaining the decisions that create the sentence-level mismatches that alignment algorithms must handle.

## Literal vs. free translation {#literal-vs-free}

### The spectrum of translation approaches {#spectrum}

Translation exists on a spectrum from strictly literal to freely adaptive:

**Literal (word-for-word) translation** preserves the structure of the source text as closely as possible. Each sentence maps to one sentence, word order is maintained where grammar allows, and the translator avoids adding or removing content.

**Free (sense-for-sense) translation** focuses on conveying the overall meaning and effect, with the translator free to restructure, condense, expand, or rearrange as needed for natural expression in the target language.

Most professional literary translation falls somewhere in between — faithful to the source's meaning and style, but adapted to read naturally in the target language. It is this adaptation that creates the alignment challenge.

### Why literal translation fails {#why-literal-fails}

A strictly literal approach often produces awkward, unnatural text because languages differ fundamentally in:

- **Word order** — English uses Subject-Verb-Object; Japanese uses Subject-Object-Verb; Arabic uses Verb-Subject-Object
- **Sentence complexity** — German tolerates very long sentences with nested subclauses; English prefers shorter, more direct constructions
- **Grammatical features** — Russian has grammatical case, allowing flexible word order; English relies on fixed word order for meaning
- **Lexical gaps** — many concepts have no single-word equivalent in the target language and require explanation
- **Cultural context** — idioms, references, and humor often cannot be translated directly

A good translator navigates all of these differences while maintaining the author's voice, creating a text that reads as if it were originally written in the target language.

## Sentence splitting and merging {#splitting-merging}

### Why translators split sentences {#why-split}

Translators frequently split one source sentence into two or more target sentences:

**Complexity management** — a long, complex sentence in the source language may be grammatically possible but stylistically awkward in the target. The translator breaks it into shorter, clearer sentences.

**Example:**
> **Russian:** "Старик, который много лет жил в маленьком домике на окраине деревни и каждое утро ходил к реке за водой, никогда не жаловался на свою судьбу, хотя жизнь его была нелегка."
>
> **English translation (split into three):**
> "The old man had lived in a small house on the edge of the village for many years."
> "Every morning he walked to the river for water."
> "He never complained about his fate, though his life was not easy."

The translator split one Russian sentence into three English ones because the nested relative clauses and coordinate structures, while natural in Russian, would produce an unwieldy English sentence.

### Why translators merge sentences {#why-merge}

The reverse also happens — multiple source sentences become one target sentence:

**Flow and rhythm** — two short, choppy sentences may read better as one longer sentence in the target language.

**Example:**
> **English (two sentences):** "She opened the door. The room was empty."
>
> **Russian (merged):** "Она открыла дверь, и комната оказалась пустой."

The translator merged the sentences because the Russian version flows more naturally as a single compound sentence. The dramatic pause that works in English ("She opened the door. [pause] The room was empty.") is expressed differently in Russian — through the conjunction and verb aspect.

### Alignment implications {#alignment-implications}

These splitting and merging decisions create the fundamental challenge for alignment:

- If the source has 500 sentences and the translation has 530, there is no simple one-to-one mapping
- The "extra" 30 sentences in the translation come from splits, and some original sentences may have been merged
- An alignment algorithm must detect and correctly handle these 1:2, 2:1, and more complex correspondences

## Cultural adaptation {#cultural-adaptation}

### Domestication vs. foreignization {#domestication}

Translators face a choice between **domestication** (adapting the text to the target culture) and **foreignization** (preserving the foreign flavor). This choice affects alignment because:

- Domesticated translations may substitute cultural references entirely, producing sentences with no direct equivalent in the source
- Translator's notes or explanatory additions may appear in the translation without a source counterpart
- Entire passages may be restructured to match target-culture conventions

### Examples of adaptation {#adaptation-examples}

**Measurement conversion:**
> **Source:** "The house was about fifty yards from the road."
> **Translation:** "Дом находился примерно в пятидесяти метрах от дороги."

The translator converted yards to meters — the sentence still aligns one-to-one, but the content has changed.

**Cultural reference replacement:**
> **Source:** "It was like something out of a Dickens novel."
> **Translation:** "Это было как из романа Достоевского."

The translator substituted a culturally equivalent reference. Again, one-to-one alignment works, but the semantic content differs.

**Explanatory expansion:**
> **Source:** "They ate haggis for dinner."
> **Translation (with explanation):** "На ужин подали хаггис — традиционное шотландское блюдо из бараньих потрохов."

The translator added an explanation for readers unfamiliar with haggis. The sentence is still one-to-one, but the target is significantly longer.

## How this creates the alignment problem {#why-alignment-hard}

The cumulative effect of these translation decisions is that source and target texts are structurally different in unpredictable ways:

1. **Different sentence counts** — splitting and merging change the total number of sentences
2. **Different paragraph structure** — translators may reorganize paragraphs for flow
3. **Non-uniform expansion** — some sentences expand dramatically, others shrink, making length-based methods unreliable
4. **Local reordering** — within a paragraph, sentence order may change slightly
5. **Content changes** — additions, omissions, and substitutions mean the content is not identical

These differences are not bugs in the translation — they are features of good, professional translation. But they make the task of automatically aligning the texts significantly harder than simply matching line numbers.

## Examples from literary translation {#literary-examples}

### The challenge of mystery fiction {#mystery-fiction}

Mystery and detective fiction — such as the works of Agatha Christie, which are commonly used as test texts for alignment — presents particular challenges:

**Dialogue-heavy passages** create many short sentences ("Yes.", "No.", "Really?", "Of course.") that look similar in embeddings, making it hard for the model to distinguish which "Yes" matches which.

**Idiomatic language** is common in dialogue and narration. Idioms rarely translate word-for-word, and the translator may use a completely different image or phrase to convey the same effect.

**Sentence rhythm** matters in mystery writing — short, punchy sentences build tension. A translator working into a language that prefers longer sentences (like Russian or German) must decide whether to preserve the staccato rhythm or adapt to the target language's norms.

### Poetry and verse {#poetry}

Poetry is the extreme case. Translators of poetry often restructure entire stanzas, change line breaks, and alter sentence boundaries to preserve meter, rhyme, or other formal properties. Aligning poetry translations requires extensive manual work and is beyond what automatic alignment handles well.

### Technical and legal texts {#technical}

At the other end of the spectrum, technical and legal texts tend to be translated more literally, with close structural correspondence between source and target. These texts align easily and produce clean diagonals in visualization.

## What this means for Lingtrain users {#practical-implications}

Understanding how translation works helps you get better results with Lingtrain:

1. **Expect conflicts.** Conflicts are not errors — they are natural consequences of good translation. A translation with zero conflicts would suggest overly literal, potentially poor translation.

2. **Use the visualization.** The scatter plot tells you how much the translation diverges from the source structure. A clean diagonal with occasional deviations indicates a faithfully translated text. A wildly scattered plot may indicate a very free adaptation or possibly a wrong text pair.

3. **Adjust expectations by genre.** Literary fiction will have more conflicts than technical documentation. Plan your editing time accordingly.

4. **Review merged and split sentences carefully.** After automatic conflict resolution, check the editor for cases where the algorithm grouped sentences incorrectly. Merged sentences (marked as multi-line pairs like "2:3") deserve special attention.

5. **Leverage the conflict resolver.** The automatic resolver handles most simple splits and merges (1:2, 2:1, 2:3) correctly. Focus your manual effort on the complex cases it leaves unresolved.

## Further reading {#further-reading}

- [Alignment process](alignment.en.md) — how to handle conflicts in practice
- [Text alignment: theory and methods](text-alignment-theory.en.md) — the algorithms behind alignment
- [Lingtrain for professional translators](use-cases-translators.en.md) — how translators use parallel corpora
- [Lingtrain for language learners](use-cases-language-learners.en.md) — creating parallel books from translated literature
