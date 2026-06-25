# SKS Portal — Section Brief: Contact, Search & Utility Pages

Bilingual corporate portal for **新光保全 (Shin Kong Security Co. / SKS, TWSE 9925)** — an AIoT / smart-living and security services company. This brief covers the "Contact, Search & Utility" cluster: how a visitor reaches SKS, searches the site, navigates the whole site, logs into customer self-service systems, and views the SKS robotics showcase.

> Localization note: the site is primarily **Traditional Chinese (繁體中文)** with a full **English (`/en`)** variant. All UI copy comes from compiled .NET resource files (`sks.Resources.*`), so both languages share one set of templates and switch by the `lang` route value (`isEn = lang == "en"`). English coverage is effectively complete for this section (a few CN-only legal/product links are guarded by `@if (!isEn)`).

---

## Page Inventory

| Page | Route / Controller | Purpose | Primary content type | Bilingual | Dynamic (DB/Admin) |
|------|--------------------|---------|----------------------|-----------|--------------------|
| Contact Us 聯絡我們 | `Contact/Index` | Lead-capture contact form | Contact form (8 fields) + hero banner + intro copy | Yes (resx) | Partly — form posts to DB; City/Area lists from model; reCAPTCHA |
| Contact Success 感謝頁 | `Contact/Success` | Post-submit confirmation + auto-redirect | Static confirmation + 5s countdown | CN only (hard-coded) | No |
| Site Search 站內搜尋 | `Search/Index` | Full-text search of the site | Google Custom Search Engine (GCSE) embed | Yes (meta + banner) | No (external Google CSE) |
| Sitemap 網站地圖 | `SiteMap/Index` | Full link directory of the portal | 9-column link directory (nav lists) | Yes (resx + `isEn` guards) | Partly — ESG report & policy PDF filenames from resources |
| User / Customer Services 用戶專區 | `User/Index` | Hub of customer self-service logins | 8 icon "service cards" linking to external systems | Yes (resx) | No (links to external subsystems) |
| Robots 機器人 | `Robots/Index` | SKS robotics product/brand showcase | Hero + dev story + 9-item tabbed product gallery + competition story + FB CTA | Yes (resx, has `robot-en.png`) | No (static content + images) |

---

## 1. Contact Us — 聯絡我們 (`Contact/Index.cshtml`)

**Purpose:** Primary lead-generation form. Captures prospect details and routes them to SKS sales/service. Fires a Google Ads conversion event (`gtag conversion AW-451894712/...`) on the success page.

**Layout & copy:**
- **Hero banner:** full-width dark-masked cover image (`about-banner.jpg`), centered heading "聯絡我們 / Contact Us" (`vh-33`, fade carousel — single slide, banner pattern reused across the whole section).
- **Intro block** (`bg-svg-contact`, decorative SVG background): greeting + lead-in copy (`Index.Des1`, `Index.Des2` — `Des2` rendered as raw HTML, so it may contain phone/links).

**Contact form — field inventory (8 inputs):**
| Field | Type | Notes |
|-------|------|-------|
| Name 姓名 | text | required, validated |
| ContactTime 方便聯絡時間 | select | options: 任何時間 (Any time) / 08:00–12:00 / 13:00–17:00 / 18:00–22:00 |
| Phone 電話 | text | required, validated |
| City 縣市 | dropdown | `Model.CityList`, driven by Taiwan cities JS (`city.js`, `_TaiwanCities`) |
| Area 行政區 | dropdown | `Model.AreaList`, dependent on City (zip/district cascade) |
| Message 訊息內容 | textarea (5 rows) | required |
| Agree 同意條款 | checkbox | consent to personal-data terms (`Index.Agree`, raw HTML — links to privacy policy) |
| reCAPTCHA | Google reCAPTCHA v2 widget | explicit render, required before submit |
- Hidden field `EventId` (associates submission with a campaign/event).
- Submit button: "送出 / Send" (`Index.Send`), rounded pill, dark-blue.
- **Client-side validation** (jQuery validate + custom): alerts for missing consent, unselected city/area, empty message; blocks submit until reCAPTCHA solved. Posts `multipart/form-data` with anti-forgery token.

**Data shape:** ViewModel `sks.ViewModels.Contact.ContactView` (Name, Phone, City, Area, Message, ContactTime, EventId, CityList, AreaList). Submission is persisted server-side (lead record).

---

## 2. Contact Success — 感謝頁 (`Contact/Success.cshtml`)

**Purpose:** Thank-you / confirmation page after a successful form post; doubles as the conversion-tracking landing.

