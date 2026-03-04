# Screenshot Registry

This file documents how each screenshot was captured so they can be reproduced
automatically when the interface changes. Each entry describes the page URL,
UI state, and actions needed to recreate the screenshot.

## apps-page.en.png

- **URL:** `/apps`
- **Section:** Apps page (Welcome)
- **State:** Logged in, no special setup needed
- **Locale:** `en`
- **How to reproduce:** Navigate to `/apps` while logged in.

## documents-empty.en.png

- **URL:** `/aligner/documents`
- **Section:** Documents tab — empty state
- **State:** Logged in, language pair set to English → Russian, no documents uploaded for this pair
- **Locale:** `en`
- **How to reproduce:** Navigate to `/aligner/documents`, select `English` → `Russian` with no documents uploaded.

## documents-uploaded.en.png

- **URL:** `/aligner/documents`
- **Section:** Documents tab — with uploaded files
- **State:** Logged in, English → Russian, two files uploaded: `agata_geran_en.txt` (EN side, 466 lines) and `agata_geran_ru.txt` (RU side, 492 lines). Scroll position: top of page.
- **Locale:** `en`
- **How to reproduce:**
  1. Navigate to `/aligner/documents`, select `English` → `Russian`.
  2. Upload `samples/agata_geran_en.txt` to the EN panel.
  3. Upload `samples/agata_geran_ru.txt` to the RU panel.
  4. Screenshot the viewport at scroll position 0.

## marks-panel.en.png

- **URL:** `/aligner/documents`
- **Section:** Documents tab — Sentence preview + Marks panel
- **State:** Same as `documents-uploaded.en.png`, scrolled to bottom of page.
- **Locale:** `en`
- **How to reproduce:** After uploading both files, scroll to page bottom so Marks panel is visible.

## alignments-list.en.png

- **URL:** `/aligner/alignments`
- **Section:** Alignments tab — list view
- **State:** Logged in, English → Russian, no alignments created yet for this pair. Shows "No alignments yet" and the "Upload alignment" section.
- **Locale:** `en`
- **How to reproduce:** Navigate to `/aligner/alignments` with no EN-RU alignments.

## create-alignment-dialog.en.png

- **URL:** `/aligner/alignments`
- **Section:** Alignments tab — Create alignment dialog
- **State:** Dialog open, name filled as "Blue Geranium EN-RU", source = `EN agata_geran_en.txt`, target = `RU agata_geran_ru.txt`.
- **Locale:** `en`
- **How to reproduce:**
  1. Click "+ Create alignment" button.
  2. Type "Blue Geranium EN-RU" in the Name field.
  3. Source and target documents auto-selected from uploaded files.

## alignment-detail.en.png

- **URL:** `/aligner/alignments/<alignment-id>`
- **Section:** Alignment detail page — initial state
- **State:** Alignment just created, status "Init", progress 0/3 batches. Shows Settings (batch size 200, count 1, window 40, shift 0) and Controls (Align next button).
- **Locale:** `en`
- **How to reproduce:** Click on the newly created alignment in the list.

## alignment-conflicts.en.png

- **URL:** `/aligner/alignments/<alignment-id>`
- **Section:** Alignment detail — after first batch aligned
- **State:** Status "Waiting", progress 1/3. Conflicts section shows "31 conflicts found" with conflict 1/31 visible (2 EN lines → 3 RU lines). Settings and Controls visible at top.
- **Locale:** `en`
- **How to reproduce:**
  1. From alignment-detail state, click "Align next".
  2. Wait for batch 1 to complete (~2 minutes).
  3. Scroll to top of page.

## alignment-visualization.en.png

- **URL:** `/aligner/alignments/<alignment-id>`
- **Section:** Alignment detail — Visualization + Editor start
- **State:** Same as alignment-conflicts, scrolled down to show: Visualization section (batch 1 scatter plot, `w: 40`, `s: 0`, en 1-199 / ru 1-242), and the beginning of the Editor section.
- **Locale:** `en`
- **How to reproduce:** Scroll down from conflicts section until Visualization and Editor header are visible.

## alignment-editor.en.png

- **URL:** `/aligner/alignments/<alignment-id>`
- **Section:** Alignment detail — Editor
- **State:** Same page, scrolled to Editor section. Shows side-by-side aligned sentence pairs with row numbers, edit/candidates/navigation buttons per row. Pagination: page 1/10, 20 lines per page.
- **Locale:** `en`
- **How to reproduce:** Scroll to Editor section on alignment detail page.

## create-export.en.png

- **URL:** `/aligner/create`
- **Section:** Create tab — settings and empty preview
- **State:** Alignment "Blue Geranium EN-RU" selected, status Waiting, progress 1/3. Settings: paragraph structure = ru, language order = en-ru, highlight style = Simple. Book preview empty ("Adjust settings and generate preview."). Download section partially visible.
- **Locale:** `en`
- **How to reproduce:** Navigate to `/aligner/create`, select the alignment.

## create-preview.en.png

- **URL:** `/aligner/create`
- **Section:** Create tab — with generated preview
- **State:** Same as create-export, but after clicking "Generate preview". Book preview shows title page ("The Blue Geranium" / "Синяя герань", "Agatha Christie" / "Кристи Агата") and first paragraphs side by side.
- **Locale:** `en`
- **How to reproduce:** From create-export state, click "Generate preview".

## create-download.en.png

- **URL:** `/aligner/create`
- **Section:** Create tab — Download section
- **State:** Same page, scrolled to bottom. Shows all 6 download cards: HTML book, TMX corpora, Sentence corpora, Paragraph corpora, Structured formats (XML/JSON), Alignment database (.lt).
- **Locale:** `en`
- **How to reproduce:** Scroll to bottom of Create page after generating preview.

---

## Notes for automation

- All screenshots use locale `en`. For `ru` locale screenshots, switch the app language via the language switcher or by setting `localStorage.setItem('locale', 'ru')` before navigating.
- Screenshot filenames follow the pattern: `<section>.<locale>.png`.
- The alignment ID in URLs varies per session. Use the alignment name "Blue Geranium EN-RU" to identify it.
- Sample texts are in `samples/` folder at the project root.
- Browser viewport size at capture: default Playwright size (1280x720).
