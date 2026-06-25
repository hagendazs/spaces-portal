# SKS Portal — Design Tokens

> Extracted from the existing SKS portal to preserve brand continuity in the redesign.
> Sources: `Content/sks/css/style.css`, `Content/sks/css/popup.css`, `Content/sks/img/logo.svg`,
> `Content/sks/img/sprite.svg`, `Views/Shared/_Layout.cshtml`, `ico.png`, `favicon.ico`,
> and the legacy `reservation/` site (`css/style.css`, `scss/`, `less/`, `images/logo*`).
>
> Tokens marked **PROPOSED** do not exist in the current site; they are sensible modern defaults
> for the redesign, derived from the extracted brand colors and spacing rhythm.

---

## 1. Brand Identity

The SKS (新光保全 / Shin Kong Security) brand is anchored by a **tri-color signature bar** and a
**navy + gold + red** palette. This is the single most load-bearing brand element and must survive
the redesign verbatim.

**Brand stripe** (from `style.css:4205`):

```css
border-image-source: linear-gradient(
  to right,
  #002f82 33.33333%,   /* navy  */
  #ffc129 33.33333%,   /* gold  */
  #ffc129 66.66667%,
  #c90e2d 66.66667%    /* red   */
);
```

Three equal thirds: **navy → gold → red**. Reuse as a top accent bar, section dividers, or focus underline.

### Logo

| Asset | Path | Notes |
|---|---|---|
| Primary logo (vector) | `Content/sks/img/logo.svg` | Wordmark + icon. Icon uses blue gradients, red `#e50012`, gold `#f6aa00`→`#fff000`. |
| Logo sprite | `Content/sks/img/sprite.svg` | Holds `logo-text`, `logo-icon`, and social/UI icons via `<use xlink:href>`. |
| Footer logo | `Content/sks/img/logo.svg` | Rendered at `width="60"` (`_Layout.cshtml:458`). |
| Favicon (PNG) | `ico.png` | 16×16 RGBA, linked as `shortcut icon`. |
| Favicon (ICO) | `favicon.ico` | Multi-res 16×16 + 32×32, 32-bit. |
| Legacy reservation logo | `reservation/images/logo.png`, `logo1.png`, `logo2.png`, `footer-logo.png` | Old marketing microsite (2015). Not the portal brand; do not carry forward. |

**Logo color behavior** (header scroll states, `style.css:3052, 4598`):

| State | `logo-text` fill | Context |
|---|---|---|
| Default (over hero / dark) | `#fff` | white wordmark |
| Scrolled (`header.scroll-down`) | `#003d9c` | navy wordmark on white header |

**Logo SVG internal gradient stops** (icon, `logo.svg`): blue `#005cac` → `#0094d8` → `#21b2e8`,
gold `#f6aa00` → `#fff000`, red `#e50012`, grays `#a7a8a6`…`#f0f0f0`.

**Logo usage rules (PROPOSED):**
- Minimum clear space: ≥ 0.5× logo height on all sides. **PROPOSED**
- Minimum render width: 60px (matches current footer usage).
- Never recolor the wordmark outside the two sanctioned fills (`#fff`, `#003d9c`).
- Provide an SVG-first logo; PNG only as fallback.

---

## 2. Color Palette

### 2.1 Core brand colors (extracted)

| Token | Hex | Usage in current site |
|---|---|---|
| `brand.navy` | `#002f82` | Primary brand color: buttons, headings, brand stripe, active states (`:3115, 3284, 4171`) |
| `brand.navy-dark` | `#012a55` | Hover/pressed navy; deep section backgrounds (`:3304, 4116, 4684`) |
| `brand.navy-darker` | `#001d4f` | Active/pressed border + bg (`:4179, 4194`) |
| `brand.navy-logo` | `#003d9c` | Scrolled logo wordmark fill (`:4598`) |
| `brand.blue` | `#0066b1` | Secondary blue: links, accents (`:3107, 3296, 4076`) |
| `brand.gold` | `#ffc129` | Brand stripe gold, badges, highlights (`:3066, 3671, 4424`) |
| `brand.gold-alt` | `#fed01f` | Theme-color meta, accent bg, left-borders (`:1682, 3345, 4241`); `theme-color` in `_Layout` |
| `brand.red` | `#c90e2d` | Brand stripe red, alerts/CTAs (`:3675, 4879`) |
| `brand.red-logo` | `#e50012` | Logo icon red (`logo.svg`) |