- Same hero banner pattern ("聯絡我們").
- Copy (hard-coded CN): "親愛的用戶您好：聯絡表單填寫成功，我們將盡速與您聯絡。" ("Dear user: your form was submitted successfully, we will contact you shortly.")
- **JS countdown:** 5-second counter, then auto-redirects to the site homepage.
- **Bilingual:** This page is hard-coded Chinese only (not resourced) — an English gap to fix in redesign.

---

## 3. Site Search — 站內搜尋 (`Search/Index.cshtml`)

**Purpose:** Site-wide keyword search.

- Hero banner "站內搜尋 / Site Search".
- **Implementation:** Embeds **Google Custom Search Engine** (`<gcse:search>`), loaded async via `cse.google.com/cse.js` with production CX `013764421759539850933:x3p_jllmv5a`. Ad blocks hidden via CSS.
- No native search UI to design beyond the banner + a results container; the search box/results are Google-rendered.
- SEO meta describes SKS as "(9925)... 家庭與商業保全、監控、POS、智慧門管、智慧建築... 5G、AI及IoT... AIoT服務的提供者" (family & business security, monitoring, POS, smart access, smart buildings; 5G/AI/IoT; AIoT service provider).
- **Bilingual:** meta/banner resourced; the Google CSE itself returns whatever it indexes.

---

## 4. Sitemap — 網站地圖 (`SiteMap/Index.cshtml`)

**Purpose:** Human-readable full directory of the portal — the best single map of the entire site's IA.

- Hero banner "網站地圖 / Site Map".
- **Structure:** 9 column groups (`col-md-3`), each a heading + `<ul>` of links. Many headings are themselves links to landing pages.

**The 9 groups and their links (CN term → EN gloss):**
1. **首頁/Home — 居家服務 (Family security):** Wireless smart home, Community cloud guard, Home care companionship, 離床偵測系統 (Bed-leave detection, *CN only*), AED (external), SKS fire extinguisher, SMART ADD-ON, Automated Proteng.
2. **商務服務 Business:** Office cloud guard, AI smart video protection, Business security, Video surveillance, Smart door keeper, SKS smart butler, Smart B&B suite, GPS fleet management, Digital dashcam, SKPOS, Driving vision assist, LADAKE mobile attendance, WorkLink.
3. **智慧建築 Smart Building:** Smart green building, Smart disaster prevention, Fire alarm monitoring, Vehicle-specific fire blanket, 鋰電池滅火器 (Lithium-battery extinguisher, *CN only*).
4. **關於新保 About SKS:** Company intro (`#list-intro`), Awards (`#list-award`), News center, Robots, Business groups.
5. **投資人關係 Investor Relations:** Company profile, Financial information, Shareholders column, Q&A, Investor contact.
6. **公司治理 Corporate Governance:** Board of directors, Functional committee, Internal regulations, Implementation status.
7. **利害關係人 Stakeholder Engagement:** Privacy policy (PDF), Personal-data profile (PDF), Sexual-harassment prevention measures (PDF), Stakeholder file (PDF), Occupational safety & health (PDF), ESG website (external `esg.sks.com.tw`).
8. **用戶專區 User / Customer Services:** Customer inquiry, Bill payment, Monitoring network, Change of contact person, Change of service settings, GPS fleet, CareU, SKS quarterly publication.
9. **企業社會責任 ESG/CSR:** AnShin classroom, Culture & Art Foundation, Community Care Welfare Foundation, ESG/CSR report (dynamic file `ViewBag.ReportFile`), ESG website.

- **Content type:** pure navigation directory (link lists), anchor-deep-links into long landing pages (`#list-*`), and external links.
- **Dynamic bits:** Stakeholder group serves **5 policy PDFs** from `https://www.sks.com.tw/FileUploads/Document/{resource-named-file}` with cache-busting `?v=Ticks`; CSR report PDF from `ViewBag.ReportFile` (Admin-managed). Filenames resolved via resources, so the actual document changes are admin/content-driven.
- **Bilingual:** Yes, full — links and labels are resourced; two product entries are `@if (!isEn)` (hidden in English).

---

## 5. User / Customer Services — 用戶專區 (`User/Index.cshtml`)

**Purpose:** Self-service hub directing existing customers to SKS operational subsystems (mostly separate legacy domains).

- Hero banner "用戶專區 / Customer Services".
- **Content type:** responsive grid of **8 "service cards"** (`col-6 col-md-4`), each = icon (SVG sprite or standalone SVG) + title (h3) + "了解更多 / Learn More" outline-pill button linking out (`target="_blank"`).

