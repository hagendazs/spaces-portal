# SKS Corporate Portal — Information Architecture Map

> **Site:** 新光保全 (Shin Kong Security) corporate portal — `https://www.sks.com.tw/`
> **Stack:** ASP.NET MVC 5 (compiled `bin/sks.dll`), bilingual via per-language route prefix (`/` = 中文版, `/en/...` = English), localized through `sks.Resources.*` `.resx` resource files. Nav tree managed by **MvcSiteMapProvider** (`Mvc.sitemap`).
> **Sources analyzed:** `Mvc.sitemap` (Admin nav, bilingual, with visibility ACLs) · `sitemap.xml` (public SEO sitemap) · `robots.txt` · `Views/` (public controllers) · `Areas/Admin/Views/` (back-office controllers).
> **Note on bilingual model:** The *public* site is bilingual by **language route/resource**, not by duplicated controllers — one controller set serves both 中文 and English via `.resx`. The *Admin* back office instead uses **duplicated controllers** (`XxxAdmin` for 中文 content, `XxxEnAdmin` for English content).

---

## 1. Public Site — Top-Level Navigation

The public front-end is driven by MVC controllers (one `Views/<Controller>/` folder = one controller). Each route below is served bilingually (中文 default; English under the `en` language route). Page titles/descriptions come from `sks.Resources.*`.

### 1.1 Public nav tree (中文版 + English)

```
Home (/)  —  首頁
│
├── 智慧家庭  Smart Home                         /SmartHome
│     ├── 社區           Community               /SmartHome/Community
│     ├── 無線           Wireless                /SmartHome/Wireless
│     └── 居家照護       Care                    /SmartHome/Care
│
├── 商用服務  Business                           /Business
│     ├── 辦公室         Office                  /Business/Office
│     ├── POS / 商店     POS                     /Business/POS
│     ├── 資訊安全       InfoSecurity            /Business/InfoSecurity
│     ├── 工作連線       WorkLink                /Business/WorkLink
│     ├── AI 保全        AISecurity              /Business/AISecurity
│     ├── 監控           Monitor                 /Business/Monitor
│     ├── 門禁           Access                  /Business/Access
│     ├── GPS 車隊       GPS                     /Business/GPS
│     ├── 保全           Security                /Business/Security
│     ├── AI 智慧旅店    AIHotel                 /Business/AIHotel
│     └── 生活購物專區   Shop                    /Business/Shop      ⚠ not in sitemap.xml
│
├── 綠能 / 綠建築  Green                          /Green
│     ├── 綠建築         Architecture            /Green/Architecture
│     └── 防災 / 預防    Prevention              /Green/Prevention
│
├── 關於新光保全  About                           /About
│     └── 集團 / 關係企業 Group                   /About/Group        ⚠ not in sitemap.xml
│
├── 新聞中心  News                                /News
│     └── 新聞內頁       Detail                  /News/Detail/{id}
│
├── 投資人關係  Shareholder                       /Shareholder
│
├── 利害關係人  Stakeholder                       /Stakeholder
│
├── 企業社會責任  Corporate Social Responsibility  /CorporateSocial
│     ├── 安心教室       ClassRoom               /CorporateSocial/ClassRoom
│     ├── 文化藝術基金會  CultureArtfoundation    /CorporateSocial/CultureArtfoundation
│     └── 關懷社會福利基金會 WelfareFoundation     /CorporateSocial/WelfareFoundation
│
├── 客戶服務區  User                              /User
│
├── 活動  Event                                   /Event
│     ├── (campaign landing)                     /Event/{...}
│     ├── 預覽            Preview                 /Event/Preview      (admin preview of campaigns)
│     └── 報名 / 聯絡     Access                  /Event/Access       🔒 Disallowed in robots.txt
│
├── 服務據點  Location                            /Location
│
├── 聯絡我們  Contact                             /Contact
│     └── 送出成功       Success                 /Contact/Success
│
├── 站內搜尋  Search                              /Search
│
├── 網站地圖  SiteMap                             /SiteMap
│
└── Robots                                        /Robots             (serves robots.txt view)
```

### 1.2 Public controllers → actions (from `Views/`)

