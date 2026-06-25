# Section Brief — Business, Products & Solutions

> Source app: SKS (新光保全 / Shin Kong Security) corporate portal, ASP.NET MVC (Razor `.cshtml`).
> Scope of this brief: `Views/Business/` (12 pages), `Views/SmartHome/`, `Views/Green/`, `Views/skpos/`, and `catalog/` PDFs.
> Audience: downstream design AI (Figma / Claude Design) rebuilding this section.

---

## 1. Purpose of the Section

This is the **commercial product & solutions catalog** of the SKS portal — the part of the site that sells SKS's services to businesses and households. It is organized into four sub-sections, each a controller with its own landing page plus detail pages:

- **Business (商務)** — security & smart-store solutions for enterprises/retail (the flagship sub-section).
- **SmartHome (智慧家庭)** — residential / community smart-living and elder-care products.
- **Green (綠建築 / 防災)** — smart-building (green building) and fire/disaster-prevention solutions.
- **skpos** — a thin wrapper page for the SKPOS point-of-sale brand (embeds an external micro-site).

The dominant pattern across the section is **marketing landing pages**: full-bleed banner → product/feature blocks → advantage grids → use-case galleries → app-download / contact CTA. It is brochure-ware, not a transactional store (the one exception is `Business/Shop`, a real product catalog).

---

## 2. Bilingual Coverage

- The site is **fully bilingual (Traditional Chinese default + English)**. Language is driven by a `lang` route value (`zh-tw` / `en`) and a `Model.Language == Language.en` flag.
- Copy is supplied from **compiled .resx resource classes** (`sks.Resources.Business.*`, `sks.Resources.SmartHome.*`, `sks.Resources.Green.*`, `sks.Resources.Layout.Resource`). The resx source files are **not in the repo** (compiled into the DLL) — strings are referenced as `@Security.AdvantagesItem1` etc.
- Almost every page contains `@if (lang == "en")` branches, mostly to **swap heading sizes** (`<h2>` for Chinese vs `<h3>` for the longer English text) — i.e. the layout is tuned for both languages.
- **Two pages are NOT resource-driven and have hard-coded Traditional Chinese only** (no English path): `Business/AIHotel` (智能旅館 Smart Hotel) and `Business/InfoSecurity` (資安閘門 InfoSec Gateway). `Business/Shop` titles are also hard-coded Chinese.

---

## 3. Content Types & Data Shapes (inventory)

| Content type | Where it appears | Shape / notes |
|---|---|---|
| **Banner / hero carousel** | Every page | Bootstrap `.carousel` with full-bleed `bg-cover` image, overlaid `<h1>/<h2>` headline + lead paragraph. Most are single-slide; some (POS, Office, Monitor, Community) have multiple slides + indicators. |
| **Product / solution link cards** | All Index pages | Alternating image + text rows (`flex-row-reverse`), each = heading + 1–2 line description + "Learn More" (了解更多) button linking to a detail page. |
| **Advantage / feature grids** | Security, POS, Office, AIHotel, InfoSecurity, Care, Community | 3–8 column icon grids; each cell = SVG icon (from `sprite.svg`) or PNG + short title + 1–2 description lines. POS has an 8-item advantage grid (省錢/省時/省人力…). |
| **Spec / feature list cards** | POS, Office, Wireless, Monitor, Access | Product image + bulleted `list-style-circle` feature list (e.g. SKPOS / mPOS / iPad-POS spec bullets). |
| **Use-case / location switcher** | Security | Interactive: 4 venue tiles (Office 辦公室 / SmallStore 小型商店 / Factory 工廠 / LargeSupermarket 大賣場) toggle `data-place` panels via JS to show tailored service sets. |
| **Use-case gallery (image tiles)** | POS, others | Masked image tiles labeled by store type (文創店/百貨/餐廳/早午餐/小吃/下午茶/速食/飲料店). |
| **Stat / counter block** | Business/Index | "Service Advantages" counters: 600 security personnel, 350 engineers, 250 sales, 250 vehicles. |
| **App-store download buttons** | Security, SmartHome pages | App Store + Google Play badge images linking to SKS apps (守護城市2 / 智慧生活2s). |
| **PDF download link** | Green/Architecture, Green/Prevention | Single CTA linking to a catalog PDF (物業管理平台型錄 / SKSFireSystem). |
| **External iframe / micro-site embed** | skpos/Index, Business/Shop(banner) | `skpos/Index` is **only** a 3350px `<iframe>` to `rowena0123.wixsite.com/dudoo` (external Wix site). |
| **DB-driven product catalog + paging** | Business/Shop | The only genuinely dynamic page — see §6. |
| **Fixed contact CTA pill** | Most pages | Floating "Dedicated Service / 專人服務" pill linking to `Contact/Index`. |

