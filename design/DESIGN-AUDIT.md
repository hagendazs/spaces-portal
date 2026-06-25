# SKS Portal — Current-State UX/UI Design Audit

**Date:** 2026-06-05
**Scope:** Public-facing visual/UX of the SKS (新光保全 / Taiwan Shin Kong Security) portal.
**Sources inspected:**
- `Content/sks/css/style.css` (4,915 lines), `Content/sks/css/popup.css`
- `Content/sks/js/` (`vendors.js`, `script.js`, `location.js`)
- `Views/Shared/_Layout.cshtml` (main site shell, 493 lines)
- `Views/Home/Index.cshtml` (homepage hero/carousel)
- `reservation/` microsite (`index.html`, `css/`, `scss/`, `less/`, `js/`)
- `Areas/Admin/Views/Shared/_Layout.cshtml` + `Content/AdminLTE-3.0.4/` back-office

---

## 1. Executive Summary

The SKS portal is **three visually and technically disjoint products stitched under one domain**:

| Tier | Framework | Era | Responsive? | State |
|------|-----------|-----|-------------|-------|
| **Main marketing site** | Bootstrap 4.5.0 (CSS) + jQuery 3.4.1 | ~2020 | Yes (fluid, 4 breakpoints) | Functional but visually dated; accessibility gaps |
| **Reservation microsite** (`/reservation`) | Bootstrap **3.1.1** + jQuery **1.10/1.11** + Modernizr | **2014–2015** | Partially | **Effectively abandoned**; security + UX liability |
| **Admin back-office** (`/Admin`) | **AdminLTE 3.0.4** + Bootstrap 4 | ~2019 | Yes (sidebar-mini) | Generic template look; off-brand |

There is **no shared design system** — three different Bootstrap versions, three font stacks, three color philosophies, three icon systems (SVG sprite / Font Awesome 4 / Font Awesome 5 + Ionicons). The brand expresses as a yellow/gold (`#ffc129`, theme-color `#fed01f`) on the main site only; neither the microsite nor the admin carries it. The overall visual language reads as **early-2020s corporate Bootstrap** — boxy cards, full-bleed background-image carousels, system-font typography, and a tri-color government-style top border (`#002f82 / #ffc129 / #c90e2d`). It is serviceable but unmistakably dated and inconsistent.

---

## 2. Layout System

**Main site:** `style.css` is a vendored/customized **Bootstrap 4.5** build. Responsive and fluid with a sane breakpoint ladder:
`576px (sm) / 768px (md) / 1024px (lg — non-standard, BS default is 992) / 1366px (xl)`. The 1024/1366 choices are bespoke (tablet/laptop-targeted) rather than Bootstrap defaults, which is fine but means the grid is forked from upstream and harder to maintain.
- Header is `position: fixed` (`.fixed-top`) — content offset is handled manually, a common source of overlap bugs on mobile when the nav wraps.
- Hero is a full-viewport `background-image` carousel using `vh-33`, `h-ratio-100vw`, `vh-55` utilities — heavy reliance on viewport-height units, which are notoriously janky on mobile Safari (address-bar resize).

**Reservation microsite:** Bootstrap **3.1.1** grid (`col-xs/sm/md`), float-based, pre-flexbox. Includes an `<!--[if lt IE 9]>` html5shiv conditional comment and `modernizr.js` — i.e. it still ships **IE8 polyfills**. This is a 2014/2015 layout frozen in time.

**Admin:** AdminLTE `sidebar-mini` fixed-sidebar shell. Responsive via the template, but `user-scalable=no` is set on the viewport (see §6).

> **Verdict:** Main site responsive and modern-enough structurally; microsite is a fixed-era relic; no shared grid across tiers.

---

## 3. Navigation Pattern

- **Main site:** Bootstrap 4 `navbar-expand-lg` with a **multi-column mega-dropdown** for "Services" (4 columns of links) plus several simple dropdowns (About / Investor / Governance / ESG). Collapses to a hamburger (`navbar-toggler`) below `lg`. The mega-menu is dense (30+ links) — reasonable for a conglomerate but cognitively heavy.
- The logo is rendered via an **SVG `<use>` sprite** inside an `<h1 class="text-hide">` with a real `<a>` text node behind it. `text-hide` is a deprecated/removed Bootstrap pattern; combined with an SVG `<use>` that has **no `<title>`/`aria-label`**, the brand link's accessible name is fragile.
- A commented-out `<img class="logo-icon" src="logo.svg">` sits right next to it — dead markup left in the shell.
- **Reservation microsite:** separate, standalone navigation — no link parity or visual continuity with the main nav.
- **Admin:** AdminLTE left sidebar driven by `MvcSiteMap` with role-based menus (`SystemAdmin` / `Event`). Standard template pattern; competent but generic.

> **Verdict:** Three unrelated navigation systems. The main mega-menu is functional but heavy and has accessibility fragility in the logo/brand link.

---

## 4. Typography