| Controller | Actions (views) | Public path | In sitemap.xml? |
|---|---|---|---|
| **Home** | Index | `/` | ✅ (priority 1.00) |
| **SmartHome** | Index, Community, Wireless, Care | `/SmartHome[/...]` | ✅ |
| **Business** | Index, Office, POS, InfoSecurity, WorkLink, AISecurity, Monitor, Access, GPS, Security, AIHotel, **Shop** | `/Business[/...]` | ✅ (except `Shop`) |
| **Green** | Index, Architecture, Prevention | `/Green[/...]` | ✅ |
| **About** | Index, **Group** | `/About[/...]` | ✅ (except `Group`) |
| **News** | Index, Detail | `/News`, `/News/Detail` | ✅ (`/News`) |
| **Shareholder** | Index | `/Shareholder` | ✅ |
| **Stakeholder** | Index | `/Stakeholder` | ✅ |
| **CorporateSocial** | Index, ClassRoom, CultureArtfoundation, WelfareFoundation | `/CorporateSocial[/...]` | ✅ |
| **User** | Index | `/User` | ✅ |
| **Event** | Index, Preview | `/Event[/...]` | ❌ (campaign pages, dynamic) |
| **Location** | Index | `/Location` | ✅ |
| **Contact** | Index, Success | `/Contact[/...]` | ✅ (`/Contact`) |
| **Search** | Index | `/Search` | ✅ |
| **SiteMap** | Index | `/SiteMap` | ✅ |
| **Robots** | Index | `/Robots` | ✅ |
| **Company** | Index | `/Company` | ⚠ **orphan — not in nav or sitemap.xml** |
| **skpos** | Index | `/skpos` | ⚠ **orphan/parked microsite — not in nav or sitemap.xml** |

### 1.3 SEO sitemap (`sitemap.xml`) coverage

Generated by an external tool (xml-sitemaps.com), all `lastmod = 2020-08-05`. 32 URLs, all priority 0.80 except home (1.00). Notably it **lists `/Robots`** as a crawlable page (priority 0.80) — a generator artifact. It does **not** include `Business/Shop`, `About/Group`, `Company`, `skpos`, or `Event` campaign pages.

### 1.4 `robots.txt`

```
User-Agent: *
Disallow: /FileUploads/     (uploaded media / documents)
Disallow: /Content/         (static assets)
Disallow: /Event/Access     (campaign signup/contact — kept out of index)
Sitemap: https://www.sks.com.tw/sitemap.xml
```

---

## 2. Admin Back Office (`/Admin` area)

Defined authoritatively in `Mvc.sitemap`. The admin menu is **organized by language first** (中文版 / 英文版 branches), then by cross-cutting tools. Every node carries a `visibility` ACL.

- **Visibility roles:** `SystemAdmin` (full back office), `Event` (limited — event editors), `!*` (hide from everyone except listed roles).
- **Bilingual model:** Content controllers are duplicated per language — `NewsAdmin`/`NewsEnAdmin`, `BannerAdmin`/`BannerEnAdmin`, `EventAdmin`/`EventEnAdmin`, `FinancialShareholderAdmin`/`FinancialShareholderEnAdmin`.
- **`type` query param** discriminates a single controller across many logical sections (e.g. `NewsAdmin?type=1..4`, `FinancialShareholderAdmin?type=1..10`).

### 2.1 Admin nav tree (from `Mvc.sitemap`)