There are **no contact forms, financial tables, or news list/detail** inside this section (those live in other sections — Contact, Shareholder, News).

---

## 4. Page Inventory

| Page (route) | CN term → EN gloss | Driven by | Key content blocks | Bilingual | Notable media/dynamic |
|---|---|---|---|---|---|
| `Business/Index` | 商務 → Business landing | resx | Hero + 8 solution link-cards (Security, Office cloud-guard, WorkLink, Access, Monitor, AISecurity, SKPOS, GPS) + stat counters | Yes | `sksandcar.png`, 8 banner thumbs |
| `Business/Security` | 保全服務 → Security Services | resx | 3-slide advantage carousel, 4-venue use-case switcher (office/store/factory/mart), AI video security, anti-theft, monitoring, access control, GPS, AED/POS extras, app downloads | Yes | ~25 imgs, sprite icons, 2 app-store CTAs |
| `Business/Office` | 商務雲端保全 → Commercial Cloud Guard (largest page, 50KB) | resx + BaseView | Multi-slide carousels (18), extensive feature/spec blocks; 19 EN branches | Yes | Many images |
| `Business/WorkLink` | WorkLink (attendance/work platform) | resx | Hero + feature blocks | Yes | — |
| `Business/Access` | 智慧門禁 → Smart Door Keeper / Access Control | resx | Multi-carousel, feature grids | Yes | sprite icons |
| `Business/Monitor` | 影像監控 → Video Surveillance | resx | 11 carousels, feature/spec grids | Yes | Many images |
| `Business/AISecurity` | AI智慧影像保全 → AI Smart Video Security | resx | Hero + AI feature grid | Yes | — |
| `Business/POS` | SKPOS → POS system | resx | 2-slide hero, 2 product entry cards (→ Event/POSQU, Event/POS-order), 8-item advantage grid, SKPOS/iPad-POS/mPOS spec lists, target-segment icons (single/chain/enterprise), 8 store-type use-case tiles | Yes | `skpos_logo.svg`, ~15 imgs |
| `Business/GPS` | 車隊衛星定位 → Fleet GPS System | resx | Hero + feature lists | Yes | — |
| `Business/AIHotel` | 智能旅館 櫃台代管 → Smart Hotel / front-desk automation | **hard-coded CN** | Hero + intuitive-OS, smart counter, back-office, cost reduction, online booking, robotic reception, hotel mgmt system, 24h cloud center | **CN only** | 3 carousels |
| `Business/InfoSecurity` | 資安閘門防護 → InfoSec Gateway (with 關貿網路 Trade-Van) | **hard-coded CN** | Hero + product features (省錢/省時/省人力), 24h security team, target-customer grid, service architecture | **CN only** | 10 carousels |
| `Business/Shop` | 生活購物專區 → Lifestyle Shopping Zone | **@model ProductIndexView (DB)** | Category sidebar + paged product grid loaded via AJAX | CN (hard-coded title) | **Dynamic catalog, paging API** |
| `SmartHome/Index` | 智慧家庭 → Smart Home landing | resx | Hero + sub-product link cards (Community, Care, Wireless) | Yes | — |
| `SmartHome/Care` | 居家照護 → Home Care & Companionship (elder care) | resx | Hero + care feature blocks (8 EN branches) | Yes | ~images |
| `SmartHome/Community` | 社區雲端保全 → Community Cloud Guard (55KB, large) | resx | 18 carousels, many feature/spec blocks (25 EN branches) | Yes | Many images |
| `SmartHome/Wireless` | 無線智慧家庭 → Wireless Smart Home | resx + BaseView | Hero + device/feature blocks; uses `Model.IsMoblie`/`Model.Language` | Yes | — |
| `Green/Index` | 綠建築 → Smart/Green Building landing | resx + BaseView | Hero + 2 link cards (Architecture 綠建築, Prevention 防災) | Yes (sizing) | banner `C0-BN.jpg` |
| `Green/Architecture` | 物業管理 / 綠建築 → Green Building & Property Mgmt | resx | Hero + feature blocks + **PDF catalog download** | Yes (14 EN branches) | PDF: 物業管理平台型錄 |
| `Green/Prevention` | 消防防災 → Fire / Disaster Prevention (91KB, largest file) | resx | Hero + extensive fire-system feature content + **PDF download** | Yes (11 EN branches) | PDF: SKSFireSystem |
| `skpos/Index` | SKPOS micro-site | none | **Single external `<iframe>`** to a Wix site (3350px tall) | n/a (external) | iframe only |

