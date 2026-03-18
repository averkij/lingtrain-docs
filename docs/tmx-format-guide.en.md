# TMX Format Guide {#tmx}

Translation Memory eXchange (TMX) is the industry-standard format for exchanging translation memory data between different tools and systems. Lingtrain Aligner can export aligned sentence pairs in TMX format, making the alignment results directly usable in professional translation workflows. This guide covers the TMX format specification, its XML structure, and how to use TMX files with popular CAT tools.

## What is TMX? {#what-is-tmx}

TMX is an XML-based format designed to represent translation memory data — pairs (or groups) of translated segments in two or more languages. It was developed by LISA (Localization Industry Standards Association) and is now maintained by GALA (Globalization and Localization Association). The current widely-used version is TMX 1.4b, published in 2005.

The key principles of TMX:

- **Tool-independent**: TMX files can be imported into any compliant translation tool
- **Language-neutral**: Any number of languages can be represented in a single TMX file
- **Metadata-rich**: Each segment can carry metadata about its origin, creation date, translator, and quality
- **Unicode-based**: Full Unicode support for any writing system

## XML structure {#structure}

A TMX file is an XML document with a well-defined structure:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tmx SYSTEM "tmx14.dtd">
<tmx version="1.4">
  <header
    creationtool="Lingtrain Aligner"
    creationtoolversion="1.0"
    segtype="sentence"
    o-tmf="text"
    adminlang="en"
    srclang="en"
    datatype="plaintext"
  />
  <body>
    <tu>
      <tuv xml:lang="en">
        <seg>The weather was beautiful that morning.</seg>
      </tuv>
      <tuv xml:lang="ru">
        <seg>Погода в то утро была прекрасной.</seg>
      </tuv>
    </tu>
    <tu>
      <tuv xml:lang="en">
        <seg>She decided to go for a walk in the park.</seg>
      </tuv>
      <tuv xml:lang="ru">
        <seg>Она решила пойти погулять в парке.</seg>
      </tuv>
    </tu>
  </body>
