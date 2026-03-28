# HTML Presentation Live Editor

A single-file HTML editor for two related workflows:

- editing normal HTML presentation/page documents
- opening, editing, creating, and exporting remark-style slideshow HTML files

Everything lives in index.html. There are no build steps, frameworks, or package dependencies.

## What It Does

- Imports normal HTML files and renders them in a live editable preview.
- Auto-detects remark slideshow wrapper files that use `<textarea id="source">` and `remark.create(...)`.
- Switches into a dedicated remark slideshow mode when that format is detected.
- Lets you work visually in the preview and structurally in the source panel.
- Exports back to the correct format for the current mode.

## Main Features

- Single-file app with inline HTML, CSS, and JavaScript only
- Compact toolbar with mode-aware actions
- Collapsible source panel
- Multi-page/page-selector workflow
- Dedicated remark slideshow mode
- `New HTML` and `New Remark Deck` actions
- Live editable preview in a sandboxed iframe
- In-place text editing for common visible text elements
- Image replacement with local files embedded as Base64 data URLs
- Add text blocks and images onto pages/slides
- Drag/move selected editable elements in the preview
- Delete selected elements
- Delete pages in normal HTML mode
- Delete slides in remark mode
- Clean export without editor-only UI attributes

## Remark Slideshow Support

The editor treats remark decks as a first-class format instead of as ordinary rendered HTML pages.

Supported remark workflow:

- Detect wrapper files containing `<textarea id="source">`
- Read slide source from that textarea
- Split slides on lines containing only `---`
- Preserve top-of-slide directives such as:
  - `class:`
  - `name:`
  - `layout:`
  - similar slide metadata lines
- Map each slide to one editor page in the preview
- Edit current slide `class:` metadata from the toolbar
- Create new slides from the UI
- Delete slides while keeping the deck valid
- Export back to a remark-compatible wrapper HTML file by default

## Editing Model

### Normal HTML mode

- The source panel contains HTML.
- The preview renders sanitized HTML.
- Multi-page detection is based on page-like sections already present in the document, plus pages created in the editor.
- Export returns sanitized standalone HTML.

### Remark slideshow mode

- The source panel contains the deck source that belongs inside `<textarea id="source">`.
- The preview renders one slide per page in the editor.
- Slide content can be edited visually in the preview.
- Slide structure can be edited directly in the source panel.
- Export rebuilds a full wrapper HTML file with:
  - `<textarea id="source">`
  - slide separators using `---`
  - preserved or updated slide metadata
  - `remark.create(...)` initialization

## Supported Editing Actions

- Select visible text elements and edit them in place
- Select images and replace them from local files
- Add text boxes
- Add images
- Drag/move selected editable elements
- Style selected text:
  - font family
  - size
  - weight
  - text color
  - background color
  - alignment
  - italic
  - underline
- Style selected images:
  - width
  - height
  - radius
  - opacity
  - rotation
  - object fit
  - filter preset
- Delete the currently selected element
- Delete the current page or slide with confirmation

## Delete Behavior

- `Delete Element` removes the currently selected editable/movable element.
- Keyboard `Delete` and `Backspace` also remove the selected element when you are not actively typing in an input, textarea, select, or contenteditable field.
- `Delete Page` or `Delete Slide` removes the current page/slide from the document model after confirmation.
- The final remaining page/slide is protected so the document stays valid.
- Deleted elements, pages, and slides do not appear in exported output.

## Source And Preview Behavior

- The source panel and preview stay synchronized.
- In normal HTML mode, source changes re-render the HTML preview.
- In remark mode, source changes re-parse the slide deck.
- Visual edits in the preview sync back into the source model.
- In remark mode, the editor uses a lightweight internal renderer for useful slide editing rather than depending on the full remark runtime for previewing.

## Safety Notes

The preview uses lightweight client-side sanitization for imported/rendered HTML content.

During sanitization it removes or neutralizes:

- `<script>` tags
- inline event handlers such as `onclick`
- `meta http-equiv="refresh"`
- `javascript:` URLs in links

This is meant to keep previewing practical and safer, but it is not a full security boundary for hostile documents.

## How To Use

1. Open `index.html` in a modern browser.
2. Use `Import HTML` to open an existing file, or start from `New HTML` / `New Remark Deck`.
3. If a remark wrapper file is imported, the editor switches into remark slideshow mode automatically.
4. Use the source panel for structural edits.
5. Use the preview for visual text/image edits, selection, dragging, and styling.
6. Use `New Page` / `New Slide` to add more pages.
7. Use `Delete Element` or `Delete Page` / `Delete Slide` when needed.
8. Export to download a mode-appropriate HTML file.

## Export Behavior

### Normal HTML mode export

Exports sanitized standalone HTML with:

- content edits
- inline style edits
- moved elements
- updated image sources
- removed deleted elements/pages

### Remark slideshow mode export

Exports a remark-compatible HTML wrapper with:

- `<textarea id="source">`
- current slide source
- `---` separators
- preserved or updated slide metadata
- current slide content
- removed deleted slides
- remark initialization script

## Best For

- quick editing of presentation-style HTML files
- updating static or mostly static slide-like pages
- working with existing remark slideshow HTML decks
- creating small remark decks from scratch in one file
- making copy and image updates without leaving the browser

## Limitations

- interactive HTML that depends on scripts is intentionally reduced to safer preview/export behavior
- the remark preview is lightweight and practical, not a full pixel-perfect remark runtime clone
- not every HTML element is made directly editable
- large embedded images can make exported files much bigger

## File Structure

- `index.html` — the full application
- `README.md` — usage notes and feature overview

## Development Notes

- Vanilla HTML, CSS, and JavaScript only
- No frameworks
- No package manager
- No build process
- Designed to stay maintainable as one HTML file with mode-aware logic

## License

Use and adapt as needed for your project.
