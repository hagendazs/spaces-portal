# Section Brief: News & Events (新聞與活動)

> AI-readable digest of the SKS corporate portal **News** and **Event** sections.
> Source views: `Views/News/{Index,Detail}.cshtml`, `Views/Event/{Index,Preview}.cshtml`.
> Backend is compiled (only Razor views are source); content is **DB-driven via the Admin area**.
> Site is mostly **Traditional Chinese (zh-TW)** with a parallel **English (en)** version.

---

## 1. Section Purpose

This section covers two distinct content engines that are often grouped under "News & Events":

1. **News Center (新聞中心 / News Center)** — a conventional, dated **press-release / announcement** list-and-detail system. Chronological corporate news, filterable by year.
2. **Event (活動 / Event / Campaign)** — a flexible **marketing campaign landing-page builder**. Admins assemble bespoke promotional pages from a library of reusable block layouts (hero banners, carousels, card grids, video heroes). Used for promotions, CSR campaigns, product launches, and similar time-boxed activities.

The two are mechanically different: **News** is a structured article feed; **Event** is a free-form page composer. Both are entirely populated from the Admin back office.

---

## 2. News Center (新聞中心)

### 2.1 Pages
- **`/News` — News list (`News/Index.cshtml`)**
  - Lives *inside the About (關於SKS) area shell*: a left sidebar (`aside.list-group`, desktop only) links to other About-area tabs — Introduction on SKS (SKS簡介), Awards (獲獎榮耀), News Center (active), Robots (機器人), Business Groups (事業群). News is presented as one tab of the corporate-profile experience.
  - **Hero**: a single-slide Bootstrap "carousel" (one image) using `about-banner.jpg`, dark mask, heading **關於SKS / About SKS**. Vertically centered, ~33vh.
  - **Year filter**: a `<select>` "依年份搜尋 / Search by year" populated `DateTime.Now.Year` down to **2015**. Selecting a year reloads `/News?year=YYYY` (client JS in `page.js` + inline script).
  - **News list**: `<ul>` of items, each = **date (`OnlineTime`, yyyy-MM-dd)** + **title**, linking to the detail page (`/News/Detail?id={ID}`).
  - **Pagination**: shared `_Page` partial (server-side paged result `Model.PageResult`).

- **`/News/Detail?id={ID}` — News article (`News/Detail.cshtml`)**
  - **Hero**: single-slide carousel, dark-masked title band; reuses `about-banner.jpg`.
  - **Article body**: optional **main image**, **H2 title**, a "刊登日期 / Push date" line with the date, then **rich HTML body** (`@Html.Raw(Model.Content)` — WYSIWYG content from admin).
  - **Back link**: "< 返回 / Back" returns to the appropriate list.
  - **Polymorphic by `NewsType`** — the same Detail view renders four content families, switching its title/back-link:
    - `News` → 新聞中心 / News Center → back to `/News`
    - `ClassRoom` → 安心教室 / Peace-of-Mind Classroom (event) → `/CorporateSocial/ClassRoom`
    - `Welfare` → 社會福利基金會 / Social Welfare Foundation (event) → `/CorporateSocial/WelfareFoundation`
    - `CultureAndArts` → 文化藝術基金會 / Culture and Art Foundation (event) → `/CorporateSocial/CultureArtfoundation`
  - So News detail doubles as the article renderer for **CSR / foundation "event" write-ups**, not just press releases.

### 2.2 Content types & data shape (News)
- **News list**: paged list of `{ ID, Title, OnlineTime (date), Content }`.
- **News detail**: `{ ID, Title, OnlineTime, Content (rich HTML), MainImage, Type (NewsType), Language }`.
- **SEO**: `ViewBag.Title` / `Description` auto-derived (title + first 60 chars of de-tagged body).
- **Filter dimension**: year (2015 → current).

### 2.3 Media (News)
- Main images served from `/FileUploads/NewsPhoto/` (**~229 files**) for zh-TW and `/FileUploads/NewsEnPhoto/` (**~190 files**) for English. Each article may have one `MainImage` plus inline images embedded in the rich-text body.
- Shared static hero: `/Content/sks/img/about-banner.jpg`.

---