### 2.2 Neutrals (extracted — Bootstrap-derived)

| Token | Hex | Usage |
|---|---|---|
| `neutral.white` | `#fff` | Page background, on-dark text |
| `neutral.black` | `#000` | Body text (`body { color:#000 }`) |
| `neutral.900` | `#212529` | Headings / dark text (BS `$gray-900`) |
| `neutral.700` | `#495057` | Body emphasis (BS `$gray-700`) |
| `neutral.600` | `#6c757d` | Muted text / secondary (BS `$gray-600`) — heavily used |
| `neutral.500` | `#a8a8a8` | Disabled, location icon, borders |
| `neutral.400` | `#ced4da` | **PROPOSED** input borders (BS `$gray-400`) |
| `neutral.300` | `#dee2e6` | Dividers, card borders (`:`) |
| `neutral.200` | `#e9ecef` | Subtle fills (BS `$gray-200`) |
| `neutral.100` | `#f8f9fa` | Light section bg (BS `$gray-100`) |
| `neutral.eee` | `#eee` | Hairlines, gradient stops |

### 2.3 Semantic / state colors

| Token | Hex | Source |
|---|---|---|
| `state.facebook` | `#3b5998` | Social FB icon (`:` / `4564`) |
| `state.youtube` | `red` (`#ff0000`) | Social YT icon |
| `state.success` | `#28a745` | **PROPOSED** (BS green; current site has no explicit success token) |
| `state.warning` | `#ffc129` | Reuse `brand.gold` |
| `state.danger` | `#c90e2d` | Reuse `brand.red` |
| `state.info` | `#0066b1` | Reuse `brand.blue` |
| `state.focus-ring` | `rgba(38,78,149,.5)` | Focus box-shadow on brand controls (`:`) |

### 2.4 Legacy reservation microsite palette (reference only — do NOT carry into portal)

The 2015 `reservation/` site used a teal/coral flat-UI scheme unrelated to the portal brand:
`#66cdcc` (teal), `#ed5565` (coral), `#336799` (blue), `#13ad7d`/`#00c788` (greens), `#ffce54` (amber).
Documented for completeness; the redesign should follow the **navy/gold/red** portal brand, not this.

---

## 3. Typography

### 3.1 Font families (extracted)

The portal uses a **single system+CJK stack** (no web-font download). CJK is handled by prioritizing
Microsoft JhengHei (微軟正黑體) ahead of the Latin system stack.

```css
/* body — Content/sks/css/style.css:18 */
font-family:
  sans-serif, 微軟正黑體, "Microsoft JhengHei",
  -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
  "Helvetica Neue", Arial, "Noto Sans", sans-serif,
  "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol", "Noto Color Emoji";
```

| Token | Value | Notes |
|---|---|---|
| `font.family.base` | system stack above | Latin + Traditional-Chinese. **CJK handling:** JhengHei / 微軟正黑體 prioritized for zh-TW glyphs; falls back to OS system fonts. |
| `font.family.cjk` (**PROPOSED**) | `"Microsoft JhengHei", "PingFang TC", "Noto Sans TC", sans-serif` | Modernized CJK stack — adds macOS PingFang TC + web-safe Noto Sans TC so non-Windows clients render zh-TW cleanly instead of falling to generic sans. |
| `font.family.icon` | FontAwesome / Glyphicons (reservation), sprite SVG (portal) | Portal moved to inline SVG sprite; FontAwesome only in legacy reservation site. |

> Note: The raw stack lists `sans-serif` twice and places it first — a minor authoring artifact.
> The redesign should clean this to: `"Microsoft JhengHei", "PingFang TC", "Noto Sans TC", -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif`.

