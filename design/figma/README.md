# SKS Portal — Figma hand-off

## Live Figma file
- **URL:** https://www.figma.com/design/lARXRHytFW22QO2hKv6xQg
- **File key:** `lARXRHytFW22QO2hKv6xQg`
- **Team:** Reiser's team (`team::1287276707405506692`, Pro plan / Full seat)
- Built 2026-06-05 via the Figma MCP (`use_figma`) from `REDESIGN-BRIEF.md` §6.a.

## Build status — ✅ COMPLETE

- ✅ `SKS Tokens` variable collection — 14 color + 13 spacing/radius variables (bound into all components & frames)
- ✅ Components (auto-layout, variable-bound, Noto Sans TC): **Button**, **CategoryTile**, **NewsCard**, **QuickLinkTile**
- ✅ **Home — Desktop 1440** (node `7:2`): SiteHeader (tri-color stripe, logo, 6-item nav, 中/En + utilities) · Hero · 3 pillars · navy service strip (北區/南區 hotlines + Bill Payment / Member Area) · News Center (3 cards) · App download · QuickLinks (4) · full SiteFooter
- ✅ **Home — Mobile 390** (node `14:31`): all sections stacked, CTAs full-width, quick links 2×2, condensed footer

## Flat-asset exports (`exports/`)
Rendered from Figma (2026-06-05):
- `home-desktop-1440.png` (1440×2897)
- `home-mobile-390.png` (390×3789)
- `component-button.png`, `component-categorytile.png`, `component-newscard.png`, `component-quicklinktile.png`

To re-export from Figma: select a frame → right rail → Export → PNG/SVG/PDF (2× for retina).

## Known build constraints (by design, documented)
- **Images:** the Figma MCP can't import images (beta limit), so all photos are styled placeholder fills (dark navy gradient for hero/pillars, gray block for news thumbnails) and icons are solid navy placeholder shapes. Swap in real `srcset`/WebP art + the SVG icon sprite in Figma.
- **Font:** built in `Noto Sans TC` (Regular/Medium/Bold), confirmed available in Figma. Text line-height is set in **pixels** (size × 1.25 UI / × 1.5 body) — the CJK font's default line box is oversized otherwise.

## Token re-import
`sks.tokens.json` (Tokens Studio for Figma format) is included for rebuilding the variables on any fresh file (plugin → Tools → Import). The live file already has them.

## Source of truth
Full editable Pencil source: `/Users/reiser/repo/sks/sks-redesign.pen`.
