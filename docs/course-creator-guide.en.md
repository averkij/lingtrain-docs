# Course Creator Guide {#creator-guide}

This guide walks you through every step of creating, editing, and publishing a course in Lingtrain. The course studio provides a three-rail workspace for building structured learning content with modules and units.

## Overview {#overview}

The **Course Studio** is the authoring environment for course creators. It is available at `/workspace/courses` and provides tools to:

- Create courses with bilingual titles, summaries, and metadata
- Organize content into modules and units
- Edit unit content using a visual form editor or raw JSON editor
- Import content from JSON files (single units or full course bundles)
- Generate content with AI assistance
- Validate and publish courses for learners

The studio uses a three-rail layout: a **curriculum outline** on the left, the **editor canvas** in the center, and a **validation panel** on the right. Both side rails are collapsible.

## Creating a Course {#creating-a-course}

1. Navigate to **Workspace > Courses** (`/workspace/courses`).
2. Click **Create Course**.
3. Fill in the course settings:
   - **Title** (EN and/or RU) -- at least one language is required
   - **Summary** (EN and/or RU) -- a short description shown in the catalog
   - **Domain** -- choose from: `language-learning`, `programming`, `general`, `writing`
   - **Difficulty** -- `beginner`, `intermediate`, or `advanced`
   - **Cover image** -- optional cover shown on the course card
   - **Tags** -- optional keywords for catalog filtering
4. Click **Save**. Your new course opens in the studio.

## Adding Modules and Units {#adding-modules-units}

Courses are organized in a three-level hierarchy: **Course > Modules > Units**.

### Modules {#modules}

- Click **Add Module** in the curriculum outline (left rail).
- Give the module a title. Modules act as chapters or sections.
- Drag modules to reorder them in the curriculum.

### Units {#units}

- Select a module, then click **Add Unit**.
- Choose a unit **kind**:

| Kind | Purpose |
|------|---------|
| `lesson` | Teaching content with text, media, and optional activities |
| `reference` | Reference material (glossary, grammar tables) |
| `parallel_reading` | Side-by-side bilingual text using frozen alignment snapshots |
| `drill` | Practice exercises focused on activities |
| `assessment` | Graded test with scoring and attempt limits |
| `project` | Open-ended assignment with instructions |

- Drag units to reorder within or across modules.
- Click a unit to open it in the editor canvas.

## Using the Form Editor {#form-editor}

The **Form** tab provides a visual editor for building unit content without writing JSON.

### Content Blocks {#content-blocks}

Click **Add Block** to insert content. Available block types:

| Block Type | Description |
|------------|-------------|
| `rich_text` | Formatted text with Markdown support |
| `callout` | Highlighted box with tone: `info`, `tip`, `warning`, `danger` |
| `term_grid` | Filterable vocabulary grid with terms and definitions |
| `dialogue` | Chat-bubble conversation between speakers |
| `code` | Syntax-highlighted code block with language selector |
| `parallel_reader` | Frozen bilingual text from an alignment snapshot |

Each block has a unique `block_id` generated automatically. Use the drag handle to reorder blocks.

### Activities {#activities}

Click **Add Activity** to insert interactive exercises. Available activity types:

| Activity Type | Description |
|---------------|-------------|
| `multiple_choice` | Single or multi-select question with options |
| `match_pairs` | Drag-and-drop or click-to-match pair exercise |
| `fill_in_blank` | Text with inline gaps for learner input |
| `order_sequence` | Drag items into the correct order |
| `flashcards` | Two-sided cards with 3D flip animation |
| `short_text` | Free-text input graded against accepted answers |

Activities are scored automatically by the backend scoring engine. Drill and assessment units focus on activities, while lesson units typically mix content blocks with activities.

### Completion Rules {#form-completion-rules}

Set how the unit determines learner completion:

- **All required** -- learner must interact with every activity
- **Score threshold** -- learner must achieve a minimum score percentage
- **Mixed** -- combines both requirements

## Using the JSON Editor {#json-editor}

The **JSON** tab shows the raw `course-unit.v1` payload. This is useful for:

- Pasting content from external tools
- Fine-tuning fields not exposed in the form editor
- Debugging validation errors