- **Main site:** No web font. Font stack leads with a bare `sans-serif` then CJK and Latin system fonts:
  `sans-serif, 微軟正黑體, "Microsoft JhengHei", -apple-system, ... Arial, ...`. Leading with generic `sans-serif` before the named families is **an ordering bug** — on some engines the generic keyword can short-circuit the cascade. Microsoft JhengHei is a dated Windows default; there is no modern CJK web font (e.g. Noto Sans TC) loaded, so Mac/Linux/Android users get inconsistent CJK rendering.
- **Reservation microsite:** loads Google **WebFont loader 1.4.7** (2014) — a different, ad-hoc typography approach.
- **Admin:** loads **Source Sans Pro** from Google Fonts — yet a third typographic identity, and a Latin-only font applied to a Traditional-Chinese admin.

> **Verdict:** Three different type systems, none using a modern CJK web font. Dated default-Windows feel on the main site.

---

## 5. Color & Imagery

**Color:**
- Brand accent: yellow/gold `#ffc129` (and meta `#fed01f`). Used sparingly — buttons, SVG fills, hover accents.
- A **tri-color top border** `linear-gradient(#002f82 33% / #ffc129 / #c90e2d 66%)` (blue/yellow/red) evokes a flag/government banner — a dated, heavy device.
- Body text is pure `#000` on `#fff`; greys are stock Bootstrap (`#6c757d`, `#495057`, `#dee2e6`).
- **Contrast risk:** the brand yellow `#ffc129` against white fails WCAG AA for text (~1.7:1). Anywhere `#ffc129` is used as a text/link color (`color:#ffc129 !important` appears multiple times) it is **non-compliant**. Legibility on yellow buttons depends on the text color, which should be audited per-component.
- Microsite and Admin use their own palettes (Admin = AdminLTE `skin-blue`/`sidebar-dark-primary`), so **brand color is absent outside the main site**.

**Imagery:**
- Homepage hero = full-bleed `background-image` slides set inline (`style="background-image:url(...)"`) — **not `<img>`**, so these key marketing visuals have **no alt text and are invisible to assistive tech and SEO**. A `.mask-dark.lighten` overlay is layered for text legibility (good instinct), but text-over-photo contrast is not guaranteed.
- Video slides pull `http://img.youtube.com/.../maxresdefault.jpg` over **plain HTTP** (mixed-content risk on an HTTPS site).
- `Content/sks/img/` holds **393 files**: 183 JPG, 175 PNG, 35 SVG. **No WebP/AVIF.** Many assets exceed 500 KB–1.1 MB (`OfficeSystem.png` 1.1 MB, `care-1.png` 1.1 MB, `A1-product-1.png` 1.1 MB, `c2-bn3.jpg` 952 KB). No `srcset`/`<picture>` responsive images — mobile users download desktop-weight assets. Photography style is generic stock + product renders, inconsistent in treatment.

> **Verdict:** Off-brand outside the main site; brand yellow has real contrast problems; hero imagery is alt-less inline backgrounds; heavy, unoptimized, non-responsive raster assets.

---

## 6. Mobile-Friendliness

- Main site is responsive (hamburger nav, fluid grid) — the baseline is acceptable.
- **`vh`-based hero heights** (`h-ratio-100vw`, `vh-33`, `vh-55`) cause the classic mobile-Safari viewport-jump on scroll.
- Mega-menu → accordion conversion on mobile relies on `.js-plus` toggles and custom JS (`jslist` show/hide); dense and tap-target-heavy.
- **Admin sets `user-scalable=no`** in the viewport meta — this **disables pinch-zoom**, an accessibility/WCAG failure (blocks low-vision users from magnifying).
- Microsite (Bootstrap 3) has partial mobile support but ships IE8 polyfills — its "responsive" baseline predates modern mobile expectations.

> **Verdict:** Main site mobile-OK with `vh` jank; Admin actively blocks zoom; microsite mobile experience is a decade behind.

---

## 7. Accessibility Red Flags

| Severity | Issue | Location |
|----------|-------|----------|
| **High** | Hero/banner images are inline CSS `background-image` with **no alt text** — primary content invisible to AT & SEO | `Views/Home/Index.cshtml` carousel |
| **High** | Brand yellow `#ffc129` used as **text/link color on white** fails WCAG AA contrast (~1.7:1) | `style.css` (`color:#ffc129 !important`) |
| **High** | Admin disables zoom via `user-scalable=no` | `Areas/Admin/.../_Layout.cshtml:24` |
| **Medium** | Logo via SVG `<use>` sprite with **no `<title>`/`aria-label`**, wrapped in deprecated `text-hide` — fragile accessible name for the primary brand link | `_Layout.cshtml:126-128` |
| **Medium** | Dropdown toggles use `href=".#"` placeholder anchors — keyboard/JS-dependent, non-semantic | `_Layout.cshtml` nav `<a>`s |
| **Medium** | Mixed-content: YouTube thumbnails over `http://` on an HTTPS page | `Index.cshtml:136` |
| **Medium** | Microsite ships IE8 conditional comments + Modernizr; no modern a11y baseline | `reservation/index.html:19` |
| **Low** | Carousel uses Bootstrap indicators but autoplay (`data-ride`, `data-interval=10000`) with no obvious pause/stop affordance for reduced-motion/AT users | `Index.cshtml:30` |
| **Low** | Corrupted markup: a class attribute on line 248 of `_Layout.cshtml` has a stray pasted URL embedded inside `class="..."` — broken/garbage attribute shipping to prod | `_Layout.cshtml:248` |