```
Home (root, not clickable)
│
├── 中文版  (Chinese content)
│    ├── 首頁輪播圖片      BannerAdmin/Index                              [SystemAdmin]
│    ├── 新聞中心          NewsAdmin/Index?type=1                         [SystemAdmin]
│    ├── 企業社會責任  (group)
│    │    ├── 安心教室
│    │    │     └── 活動          NewsAdmin/Index?type=2                  [SystemAdmin]
│    │    ├── 關懷社會福利基金會
│    │    │     ├── 活動          NewsAdmin/Index?type=3                  [SystemAdmin]
│    │    │     └── 財務          FinancialShareholderAdmin/Index?type=6  [SystemAdmin]
│    │    └── 文化藝術基金會
│    │          ├── 活動          NewsAdmin/Index?type=4                  [SystemAdmin]
│    │          └── 財務          FinancialShareholderAdmin/Index?type=7  [SystemAdmin]
│    ├── 投資人關係  (group)
│    │    ├── 公司治理
│    │    │     ├── 運作情形      FinancialShareholderAdmin/Index?type=8  [SystemAdmin]
│    │    │     ├── 溝通情形      FinancialShareholderAdmin/Index?type=9  [SystemAdmin]
│    │    │     └── 公司內規      FinancialShareholderAdmin/Index?type=10 [no ACL — all roles]
│    │    ├── 財務資訊
│    │    │     ├── 財務報告      FinancialShareholderAdmin/Index?type=1  [SystemAdmin]
│    │    │     ├── 營業收入      FinancialShareholderAdmin/Index?type=2  [SystemAdmin]
│    │    │     └── 年報          FinancialShareholderAdmin/Index?type=3  [SystemAdmin]
│    │    └── 股東專欄
│    │          ├── 股東會        FinancialShareholderAdmin/Index?type=4  [SystemAdmin]
│    │          └── 法說會        FinancialShareholderAdmin/Index?type=5  [SystemAdmin]
│    └── 活動管理          EventAdmin/Index                               [Event, SystemAdmin]*
│
├── 英文版  (English content — mirror of 中文版)
│    ├── 首頁輪播圖片      BannerEnAdmin/Index                            [SystemAdmin]
│    ├── 新聞中心          NewsEnAdmin/Index?type=1                       [SystemAdmin]
│    ├── 企業社會責任  (group)
│    │    ├── 安心教室 → 活動      NewsEnAdmin/Index?type=2               [SystemAdmin]
│    │    ├── 關懷社會福利基金會
│    │    │     ├── 活動          NewsEnAdmin/Index?type=3               [SystemAdmin]
│    │    │     └── 財務          FinancialShareholderEnAdmin/Index?type=6 [SystemAdmin]
│    │    └── 文化藝術基金會
│    │          ├── 活動          NewsEnAdmin/Index?type=4               [SystemAdmin]
│    │          └── 財務          FinancialShareholderEnAdmin/Index?type=7 [SystemAdmin]
│    ├── 投資人關係  (group)
│    │    ├── 公司治理 → 運作情形/溝通情形/公司內規   FinancialShareholderEnAdmin?type=8/9/10 [SystemAdmin]
│    │    ├── 財務資訊 → 財務報告/營業收入/年報        FinancialShareholderEnAdmin?type=1/2/3  [SystemAdmin]
│    │    └── 股東專欄 → 股東會/法說會                 FinancialShareholderEnAdmin?type=4/5    [SystemAdmin]
│    └── 活動管理          EventEnAdmin/Index                            [Event, SystemAdmin]
│
├── 股利政策              ShareholderPolicyAdmin/Index   (不分 / language-neutral) [SystemAdmin]
├── 活動聯絡我們管理      EventContactAdmin/Index        (不分)                    [no ACL]
├── 後台管理員            ManagerAdmin/Index             (不分)                    [SystemAdmin]
├── 登入紀錄              LogsAdmin/Index                (不分)                    [SystemAdmin]
├── 修改密碼              ManagerAdmin/Edit              (不分)                    [Event]
├── 聯絡我們              ContactAdmin/Index             (不分)                    [SystemAdmin]
├── 獎項                  AwardAdmin/Index               (不分)                    [SystemAdmin]
├── ──────────── (divider) ────────────
└── 登出                  AuthAdmin/Logout
```

\* Note: the 中文 "活動管理" node has a malformed visibility value `Event,SystemAdmin!*` (missing comma before `!*`) vs. the English node's correct `Event,SystemAdmin,!*` — a data bug worth flagging in redesign.

### 2.2 Admin controllers → actions (from `Areas/Admin/Views/`)

| Controller | Actions / partial views | Purpose | Lang |
|---|---|---|---|
| **AuthAdmin** | Login (+ Logout action) | Authentication | neutral |
| **HomeAdmin** | Index | Admin dashboard landing | neutral |
| **ManagerAdmin** | Index, Edit | Back-office user accounts / change password | neutral |
| **LogsAdmin** | Index | Login audit log | neutral |
| **BannerAdmin** | Index, Edit | Homepage carousel banners | 中文 |
| **BannerEnAdmin** | Index, Edit | Homepage carousel banners | English |
| **NewsAdmin** | Index, Edit | News + CSR activity posts (type=1..4) | 中文 |
| **NewsEnAdmin** | Index, Edit | News + CSR activity posts (type=1..4) | English |
| **FinancialShareholderAdmin** | Index, Edit | Financial / IR / CSR finance docs (type=1..10) | 中文 |
| **FinancialShareholderEnAdmin** | Index, Edit | Financial / IR / CSR finance docs (type=1..10) | English |
| **EventAdmin** | Index, Edit, _EventContactChangeFieldNames, _EventInformation, _EventInformationItems | Campaign/event builder + dynamic form fields | 中文 |
| **EventEnAdmin** | Index, Edit, _EventENContactChangeFieldNames, _EventENInformation, _EventENInformationItems | Campaign/event builder + dynamic form fields | English |
| **EventContactAdmin** | Index, Edit | Event signup/contact submissions | neutral |
| **ShareholderPolicyAdmin** | Index | Dividend policy content | neutral |
| **ContactAdmin** | Index, Edit | "Contact us" submissions / settings | neutral |
| **AwardAdmin** | Index, Edit | Company awards | neutral |
| **ProductAdmin** | Index, Edit | Product catalog items | neutral ⚠ |
| **ProductCategoryAdmin** | Index, Edit | Product categories | neutral ⚠ |
| **QAAdmin** | Index | FAQ / Q&A management | neutral ⚠ |