</tmx>
```

### Key elements {#key-elements}

- **`<tmx>`** — Root element, specifies the TMX version
- **`<header>`** — Contains metadata about the file: which tool created it, the source language, segment type, and data format
- **`<body>`** — Contains all translation units
- **`<tu>`** — Translation Unit, a single aligned segment group. Each `<tu>` contains one or more `<tuv>` elements
- **`<tuv>`** — Translation Unit Variant, one language version of the segment. The `xml:lang` attribute specifies the language code
- **`<seg>`** — The actual text segment

### Header attributes {#header-attributes}

The `<header>` element supports several attributes:

| Attribute | Required | Description |
|-----------|----------|-------------|
| `creationtool` | Yes | Name of the tool that created the file |
| `creationtoolversion` | Yes | Version of the creating tool |
| `segtype` | Yes | Segmentation type: `sentence`, `paragraph`, or `block` |
| `o-tmf` | Yes | Original translation memory format |
| `adminlang` | Yes | Language for administrative metadata |
| `srclang` | Yes | Source language code (e.g., "en", "ru", "zh") |
| `datatype` | Yes | Data type: `plaintext`, `html`, `xml`, etc. |
| `creationdate` | No | Date of file creation (ISO 8601 format) |
| `creationid` | No | Identifier of the person/system who created the file |

### Translation unit attributes {#tu-attributes}

Each `<tu>` can carry additional metadata:

- `tuid` — A unique identifier for the translation unit
- `changedate` — Date of last modification
- `changeid` — Who last modified the unit
- `creationdate` — When the unit was created
- `creationid` — Who created the unit
- `srclang` — Source language (overrides header if different)

### Language codes {#language-codes}

TMX uses language codes following RFC 4646 (now RFC 5646). Common codes:

| Code | Language |
|------|----------|
| `en` | English |
| `en-US` | English (United States) |
| `en-GB` | English (United Kingdom) |
| `ru` | Russian |
| `de` | German |
| `fr` | French |
| `es` | Spanish |
| `zh` | Chinese |
| `zh-CN` | Chinese (Simplified) |
| `zh-TW` | Chinese (Traditional) |
| `ja` | Japanese |
| `ko` | Korean |
| `ar` | Arabic |
| `pt-BR` | Portuguese (Brazil) |

## How Lingtrain exports TMX {#lingtrain-export}

When you export alignment results in TMX format from Lingtrain:

1. Each alignment chain becomes one `<tu>` element
2. Multi-sentence chains (e.g., 2:1) have their sentences concatenated with a space separator in the `<seg>` element
3. The header includes Lingtrain as the creation tool
4. Language codes are set based on the languages selected during alignment setup
5. The segmentation type is set to `sentence`

The resulting TMX file is immediately importable into any TMX-compliant CAT tool.

## Importing TMX into CAT tools {#importing}

### SDL Trados Studio {#trados}

SDL Trados Studio (now Trados Studio) is the most widely used professional CAT tool.

To import a TMX file:

1. Open Trados Studio
2. Go to **Translation Memories** view
3. Click **New** to create a new TM, or select an existing TM
4. Right-click the TM and select **Import**
5. Browse to your TMX file and click **Open**
6. In the import settings, configure:
   - **Import action**: Choose whether to add new units, overwrite existing ones, or merge
   - **Field mapping**: Map any custom fields from the TMX to Trados fields
7. Click **Import** to begin the process

Trados will report the number of units imported, any duplicates found, and any errors encountered.

### memoQ {#memoq}

memoQ is a popular CAT tool especially in European markets.

To import TMX:

1. Open memoQ and go to **Resource Console** (or **Translation memories** in project settings)
2. Create a new TM or select an existing one
3. Click **Import/Export** then **Import from TMX**
4. Select your TMX file
5. Configure import options:
   - **Handling of duplicates**: Add, skip, or overwrite
   - **Segment status**: Set the status of imported segments (confirmed, unconfirmed)
6. Click **Import**

memoQ handles TMX files from Lingtrain without issues, as Lingtrain produces standard-compliant TMX.

### OmegaT {#omegat}

OmegaT is a free, open-source CAT tool popular in academic and non-profit settings.

To use TMX files with OmegaT:

1. Place the TMX file in the **tm** folder of your OmegaT project:
   - `project_root/tm/` for reference TMs (fuzzy matches shown but not auto-inserted)
   - `project_root/tm/auto/` for auto-inserted matches (100% matches are automatically inserted)
   - `project_root/tm/enforce/` for enforced matches (always used for exact matches)
2. Reload the project (**Project > Reload**)
3. OmegaT will automatically read the TMX file and display matches as you translate

OmegaT uses TMX as its native format for translation memories, making import seamless.

### Memsource (Phrase TMS) {#memsource}

Memsource, now known as Phrase TMS, is a cloud-based translation management system.

To import TMX:

1. Log into Phrase TMS
2. Navigate to **Translation Memories**
3. Click **New** to create a TM, or select an existing one
4. Click **Import** in the TM detail view
5. Upload the TMX file
6. Configure import settings (duplicate handling, segment status)
7. Click **Import**

Phrase TMS processes TMX files quickly, even for large files with hundreds of thousands of segments.

### Other tools {#other-tools}

TMX is also supported by:

- **Wordfast** (Pro and Classic) — Import via Translation Memory menu
- **MateCat** — Upload TMX in project setup
- **CafeTran Espresso** — Drag and drop TMX into the TM panel
- **Déjà Vu** — Import via Memory menu
- **Across** — Import via crossTank management

## Editing TMX files {#editing}

TMX files are plain XML, so they can be edited with any text editor. However, for large files or complex operations, specialized tools are more practical:

### Olifant {#olifant}

Olifant (part of the Okapi Framework) is a free TMX editor that provides:
- Visual browsing and searching of TMX content
- Filtering by language, text content, or metadata
- Editing individual segments
- Batch operations (find and replace, delete matching entries)
- Splitting and merging TMX files

### TMXEditor by Maxprograms {#tmxeditor}

A cross-platform TMX editor that offers:
- Validation against the TMX specification
- Search and replace with regex support
- Removing duplicates
- Filtering and exporting subsets

### Programmatic editing {#programmatic}

For batch processing, TMX files can be manipulated with any XML library. Python example:

```python
import xml.etree.ElementTree as ET