## 3. Event (活動 / Campaign builder)

### 3.1 Pages
- **`/Event/...` — Live event page (`Event/Index.cshtml`)**
  - Gated by date window: renders only if `StartDate <= today <= EndDate`.
  - Per-page **font selector** (DFKai-sb 標楷體 / PMingLiU 新細明體 / Microsoft JhengHei 微軟正黑體) applied to `<body>`.
  - Full SEO via `MetaTitle` / `MetaDesc`.
- **`/Event/Preview` — Admin preview (`Event/Preview.cshtml`)**
  - Near-identical renderer **without** the date-window gate, plus explicit `<title>`/OG/Twitter/canonical meta and a CAPTCHA-refresh helper. Used by editors to preview unpublished/scheduled campaigns.

Both files implement the **same block engine**, branched by `Model.Language` (zh-TW vs English data sets).

### 3.2 The block system — content TYPES (Arrangement A–H)
An Event page is an ordered list of **`EventInformations` blocks**, each tagged with an `Arrangement` type that selects a layout template. Each block holds an ordered list of **`EventInformationItems`** (the actual content cards/slides). This is the core data shape a redesign must reproduce:

| Type | Layout | Key item fields used |
|------|--------|----------------------|
| **A** | Full-width text band (custom bg color/image) | Title, Content (rich HTML), BackgroundLink (bg), BgHeight |
| **B** | Carousel: icon + heading + copy + up to 2 buttons on left, hero image on right | Background, Icon, Title, Content, MainPic, Link1/2 + LinkName1/2 + BtnBgColor1/2 |
| **C** | Two-column text + media (image **or** YouTube embed), text left/right configurable | Title, Content, MainPic / VideoUrl, MainPicType (Image/Video), TextPosition, Links, isTargetBlank |
| **D** | Carousel: text panel over a full-bleed background image, panel position + button alignment configurable | Background, TextBgColor, TextContentHeight, Title, Content, Links, TextPosition, BtnPosition |
| **E** | Carousel: text block over background image with optional dark mask | Background, Title, Content, Links, TextPosition, BtnPosition, TextContentHeight |
| **F** | **Card grid carousel** (N cards per slide, `ShowItem`), image-top cards w/ title, 4-line clamped text, buttons | HeadLine, MainPic, Title, Content, Links, BgColor, ShowItem |
| **G** | **Icon/feature grid carousel** (N per slide), icon-top, optional linked icon, divider + text | HeadLine, Icon, Title, Content, Link1, isTargetBlank, ShowItem |
| **H** | **Video hero** — YouTube thumbnail background + SVG play button, click opens modal player; side text panel left/right | VideoUrl (→ YouTube ID), Title, Content, TextBgColor, BtnBgColor1, TextPosition |

Cross-cutting item capabilities: per-item **background color or image**, **two CTA buttons** with custom labels (rich-text, span-only) and **custom button colors**, **YouTube video** (parsed from `watch?v=` or `youtu.be/` URLs, embedded inline or in a shared `#sksVideo` modal), configurable **block min-height** and **section background**.

### 3.3 Event contact form (present but currently commented out)
`Preview.cshtml` contains a fully built **lead-capture / event-signup form** (toggled by `isContactStatus`, currently disabled in markup but backed by `EventContactAdmin`):
- **Category dropdown** (選項) from `EventContactChangeFieldNames` where `CoumnId == 0`.
- Dynamic fields by `CoumnId`: **1 Name (姓名, required)**, **2 Phone (電話)**, **3 Email (required)**, **4 City + Area cascading dropdowns + Address (required)** (`city.js`), **5 Remark/message textarea (required)**.
- Admin-customizable field labels, contact title/description, background color.
- Side **service-hotline (服務專線)** panel with phone-call icon and rich-text phone list.
- **CAPTCHA** image via `/Event/VerificationCode` with a Refresh() helper.
- A model exists (`Areas/Admin/Views/EventContactAdmin/{Index,Edit}.cshtml`) — so event signup/contact capture is a real, admin-managed capability even though the front-end block is disabled.

