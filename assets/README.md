# Assets

Everything here is **concept material for a product that does not exist yet**. Nothing is a screenshot, a rendering of
a real build, or a representation of working software.

| Directory | Contents | Status |
| --- | --- | --- |
| `brand/icon-concepts/` | Three original app-icon directions, hand-authored SVG (512 × 512) | Concepts — none selected |
| `brand/icon-concept-sheet.svg` | The three directions side by side with rationale and 16/32 px legibility tests | Concept board |
| `brand/ipaforge-lockup.svg` | Mark + wordmark lockup for documentation headers | Concept |
| `mockups/` | Three UI concept screens: import/inspect, modification plan, review/sign/export | Concepts — UI not implemented |

Every file here is hand-authored SVG. No raster previews are committed; see *Rendering previews* below.

## Rules for using these assets

- Keep the "concept" framing visible whenever a mockup is shown outside this repository. Do not caption them as
  screenshots, previews, betas, or "current UI".
- All app names, bundle identifiers, versions, sizes, checks, and statuses in the mockups are invented sample data.
  There are no real certificates, team IDs, device identifiers, UDIDs, or paths in any asset.
- Do not present these marks as endorsed by, or derived from, Apple or any upstream project. They are original
  geometry.
- The mockups are not a specification. If implementation later diverges, update the mockups rather than the software
  to match.

## Format notes

- **SVG is the source of truth** for icons and mockups: it is text, diffable in review, and renders at any size.
- The SVGs are self-contained — no external fonts, images, or network references — so they display in GitHub, a
  browser, or any SVG-aware editor without a build step.

## Rendering previews

**No PNG previews are committed.** Two local rasterizers were tried while preparing this repository and both produced
incorrect output:

- macOS Quick Look (`qlmanage -t -s 1600`) rendered the 1600 × 1000 screens onto a 1600 × 1600 square and cropped the
  right-hand third, including the inspector panel.
- The available browser preview viewport was capped at 1280 × 720 and its full-page capture did not complete, which
  clipped the same region.

Committing either would have misrepresented the layout, so only the SVG sources are included. If you generate PNGs,
record the renderer and version here and verify each output is exactly 1600 × 1000 (mockups) or 1400 × 780 (icon
board) before committing:

```bash
# Example, if a working rasterizer is available
rsvg-convert -w 1600 -h 1000 -o assets/mockups/01-import-and-inspect.png assets/mockups/01-import-and-inspect.svg
```