### 3.2 Type scale (extracted, root = 16px)

| Token | rem | px | Usage |
|---|---|---|---|
| `font.size.base` | `1rem` | 16 | Body (`:19`) |
| `font.size.sm` | `.875rem` | 14 | Small text (`:4640`) |
| `font.size.xs` | `.75rem` | 12 | Captions, labels (`:338`) |
| `font.size.h6` | `1rem` | 16 | (`:207`) |
| `font.size.h5` | `1.125rem` | 18 | (`:203`) |
| `font.size.h4` | `1.25rem` | 20 | (`:195`) |
| `font.size.h3` | `1.5rem` | 24 | (`:`) most common heading |
| `font.size.h2` | `2rem` | 32 | (`:191`) |
| `font.size.h1` | `2.5rem` | 40 | (`:187`) |
| `font.size.display-md` | `3rem`–`3.125rem` | 48–50 | Hero (responsive, `:212, 242`) |
| `font.size.display-lg` | `4.375rem` | 70 | Largest hero (`:238`) |

### 3.3 Weights & line-height (extracted)

| Token | Value | Notes |
|---|---|---|
| `font.weight.regular` | `400` | Body default (`:20`) |
| `font.weight.bold` | `700` | Headings, emphasis (only two weights used) |
| `font.weight.medium` (**PROPOSED**) | `500` | For UI labels/buttons in redesign |
| `line.height.base` | `1.5` | Body (`:21`) |
| `line.height.heading` (**PROPOSED**) | `1.25` | Tighter headings |
| `line.height.cjk` (**PROPOSED**) | `1.7` | Looser leading improves zh-TW readability |

---

## 4. Spacing

The site is Bootstrap-4-based; spacing follows the `.25rem` (4px) step scale. No custom spacing
tokens were defined, so the scale below is the Bootstrap rhythm the markup already relies on.

| Token | rem | px | Source |
|---|---|---|---|
| `space.0` | `0` | 0 | BS `$spacer*0` |
| `space.1` | `.25rem` | 4 | BS step |
| `space.2` | `.5rem` | 8 | BS step |
| `space.3` | `1rem` | 16 | BS base spacer |
| `space.4` | `1.5rem` | 24 | BS step |
| `space.5` | `3rem` | 48 | BS step |
| `space.6` (**PROPOSED**) | `4.5rem` | 72 | Section padding for redesign |
| `space.7` (**PROPOSED**) | `6rem` | 96 | Large section padding |

**Container / breakpoints (Bootstrap 4, used by markup):**

| Token | Value |
|---|---|
| `breakpoint.sm` | `576px` |
| `breakpoint.md` | `768px` |
| `breakpoint.lg` | `992px` |
| `breakpoint.xl` | `1200px` |

---

## 5. Border Radius (extracted)

| Token | Value | Usage |
|---|---|---|
| `radius.none` | `0` | Squared elements |
| `radius.sm` | `2px` | Small chips (`:`) |
| `radius.base` | `.25rem` (4px) | Buttons, cards (BS default, `:`) |
| `radius.md` | `.3rem` (≈5px) | Large buttons (`:`) |
| `radius.lg` | `10px` | Panels / popovers (`:`) |
| `radius.xl` | `25px` | Pills (`:`) |
| `radius.pill` | `50rem` | Fully-rounded badges (`:`) |
| `radius.circle` | `50%` | Avatars, icon buttons |

---

## 6. Shadows (extracted)

| Token | Value | Usage |
|---|---|---|
| `shadow.xs` | `0 .125rem .25rem rgba(0,0,0,.075)` | Subtle card (BS `$box-shadow-sm`) |
| `shadow.md` | `0 .5rem 1rem rgba(0,0,0,.15)` | Raised card / dropdown (BS `$box-shadow`) |
| `shadow.drop` | `2px 2px 5px rgba(0,0,0,.3)` | Legacy element drop shadow |
| `shadow.focus.neutral` | `0 0 0 .2rem rgba(168,168,168,.25)` | Default focus ring |
| `shadow.focus.brand` | `0 0 0 .2rem rgba(38,78,149,.5)` | Navy control focus ring |
| `shadow.focus.blue` | `0 0 0 .2rem rgba(0,102,177,.5)` | Blue control focus ring |
| `shadow.lg` (**PROPOSED**) | `0 1rem 3rem rgba(0,0,0,.175)` | Modals / hero cards in redesign |

