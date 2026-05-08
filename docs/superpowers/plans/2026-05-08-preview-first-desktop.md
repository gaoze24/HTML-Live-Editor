# Preview-First Desktop Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Tune the editor's desktop layout so a 1920x1080 / wide 16:9 workspace renders preview-first, with aligned chrome, without changing app behavior.

**Architecture:** All edits are CSS-only changes inside `index.html`. We adjust the workspace grid defaults, the desktop-only toolbar spacing, and the panel header alignment. No JS, no markup restructuring.

**Tech Stack:** Vanilla HTML / CSS in a single `index.html`. No build, no test framework. Verification is manual via a local `python3 -m http.server` plus a browser snapshot.

---

## File Structure

- Modify: `index.html` (CSS only). All changes live inside the existing `<style>` block in the document head.
- No new files. No JS changes.

## Verification Setup (used by every task)

There is no test runner. Each task verifies in the browser.

Start the local server once at the start of execution:

```bash
cd /Users/bytedance/Documents/Project/HTML-Live-Editor
python3 -m http.server 8000
```

Open `http://localhost:8000/index.html` in a browser at a 1920x1080-equivalent window before each verification step. If using the Cursor browser tool, also call `browser_resize` with `width: 1920, height: 1080` before each snapshot.

---

### Task 1: Widen default workspace and shrink default source pane

**Files:**
- Modify: `index.html` (the `.workspace` rule, around lines 483–491)

Goal: at default load on desktop, source should be narrower than today and preview should get the rest of the width.

- [ ] **Step 1: Read current rule**

Open `index.html` and confirm the existing rule:

```css
.workspace {
  min-height: 0;
  height: 100%;
  display: grid;
  grid-template-columns: minmax(18rem, var(--source-pane-size, 31rem)) 0.8rem minmax(0, 1fr);
  gap: 0.75rem;
  transition: grid-template-columns 220ms ease;
  overflow: hidden;
}
```

- [ ] **Step 2: Replace the rule with a narrower default source pane**

Use StrReplace to replace the block above with:

```css
.workspace {
  min-height: 0;
  height: 100%;
  display: grid;
  grid-template-columns: minmax(16rem, var(--source-pane-size, 24rem)) 0.8rem minmax(0, 1fr);
  gap: 0.75rem;
  transition: grid-template-columns 220ms ease;
  overflow: hidden;
}
```

Only the `grid-template-columns` line changes (`18rem` → `16rem`, `31rem` → `24rem`). Everything else stays identical.

- [ ] **Step 3: Verify in browser at 1920x1080**

Reload `http://localhost:8000/index.html`. Confirm:

- Source and preview are side-by-side.
- Preview occupies clearly more horizontal space than source (roughly 70/30 or wider in preview's favor at 1920px).
- Resize handle still works: dragging the divider widens/narrows the source pane.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "style(workspace): narrow default source pane for preview-first desktop"
```

---

### Task 2: Keep desktops above 1280px on the side-by-side layout

**Files:**
- Modify: `index.html` (the `@media (max-width: 980px)` block, around lines 1007–1071)

Goal: avoid prematurely collapsing to a stacked layout on narrow desktops. The current 980px breakpoint is fine for tablets, but make sure no other rule above it forces stacking.

- [ ] **Step 1: Read current breakpoints**

Confirm in `index.html`:

- A `@media (max-width: 1260px)` rule exists and only adjusts `#pageControlsGroup` width.
- A `@media (max-width: 980px)` rule switches `.workspace` to a single column / stacked rows layout.
- A `@media (max-width: 640px)` rule tightens mobile spacing.

If any other media query above `980px` changes `.workspace` `grid-template-columns`, list it and stop. Otherwise continue.

- [ ] **Step 2: Add a desktop-floor rule above the 980px block**

Insert a new media query immediately above `@media (max-width: 1260px)`:

```css
@media (min-width: 1440px) {
  .workspace {
    grid-template-columns: minmax(16rem, var(--source-pane-size, 22rem)) 0.8rem minmax(0, 1fr);
  }
}
```

This keeps wider desktops (1440px and up, including 1920x1080) at a slightly more compact source default than mid-size desktops, so the preview gets even more room.

- [ ] **Step 3: Verify**

Reload at 1920x1080. Source pane should look slightly narrower than at, say, 1280px. Drag the resize handle and confirm the user-set width still wins (the rule respects `var(--source-pane-size, …)`).

Then resize browser to ~1100px and confirm side-by-side is still in effect (no stacking until ≤ 980px).

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "style(workspace): tighten default source pane on >=1440px desktops"
```

---

### Task 3: Tighten desktop toolbar grouping

**Files:**
- Modify: `index.html` (the `.toolbar` and `.toolbar__row` rules around lines 208–268, plus a new desktop media query)

Goal: on wide desktops, the toolbar pills currently feel spaced out. We want tighter, more deliberate clustering without changing the two-row contract.

- [ ] **Step 1: Locate base rules**

Confirm in `index.html`:

```css
.toolbar {
  display: grid;
  grid-template-rows: repeat(2, var(--toolbar-row-height));
  gap: 0.36rem;
  width: 100%;
  min-width: 0;
}

.toolbar__row {
  display: flex;
  align-items: stretch;
  gap: 0.38rem;
  /* ... */
}

.toolbar__group {
  display: flex;
  flex-wrap: nowrap;
  gap: 0.32rem;
  align-items: center;
  padding: 0.18rem;
  /* ... */
}
```

- [ ] **Step 2: Add desktop-only toolbar tightening**

Insert this new media query immediately below the existing `@keyframes shell-rise` block (around line 999), so it lives above the existing `@media (max-width: 1260px)` block:

```css
@media (min-width: 1440px) {
  .toolbar {
    gap: 0.3rem;
  }

  .toolbar__row {
    gap: 0.32rem;
  }

  .toolbar__group {
    gap: 0.26rem;
    padding: 0.16rem;
  }
}
```

- [ ] **Step 3: Verify**

Reload at 1920x1080. Confirm:

- Toolbar still has two visible rows (primary + context).
- Toolbar groups look like clusters, not floating pills.
- No row clipping; all primary buttons (Hide Source, Import HTML, New HTML, New Remark Deck, New Page, Add Text, Add Image, Add Code, Export HTML) remain reachable.
- Status / mode badge still right-aligns via `.toolbar__group--status`.

- [ ] **Step 4: Commit**

```bash
git add index.html
git commit -m "style(toolbar): tighten group spacing on wide desktops"
```

---

### Task 4: Align panel headers between source and preview

**Files:**
- Modify: `index.html` (the `.panel__header` block around lines 531–545 and `.panel__header-actions` around 571–578)

Goal: the source and preview panel headers should share a consistent baseline so titles, helper text, and action pills line up across panels instead of looking offset when one panel has a badge.

- [ ] **Step 1: Read current rules**

Confirm:

```css
.panel__header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 0.85rem;
  padding: 1rem 1.05rem 0.82rem;
  border-bottom: 1px solid var(--panel-border);
  background: rgba(255, 251, 246, 0.46);
  flex-shrink: 0;
}
```

and:

```css
.panel__header-actions {
  display: inline-flex;
  align-items: center;
  justify-content: flex-end;
  gap: 0.45rem;
  flex-wrap: wrap;
  flex-shrink: 0;
}
```

- [ ] **Step 2: Update `.panel__header` to align children on a shared baseline**

Use StrReplace to change `align-items: flex-start;` to `align-items: center;` inside `.panel__header`. The full updated rule should read:

```css
.panel__header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.85rem;
  padding: 0.78rem 1.05rem;
  min-height: 3.4rem;
  border-bottom: 1px solid var(--panel-border);
  background: rgba(255, 251, 246, 0.46);
  flex-shrink: 0;
}
```

Changes vs original: `align-items: flex-start` → `center`, padding becomes symmetric `0.78rem 1.05rem`, and a `min-height: 3.4rem` is added so headers without a badge match the height of headers with a badge.

- [ ] **Step 3: Update `.panel__header-actions` to stop wrapping at desktop**

Replace the existing `.panel__header-actions` rule with:

```css
.panel__header-actions {
  display: inline-flex;
  align-items: center;
  justify-content: flex-end;
  gap: 0.45rem;
  flex-wrap: nowrap;
  flex-shrink: 0;
}
```

Only `flex-wrap: wrap` → `nowrap` changes. This prevents the actions block from wrapping below the title at wide viewports, which was a major source of visual misalignment between panels.

- [ ] **Step 4: Re-confirm presenter-notes special case still wins**

The existing rule:

```css
.presenter-notes-panel .panel__header {
  justify-content: flex-start;
  gap: 0.5rem;
  padding-bottom: 0.76rem;
}
```

stays as-is. Verify it still appears later in the stylesheet so it overrides the base `.panel__header`.

- [ ] **Step 5: Verify in browser**

Reload at 1920x1080. Confirm:

- Source panel header and preview panel header align vertically: titles share the same baseline; action pills (`Safe preview ready` badge on source, zoom controls on preview) sit on the same row as the title.
- Neither header looks taller than the other.
- The presenter notes header (visible only in remark mode after toggling presenter view) still left-aligns its title.

- [ ] **Step 6: Commit**

```bash
git add index.html
git commit -m "style(panel-header): center align headers and lock min-height for parity"
```

---

### Task 5: End-to-end manual verification at 1920x1080

**Files:**
- No edits. Verification only.

- [ ] **Step 1: Reload at 1920x1080 and walk the acceptance criteria from the spec**

Confirm each item from `docs/superpowers/specs/2026-05-08-preview-first-desktop-design.md`:

- [ ] At 1920x1080, source and preview are side-by-side.
- [ ] At 1920x1080, preview has materially more width than source by default.
- [ ] Source collapse (`Hide Source` button) still toggles and the workspace becomes preview-only.
- [ ] Maximize preview (`Maximize` button) still hides chrome and gives full preview.
- [ ] Browser fullscreen button still works.
- [ ] Toolbar still has exactly two rows; no third row.
- [ ] Source panel header and preview panel header are visually aligned.
- [ ] Document parsing, export, and visual edits in the iframe still work (try `Add Text`, edit text in preview, then `Export HTML`).

- [ ] **Step 2: Resize sweep**

Drag the browser between roughly 1100px, 1440px, and 1920px wide. Confirm there is no layout jank and the workspace stays side-by-side above 980px. Confirm stacking still kicks in below 980px.

- [ ] **Step 3: Final commit if any cleanup landed**

If Task 5 surfaced no edits, skip this step. Otherwise:

```bash
git add index.html
git commit -m "style(workspace): polish from desktop verification pass"
```

---

## Out of Scope Reminder

These were explicitly excluded by the spec and must not be touched in this iteration:

- preview rendering performance refactors
- source/preview synchronization
- preview zoom and selection overlay math
- a full toolbar command-bar redesign
- framework or build-system changes

If something in those areas appears broken during verification, log it for a future iteration. Do not fix here.