⚠ **`ProductAdmin`, `ProductCategoryAdmin`, `QAAdmin` views exist but have NO node in `Mvc.sitemap`** — orphan/parked admin modules (controllers still reachable by direct URL but absent from the menu; likely legacy product-shop / FAQ features, matching the parked public `Business/Shop` page).

### 2.3 Admin shared infrastructure (`Areas/Admin/Views/Shared/`)

`_Layout.cshtml`, `_PageAdmin.cshtml`, `_PageSizeAdmin.cshtml` (pagination), `_SidebarMenu.cshtml` (renders the `Mvc.sitemap` tree), `_PromptNotification.cshtml`, `Error.cshtml`, `NotFound.cshtml`, plus `DisplayTemplates/` & `EditorTemplates/`.

---

## 3. Bilingual Structure Summary

| Layer | Bilingual mechanism |
|---|---|
| **Public site** | Single controller set; language by route prefix (`/` 中文, `/en/...` English) + `sks.Resources.*` `.resx`. Page `<title>`/meta pulled from resources. |
| **Admin content** | Duplicated controllers: `*Admin` (中文) vs `*EnAdmin` (English). Menu split into top-level 中文版 / 英文版 branches that mirror each other 1:1. |
| **Language-neutral admin** | Auth, Manager, Logs, Award, Contact, ShareholderPolicy, EventContact, Product*, QA — single controller, marked `不分` ("not divided") in the sitemap. |

---

## 4. Orphan / Parked / Anomalous Pages

| Item | Type | Status |
|---|---|---|
| `Views/Company/Index` (`/Company`) | Public page | Orphan — not in nav nor `sitemap.xml`. Uses `ShareholderView` model (likely a parked/duplicate IR page). |
| `Views/skpos/Index` (`/skpos`) | Public microsite | Parked — standalone full-bleed landing (custom `body` styles), no nav/sitemap entry. |
| `Views/Business/Shop` (`/Business/Shop`) | Public page | "生活購物專區" shopping zone — built (uses `ProductIndexView`) but excluded from `sitemap.xml`; pairs with the parked Product admin modules. |
| `Views/About/Group` (`/About/Group`) | Public page | Built but not in `sitemap.xml`; reachable sub-page of About. |
| `Areas/Admin/.../ProductAdmin`, `ProductCategoryAdmin`, `QAAdmin` | Admin modules | Orphan — no `Mvc.sitemap` node; legacy product-catalog & FAQ back office. |
| `sitemap.xml` `/Robots` entry | SEO artifact | The `Robots` controller page is wrongly listed as a content URL. |
| `sitemap.xml` `lastmod` all `2020-08-05` | SEO staleness | Sitemap appears generated once and never regenerated. |
| 中文 `活動管理` `visibility="Event,SystemAdmin!*"` | Data bug | Malformed ACL (missing comma) vs. English node's correct form. |
| Legacy static dirs: `/about` (HTML), `/catalog` (PDFs), `/reservation` (PHP+HTML), `/ebook`, `/apache` | Non-MVC content | Static/legacy assets co-deployed under web root, outside the MVC IA. |

---

## 5. Quick Reference — Public Top-Level Sections (for redesign IA)

1. **Home** `/`
2. **Smart Home** `/SmartHome` — Community · Wireless · Care
3. **Business** `/Business` — Office · POS · InfoSecurity · WorkLink · AISecurity · Monitor · Access · GPS · Security · AIHotel · (Shop)
4. **Green** `/Green` — Architecture · Prevention
5. **About** `/About` — Group
6. **News** `/News`
7. **Shareholder / Investor Relations** `/Shareholder`
8. **Stakeholder** `/Stakeholder`
9. **CSR** `/CorporateSocial` — ClassRoom · CultureArtfoundation · WelfareFoundation
10. **Customer Service** `/User`
11. **Events** `/Event`
12. **Location** `/Location`
13. **Contact** `/Contact`
14. **Search** `/Search`
15. **SiteMap** `/SiteMap`
