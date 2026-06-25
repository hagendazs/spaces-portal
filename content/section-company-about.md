# Section Brief: Company & About — 新光保全 (Shin Kong Security, "SKS")

> AI-readable content digest for redesign. Source: ASP.NET MVC (.cshtml Razor) portal at `/Users/reiser/repo/sks`.
> Primary language: Traditional Chinese (zh-TW). English (en) served via the same views with a `lang` route value and culture-bound resource strings.

## 1. Section Purpose

The "Company & About" cluster is the corporate-identity and governance hub of the SKS site. It tells visitors **who SKS is** (history, vision, certifications, scale, awards, group structure), **how it is governed** (board, committees, internal rules, governance training), and **where to find SKS** (nationwide + overseas service offices). It blends marketing storytelling (vision/philosophy graphics) with regulatory/investor-relations compliance content (board roster, committee charters, downloadable governance PDFs).

Routes / controllers involved:
- `/About/Index` — About SKS (main story page, tabbed)
- `/About/Group?cid=N` — Subsidiary/affiliate detail card (data-driven by query param)
- `/Company/Index` — Corporate Governance (公司治理) — board, committees, rules, implementation
- `/Location/Index` — Service Locations (服務據點) — searchable office directory
- `/about/*.html` — legacy static redirect + standalone privacy-policy pages

---

## 2. Page-by-Page Content

### 2.1 About SKS — `Views/About/Index.cshtml`
Page title: `關於新光保全 / About SKS`. Banner: full-width hero carousel (single slide) over `about-banner.jpg` with dark mask and centered heading.

Left rail = tab navigation (desktop `list-group`, hash-routed tabs). Tabs:
1. **公司簡介 / Introduction on SKS** (`#list-intro`) — default active.
2. **獲獎紀錄 / Awards** (`#list-award`).
3. **新聞中心 / News Center** — link out to `/News`.
4. **機器人 / Robots Section** — link out to `/Robots`.
5. **關係企業 / Business Groups** (`#list-groups`).
(Commented-out, present in source but disabled: 里程碑/Milestone, 關係企業/affiliated-enterprises mega-list.)

**Intro tab content themes:**
- Company introduction paragraphs (`AboutSKS` + `AboutSKSDes1–4`) — resource-driven prose about SKS as a security company.
- **企業願景 / Enterprise Vision** block paired with a hand-built decorative SVG (AI / IoT / "Security Experience" / "Leader" / business-and-enterprises iconography) — illustrates SKS positioning around AI + IoT security.
- **經營理念 / Business Philosophy** — three gradient "circle" cards: 經營理念 (Business Philosophy), 企業願景 (Enterprise Vision), 創業精神 (Entrepreneurial Spirit), each with two short slogan lines (some `Html.Raw`).
- **智慧財產保障 / Intellectual (property) Guarantee** — two certification SVG badges: **ISO 9001** and **ISO 27001**, plus explanatory copy.
- **Scale / reach stat block** — big-number callouts: **6 區 (Districts)**, **31 駐點 (Stationed Areas)**, **69 營業所 (Operations Offices)**, flanked by two photos (`About6Image` "跨國服務/cross-border service", `About7Image` "深耕台灣/rooted in Taiwan"), with English-swapped image variants (`-en` suffix).

**Awards tab:** responsive grid (`col-6 / col-md-4 / col-xl-3`) of award cards. Each card = award photo + year/sort label + award title. **DB-driven** from `Model.AwardList` (images served from `~/FileUploads/AwardPhoto/`). zh-TW splits multi-line titles on commas; en shows raw title.