---

## 5. Media & Assets

- **Image library**: `~/Content/sks/img/` holds **~393 files**; this section references roughly **70 unique images** and **~238 image/background references** total (heavy use of CSS `background-image:url(...)` for hero/section backgrounds).
- **Icon system**: a shared **SVG sprite** (`Content/sks/img/sprite.svg`) referenced via `<use xlink:href="…#icon-…">` for advantage/feature icons, plus per-feature PNG icons (e.g. `3-1AI智慧影像保全-01.png` … `-07.png`).
- **Brand logos**: `skpos_logo.svg`, `shop-logo.png`, `SK-PLUS-icon_02.png`, smart-life SVGs.
- **App-store badges**: `download-appstore.png`, `download-googleplay.png` linking to real SKS iOS/Android apps (守護城市2, 智慧生活2s).
- **PDFs (catalog/)** — 8 files, used as downloadable brochures / sales collateral:
  - `catalog/pms2022/新光保全_物業管理平台型錄.pdf` — Property-Management Platform catalog (linked from Green/Architecture).
  - `catalog/SKSFireSystem.pdf` — Fire System catalog (linked from Green/Prevention).
  - `catalog/多元化政策與落實情形.pdf` — Diversity policy (governance, ~5MB; not a product page but lives in catalog/).
  - Sales case-study / talk-track set (referenced as sales collateral, not directly linked in these views):
    `案例1連鎖大賣場.pdf` (chain hypermarket case), `案例2 知名五金百貨案例分享.pdf` (hardware-dept-store case), `案例2話術.pdf` (case-2 sales script), `案例3 知名國際精品案例分享.pdf` & `案例3國際精品.pdf` (international-luxury-brand case).
- **External embeds**: `skpos/Index` → `rowena0123.wixsite.com/dudoo` (Wix). POS detail-page CTAs deep-link to `/Event/POSQU` and `/Event/POS-order`.

---

## 6. Dynamic / Admin-Driven Content

Most pages are **static marketing copy** rendered from compiled .resx resources (editable only by rebuilding the app, not via Admin). The `@model` in the majority of pages is `sks.ViewModels.Shared.BaseView`, which carries only **language + mobile flags** (`Model.Language`, `Model.IsMoblie`) — not editable content.

The genuinely **DB / Admin-driven** page is:

- **`Business/Shop` (生活購物專區 Lifestyle Shopping Zone)** — `@model sks.ViewModels.Product.ProductIndexView`:
  - Renders a **category sidebar** from `Model.CategoryList` (`@foreach` over categories with `data-type`).
  - Loads the **product grid via AJAX** from `@Url.Action("GetProductList")` (`?type=…&CurrentPage=…`), with a **paging partial** `_Page` (`Model.PageResult`) and `Scripts/Admin/page.js`.
  - Categories/products are managed in the **Admin area** (note the `Scripts/Admin/page.js` reference and `localStorage 'producttype'` filtering). Default category label hard-coded "生活家電" (lifestyle home appliances).

No other page in this section has forms, financial tables, or news list/detail content — those data shapes belong to other portal sections.

---

## 7. Design-Rebuild Notes (for the design AI)

- **Repeating layout primitive**: full-bleed hero carousel → alternating image/text "solution card" rows → icon advantage grid → use-case image gallery → app/PDF/contact CTA. Designing 3–4 reusable components covers ~80% of the section.
- **Component candidates**: HeroCarousel, SolutionLinkCard (image+text+CTA, mirror-able), AdvantageIconGrid (3/4/8-up), SpecFeatureList (image + bullet list), UseCaseTileGallery, VenueSwitcher (tabbed, Security page only), StatCounterRow, AppDownloadBlock, PdfDownloadCTA, FloatingContactPill.
- **Bilingual must-have**: every text block needs CN + EN variants and tolerant heading sizing (EN strings are longer → current site downsizes `h2`→`h3`). Two pages (AIHotel, InfoSecurity) and Shop currently lack EN — flag for content parity in redesign.
- **Tone / themes**: trust, 24-hour cloud monitoring, AI smart video, cost/time/manpower savings (省錢/省時/省人力), nationwide service scale (600 guards / 350 engineers), smart living & elder care, green building & fire safety.
- **Inconsistencies worth fixing**: `skpos/Index` is just an external Wix iframe (off-brand); POS page also has a separate, duplicative SKPOS landing under `Business/POS`. Some `data-mart="ture"` typos in Security page panel toggles.