### 3.4 Media (Event)
- Campaign assets served from `/FileUploads/EventPhoto/` (**~1,004 files** — by far the largest media store in the section) for zh-TW and `/FileUploads/EventEnPhoto/` (**~205 files**) for English: backgrounds, icons, main pictures, card images.
- **YouTube videos** embedded (inline iframes and modal player); thumbnails pulled from `img.youtube.com/.../maxresdefault.jpg`.
- CSS: `Content/sks/css/popup.css` (video modal), Toastr (form notifications).

---

## 4. Bilingual coverage

**Yes — full English version exists for both sub-sections**, via parallel data and separate admin areas:

- **News**: zh `NewsPhoto` + `NewsAdmin`; en `NewsEnPhoto` + `NewsEnAdmin`. Detail view switches image folder and back-link prefix (`/en`) by `Model.Language`.
- **Event**: zh `EventInformations` (`EventPhoto`, `EventAdmin`) vs en `EventENInformations` (`EventEnPhoto`, `EventEnAdmin`). The Event views literally branch the entire render loop on `Model.Language` to walk the EN entity set.
- English image stores are smaller (190 vs 229 News; 205 vs 1,004 Event), implying English coverage is **partial / a subset** of the Chinese content in practice, especially for Events.

**Term glossary**: 新聞中心 News Center · 活動 Event/Campaign · 依年份搜尋 Search by year · 刊登日期 Push/publish date · 返回 Back · 服務專線 Service hotline · 安心教室 Peace-of-Mind Classroom · 社會福利基金會 Social Welfare Foundation · 文化藝術基金會 Culture & Art Foundation.

---

## 5. Dynamic / admin-driven content

**Everything in this section is DB-driven from the Admin back office.** Relevant admin areas:
- `Areas/Admin/Views/NewsAdmin` + `NewsEnAdmin` — manage news articles (title, date, body, main image, NewsType).
- `Areas/Admin/Views/EventAdmin` + `EventEnAdmin` — compose campaign pages (blocks, arrangement type, items, links, colors, fonts, date window, SEO meta).
- `Areas/Admin/Views/EventContactAdmin` — configure the event contact/signup form fields and view submissions.

The front-end views contain **no hard-coded copy** beyond UI chrome labels (which come from a localized `Resource` resx). All headings, body text, images, CTAs, colors, and layouts are editor-supplied.

---

## 6. Page inventory

| Page | Route | Type | Content model | Bilingual | Media source |
|------|-------|------|---------------|-----------|--------------|
| News list | `/News` (`/en/News`) | List + year filter + pagination | Paged `News[]` `{ID,Title,OnlineTime,Content}` | zh + en | `about-banner.jpg` (static) |
| News detail | `/News/Detail?id=` | Article (rich HTML) | `News {Title,OnlineTime,Content,MainImage,Type}` | zh + en | `NewsPhoto/` ~229 · `NewsEnPhoto/` ~190 |
| Event (live) | `/Event/...` | Composed campaign landing page (blocks A–H) | `EventView {EventInformations[].Items[], dates, font, meta}` | zh + en | `EventPhoto/` ~1,004 · `EventEnPhoto/` ~205 + YouTube |
| Event preview | `/Event/Preview` | Same engine, no date gate, +contact form scaffold | same + `EventContact*` | zh + en | same |

---

## 7. Design notes for redesign

- **News** wants a clean, scannable **dated-article list** with year facet + simple article template (image, title, date, rich body). It currently rides inside the About-area sidebar shell — a redesign could give it its own newsroom IA or keep the corporate-profile grouping.
- **Event** is effectively a **CMS page-builder / section library**. A redesign must preserve a block palette equivalent to A–H: text band, image+text carousel, split media (image/video), background-image text panels, card grid, icon-feature grid, and video hero. Each block needs: optional heading, rich text, 0–2 themeable CTA buttons, background color/image, height control, and image-or-YouTube media.
- Heavy reliance on **Bootstrap carousels** and inline styles; redesign should modernize but keep slide/card grouping and per-item color theming.
- The **video modal** (`#sksVideo`) and **YouTube auto-thumbnail hero** are recurring patterns worth componentizing.
- The **event contact form** (name/phone/email/city-area-address/remark + CAPTCHA + category) is a ready-made lead-capture component to formalize.
