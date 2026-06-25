# Section Brief — Corporate Social Responsibility (企業社會責任 / CSR)

> Source: `Views/CorporateSocial/` in the SKS (新光保全 / Shin Kong Security) ASP.NET MVC bilingual portal.
> Controller: `CorporateSocial` (compiled into `/bin`; not present as source in this repo). View models reference `sks.ViewModels.CorporateSocial.CorporateSocialView` and resource files `sks.Resources.CorporateSocial.*` (also compiled, not in source tree).
> Audience for this brief: a design AI rebuilding the section. Preserve key Chinese terms with English gloss.

---

## 1. Purpose of the Section

The CSR section is the public-facing hub for SKS's social-good activities. It is a **landing hub + four destinations** structure: a hub page (`Index`) introduces four CSR pillars, three of which are full content pages within this section, and one is an outbound link to the corporate ESG microsite.

The three in-section destinations are SKS's charitable/foundation programs:
- 安心教室 (**An-Shin Classroom / "Peace of Mind Classroom"**) — child-safety education program.
- 文化藝術基金會 (**Culture and Art Foundation**).
- 社區關懷社會福利基金會 (**Community Care Social Welfare Foundation**).

Each foundation page combines an **about/mission narrative**, an **organizational chart**, a **regulatory disclosure download table** (statutory financial/operational reports), and a **news/announcements feed** scoped to that program.

---

## 2. Page Inventory

| Page | Route (`CorporateSocial/…`) | File | Purpose | Primary content types |
|------|------------------------------|------|---------|------------------------|
| CSR Hub | `/Index` | `Index.cshtml` | Overview + entry to 4 pillars | Hero banner, 4 icon+text link cards, report download, outbound ESG link |
| An-Shin Classroom 安心教室 | `/ClassRoom` | `ClassRoom.cshtml` | Child-safety education program intro + news | Hero banner, 2 narrative blocks (9 + 9 paragraphs), Facebook CTA, news list (zh only) |
| Culture & Art Foundation 文化藝術基金會 | `/CultureArtfoundation` | `CultureArtfoundation.cshtml` | Foundation mission, org chart, disclosures, messages | Banner, mission text, org chart (SVG/img), 5 role cards, PDF disclosure table, news list (zh only) |
| Community Care Welfare Foundation 社會福利基金會 | `/WelfareFoundation` | `WelfareFoundation.cshtml` | Foundation mission, org chart, disclosures, news | Banner, mission text, org chart (SVG), role cards, PDF disclosure table, news list (zh only) |

API endpoint used by foundation/classroom pages: `CorporateSocial/GetNewsList?year=&lang=&type=&CurrentPage=` returns a paged HTML partial (`_Page`) of news items.

---

## 3. Page-by-Page Content & Copy Themes

### 3.1 Hub — `Index.cshtml`
- **Hero banner** (single static slide in a Bootstrap carousel shell, full-bleed `K0-BN.jpg`, 75vh): heading 永續發展 / **Sustainability** + a 4-line mission paragraph (`SustainabilityDes1–4`).
- **Four pillar cards** (icon SVG + heading + description + "了解更多 / Learn More" arrow link):
  1. 安心教室 **An-Shin Classroom** → `/ClassRoom`
  2. 文化藝術基金會 **Culture and Art Foundation** → `/CultureArtfoundation`
  3. 社區關懷社會福利基金會 **Community Care Welfare Foundation** → `/WelfareFoundation`
  4. **ESG Website** → outbound `https://esg.sks.com.tw/zh-TW` or `/en` (opens new tab) + a **report download** link (`ViewBag.ReportFile`, filename from `Index.ReportName`).
- Closing full-viewport background image band (`K0-bg.jpg`).
- Icons pulled from an SVG sprite (`sprite.svg#icon-kicon1..4-02`).

