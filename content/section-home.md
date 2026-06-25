# SKS Portal — Section Brief: Home & Global Navigation

> AI-readable content digest for redesign. Source: SKS (新光保全 / **Taiwan Shin Kong Security Co., Ltd**) ASP.NET MVC bilingual corporate site.
> Primary language Traditional Chinese (zh-TW); secondary English (`/en` route). Brand color: SKS yellow `#fed01f`, SKS blue.

---

## 1. Purpose of this Section

This section covers the **front door of the site**: the homepage (`Views/Home/Index.cshtml`) plus the **global chrome** — header navigation, footer, and shared scaffolding (`Views/Shared/_Layout.cshtml`, `_Page.cshtml`, `Error.cshtml`) — that wraps **every** page on the site.

- **Homepage** = a marketing landing page that funnels visitors into the three core business pillars, surfaces latest news, promotes the mobile apps, and exposes self-service/payment entry points.
- **Global nav (header + footer)** = the master information architecture for the entire portal. It is the canonical map of every product, corporate, investor, ESG, and self-service destination.

---

## 2. Page Inventory

| Page / Component | File | Route | Type | Bilingual | Dynamic source |
|---|---|---|---|---|---|
| Homepage | `Views/Home/Index.cshtml` | `/` and `/en` | Landing page (carousel + pillars + news + apps) | Yes (zh-TW + en) | Banner list + News list from DB (Admin) |
| Master layout (header nav, footer, contact strip) | `Views/Shared/_Layout.cshtml` | all pages | Global chrome | Yes | Mostly static resx; PDF links + report file dynamic |
| Pagination partial | `Views/Shared/_Page.cshtml` | reused on list pages | UI component | n/a | Bound to `Models.Cmind.Page` |
| Error / 404 page | `Views/Shared/Error.cshtml` | error fallback | Static error page | Yes | Static |

---

## 3. Homepage Content (`Views/Home/Index.cshtml`)

Rendered top-to-bottom as distinct full-width sections:

### 3.1 Hero Banner Carousel (dynamic, admin-driven)
- **Bootstrap carousel** (`#main-banner`), auto-rotating, 10s per slide, indicator dots generated per slide.
- Slides come from `Model.BannerList` — managed in Admin (`BannerAdmin` zh / `BannerEnAdmin` en).
- Two slide **types**:
  - **Picture slide** (`BannerType.Pic`): full-bleed background image with overlaid copy — an **intro line** (`item.Intro`, rendered `h3`), a **title** (`item.Title`, rendered `h1`), and an optional **"Learn More" CTA button** (`Resource.LearnMore`) linking to an internal route or external URL. Supports per-slide config: text color (white/black), text position (left / right with `bg-left` panel), vertical/horizontal offsets, optional SKS wireless logo SVG, and a **separate mobile image** (`item.MobileImg`).
  - **Video slide** (`BannerType.Video`): YouTube thumbnail (`img.youtube.com/vi/{VideoId}/maxresdefault.jpg`) with a play-button SVG that opens a modal video player (`#sksVideo`, `data-video`).
- **Banner data shape:** `{ Image, MobileImg, Intro, Title, Link, Type, VideoId, Position, TextColor, isOffset, isPhoneOffset, isLogo }`.

### 3.2 Three Business Pillars (the site's primary navigation tiles)
Three equal background-image tiles with dark-mask hover, each a fadeInDown animated link:
- **智慧家 Smart Home** (`Resource.Home`) → `SmartHome/Index`, image `home.jpg`
- **智慧商務 Business** (`Resource.Business`) → `Business/Index`, image `office.jpg`
- **智慧建築 Smart Building** (`Resource.SmartBuilding`) → `Green/Index`, image `intelligence.jpg`

### 3.3 Service / Self-Service Strip (SKS blue band)
- **Customer service hotline copy** (`Resource.ServiceDes`) with two regional toll-free numbers:
  - 北區 Northern Area `0800-097668`
  - 南區 Southern Area `0800-897668`
- Two pill CTA buttons:
  - **線上繳費 Bill Payment** → external `payportal.skspay.com.tw`
  - **會員專區 User Section** → `User/Index` (user icon)
- Layout differs slightly between zh and en (en stacks the hotline full-width).

### 3.4 News Center (dynamic, admin-driven)
- Heading **新聞中心 News Center** (`Resource.NewsCenter`) linking to `News/Index`.
- 3-column list from `Model.NewsList`: each item = **date** (`OnlineTime`, `yyyy-MM-dd`) + **title** (3-line clamp ellipsis), linking to `News/Detail?id={ID}`.
- zh titles get custom word-spacing logic for embedded English tokens.
- **Data shape:** `News { ID, Title, OnlineTime }` → list+detail pattern.

