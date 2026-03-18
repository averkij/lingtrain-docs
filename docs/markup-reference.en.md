# Markup Tags Reference {#markup-reference}

Lingtrain Aligner uses a special markup system to annotate structural elements in your text files. This page is a complete reference for all supported markup tags.

## Syntax {#syntax}

All markup tags follow the same pattern:

```
Text content%%%%%tag.
```

- The tag is placed **at the end of the line**, immediately after the text content
- The prefix is exactly five percent signs: `%%%%%`
- The tag name is followed by a period (`.`)
- There must be no trailing whitespace after the period
- Each tagged line contains only one tag

## How Tags Affect Alignment {#alignment-effect}

Tagged lines are **excluded from the alignment algorithm**. They are not converted into embeddings and are not matched against sentences in the other language. This means:

- Tags do not consume batch capacity
- Tags do not generate conflicts
- Tags are stored as metadata in the alignment database

Both source and target texts should have **matching tags**. For example, if the English text has `%%%%%author.` and `%%%%%title.` tags, the Russian translation should have them too. Mismatched tag counts between texts can cause issues during export.

## How Tags Affect Export {#export-effect}

During export, tags are used to format the output:

- **HTML book**: Tags produce title pages, chapter headings, quotation blocks, section dividers, and other structural elements
- **TMX**: Tags are excluded from sentence pairs (they are not translation units)
- **Sentence/paragraph corpora**: Tagged lines are excluded
- **XML/JSON**: Tags are included as metadata elements in the structured output
- **Alignment database (.lt)**: Tags are stored in the marks table

## Tag Reference {#tag-reference}

### %%%%%author. {#tag-author}

**Purpose:** Identifies the author name.

**Syntax:**
```
Agatha Christie%%%%%author.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Displayed on the title page in a styled author block
- Both texts should have this tag; the author name may differ between languages (e.g., "Agatha Christie" vs. "Агата Кристи")

**Example:**
```
English: Agatha Christie%%%%%author.
Russian: Агата Кристи%%%%%author.
```

---

### %%%%%title. {#tag-title}

**Purpose:** Identifies the book or work title.

**Syntax:**
```
The Blue Geranium%%%%%title.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Displayed prominently on the title page
- This is one of the most important tags for book export

**Example:**
```
English: The Blue Geranium%%%%%title.
Russian: Синяя герань%%%%%title.
```

---

### %%%%%h1. through %%%%%h5. {#tag-headings}

**Purpose:** Heading levels 1 through 5, used for chapter titles, part titles, section headings, and sub-headings.

**Syntax:**
```
Chapter One%%%%%h1.
Part I%%%%%h2.
Section 1.1%%%%%h3.
Subsection A%%%%%h4.
Minor heading%%%%%h5.
```

**Effect on alignment:** Lines are excluded from sentence alignment.

**Effect on export:**
- HTML book: Rendered as HTML headings (`<h1>` through `<h5>`) with appropriate styling
- Creates visual chapter/section breaks in the parallel book
- The table of contents (if generated) uses these headings

**Example:**
```
English: Chapter One%%%%%h1.
Russian: Глава первая%%%%%h1.

English: Part I. The Beginning%%%%%h2.
Russian: Часть I. Начало%%%%%h2.
```

**Best practices:**
- Use `h1` for major chapter divisions
- Use `h2` for parts or sub-chapters
- Use `h3`–`h5` sparingly for finer divisions
- Keep heading levels consistent between source and target texts

---

### %%%%%qtext. {#tag-qtext}

**Purpose:** Marks a quotation or epigraph text.

**Syntax:**
```
To be or not to be, that is the question%%%%%qtext.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Rendered in a styled quotation block (typically italicized, indented)
- Often paired with `%%%%%qname.` for attribution

**Example:**
```
To be or not to be, that is the question%%%%%qtext.
William Shakespeare%%%%%qname.
```

---

### %%%%%qname. {#tag-qname}

**Purpose:** Attribution for a quotation (the person being quoted).

**Syntax:**
```
William Shakespeare%%%%%qname.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Rendered as attribution text below the quotation, typically right-aligned or in a smaller font

**Example:**
```
English:
The only way to do great work is to love what you do%%%%%qtext.
Steve Jobs%%%%%qname.

Russian:
Единственный способ делать великие дела — любить то, что делаешь%%%%%qtext.
Стив Джобс%%%%%qname.
```

