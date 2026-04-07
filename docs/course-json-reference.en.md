# Course JSON Format Reference {#json-reference}

This reference documents the JSON schemas used for course content in Lingtrain. These schemas are used by the unit editor, import/export, and AI generation features.

## course-unit.v1 Schema {#unit-schema}

Every unit's content is stored as a JSON object following the `course-unit.v1` schema.

### Top-Level Fields {#unit-top-level}

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `schema_version` | `string` | Yes | Must be `"course-unit.v1"` |
| `kind` | `string` | Yes | Unit type: `lesson`, `reference`, `parallel_reading`, `drill`, `assessment`, `project` |
| `title` | `string` | Yes | Unit display title |
| `summary` | `string` | No | Short description of the unit |
| `learning_objectives` | `string[]` | No | List of learning goals for the unit |
| `estimated_minutes` | `integer` | No | Estimated time to complete (minutes) |
| `content` | `Block[]` | No | Array of content blocks (see [Block Types](#block-types)) |
| `activities` | `Activity[]` | No | Array of interactive activities (see [Activity Types](#activity-types)) |
| `completion_rule` | `CompletionRule` | No | How the unit determines learner completion (see [Completion Rules](#completion-rules)) |

**Size limit:** 256 KB per unit spec payload.

### Validation Rules {#unit-validation}

- `schema_version` must be exactly `"course-unit.v1"`
- `kind` must be one of the six valid values
- `title` must be a non-empty string
- Each block must have a unique `block_id` and a valid `type`
- Each activity must have a unique `activity_id` and a valid `type`
- Missing `summary`, `learning_objectives`, or `estimated_minutes` produce warnings (not errors)

## Block Types {#block-types}

Content blocks are the building blocks of unit content. Each block has a common `block_id` and `type` field, plus type-specific fields.

### rich_text {#block-rich-text}

Formatted text content with Markdown support.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"rich_text"` |
| `body` | `string` | Yes | Markdown-formatted text content |

### callout {#block-callout}

Highlighted information box with a visual tone.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"callout"` |
| `tone` | `string` | Yes | One of: `info`, `tip`, `warning`, `danger` |
| `title` | `string` | No | Optional callout heading |
| `body` | `string` | Yes | Callout content text |

### term_grid {#block-term-grid}

Filterable vocabulary grid with terms and definitions.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"term_grid"` |
| `terms` | `Term[]` | Yes | Array of term objects |

Each `Term` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `term` | `string` | Yes | The term or word |
| `definition` | `string` | Yes | The definition or translation |

### dialogue {#block-dialogue}

Chat-bubble conversation between speakers.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"dialogue"` |
| `lines` | `Line[]` | Yes | Array of dialogue lines |

Each `Line` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `speaker` | `string` | Yes | Speaker name or identifier |
| `text` | `string` | Yes | The spoken line |

### code {#block-code}

Syntax-highlighted code block.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"code"` |
| `language` | `string` | No | Programming language for syntax highlighting |
| `code` | `string` | Yes | The code content |

### parallel_reader {#block-parallel-reader}

Side-by-side bilingual text from an alignment snapshot.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `block_id` | `string` | Yes | Unique block identifier |
| `type` | `string` | Yes | Must be `"parallel_reader"` |
| `alignment_id` | `integer` | No | Source alignment ID (resolved at publish time) |
| `pairs` | `Pair[]` | Yes | Array of sentence pairs |

Each `Pair` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `from` | `string` | Yes | Source language text |
| `to` | `string` | Yes | Target language text |

## Activity Types {#activity-types}

Activities are interactive exercises scored by the backend engine. Each activity has a common `activity_id` and `type` field.

### multiple_choice {#activity-multiple-choice}

Single or multi-select question with answer options.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"multiple_choice"` |
| `prompt` | `string` | Yes | The question text |
| `options` | `Option[]` | Yes | Array of answer options |
| `multiple` | `boolean` | No | Allow multiple selections (default: `false`) |

Each `Option` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | `string` | Yes | Option identifier |
| `text` | `string` | Yes | Option display text |
| `correct` | `boolean` | Yes | Whether this is a correct answer |
| `explanation` | `string` | No | Explanation shown after attempt lock |

### match_pairs {#activity-match-pairs}

Drag-and-drop or click-to-match pair exercise.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"match_pairs"` |
| `prompt` | `string` | No | Optional instruction text |
| `pairs` | `MatchPair[]` | Yes | Array of pairs to match |

Each `MatchPair` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `left` | `string` | Yes | Left-side item |
| `right` | `string` | Yes | Right-side matching item |

### fill_in_blank {#activity-fill-in-blank}

Text with inline gaps for learner input.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"fill_in_blank"` |
| `prompt` | `string` | No | Optional instruction text |
| `segments` | `Segment[]` | Yes | Array of text and gap segments |

Each `Segment` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | `string` | Yes | `"text"` or `"gap"` |
| `value` | `string` | Yes | Display text (for `text`) or correct answer (for `gap`) |
| `alternatives` | `string[]` | No | Additional accepted answers (for `gap` only) |

### order_sequence {#activity-order-sequence}

Drag items into the correct order.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"order_sequence"` |
| `prompt` | `string` | No | Optional instruction text |
| `items` | `string[]` | Yes | Items in the correct order (shuffled for display) |

### flashcards {#activity-flashcards}

Two-sided cards with 3D flip animation.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"flashcards"` |
| `prompt` | `string` | No | Optional instruction text |
| `cards` | `Card[]` | Yes | Array of flashcard objects |

Each `Card` object:

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `front` | `string` | Yes | Front side text (question/term) |
| `back` | `string` | Yes | Back side text (answer/definition) |

### short_text {#activity-short-text}

Free-text input graded against accepted answers.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `activity_id` | `string` | Yes | Unique activity identifier |
| `type` | `string` | Yes | Must be `"short_text"` |
| `prompt` | `string` | Yes | The question or instruction |
| `accepted_answers` | `string[]` | Yes | List of correct answers (case-insensitive matching) |
| `explanation` | `string` | No | Explanation shown after attempt lock |

## Completion Rules {#completion-rules}

The `completion_rule` field controls how the backend determines whether a learner has completed a unit.

| Rule | Fields | Description |
|------|--------|-------------|
| `all_required` | `{ "type": "all_required" }` | Learner must interact with every activity |
| `score_threshold` | `{ "type": "score_threshold", "threshold": 0.8 }` | Learner must achieve the specified score (0.0--1.0) |
| `mixed` | `{ "type": "mixed", "threshold": 0.8 }` | Learner must interact with all activities AND achieve the score threshold |

If `completion_rule` is omitted, the default behavior is `all_required`.

## course-bundle.v1 Schema {#bundle-schema}

A course bundle packages an entire course (with modules and units) into a single JSON file for import or AI generation.

### Top-Level Fields {#bundle-top-level}

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `schema_version` | `string` | Yes | Must be `"course-bundle.v1"` |
| `title_en` | `string` | No | Course title in English |
| `title_ru` | `string` | No | Course title in Russian |
| `summary_en` | `string` | No | Course summary in English |
| `summary_ru` | `string` | No | Course summary in Russian |
| `domain` | `string` | Yes | One of: `language-learning`, `programming`, `general`, `writing` |
| `difficulty` | `string` | Yes | One of: `beginner`, `intermediate`, `advanced` |
| `tags` | `string[]` | No | Course tags for catalog filtering |
| `modules` | `Module[]` | Yes | Array of module objects |

At least one of `title_en` or `title_ru` must be provided.

### Module Object {#bundle-module}

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | `string` | Yes | Module title |
| `units` | `Unit[]` | Yes | Array of unit objects in this module |

### Unit Object (in bundle) {#bundle-unit}

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `title` | `string` | Yes | Unit title |
| `kind` | `string` | Yes | Unit type (same values as `course-unit.v1`) |
| `estimated_minutes` | `integer` | No | Estimated completion time |
| `unit_spec` | `object` | Yes | Full `course-unit.v1` payload |

## Examples {#examples}

### Lesson Unit {#example-lesson}

A teaching unit with rich text, a callout, and a multiple-choice activity.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "lesson",
  "title": "Introduction to Present Tense",
  "summary": "Learn the basics of present tense verb conjugation in English.",
  "learning_objectives": [
    "Identify present tense verbs",
    "Conjugate regular verbs in present tense"
  ],
  "estimated_minutes": 15,
  "content": [
    {
      "block_id": "b1",
      "type": "rich_text",
      "body": "The **present tense** is used to describe actions happening now or habitual actions.\n\nFor regular verbs, add **-s** or **-es** to the base form for third person singular."
    },
    {
      "block_id": "b2",
      "type": "callout",
      "tone": "tip",
      "title": "Remember",
      "body": "Verbs ending in -ch, -sh, -ss, -x, or -z add **-es** instead of **-s**."
    }
  ],
  "activities": [
    {
      "activity_id": "a1",
      "type": "multiple_choice",
      "prompt": "Which sentence uses the correct present tense form?",
      "options": [
        { "id": "o1", "text": "She walk to school.", "correct": false },
        { "id": "o2", "text": "She walks to school.", "correct": true, "explanation": "Third person singular requires the -s ending." },
        { "id": "o3", "text": "She walking to school.", "correct": false }
      ]
    }
  ],
  "completion_rule": { "type": "all_required" }
}
```

### Reference Unit {#example-reference}

A vocabulary reference with a term grid.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "reference",
  "title": "Kitchen Vocabulary",
  "summary": "Essential kitchen terms in English and Russian.",
  "estimated_minutes": 5,
  "content": [
    {
      "block_id": "b1",
      "type": "term_grid",
      "terms": [
        { "term": "knife", "definition": "A sharp tool for cutting" },
        { "term": "frying pan", "definition": "A flat pan for cooking with oil" },
        { "term": "oven", "definition": "An enclosed compartment for baking" },
        { "term": "cutting board", "definition": "A flat surface for chopping food" }
      ]
    }
  ],
  "activities": [],
  "completion_rule": { "type": "all_required" }
}
```

### Parallel Reading Unit {#example-parallel-reading}

Side-by-side bilingual text for reading practice.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "parallel_reading",
  "title": "The Little Prince, Chapter 1",
  "summary": "Read the opening of The Little Prince in English and Russian.",
  "estimated_minutes": 10,
  "content": [
    {
      "block_id": "b1",
      "type": "parallel_reader",
      "pairs": [
        { "from": "Once when I was six years old I saw a magnificent picture in a book.", "to": "When I was six I once saw a splendid picture in a book about the primeval forest." },
        { "from": "It was a picture of a boa constrictor in the act of swallowing an animal.", "to": "On the picture a boa constrictor was shown swallowing a wild animal." }
      ]
    }
  ],
  "activities": [],
  "completion_rule": { "type": "all_required" }
}
```

### Drill Unit {#example-drill}

A practice unit focused on interactive exercises.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "drill",
  "title": "Word Order Practice",
  "summary": "Practice arranging words into correct English sentences.",
  "estimated_minutes": 10,
  "content": [
    {
      "block_id": "b1",
      "type": "rich_text",
      "body": "Arrange the words in the correct order to form a grammatically correct sentence."
    }
  ],
  "activities": [
    {
      "activity_id": "a1",
      "type": "order_sequence",
      "prompt": "Put these words in order:",
      "items": ["The", "cat", "sat", "on", "the", "mat"]
    },
    {
      "activity_id": "a2",
      "type": "fill_in_blank",
      "prompt": "Complete the sentences:",
      "segments": [
        { "type": "text", "value": "She " },
        { "type": "gap", "value": "goes", "alternatives": ["goes"] },
        { "type": "text", "value": " to the store every " },
        { "type": "gap", "value": "morning", "alternatives": ["morning", "day"] },
        { "type": "text", "value": "." }
      ]
    }
  ],
  "completion_rule": { "type": "all_required" }
}
```

### Assessment Unit {#example-assessment}

A graded test with scoring threshold and multiple activity types.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "assessment",
  "title": "Present Tense Quiz",
  "summary": "Test your knowledge of present tense verb forms.",
  "estimated_minutes": 15,
  "content": [
    {
      "block_id": "b1",
      "type": "callout",
      "tone": "info",
      "body": "You have 3 attempts per question. You need 80% to pass."
    }
  ],
  "activities": [
    {
      "activity_id": "a1",
      "type": "multiple_choice",
      "prompt": "Select ALL correct present tense forms:",
      "multiple": true,
      "options": [
        { "id": "o1", "text": "runs", "correct": true },
        { "id": "o2", "text": "ran", "correct": false },
        { "id": "o3", "text": "eats", "correct": true },
        { "id": "o4", "text": "eaten", "correct": false }
      ]
    },
    {
      "activity_id": "a2",
      "type": "match_pairs",
      "prompt": "Match each verb with its third person form:",
      "pairs": [
        { "left": "go", "right": "goes" },
        { "left": "have", "right": "has" },
        { "left": "do", "right": "does" },
        { "left": "be", "right": "is" }
      ]
    },
    {
      "activity_id": "a3",
      "type": "short_text",
      "prompt": "What is the third person singular form of 'fly'?",
      "accepted_answers": ["flies"],
      "explanation": "Verbs ending in consonant + y change to -ies: fly -> flies."
    }
  ],
  "completion_rule": { "type": "score_threshold", "threshold": 0.8 }
}
```

### Project Unit {#example-project}

An open-ended assignment with instructions and flashcards for reference.

```json
{
  "schema_version": "course-unit.v1",
  "kind": "project",
  "title": "Write a Short Story",
  "summary": "Apply what you have learned by writing a short story using present tense.",
  "learning_objectives": [
    "Apply present tense in creative writing",
    "Use vocabulary from this module"
  ],
  "estimated_minutes": 30,
  "content": [
    {
      "block_id": "b1",
      "type": "rich_text",
      "body": "Write a short story (200-300 words) using **only present tense** verbs. Include at least 5 vocabulary words from the Kitchen Vocabulary reference unit.\n\nSubmit your story to your instructor for feedback."
    },
    {
      "block_id": "b2",
      "type": "callout",
      "tone": "tip",
      "title": "Getting Started",
      "body": "Start with a simple scene: describe someone cooking a meal. Use the vocabulary flashcards below for inspiration."
    }
  ],
  "activities": [
    {
      "activity_id": "a1",
      "type": "flashcards",
      "prompt": "Review these vocabulary words before writing:",
      "cards": [
        { "front": "chop", "back": "To cut into small pieces with a knife" },
        { "front": "stir", "back": "To mix a liquid by moving a spoon in circles" },
        { "front": "simmer", "back": "To cook gently just below boiling point" }
      ]
    }
  ],
  "completion_rule": { "type": "all_required" }
}
```
