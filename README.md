# HTML Presentation Live Editor

A single-file HTML application for quickly editing existing HTML presentation materials.

Everything is contained in [index.html](/d:/Project/HTML%20Live%20Editor/index.html): the UI, styles, and JavaScript. There are no external dependencies, build steps, or libraries.

## What It Does

- Paste raw HTML into a source panel.
- See the result render live in a preview panel.
- Edit visible text directly in the preview.
- Click images in the preview to replace them with local files.
- Export the updated page as a clean standalone HTML file.

## Features

- Modern two-column editor layout
- Collapsible source panel
- Automatic light/dark mode via `prefers-color-scheme`
- Safe preview approach using a sandboxed iframe
- Script tags removed from pasted HTML
- Inline event handler attributes removed from pasted HTML
- `javascript:` links neutralized
- In-place editing for common text-bearing elements
- Image replacement with Base64 data URLs
- Clean export without editor-only attributes or controls
- Fully self-contained output file

## File Structure

- `index.html` — the complete application

## How To Use

1. Open `index.html` in a modern browser.
2. Paste a full HTML document or HTML fragment into the left source panel.
3. Edit text directly in the right preview panel.
4. Click any image in the preview to choose a replacement from your device.
5. Click `Export Page` to download `edited_presentation.html`.

## Export Behavior

The exported file includes:

- text edits made through in-place editing
- updated image `src` values, including Base64-embedded local replacements
- the cleaned HTML structure from the live preview

The exported file does not include:

- editor UI controls
- preview-only helper attributes
- temporary editing markers
- `contenteditable` attributes added only for editor mode

## Safety Notes

The app is designed to preserve presentation content while avoiding obvious executable behavior in the preview.

During sanitization it removes or neutralizes:

- `<script>` tags
- inline event handlers such as `onclick`
- `meta http-equiv="refresh"`
- `javascript:` URLs in links

This is a lightweight client-side sanitization approach, not a full security sandbox for hostile documents.

## Best For

- updating slide-style HTML pages
- editing landing-page-like presentation files
- making quick copy changes without digging through source markup
- replacing embedded presentation images before export

## Limitations

- complex interactive pages are intentionally reduced to safer, mostly static editing behavior
- scripts from pasted HTML do not run in the preview
- not every element is made editable; the editor focuses on suitable visible text containers
- large embedded images can increase exported file size significantly

## Development Notes

- Vanilla HTML, CSS, and JavaScript only
- No frameworks
- No package manager required
- No build process required

## License

Use and adapt as needed for your project.