tree = ET.parse('alignment.tmx')
root = tree.getroot()

for tu in root.iter('tu'):
    for tuv in tu.findall('tuv'):
        lang = tuv.get('{http://www.w3.org/XML/1998/namespace}lang')
        seg = tuv.find('seg')
        print(f"[{lang}] {seg.text}")
```

## Merging translation memories {#merging}

When working on large projects, you may need to merge TMX files from different sources (e.g., different chapters aligned separately):

### Simple concatenation {#concatenation}

For basic merging, you can concatenate the `<tu>` elements from multiple TMX files into a single `<body>`. Ensure that the `<header>` attributes are consistent (same language codes, same segmentation type).

### Deduplication {#deduplication}

After merging, you may have duplicate entries (the same source-target pair appearing multiple times). Most CAT tools handle this during import, but you can also deduplicate in advance:

1. Import all TMX files into a single TM in your CAT tool
2. Export the merged TM as a new TMX file
3. The export process typically removes exact duplicates

Alternatively, use a tool like Olifant to identify and remove duplicates.

### Handling conflicts {#merge-conflicts}

When the same source segment appears with different translations (from different translators or different versions of the text), you need a merge strategy:

- **Keep all**: Preserve all variants. Most CAT tools will show the most recent one as the primary match.
- **Keep latest**: Prefer the most recently created entry.
- **Keep highest quality**: If entries have quality scores, keep the one with the highest score.

## TMX 1.4 standard details {#standard}

### Inline elements {#inline}

TMX supports inline elements within `<seg>` for representing formatting:

- `<bpt>` / `<ept>` — Begin/end paired tags (e.g., bold, italic)
- `<it>` — Isolated tag
- `<ph>` — Placeholder (e.g., an image reference)
- `<hi>` — Highlighted text
- `<sub>` — Sub-flow (embedded content)

Lingtrain exports plain text segments without inline formatting. If your source texts contained formatting, it will not be preserved in the TMX export.

### Character encoding {#encoding}

TMX files must be valid XML and should use UTF-8 encoding (the default). Special characters must be properly escaped:

- `&` becomes `&amp;`
- `<` becomes `&lt;`
- `>` becomes `&gt;`
- `"` becomes `&quot;` (in attributes)

Lingtrain handles this escaping automatically during export.

### Compliance levels {#compliance}

The TMX standard defines two compliance levels:

- **Level 1**: Plain text segments only (no inline formatting). This is what Lingtrain produces.
- **Level 2**: Rich text with inline formatting tags. Required for preserving formatting from CAT tools.

Level 1 TMX files are universally compatible. Level 2 files may have tool-specific inline tag conventions that limit portability.

## Best practices {#best-practices}

1. **Use consistent language codes**: Ensure your TMX files use the same language codes across all projects. Mixing `en` and `en-US` can cause matching failures in some tools.

2. **Include metadata**: Adding creation date and tool information helps track the provenance of translation memory entries.

3. **Validate before importing**: Use a TMX validator (like the one in Maxprograms TMXEditor) to check for structural issues before importing into production TMs.

4. **Back up before merging**: Always back up existing translation memories before importing new TMX data, in case of corruption or unintended overwrites.

5. **Review alignment quality**: TMX files from automated alignment (like Lingtrain) should be reviewed for accuracy before being added to production translation memories. Low-quality entries can degrade translation suggestions.

6. **Segment appropriately**: TMX files work best with sentence-level segmentation. Paragraph-level segments produce fewer but less reusable matches.
