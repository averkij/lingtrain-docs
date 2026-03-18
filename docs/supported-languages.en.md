# Supported Languages {#supported-languages}

Lingtrain Aligner supports a wide range of languages for text alignment. This page provides a complete reference of explicitly supported languages, the "General" mode for additional languages, and tips for working with specific writing systems.

## Explicitly Supported Languages {#explicit-languages}

The following 22 languages have dedicated sentence-splitting rules and preprocessing pipelines in Lingtrain. When you select one of these languages, the system applies language-specific logic to split your text into sentences accurately.

| Language | Code | Sentence Splitter | Script | Language Family |
|----------|------|-------------------|--------|-----------------|
| Russian | `ru` | razdel | Cyrillic | Slavic (East) |
| English | `en` | Standard tokenizer | Latin | Germanic (West) |
| German | `de` | Standard tokenizer | Latin | Germanic (West) |
| French | `fr` | Standard tokenizer | Latin | Romance |
| Spanish | `es` | Standard tokenizer | Latin | Romance |
| Italian | `it` | Standard tokenizer | Latin | Romance |
| Portuguese | `pt` | Standard tokenizer | Latin | Romance |
| Turkish | `tr` | Standard tokenizer | Latin | Turkic |
| Polish | `pl` | Standard tokenizer | Latin | Slavic (West) |
| Hungarian | `hu` | Standard tokenizer | Latin | Uralic |
| Czech | `cz` | Standard tokenizer | Latin | Slavic (West) |
| Chinese | `zh` | Regex (CJK punctuation) | CJK | Sino-Tibetan |
| Japanese | `ja` | Regex (Japanese punctuation) | CJK / Kana | Japonic |
| Korean | `ko` | Standard tokenizer | Hangul | Koreanic |
| Swedish | `sw` | Standard tokenizer | Latin | Germanic (North) |
| Dutch | `nl` | Standard tokenizer | Latin | Germanic (West) |
| Ukrainian | `uk` | razdel | Cyrillic | Slavic (East) |
| Belarusian | `bu` | razdel | Cyrillic | Slavic (East) |
| Bashkir | `ba` | Standard tokenizer | Cyrillic | Turkic |
| Chuvash | `cv` | Standard tokenizer | Cyrillic | Turkic |
| Armenian | `hy` | Standard tokenizer | Armenian | Indo-European (Armenian) |
| General | `xx` | Standard tokenizer | Any | Any |

### Sentence Splitting Details {#splitting-details}

Lingtrain uses different sentence-splitting strategies depending on the language:

**razdel-based splitting** (Russian, Ukrainian, Belarusian): The `razdel` library is a rule-based sentence segmenter designed specifically for Cyrillic texts. It handles common Cyrillic abbreviations, quotation marks, and dialogue formatting (em-dashes) that are frequent in Russian literature.

**Regex-based splitting** (Chinese, Japanese): These languages use full-width punctuation marks for sentence endings. Chinese splits on `。`, `！`, and `？`. Japanese uses similar patterns including Japanese quotation marks and punctuation. The splitter handles mixed scripts and ignores embedded Latin text.

**Standard tokenizer** (all other languages): A general-purpose sentence tokenizer that splits on period, exclamation mark, and question mark, with heuristics for abbreviations, decimal numbers, and titles (Mr., Dr., etc.). This works well for most Latin-script and Cyrillic-script languages.

## General Mode {#general-mode}

For languages not in the explicit list, select **"General"** (`xx`). This mode uses the standard tokenizer for sentence splitting and relies entirely on the embedding model for cross-lingual matching.

The embedding models used by Lingtrain (LaBSE, SONAR) support a far wider range of languages than the explicit list. Here is a sample of additional languages supported through General mode:

### Indo-European Languages {#indo-european}

- **Germanic**: Danish, Norwegian (Bokmal/Nynorsk), Icelandic, Afrikaans, Luxembourgish, Yiddish
- **Romance**: Romanian, Catalan, Galician, Occitan, Sardinian
- **Slavic**: Slovak, Slovenian, Bulgarian, Serbian, Croatian, Bosnian, Macedonian, Sorbian
- **Baltic**: Lithuanian, Latvian
- **Celtic**: Irish, Welsh, Breton, Scottish Gaelic
- **Indo-Iranian**: Hindi, Urdu, Bengali, Marathi, Gujarati, Punjabi, Nepali, Sinhala, Persian (Farsi), Kurdish, Pashto, Tajik
- **Albanian**, **Greek**, **Georgian** (Kartvelian)

### Afro-Asiatic Languages {#afro-asiatic}

- Arabic (Modern Standard, dialects), Hebrew, Amharic, Tigrinya, Hausa, Somali, Maltese

### Turkic Languages {#turkic}