### 3.5 "Intro" Animated Diagram Section (dark night background)
- Marketing copy `Resource.Intro` + `Resource.Intro2` (vision/brand statement) over `bg-night`.
- Decorative inline **SVG animation** — glowing nodes, dashed connector lines, and 5 product icons (`icon-index-icon-92..96`) representing a connected/IoT security network. Purely illustrative, no data.

### 3.6 Mobile App Promotion (light-gray band)
Two app cards, each = icon + name + description + App Store & Google Play badge links:
- **守護城市2 City Guardian 2** (`SK-PLUS-icon_02.png`) — `Resource.CityGuardian2` / `CityGuardian2Des`. iOS + Android links.
- **智慧生活2s Smart Life 2s** (`1 首頁 智慧家2S素色款200X200.svg`) — `SmartLife2s` / `SmartLifeDes`. iOS + Android links.

---

## 4. Global Header Navigation (`_Layout.cshtml`)

Fixed-top Bootstrap navbar. Logo (SVG text `icon-sks-text` / `icon-sks-text-en`) links home. Mobile collapses to hamburger. Top-level menu:

1. **服務項目 Service Items** (`Resource.ServiceItems`) — mega-dropdown, 4 columns mirroring the three pillars:
   - **智慧家 Home:** 家庭保全服務 Family Security · 社區雲端服務 Community Cloud Guard · 居家照護陪伴 Home Care · 離床偵測系統 Bed-Exit Detection (zh only) · AED (external) · SKS滅火 Fire Out · SMART ADD-ON · 自動式滅火 Automate Proteng
   - **智慧商務 Business:** 企業雲端服務 Business Cloud Guard · AI智慧影像保全 AI Smart Video · 商務保全服務 · 影像監控 Video Surveillance · 智慧門禁 Smart Door Keeper · SKS智慧管家 Smart Butler · 智慧民宿 Smart B&B · GPS車隊管理 · 數位行車紀錄器 Dash Camera · SKPOS · 行車視野輔助 Driving Vision Assist · LADAKE行動考勤 · WorkLink
   - **智慧建築 Smart Building:** 智慧綠建築 Smart Green Building · 智慧防災 Smart Disaster Prevention · 火警監控服務 Fire Alarm Monitoring · 車用滅火毯 Vehicle Fire Blanket · 鋰電池滅火器 Lithium-Battery Extinguisher (zh only)
2. **關於新保 About SKS** (`Resource.AboutSKS`) → `/About/Index` tabs: 公司簡介 Introduction · 獲獎紀錄 Awards · 新聞中心 News Center · 機器人 Robots · 事業群 Business Groups
3. **投資人關係 Investor Relations** → `Shareholder/Index` tabs: 公司簡介 Company Profile · 財務資訊 Financial Information · 股東專欄 Shareholders' Column · Q&A · 投資人聯絡 Investor Contact
4. **公司治理 Corporate Governance** → `Company/Index` tabs: 董事會成員 Board Members · 功能性委員會 Functional Committee · 公司內規 Internal Regulations · 運作情形 Implementation Status
5. **利害關係人 Stakeholder Engagement** → `Stakeholder/Index` (mobile dropdown links to policy PDFs + ESG site)
6. **企業社會責任 ESG / CSR** (`Resource.ESGSection`) → `CorporateSocial/Index`: 安心教室 An-Shin Classroom · 文化藝術基金會 Culture & Art Foundation · 社會福利基金會 Welfare Foundation · 永續報告書 Sustainability Report
7. **線上繳費 Bill Payment** (mobile only, external)
8. **Language toggle** 中 / En → `/` vs `Home/Index?lang=en`
- **Utility icons (right):** Facebook, YouTube, User/會員 area, **Search** (`Search/Index`).

---

## 5. Global Footer (`_Layout.cshtml`)

### 5.1 Contact / Action Row (above footer)
Four icon links: **服務據點 Service Location** (`Location`) · **聯絡我們 Contact Us** (`Contact`) · **人才招募 Careers** (external 104 jobs) · **利害關係人 Stakeholder** (`Stakeholder`).

### 5.2 Footer Link Columns
Full sitemap repeated as 7 columns:
- **居家用戶 Home Users** (8 product links, same as Home pillar)
- **企業用戶 Business Users** (13 product links)
- **智慧建築 Smart Building** (5 links)
- **關於新保 About SKS** (Intro, Awards, News, Robots, Business Groups)
- **企業社會責任 ESG** (Classroom, Foundations, Sustainability Report, external ESG site)
- **利害關係人 Stakeholder** — links to policy **PDF documents** (see §6)
- **會員專區 User Section** — external self-service portals: 客戶查詢 Customer Inquiry · 線上繳費 Bill Payment · 監看網 Monitoring Network (isee24) · 變更聯絡人 / 服務設定 Change Contact/Settings · GPS車隊 · CareU · 季刊 SKS Quarterly Publication