The CSS does honor `prefers-reduced-motion: reduce` (inherited from Bootstrap), which is a positive.

---

## 8. Date-ness of the Visual Language

- **Main site:** reads as **2019–2021 corporate Bootstrap 4**. Tells: full-bleed background-photo carousel hero, boxy bordered cards, system-font CJK type, flag-stripe top border, `text-hide` SVG logo, jQuery 3.4.1 + GA `UA-` (Universal Analytics, sunset 2023) and `X-UA-Compatible IE=edge` still in `<head>`. Not broken, but clearly a generation behind current (no fluid type scale, no design tokens, no CSS custom properties, no modern CSS grid/container queries).
- **Reservation microsite:** **2014/2015**. Bootstrap 3.1.1, jQuery 1.10/1.11, Modernizr, IE8 shiv, two jQuery versions double-loaded (`jquery.js` + `jquery-1.11.1.min.js`), Font Awesome 4. This is the most dated and most fragile surface — effectively unmaintained and a security/UX liability.
- **Admin:** AdminLTE 3.0.4 — a recognizable, generic "free admin template" look. Dated by association and entirely off-brand.

---

## 9. Cross-Tier Consistency

**There is essentially none.** Concrete divergences:

| Dimension | Main site | Reservation | Admin |
|-----------|-----------|-------------|-------|
| CSS framework | Bootstrap 4.5 | Bootstrap 3.1.1 | AdminLTE 3 / BS4 |
| jQuery | 3.4.1 | 1.10 + 1.11 (both) | (AdminLTE bundle) |
| Icons | SVG sprite | Font Awesome 4 | FA5 + Ionicons 2 |
| Type | System CJK stack | Google WebFont loader | Source Sans Pro |
| Brand color | `#ffc129` yellow | none | AdminLTE blue |
| Responsiveness | Fluid, 4 BPs | Partial, float grid | Sidebar-mini |

A user moving from the marketing site → reservation booking → (staff) admin experiences three different products with no shared shell, header, color, or interaction language.

---

## 10. Prioritized Issues

### P0 — Fix now (correctness / accessibility / security)
1. **Alt-less hero imagery** — move primary marketing visuals to real `<img alt>` / `<picture>`, or add equivalent text. (SEO + a11y)
2. **Brand-yellow contrast** — stop using `#ffc129` as a text/link color on white; reserve it for large surfaces/icons with a compliant pairing.
3. **Admin `user-scalable=no`** — remove; never block zoom.
4. **Mixed content** — switch YouTube thumbnail URLs to `https://`.
5. **Corrupted `class` attribute** in `_Layout.cshtml:248` — remove the pasted URL fragment.

### P1 — High value
6. **Retire or rebuild the `/reservation` microsite** — Bootstrap 3 / jQuery 1.x / IE8 shiv / double-loaded jQuery is unmaintained and a liability. Fold booking into the main design system.
7. **Image performance** — generate WebP/AVIF + `srcset`/`<picture>`; compress the 0.5–1.1 MB assets. Mobile is currently downloading desktop-weight art.
8. **Establish a shared design system / tokens** — single color palette (with brand yellow + accessible pairings), one CJK web font (e.g. Noto Sans TC), one icon system, CSS custom properties shared across all three tiers.
9. **Fix the font stack ordering bug** (leading generic `sans-serif`) and adopt a modern CJK web font.

### P2 — Modernization
10. **Replace `vh`-based hero heights** with `dvh`/`svh` or aspect-ratio to kill mobile-Safari jank.
11. **Rebrand the AdminLTE back-office** to carry SKS identity, or migrate to a maintained admin framework.
12. **Logo accessibility & cleanup** — give the SVG sprite logo a proper `<title>`/`aria-label`, drop deprecated `text-hide`, remove commented-out dead `<img>`/menu markup.
13. **Modernize analytics/meta** — migrate off Universal Analytics (`UA-…`), drop `X-UA-Compatible IE=edge`.
14. **Carousel UX** — add visible pause control; respect reduced-motion for autoplay.

---

## 11. What's Actually Fine (don't churn)

- Main-site responsive grid and breakpoint ladder are reasonable.
- `prefers-reduced-motion` is respected in CSS.
- OG/Twitter/canonical meta, sitemap, and i18n (`isEn`) plumbing are in place.
- Nav has a real (if heavy) IA with role-based admin menus.
- The instinct of a dark overlay (`.mask-dark.lighten`) over hero photos for legibility is correct — it just needs measured contrast.