The editor validates your JSON in real time. Errors appear inline and are also listed in the validation panel (right rail).

See the [Course JSON Format Reference](course-json-reference.en.md) for the full schema documentation.

## Importing Content {#importing-content}

### Import a Single Unit {#import-unit}

1. Open the unit editor and click **Import** in the toolbar.
2. Select a `.json` file containing a `course-unit.v1` payload.
3. The system validates the file (256 KB size limit) and shows a preview of any errors or warnings.
4. Confirm to replace the current unit content.

### Import a Course Bundle {#import-bundle}

1. From the course list page, click **Import Course**.
2. Select a `.json` file containing a `course-bundle.v1` payload.
3. The system creates a new draft course with all modules and units from the bundle.
4. Review the imported content in the studio before publishing.

See the [JSON Reference](course-json-reference.en.md#bundle-schema) for the bundle format.

## AI-Assisted Generation {#ai-generation}

The studio integrates AI to help generate course content. All AI features require an active subscription with available AI operation credits.

### Generate a Unit {#ai-generate-unit}

1. Open a unit in the editor and click **Generate with AI** in the toolbar.
2. Fill in the structured form:
   - **Topic** -- what the unit should teach
   - **Domain** -- target subject area
   - **Kind** -- unit type (lesson, drill, assessment, etc.)
   - **Difficulty** -- beginner, intermediate, or advanced
   - **Seed text** (optional) -- paste existing content as a starting point
   - **Modifier** (optional) -- free-form instructions to guide the AI
3. Click **Generate**. The AI produces a `course-unit.v1` payload.
4. Review the result in **dual preview**:
   - **Rendered** tab -- shows how learners will see the content
   - **JSON** tab -- shows the raw payload with validation status
5. Choose an action:
   - **Use this** -- imports the content into the editor as a draft
   - **Edit** -- switches to the JSON tab for manual adjustments
   - **Regenerate** -- generates a new version

### Generate a Course Bundle {#ai-generate-bundle}

1. From the course list page (`/workspace/courses`), click **Generate course with AI**.
2. Fill in the same structured form plus module count (1--3 modules).
3. The AI generates a full `course-bundle.v1` with modules and units.
4. On success, a new draft course is created. Click **Open in Studio** to review.

### Fix with AI {#ai-fix}

When the JSON editor shows validation errors:

1. Click **Fix with AI** (appears in the JSON tab toolbar when errors exist).
2. The system sends the noisy JSON to the AI normalization service.
3. Review the corrected JSON in a confirmation dialog.
4. Click **Apply** to replace the editor content.

### AI Content Review Requirement {#ai-review}

All AI-generated content is saved as a **draft** with the `ai_generated` flag set to `true`. This flag **blocks publishing** until you review and edit the content:

- The validation panel shows a warning: *"N AI-generated unit(s) need review"*
- Any save in Form mode or JSON mode **automatically clears** the flag
- This ensures every AI-generated unit has been reviewed by a human before publication

## Publishing {#publishing}

### Validation {#validation}

Before publishing, the validation panel (right rail) checks for:

- Missing required fields (title, schema version)
- Invalid block or activity types
- Size limit violations (256 KB per unit)
- AI-generated units that need review
- Missing learning objectives and summaries (warnings)

Fix all **errors** before publishing. Warnings are informational.

### Creating a Release {#creating-release}

1. Ensure all validation errors are resolved.
2. Click **Publish** in the course settings.
3. The system creates an immutable **release snapshot** that freezes all content, including parallel text references.
4. The published course appears in the learner catalog.

Learners always access the published release, not the live draft. You can continue editing after publishing -- changes appear only when you create a new release.

## Tips {#tips}

- **Starter templates** -- use the template dialog when creating a new unit to start from a pre-built structure instead of a blank canvas.
- **Preview mode** -- the Preview tab shows exactly how learners will see the unit, including interactive activities.
- **Autosave** -- the studio autosaves your work as you edit. The autosave indicator in the toolbar shows the current save status.
- **Keyboard workflow** -- use the curriculum outline to quickly navigate between units without leaving the studio.
- **Reorder across modules** -- drag a unit from one module to another to reorganize your curriculum.
