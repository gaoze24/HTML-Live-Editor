# Preview-First Desktop Design

## Goal

Improve the editor's wide-screen desktop experience for at least a 1920x1080, 16:9 display. The preview should feel like the primary workspace, while the source editor remains available and aligned with the rest of the chrome.

## Scope

This iteration focuses on responsive UI and alignment only:

- make the preview panel visually dominant on wide desktop screens
- reduce the default source pane width
- align source and preview panel headers
- tighten toolbar grouping and spacing on desktop
- preserve existing controls, behaviors, and keyboard interactions

Out of scope for this pass:

- preview rendering performance refactors
- source/preview synchronization changes
- preview zoom and selection overlay math changes
- a full toolbar command-bar redesign
- framework or build-system changes

## Design

Use the existing two-panel layout, but tune its desktop proportions toward preview-first editing. The source pane should start narrower than it does today, with the preview receiving the extra horizontal space. The current resize handle remains the way users override the default.

On 1920x1080 displays, the app should avoid feeling like it has fallen into a cramped responsive mode. The workspace should remain side-by-side, with enough preview width to judge slide/page composition comfortably.

The top toolbar stays two rows, but desktop spacing should be tightened so groups read as deliberate clusters. This keeps the existing stable-height contract while reducing visual drift and excess pill spacing.

Panel headers should share a consistent vertical rhythm. Titles, helper text, badges, and action controls should align cleanly instead of appearing offset when one panel has a badge or a larger action group.

## Acceptance Criteria

- At 1920x1080, source and preview are side-by-side.
- At 1920x1080, preview has materially more width than source by default.
- Source collapse, preview maximize, and fullscreen controls continue to work.
- Toolbar rows keep their fixed two-row behavior and do not introduce a third row.
- Panel headers look aligned between source and preview panels.
- No changes are made to document parsing, export, iframe rendering, or visual edit synchronization.

## Testing

Manual verification is expected because this repo has no test harness or package scripts. The test pass should include:

- open `index.html` through a local static server
- inspect the default 1920x1080 layout
- resize around desktop widths above 980px
- toggle source collapse
- toggle preview maximize
- verify toolbar controls remain reachable
- verify source and preview panels still scroll/render normally

Any future lightweight browser smoke script should focus on layout state assertions rather than visual pixel perfection.