### 5.3 Footer Base
- SKS logo (`logo.svg`).
- **客服專線 Customer Service Hotline:** 北區 `0800-097668` / 南區 `0800-897668`.
- Copyright: `©新光保全股份有限公司 TAIWAN SHIN KONG SECURITY CO.,LTD` · address (Google Maps link, 台北市內湖區行愛路128號) · 網站地圖 SiteMap link.
- Floating "TOP" scroll-to-top button.

---

## 6. Content Types & Data Shapes (this section)

- **Banner carousel** — DB list of image OR video slides with rich per-slide layout config (admin-managed, separate desktop/mobile images).
- **News list + detail** — DB list (date + title, 3-up, ellipsis), links to detail page by ID.
- **Mega-menu / hierarchical nav** — static link tree (resx-driven), 6 top-level groups feeding the full IA.
- **PDF document download links** (footer Stakeholder column + mobile menu), cache-busted with `?v={Ticks}`:
  - 隱私權政策聲明 Privacy Policy Statement
  - 個人資料檔案安全維護計畫 Personal Data File Security Maintenance Plan
  - 性騷擾防治措施 Sexual Harassment Prevention Measures
  - 利害關係人關注議題與溝通管道 Stakeholder Concerns & Communication Channels
  - 職業安全衛生 Occupational Safety and Health
- **App-store promotion cards** — icon + name + copy + 2 store badge links.
- **Pagination component** (`_Page.cshtml`) — bound to `Page { CurrentPage, PageSize, TotalPages, Count }`, ±2 window.
- **Contact/hotline data** — toll-free numbers, address, social links (hardcoded in layout).

---

## 7. Media / Assets

- **Banner images (admin uploads):** `/FileUploads/BannerPhoto/` ≈ **63 images** (zh), `/FileUploads/BannerEnPhoto/` = **3 images** (en) — confirms English banner coverage is thin.
- **Homepage section imagery (static, `Content/sks/img/`):** pillar tiles `home.jpg`, `office.jpg`, `intelligence.jpg`; night BG `night-bg.jpg`/`bgNight.png`; app icons `SK-PLUS-icon_02.png`, `1 首頁 智慧家2S素色款200X200.svg`; store badges `download-appstore.png`, `download-googleplay.png`.
- **SVG sprite** (`Content/sks/img/sprite.svg`) — logo text, social icons (fb, yt), search, footer icons, IoT diagram icons (`icon-index-icon-92..97`).
- **PDFs:** site-wide policy docs served from `/FileUploads/Document/`; repo `Content/sks/file/` holds 2 sample PDFs (ESG報告書, 性騷擾防治辦法).
- **Total `Content/sks/img/` library:** ~393 image/SVG assets (whole-site shared pool).
- **OG/share image:** `Content/sks/img/Imgog.jpg`; favicon `ico.png`.

---

## 8. Bilingual Coverage

- **Full bilingual chrome.** Layout, nav labels, footer, error page all resolve through resx resources (`sks.Resources.Layout.Resource`, etc.) with `zh-TW` and `en-US` variants. `isEn` switches layout (e.g. fluid container, wider mega-menu, full-width hotline) and hides zh-only product items (離床偵測系統, 鋰電池滅火器).
- **Homepage** is bilingual; banners/news have separate EN admin controllers (`BannerEnAdmin`, `NewsEnAdmin`).
- **Caveat:** English **banner** content is sparse (3 vs 63 images), so the EN homepage carousel is far lighter than zh.

---

## 9. Dynamic / Admin-Driven Content (from `Mvc.sitemap`)

The Admin area (`/Admin`) drives the homepage and feeds many linked sections. Relevant to this section:
- **首頁輪播圖片 Home Banner** — `BannerAdmin` (zh) / `BannerEnAdmin` (en) → drives §3.1 carousel.
- **新聞中心 News Center** — `NewsAdmin?type=1` / `NewsEnAdmin?type=1` → drives §3.4 news list.
- **活動聯絡我們 / 聯絡我們 Contact** — `EventContactAdmin`, `ContactAdmin` → feed Contact Us (footer link).
- **獎項 Awards** — `AwardAdmin` → About section.
- Other admin-managed (linked from nav but other sections): News by type (安心教室=2, 關懷基金會=3, 文化藝術基金會=4), Financial/Shareholder tables by type 1-10 (財務報告, 營業收入, 年報, 股東會, 法說會, 運作/溝通情形, 公司內規), 股利政策 ShareholderPolicyAdmin, 活動管理 EventAdmin.
- Admin nodes are role-gated (`SystemAdmin`, `Event`).

---

## 10. Notes for Redesign

- Header IA is **dense** (6 top-level + a 4-column mega-menu of ~25 products) — primary consolidation/grouping opportunity.
- Product links are duplicated across header mega-menu AND footer columns (maintenance smell).
- Many footer/User links point to **external legacy portals** (different domains/skins) — UX seam to address.
- Embedded GTM/gtag analytics and inline styles live in `_Layout` head.
- The hero supports image+text and YouTube-video slides; preserve both modes.