### 3.2 An-Shin Classroom — `ClassRoom.cshtml`
- **Hero banner**: `room_BN.jpg` background + program logo `room_BN_logo2.png` (50vh).
- **Narrative block 1** over `children.jpg`: program title + 9 paragraphs (`ClassRoom.Des1–Des9`) describing the 安心教室 child-safety / community-care education initiative.
- **Facebook CTA module**: image `animals.jpg` + a styled "search box" graphic + FB icon linking to `https://www.facebook.com/homeshoplove` (the program's Facebook page, label 居家好物 love / "homeshoplove").
- **Narrative block 2** over `children2.jpg`: sub-heading `Des10` + 8 paragraphs (`Des11–Des18`).
- **News Center 新聞中心** (rendered **only for zh-TW**): year filter `<select>` (current year down to 2017) + AJAX-paged news list (news `type = ClassRoom`).

### 3.3 Culture & Art Foundation — `CultureArtfoundation.cshtml`
- **Banner**: `foundation-BN.jpg` + title image `art-title.png`.
- **Intro title** (`CultureArtfoundation.Title`).
- **Mission panel** (gray bg, 2-col with `concert.jpg`): four labeled sections — 基本資料 **Info**, 設立宗旨 **Purpose**, 工作目標 **Objective**, 獲獎紀錄 **Awards Record** — each heading + paragraph.
- **Organizational chart 組織架構**:
  - zh-TW: inline hand-built **SVG org chart** with text nodes — 董事會 BoardDirector → 董事長 Chairperson → 執行長 CEO → 副執行長 ViceCEO → 企劃 Planning / 財務 Finance / 行政 Administration.
  - en: static image `公司組織圖-英文網站.svg`.
  - Below chart: **5 role cards** (heading + description): BoardDirector, Chairperson, CEO, Accounting (會計), AssociationAffairs (會務).
- **Disclosed Information 資訊公開** table (**zh-TW only**): financial/operational report downloads grouped by ROC fiscal year (民國年, current year−1911 down to 106 / 2017). Columns: 年度 Year · 項目 Item · 下載 Download. Rows render from `Model.FileList` (DB-driven), with rowspan grouping per year; download links point to `/FileUploads/Document/{FileName}`.
- **Message 訊息 / news list** (zh-TW only): same year-filter + AJAX paged list (news `type = CultureAndArts`).

### 3.4 Community Care Welfare Foundation — `WelfareFoundation.cshtml`
Structurally near-identical to the Culture & Art page:
- **Banner**: `welfare-BN.jpg` + title image `welfare-title.png`. Custom gradient bg using `welfare.jpg`.
- **Mission panel**: 基本資料 Info, 設立宗旨 Purpose, 工作目標 Objective (heading + paragraph each).
- **Organizational chart**: large inline SVG org chart (董事會 / 董事長 / 執行長 …, with many clip-path text nodes) + role cards (BoardDirector, Chairperson, CEO …).
- **Disclosed Information 資訊公開** PDF table (zh-TW only), grouped by ROC fiscal year from `Model.FileList`, download links → `/FileUploads/Document/`.
- **Message / news list** (zh-TW only): year filter + AJAX paged list (news `type = Welfare`).

---

## 4. Content Types & Data Shapes

- **Hero / banner carousel**: Bootstrap `carousel` markup but effectively a single static slide per page (full-bleed background image + overlaid heading/paragraph or title PNG). Reusable pattern across all 4 pages.
- **Icon link cards** (hub): SVG sprite icon + H3 + paragraph + "Learn More" arrow link. 4 per hub.
- **Narrative blocks**: heading + an ordered run of short paragraphs from resource strings (no rich HTML; paragraph count is fixed in markup — Classroom has 17 body paragraphs total).
- **Role/feature cards** (org section): H3 + paragraph, laid out in a responsive grid (≈5–6 per foundation page).
- **Organizational chart**: hand-coded SVG (text nodes are localized resource strings) for zh; a flat SVG image for en.
- **Disclosure download table** (`table-download`): server-rendered from `Model.FileList` (collection of items with at least `FiscalYear` (ROC year int), `Title`, `FileName`). Grouped by year with rowspan; each row is a PDF/Doc download. DB/Admin-driven.
- **News / message feed**: `Model.NewsList` (a paged `sks.Models.Cmind.Page` model) rendered via shared `_Page` partial; augmented by AJAX (`GetNewsList`) for year filtering and pagination. News items carry a `MainImage` served from `/FileUploads/NewsPhoto` (zh) — individual articles open in `News/Detail`. News is **filtered by `NewsType`** enum: `ClassRoom`, `CultureAndArts`, `Welfare`.
- **External link + file download** (hub ESG card): outbound link + downloadable report (`ViewBag.ReportFile`).
- **Facebook embed CTA** (Classroom): decorative "search box" UI linking out to the program's Facebook page.
- No contact form or product-spec cards in this section.

---

## 5. Media & Assets

All under `/Content/sks/img/` unless noted. Approximate inventory used by this section:

**Backgrounds / banners (JPG):** `K0-BN.jpg`, `K0-bg.jpg`, `room_BN.jpg`, `children.jpg`, `children2.jpg`, `animals.jpg`, `foundation-BN.jpg`, `concert.jpg`, `welfare-BN.jpg`, `welfare.jpg` (~10 photographic images).

**Logos / title art (PNG):** `room_BN_logo2.png` (An-Shin Classroom logo), `art-title.png` (Culture & Art title), `welfare-title.png` (Welfare title) (~3).

**Vector / icons (SVG):** `sprite.svg` (hub pillar icons `#icon-kicon1..4-02`, `#icon-fb`); `公司組織圖-英文網站.svg` (English org chart); inline hand-coded org-chart SVGs in the foundation pages.

**Downloadable documents (PDF/DOC/XLS):** served from `/FileUploads/Document/` (a shared pool of **~639 files** across the whole site). Roughly **70+** are CSR/foundation-related, e.g. `捐款名冊` (donor rosters), `愛心米捐款芳名錄` (charity-rice donation lists), `預算書 / 工作計畫及經費預算` (budget & work plans), `工作報告及財務決算` (annual operating & financial reports, incl. 衛福部 MOHW filing forms), and foundation `財報` (financial statements). The exact rows shown are chosen by `FiscalYear` matching, so the table content is data-driven, not hard-coded.

---

## 6. Bilingual Coverage (zh-TW primary, en secondary)

- The portal runs both `zh-TW` (Traditional Chinese, primary) and `en-US`. All static copy comes from localized resource classes (`sks.Resources.CorporateSocial.*`), so headings/body **do have English versions**.
- **Partial English parity** — several blocks are deliberately **zh-TW only** (`@if (Model.Language != Language.en)`):
  - News / Message feeds are hidden in English on all three foundation/classroom pages.
  - The **Disclosed-Information PDF tables** are hidden in English (regulatory disclosures are zh-only).
  - The hand-coded org-chart SVG is zh-only; English uses the flat `公司組織圖-英文網站.svg` image.
- The hub ESG link switches locale (`/zh-TW` vs `/en`).
- **Net:** English visitors get the hub + the narrative/mission + org chart for each program, but **not** the news feeds or statutory download tables.

---

## 7. Dynamic / Admin-Driven Content

Rendered from the database via the Admin area (`Areas/Admin`), not hard-coded:
- **News / announcements** per program — created in Admin, filtered by `NewsType` (ClassRoom / CultureAndArts / Welfare), with main images under `/FileUploads/NewsPhoto`; consumed via the `GetNewsList` API + `_Page` pagination partial; detail at `News/Detail`.
- **Disclosure file lists** (`Model.FileList`) — uploaded documents with `FiscalYear`, `Title`, `FileName`, stored under `/FileUploads/Document/`; surfaced in the year-grouped download tables.
- **Hub ESG report** (`ViewBag.ReportFile` / `Index.ReportName`) — an admin-set downloadable report.

Static (resource-string / markup) content: hero copy, pillar card text, foundation mission narratives, org-chart structure, role-card descriptions.

---

## 8. Design Notes for Rebuild

- Four-pillar hub with strong icon language; pillar 4 (ESG) is an outbound bridge, not an in-section page — keep it visually distinct.
- Foundation pages share one template: **Banner → Mission (labeled sections) → Org chart → Disclosure downloads → News**. Consolidate into a reusable layout.
- Replace hand-coded SVG org charts with a real localizable component (current approach has zh/en divergence and an image fallback).
- Disclosure tables are dense, year-grouped PDF lists — design for scannability (year anchors, file-type/size hints) and consider exposing them in English too.
- The "carousel" wrappers hold a single slide each; they can be flattened to static hero sections unless multi-slide is wanted.
- An-Shin Classroom is the most copy-heavy (17 narrative paragraphs + Facebook CTA) — needs editorial/long-form treatment distinct from the foundation template.