---

### %%%%%image. {#tag-image}

**Purpose:** References an image file to be included in the exported book.

**Syntax:**
```
illustration_01.jpg%%%%%image.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Generates an `<img>` tag referencing the specified image file
- The image file must be available at the expected path during export

**Notes:** Image support is primarily designed for the HTML book export. The image file itself is not uploaded through the markup — it is referenced by name. Both texts should have matching image tags at corresponding positions.

---

### %%%%%translator. {#tag-translator}

**Purpose:** Credits the translator of the text.

**Syntax:**
```
Translated by John Doe%%%%%translator.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Displayed on the title page below the author name
- Typically present only in the target (translated) text, but can appear in both

**Example:**
```
English: Original text%%%%%translator.
Russian: Перевод Иванова И. И.%%%%%translator.
```

---

### %%%%%divider. {#tag-divider}

**Purpose:** Creates a section divider (visual separator between sections within a chapter).

**Syntax:**
```
* * *%%%%%divider.
```

**Effect on alignment:** Line is excluded from sentence alignment.

**Effect on export:**
- HTML book: Rendered as a horizontal rule or decorative separator (`<hr>` or styled element)
- Creates visual breaks between sections

**Example:**
```
...end of previous section.

* * *%%%%%divider.

Beginning of next section...
```

**Best practices:**
- The text content of the divider line (e.g., "* * *") is typically the same in both languages, but the actual text is less important than the tag itself
- Both texts must have dividers at corresponding positions

## Marks Panel {#marks-panel}

After uploading documents, the **Marks** panel at the bottom of the Documents tab displays all detected markup tags from both texts side by side. Use this panel to verify:

1. **Both texts have the same number of each tag type** — for example, if the English text has 12 `h1` tags, the Russian text should also have 12
2. **Tag content matches** — the author name appears in both languages, chapter titles correspond to each other
3. **Tag order is consistent** — tags appear in the same sequence in both texts

Mismatched mark counts are a common source of export issues. The Marks panel makes it easy to spot discrepancies before alignment.

## Complete Example {#complete-example}

Here is a complete example of a properly tagged text file:

```
Agatha Christie%%%%%author.
The Blue Geranium%%%%%title.
Translated by Mary Smith%%%%%translator.


The meaning of life is not to find yourself, but to create yourself%%%%%qtext.
George Bernard Shaw%%%%%qname.


Chapter 1. The Beginning%%%%%h1.

'When I was down here last year -' said Sir Henry Clithering, and stopped.

His hostess, Mrs Bantry, looked at him curiously.

* * *%%%%%divider.

The Ex-Commissioner of Scotland Yard was staying with old friends.

Chapter 2. The Investigation%%%%%h1.

She opened the door and looked inside.
```

And its corresponding Russian counterpart:

```
Агата Кристи%%%%%author.
Синяя герань%%%%%title.
Перевод Ивановой М. А.%%%%%translator.


Смысл жизни не в том, чтобы найти себя, а в том, чтобы создать себя%%%%%qtext.
Джордж Бернард Шоу%%%%%qname.


Глава 1. Начало%%%%%h1.

— Когда я был здесь в прошлом году… — начал сэр Генри Клитеринг и умолк.

Хозяйка дома, миссис Бантри, посмотрела на него с любопытством.

* * *%%%%%divider.

Экс-комиссар Скотленд-Ярда гостил у старых друзей.

Глава 2. Расследование%%%%%h1.

Она открыла дверь и заглянула внутрь.
```

## Best Practices {#best-practices}

1. **Always add `author` and `title` tags** — they produce the title page in HTML export
2. **Keep tags consistent between texts** — same number of each tag type in both source and target
3. **Add heading tags for chapters** — `h1` for major chapters, `h2` for sub-sections
4. **Place dividers at section breaks** — they improve the visual structure of exported books
5. **Check the Marks panel** after uploading — verify tag counts and order before alignment
6. **Do not nest tags** — each line can have only one tag
7. **Do not add extra whitespace** after the tag period — it may prevent recognition
8. **Use quotation tags sparingly** — `qtext` and `qname` are for epigraphs and prominent quotes, not for every quotation in the text