---

## 7. Motion (extracted / proposed)

| Token | Value | Source |
|---|---|---|
| `motion.duration.fast` | `.3s` | Logo/icon/fill transitions (`:`) |
| `motion.duration.base` (**PROPOSED**) | `.2s` | Standard UI transitions |
| `motion.easing.base` (**PROPOSED**) | `cubic-bezier(.4,0,.2,1)` | Material-style ease |

---

## 8. Token JSON (Figma Variables / Claude Design ingest)

```json
{
  "$schema": "https://design-tokens.org/format",
  "color": {
    "brand": {
      "navy":        { "$type": "color", "$value": "#002f82" },
      "navyDark":    { "$type": "color", "$value": "#012a55" },
      "navyDarker":  { "$type": "color", "$value": "#001d4f" },
      "navyLogo":    { "$type": "color", "$value": "#003d9c" },
      "blue":        { "$type": "color", "$value": "#0066b1" },
      "gold":        { "$type": "color", "$value": "#ffc129" },
      "goldAlt":     { "$type": "color", "$value": "#fed01f" },
      "red":         { "$type": "color", "$value": "#c90e2d" },
      "redLogo":     { "$type": "color", "$value": "#e50012" }
    },
    "neutral": {
      "white": { "$type": "color", "$value": "#ffffff" },
      "black": { "$type": "color", "$value": "#000000" },
      "900":   { "$type": "color", "$value": "#212529" },
      "700":   { "$type": "color", "$value": "#495057" },
      "600":   { "$type": "color", "$value": "#6c757d" },
      "500":   { "$type": "color", "$value": "#a8a8a8" },
      "400":   { "$type": "color", "$value": "#ced4da", "$extensions": { "proposed": true } },
      "300":   { "$type": "color", "$value": "#dee2e6" },
      "200":   { "$type": "color", "$value": "#e9ecef" },
      "100":   { "$type": "color", "$value": "#f8f9fa" }
    },
    "state": {
      "facebook":  { "$type": "color", "$value": "#3b5998" },
      "youtube":   { "$type": "color", "$value": "#ff0000" },
      "success":   { "$type": "color", "$value": "#28a745", "$extensions": { "proposed": true } },
      "warning":   { "$type": "color", "$value": "#ffc129" },
      "danger":    { "$type": "color", "$value": "#c90e2d" },
      "info":      { "$type": "color", "$value": "#0066b1" }
    }
  },
  "gradient": {
    "brandStripe": {
      "$type": "gradient",
      "$value": "linear-gradient(to right,#002f82 33.333%,#ffc129 33.333%,#ffc129 66.667%,#c90e2d 66.667%)"
    },
    "logoIcon": {
      "$type": "gradient",
      "$value": "linear-gradient(135deg,#005cac,#0094d8,#21b2e8)"
    }
  },
  "font": {
    "family": {
      "base": { "$type": "fontFamily", "$value": "\"Microsoft JhengHei\", -apple-system, BlinkMacSystemFont, \"Segoe UI\", Roboto, \"Helvetica Neue\", Arial, \"Noto Sans\", sans-serif" },
      "cjk":  { "$type": "fontFamily", "$value": "\"Microsoft JhengHei\", \"PingFang TC\", \"Noto Sans TC\", sans-serif", "$extensions": { "proposed": true } }
    },
    "size": {
      "xs":        { "$type": "dimension", "$value": "0.75rem" },
      "sm":        { "$type": "dimension", "$value": "0.875rem" },
      "base":      { "$type": "dimension", "$value": "1rem" },
      "h5":        { "$type": "dimension", "$value": "1.125rem" },
      "h4":        { "$type": "dimension", "$value": "1.25rem" },
      "h3":        { "$type": "dimension", "$value": "1.5rem" },
      "h2":        { "$type": "dimension", "$value": "2rem" },
      "h1":        { "$type": "dimension", "$value": "2.5rem" },
      "displayMd": { "$type": "dimension", "$value": "3rem" },
      "displayLg": { "$type": "dimension", "$value": "4.375rem" }
    },
    "weight": {
      "regular": { "$type": "fontWeight", "$value": 400 },
      "medium":  { "$type": "fontWeight", "$value": 500, "$extensions": { "proposed": true } },
      "bold":    { "$type": "fontWeight", "$value": 700 }
    },
    "lineHeight": {
      "base":    { "$type": "number", "$value": 1.5 },
      "heading": { "$type": "number", "$value": 1.25, "$extensions": { "proposed": true } },
      "cjk":     { "$type": "number", "$value": 1.7,  "$extensions": { "proposed": true } }
    }
  },
  "space": {
    "0": { "$type": "dimension", "$value": "0" },
    "1": { "$type": "dimension", "$value": "0.25rem" },
    "2": { "$type": "dimension", "$value": "0.5rem" },
    "3": { "$type": "dimension", "$value": "1rem" },
    "4": { "$type": "dimension", "$value": "1.5rem" },
    "5": { "$type": "dimension", "$value": "3rem" },
    "6": { "$type": "dimension", "$value": "4.5rem", "$extensions": { "proposed": true } },
    "7": { "$type": "dimension", "$value": "6rem",   "$extensions": { "proposed": true } }
  },
  "radius": {
    "none":   { "$type": "dimension", "$value": "0" },
    "sm":     { "$type": "dimension", "$value": "2px" },
    "base":   { "$type": "dimension", "$value": "0.25rem" },
    "md":     { "$type": "dimension", "$value": "0.3rem" },
    "lg":     { "$type": "dimension", "$value": "10px" },
    "xl":     { "$type": "dimension", "$value": "25px" },
    "pill":   { "$type": "dimension", "$value": "50rem" },
    "circle": { "$type": "dimension", "$value": "50%" }
  },
  "shadow": {
    "xs":          { "$type": "shadow", "$value": "0 0.125rem 0.25rem rgba(0,0,0,0.075)" },
    "md":          { "$type": "shadow", "$value": "0 0.5rem 1rem rgba(0,0,0,0.15)" },
    "drop":        { "$type": "shadow", "$value": "2px 2px 5px rgba(0,0,0,0.3)" },
    "focusBrand":  { "$type": "shadow", "$value": "0 0 0 0.2rem rgba(38,78,149,0.5)" },
    "lg":          { "$type": "shadow", "$value": "0 1rem 3rem rgba(0,0,0,0.175)", "$extensions": { "proposed": true } }
  },
  "motion": {
    "durationFast": { "$type": "duration", "$value": "300ms" },
    "durationBase": { "$type": "duration", "$value": "200ms", "$extensions": { "proposed": true } },
    "easingBase":   { "$type": "cubicBezier", "$value": [0.4, 0, 0.2, 1], "$extensions": { "proposed": true } }
  },
  "breakpoint": {
    "sm": { "$type": "dimension", "$value": "576px" },
    "md": { "$type": "dimension", "$value": "768px" },
    "lg": { "$type": "dimension", "$value": "992px" },
    "xl": { "$type": "dimension", "$value": "1200px" }
  }
}
```

---

## 9. Continuity Checklist for the Redesign

1. **Keep** the navy/gold/red tri-color stripe — it is the strongest brand signal.
2. **Keep** `brand.navy #002f82` as primary and `brand.gold-alt #fed01f` as the `theme-color`.
3. **Keep** the SVG-sprite logo and its two sanctioned wordmark fills (`#fff`, `#003d9c`).
4. **Improve** the CJK font stack (add PingFang TC + Noto Sans TC) so non-Windows users render zh-TW well.
5. **Drop** the legacy reservation teal/coral palette and FontAwesome/Glyphicons dependencies.
6. **Adopt** the PROPOSED tokens (medium weight, larger section spacing, modern focus/motion) to modernize.