- Azerbaijani, Kazakh, Uzbek, Kyrgyz, Turkmen, Tatar, Uyghur

### Uralic Languages {#uralic}

- Finnish, Estonian

### Austronesian Languages {#austronesian}

- Indonesian (Malay), Tagalog (Filipino), Javanese, Cebuano, Malagasy

### Sino-Tibetan and East/Southeast Asian {#east-asian}

- Vietnamese, Thai, Lao, Burmese, Khmer, Tibetan

### Niger-Congo (African) {#african}

- Swahili, Yoruba, Igbo, Zulu, Xhosa, Shona, Kinyarwanda

### Dravidian {#dravidian}

- Tamil, Telugu, Kannada, Malayalam

This is not an exhaustive list. The LaBSE model covers approximately 109 languages, and SONAR covers over 200 languages. If your language is written in a supported script and has sufficient digital presence, it is likely to work.

## Language Coverage by Model {#model-coverage}

Different embedding models available in Lingtrain have different language coverage:

| Model | Approximate Language Count | Best For |
|-------|---------------------------|----------|
| distiluse-base-multilingual-cased-v2 | 50+ | Major European and Asian languages |
| LaBSE | 109 | Broad multilingual coverage, low-resource languages |
| XLM-R-100langs | 100 | Wide cross-lingual coverage |
| rubert-tiny2 | Russian + some | Russian-centric tasks (fast inference) |
| SONAR | 200+ | Widest coverage, extremely low-resource languages |

The available models for a specific language pair are shown in the model selector when creating or configuring an alignment.

## Tips for Specific Scripts {#script-tips}

### CJK Languages (Chinese, Japanese, Korean) {#cjk-tips}

- **Chinese**: Ensure your text uses standard Chinese punctuation (`。`, `！`, `？`, `，`). Mixed use of full-width and half-width punctuation can cause splitting issues. If your text is in Traditional Chinese, it works the same as Simplified Chinese for alignment purposes — the embedding model handles both.

- **Japanese**: The splitter expects Japanese sentence-ending punctuation. For texts mixing Japanese with English quotations, the splitter will handle the mixed content, but very long passages of embedded English may not split optimally.

- **Korean**: Korean uses a mix of sentence-ending particles and punctuation. The standard tokenizer handles Korean reasonably well, but for best results ensure your text uses standard Korean punctuation (`.`, `!`, `?`).

### Arabic Script {#arabic-tips}

- Arabic, Persian, Urdu, and other Arabic-script languages are supported through the General mode
- Right-to-left rendering is handled by the browser; no special configuration is needed
- Ensure your text uses standard Arabic punctuation marks (`.`, `؟`, `!`)
- For Arabic, the embedding model handles both Modern Standard Arabic and common dialects

### Cyrillic Script {#cyrillic-tips}

- Russian, Ukrainian, and Belarusian have dedicated splitters that handle Cyrillic-specific abbreviations and dialogue formatting
- Other Cyrillic-script languages (Serbian, Bulgarian, Macedonian, Kazakh, Kyrgyz, Mongolian, etc.) should use the General mode
- The `razdel` splitter for Russian/Ukrainian/Belarusian correctly handles em-dash dialogue (— Привет! — сказал он.), which is common in Russian literature

### Devanagari and Indic Scripts {#indic-tips}

- Hindi, Marathi, Nepali (Devanagari), Bengali, Gujarati, Kannada, Malayalam, Tamil, Telugu are all supported through the General mode
- The standard tokenizer works with the Indic sentence-ending marker (purna viram `।`) when present
- For best results, ensure consistent punctuation throughout the text

## Proxy Texts for Low-Resource Languages {#proxy-for-low-resource}

For languages with limited representation in the embedding model, alignment quality can be significantly improved by using [proxy texts](proxy.en.md). The recommended approach:

1. Upload your text in the low-resource language
2. Download the split text
3. Machine-translate it into a well-supported language (English or Russian work well)
4. Upload the translation as a proxy document

This is particularly recommended for:
- Bashkir, Chuvash, and other Turkic minority languages
- Languages with fewer than 10 million speakers
- Language pairs where neither language is English, Russian, Chinese, or another major language

## Choosing the Right Configuration {#choosing-config}

| Scenario | Language Setting | Model | Proxy? |
|----------|-----------------|-------|--------|
| English + French novel | en / fr | Default or LaBSE | No |
| Russian + Chinese novel | ru / zh | LaBSE | No |
| English + Bashkir text | en / ba | LaBSE | Recommended for Bashkir side |
| Finnish + Japanese text | General / ja | LaBSE or SONAR | Consider for both sides |
| Hindi + Tamil text | General / General | SONAR or LaBSE | Consider proxy via English |
| Arabic + English text | General / en | LaBSE | No |