**The 8 cards (CN → EN gloss → destination):**
| Card | Destination (external system) |
|------|-------------------------------|
| 客戶查詢服務 Customer Inquiry | `www2.sks.com.tw/svc/` |
| 電子帳單 Bill Payment | `payportal.skspay.com.tw/SKSPayment/login` |
| 影像監控網 Monitoring Network | `isee24.com.tw/Login.aspx` |
| 變更聯絡人 Change of Contact Person | `www2.sks.com.tw/kman/changekmanreq.aspx` |
| 變更服務設定 Change of Service Settings | `www2.sks.com.tw/kman/changeNO.aspx` |
| GPS車隊管理 GPS Fleet Management | `gps2.skgps.com.tw/Login.aspx` |
| CareU | `careu.sklc.com.tw/login` |
| 新保季刊 SKS Quarterly Publication | internal `Event/publication` |

- **Media/assets:** icons from `sprite.svg` (`#icon-USER-01,03,04,05,06,07`), one standalone `電子帳單.svg` (e-bill), and one **inline SVG** (an open-book/magazine path) for the quarterly publication card.
- **Bilingual:** Yes (titles/buttons resourced); SEO description concatenates the service names with a locale-aware separator (`、` for CN, `, ` for EN).
- **Dynamic:** No DB content — static card grid linking to external apps.

---

## 6. Robots — 機器人 (`Robots/Index.cshtml`)

**Purpose:** Brand/product showcase for SKS service & security robots (a marketing/showcase page, not a utility page — included here because of the route grouping). Richest visual page in this cluster.

**Sections (top to bottom):**
1. **Tall hero** (`vh-75`, `robots-bn.jpg`, right-aligned): title `@Index.Robots` + tagline `@Index.RobotsDes`, carousel indicator.
2. **"Robot development" story** (dark cover `robot-bg.jpg`): heading + 5 paragraphs of development narrative (`RobotDevelopmentDes1–5`) beside a main product image (`@Index.RobotMainImage` → `robot.png` CN / `robot-en.png` EN — locale-specific asset).
3. **Tabbed product gallery** (`bg-gray-tri`): left vertical menu of **9 robots** (`No1`–`No8` + **Shinbobo**) driving a `list-change` JS tab switcher; each panel = product image (`robot-1.png … robot-9.png`) + name (h2, blue) + sub-headline (`NoXDes1`) + description (`NoXDes2`). Content type = image + spec/description card set.
4. **Competition / achievement story** (`bg-robot`): `competition.jpg` photo, a large inline brand SVG (multi-color robot/logo artwork), and a white shadow-box with heading `@Index.Competition` + 3 paragraphs (`CompetitionDes1–3`).
5. **Facebook CTA:** styled fake "search box" showing `@Index.FBName`, fb sprite icon, whole block linking to `facebook.com/sksrobot`.

- **Media/assets (~14 images):** `robots-bn.jpg`, `robot-bg.jpg`, `robot.png` / `robot-en.png`, `robot-1.png`…`robot-9.png` (9), `competition.jpg`, plus `sprite.svg` icons and inline SVG artwork.
- **Bilingual:** Yes — all copy resourced (`sks.Resources.Robot.Index`), with a dedicated English hero/main image (`robot-en.png`).
- **Dynamic:** No — static showcase (image filenames hard-coded in markup; copy from resources).

---

## Cross-Cutting Notes for Redesign

- **Shared hero/banner pattern:** Every page (except where noted) opens with the same `carousel-fade` + dark-masked cover (`about-banner.jpg`) + centered `h1` heading at `vh-33`. Robots is the exception (taller `vh-75`, custom imagery). A single reusable "section hero" component covers the whole cluster.
- **Bilingual architecture:** One template set, two languages via `lang` route value + compiled `.resx` resources (`sks.Resources.{Contact,Robot,Layout,...}`). No separate English templates. The redesign should keep string externalization; the **Contact Success page is the one hard-coded-Chinese gap**.
- **Form vs. directory vs. showcase:** Only Contact is a true interactive form (validation, reCAPTCHA, conversion tracking, DB write). User and Sitemap are link directories. Search is an external Google CSE embed. Robots is a static marketing showcase.
- **External dependencies:** Google reCAPTCHA v2, Google Custom Search, Google Ads conversion (gtag), and ~8 legacy SKS subsystem domains (`www2.sks.com.tw`, `skspay`, `isee24`, `skgps`, `sklc`, `esg.sks.com.tw`).
- **Admin/DB-driven content** is limited in this cluster: Contact form submissions (write) + City/Area model lists; Sitemap's policy/ESG **PDF filenames** resolved from resources and `ViewBag.ReportFile` (content team swaps the documents). Everything else is static template + resource copy.
- **Document assets referenced (Sitemap):** ~5 policy PDFs (privacy policy, personal-data profile, sexual-harassment prevention, stakeholder file, occupational safety & health) + 1 CSR/ESG report PDF, all served from `/FileUploads/Document/`.