**Business Groups tab:** static `<ul>` of ~18 group/subsidiary companies. Each item = SVG sprite logo (or PNG) + Chinese company name + English gloss. Mix of external links (open new tab) and internal links to `/About/Group?cid=N`. Companies include 台灣保全 (Taiwan Security), 聯安服務 (Lan An), 誼光保全 (Yi Kong), 新誼整合科技 (Shin Soft), 昕保租賃 (Shin-Po Leasing), 新光電通, 新群電子, 新工光電, 新保生活關懷, 新堡科技 (E Tech Pro), 新昕國際 (New Light Int'l), 台保服務科技, 新光保全(上海) (Shanghai), Security Shin Kong (Thai), 漢軍科技 (Hundure), 思邁特國際 (Smart Verge), etc.

### 2.2 Group / Subsidiary Detail — `Views/About/Group.cshtml`
Reusable subsidiary "business card" page. Same banner. Renders a single company's profile from a **client-side JS lookup table keyed by `cid`** query param. Data shape per company: `name, sprite(logo), est(成立日期/incorporation date), capital(資本額), chairman(董事長), manager(總經理/CEO), idea(經營理念)`. Companies wired in JS: cid 1 = Shanghai, 2 = Lan An, 4 = Thai, 5 = New Light Int'l, 6 = Shin-Po Leasing. Table columns: Incorporation Date / Chairperson / CEO / Capital, then a Business-Philosophy paragraph and a "< Back" link to `#list-groups`.

### 2.3 Corporate Governance — `Views/Company/Index.cshtml`
Page title: `公司治理 / Corporate Governance`. Banner reuses `about-banner.jpg`. Left rail tabs:
1. **董事會成員 / Members of Board of Director** (`#list-chairman`) — default.
2. **功能性委員會 / Functional Committee** (`#list-committee`).
3. **內部規章 / Internal Regulations** (`#list-rules`).
4. **執行情形 / Implementation Status** (`#list-chaining`).

**Board tab:** intro resume prose (`Resume`, `ResumeDes1–12`) then a **board roster table** (cols: 職稱/Title, 姓名/Name, 法人股東/Institutional Shareholder, 學歷/Education, 經歷/Experience). ~9 hard-coded directors (Chairperson Richard Wu, directors incl. ALSOK's Tsuyoshi Murai, plus independent directors). All values are resource-keyed (translatable), not DB-driven.

**Committee tab:** four committee sections, each with charter prose + a member table (Title / Name / Experience):
- 審計委員會 / Audit Committee (`AuditDes1–13`)
- 薪資報酬委員會 / Compensation Committee
- 永續發展委員會 / Sustainability Committee
- 投資評估委員會 / Investment Evaluation Committee

**Internal Regulations tab & Implementation Status tab:** identical **PDF/link download tables** (cols: 項目/Item, 下載/Download). **DB-driven** from `Model.FileList` filtered by `GovernanceType.Rules` and `GovernanceType.Training`, ordered newest-first. Each row is either a downloadable file (`~/FileUploads/Document/{FileName}`, download-icon button) or an external link (play-icon). Managed via Admin → ShareholderPolicyAdmin.

### 2.4 Service Locations — `Views/Location/Index.cshtml`
Page title: `服務據點 / Service Location`. Banner reuses `about-banner.jpg`. Content = a **search/filter form + results table**:
- Filter form fields: **地區/Region** dropdown, **縣市/County** dependent dropdown, **地址關鍵字/Address** text input, **搜尋/Search** button.
- Results table (cols: 營業所/Operations Office, 地址/Address (Google Maps link), 電話/Phone (tel: link)). Result count line: "搜尋結果共 N 筆".
- Data source: **`Content/sks/js/location.js`** — a static JS array of **~85 office records**, shape `{ region, county, area, addr, tel, href(Google Maps) }`. Regions: 北部/中部/南部/東部/外島/海外 (North/Central/South/East/Outlying islands/Overseas). Overseas = Shanghai (x2), Xiamen, Bangkok (Thai). Includes geolocation "find nearest" helper.

### 2.5 Legacy static pages — `about/`
- `about/Index.html` — tiny JS redirect shim → `/About/Index`.
- `about/apple.html`, `about/googleprivacystatement.html` — standalone **隱私權政策 / Privacy Policy** HTML pages (Dreamweaver-era, zh-TW only, old `細明體` styling). Likely linked from native-app (APK) store listings (`2sapp.apk` present in repo). `about/pub/` holds one stakeholder PDF: "2022 利害關係人身份、關注議題、溝通管道與回應方式.pdf" (stakeholder identification & engagement). `about/backup/` = historical copies.

---

## 3. Content Types & Data Shapes (inventory)

- **Hero banner carousel** — single-slide Bootstrap carousel, background-image + dark mask + centered H1. Same `about-banner.jpg` reused across About / Company / Location / Group.
- **Tabbed left-rail navigation** — hash-routed `list-group` (desktop) / `nav-tabs` (mobile), with prev/next mobile controllers.
- **Long-form prose blocks** — resource-keyed paragraphs (intro, vision, philosophy, committee charters, director resume).
- **Decorative inline SVGs** — vision diagram (AI/IoT), ISO badges, group logos via `sprite.svg` symbol references.
- **Stat callouts** — big-number reach metrics (6 / 31 / 69).
- **Award card grid** — image + label, DB-driven list.
- **Board / committee roster tables** — multi-column person tables, resource-keyed.
- **PDF/link download tables** — DB-driven file lists (Document folder) with download/play icons.
- **Subsidiary profile card** — key-value table driven by JS `cid` lookup.
- **Location search** — region→county dependent selects + address keyword + geolocation, rendered into a Maps/tel-linked table from a static JS dataset.
- **Static privacy-policy HTML** — flat legacy pages.

---

## 4. Media / Assets

- **Hero image:** `Content/sks/img/about-banner.jpg` (shared by all 4 dynamic pages).
- **Intro imagery:** `about-1-1.jpg`, plus reach photos `About6Image`/`About7Image` (e.g. `about-1-6.jpg` / `about-1-6-en.png`, `about-1-7-04.png` / `-en.png`) — **English-specific image variants exist** (`-en` suffix swaps text-in-image).
- **Icon/logo system:** `Content/sks/img/sprite.svg` (group company logos, location/tel/download/play icons) + a few PNG/JPG logos (harvest.png, sk-store.jpg, shienya.png).
- **Inline hand-coded SVGs:** vision diagram, ISO 9001 / ISO 27001 certificate badges (no external file).
- **Award photos:** `FileUploads/AwardPhoto/` — **~73 image files** (admin-uploaded).
- **Governance documents:** `FileUploads/Document/` — **~639 PDF/files** total (the Rules + Training/Implementation tables draw from this pool, filtered by type).
- **Stakeholder PDF:** `about/pub/2022 利害關係人...pdf`.
- **Location data:** `Content/sks/js/location.js` (~85 offices, with Google Maps URLs).

---

## 5. Bilingual Coverage

| Area | zh-TW | English |
|---|---|---|
| About / Company / Location / Group views | Yes (default) | **Yes** — same views, `lang` route value + `Resources.About.Index` / `Resources.Company.Index` / `Resources.Layout.Resource` culture strings |
| Award titles | comma-split multiline | single-string en branch |
| Group company names | Chinese + English gloss inline (hard-coded, always both) | English gloss shown |
| Intro reach images | `.jpg` | `-en.png` variants |
| Group detail (Group.cshtml) | resource + JS data | en chairperson/title swap for cid=4 |
| Privacy policy (`about/*.html`) | zh-TW only | **No English** |

Resource strings are NOT present as `.resx` in the source tree (compiled into the bin assembly), so exact English copy must be extracted from the running app or the compiled resources — but the bilingual mechanism is confirmed throughout.

---

## 6. Dynamic / Admin-Driven Content

| Content | Source | Admin area |
|---|---|---|
| Awards grid (About) | `Model.AwardList` → `FileUploads/AwardPhoto/` | `Areas/Admin/Views/AwardAdmin` |
| Governance Internal Regulations + Implementation download tables (Company) | `Model.FileList` (`GovernanceType.Rules` / `.Training`) → `FileUploads/Document/` | `Areas/Admin/Views/ShareholderPolicyAdmin` |
| Board roster, committee tables, intro/vision/philosophy prose | Resource strings (translatable, **not** DB) | — (code/resource managed) |
| Group company list & subsidiary profiles | Hard-coded HTML + client-side JS table | — (code managed) |
| Service locations | Static `location.js` array | — (code managed, not admin UI) |

Note: related admin modules for News, Banners, Financial/Shareholder reports exist (`NewsAdmin`, `BannerAdmin`, `FinancialShareholderAdmin`, with `*EnAdmin` English twins) but those feed adjacent sections (News Center / IR) linked from About rather than the About page body itself.

---

## 7. Page Inventory

| Page | Route | Purpose | Content types | Bilingual | Dynamic source |
|---|---|---|---|---|---|
| About SKS | `/About/Index` | Company story, vision, certs, scale, awards, group list | Banner, tabs, prose, SVG diagram, ISO badges, stat block, award grid, group link list | zh + en (+ `-en` images) | Awards = DB (AwardAdmin) |
| Group Detail | `/About/Group?cid=N` | Single subsidiary profile | Banner, key-value table, philosophy text, back link | zh + en | JS `cid` lookup |
| Corporate Governance | `/Company/Index` | Board, committees, internal rules, governance training | Banner, tabs, resume prose, roster tables, committee tables, PDF download tables | zh + en | File lists = DB (ShareholderPolicyAdmin) |
| Service Locations | `/Location/Index` | Searchable office directory | Banner, filter form (region/county/address), Maps+tel results table, geolocation | zh (form labels en-capable) | Static `location.js` (~85 offices) |
| Privacy Policy (legacy) | `/about/apple.html`, `/about/googleprivacystatement.html` | App/privacy policy | Static HTML prose | zh-TW only | Static |
| Redirect shim | `/about/Index.html` | JS redirect → `/About/Index` | — | — | Static |

---

## 8. Redesign Notes for Design AI

- One shared dark-masked hero banner is reused across the whole section — opportunity to differentiate per page or unify intentionally.
- Heavy reliance on hand-coded decorative SVGs (vision AI/IoT diagram, ISO badges) — candidates for a refreshed iconography/illustration system.
- Two recurring functional patterns dominate: (a) **left-rail tabbed long-form page** (About, Company), (b) **filter + data table** (Location, governance downloads). Design both as reusable components.
- Group company list (~18 logos) and award grid (~73 images) are visual-density-heavy grids needing responsive card treatment.
- Bilingual: budget for image text-swaps, comma-split award titles, and longer/shorter copy reflow between zh and en.
