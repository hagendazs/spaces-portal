# Section Brief — eBooks & Catalog Library (電子書與型錄庫)

> SKS (新光保全 / Shin Kong Security Co., Ltd.) bilingual corporate portal.
> Source repo: `/Users/reiser/repo/sks`. This brief digests the CONTENT of the
> eBook library (`ebook/`) and the Catalog/document library (`catalog/`) so a
> downstream design AI can redesign the section.

---

## 1. Purpose of the Section

This is the company's **publications & downloadable-document library**. It is NOT a
single CMS-driven page — it is two clusters of mostly static assets that the main
ASP.NET MVC portal links out to:

1. **eBook library (`ebook/`)** — the company quarterly magazine,
   **新保季刊 (Shin Kong Security Quarterly)**, published as page-flip "FlipBook"
   microsites. One numbered folder per issue, issues **166 → 186** (21 issues).
   Each issue is a self-contained Flash/HTML flipbook of a scanned print magazine.

2. **Catalog & document library (`catalog/`)** — flat PDF downloads: a product
   catalog, a fire-system brochure, sales case studies, and a corporate
   diversity-policy document. These are linked as "Learn More / 了解更多" CTAs from
   the **Green / 防災 (Prevention)** and **Architecture / 商業平台 (Commercial
   Platform)** marketing pages.

There is **no dedicated index/listing page** for either cluster inside the MVC
Views. eBooks are reached by direct URL per issue; catalog PDFs are reached from
inline buttons on content pages. A redesign opportunity: build a proper
**Publications hub** (magazine archive grid + document download center).

---

## 2. eBook Library — `ebook/` (新保季刊 Quarterly Magazine)

### 2.1 What each issue is
Every issue folder (e.g. `ebook/186/`) is a **Flip PDF Professional** export
(generator: *Flip PDF Professional 2.4.x at flipbuilder.com*). It renders a scanned
print magazine as an interactive page-turning book. Content is **image-based**
(scanned pages) — there is no extractable body text; page copy lives inside JPG
page scans, not HTML. Titles are in Traditional Chinese.

### 2.2 Per-issue file/data shape
| Item | Detail |
|------|--------|
| `index.html` | Entry point. `<title>` = e.g. `新保季刊186期電子書` ("Shin Kong Security Quarterly Issue 186 eBook"). Embeds Flash `book.swf` via SWFObject; OpenGraph tags for social share (`og:title`, `og:image=files/shot.png`, `og:video=book.swf`). |
| `book.swf` / `bookContent.swf` | Flash flipbook player + content payload (~800KB–950KB). Legacy — Flash is end-of-life; must be replaced in redesign. |
| `files/page/*.swf` | Per-page Flash assets (≈32 pages/issue). |
| `files/thumb/*.jpg` | Per-page thumbnail images (≈32/issue) — usable as a modern image gallery source. |
| `files/basic-html/` | **Non-Flash fallback**: `index.html` + `page1.html … page32.html`. Each page shows one scanned-page thumbnail + prev/next nav. Title pattern: `Page N - 新保季刊186期電子書`. This is the only HTML-readable layer, but body text is still embedded in images. |
| `files/config.xml` | **Encrypted** flipbook config (base64 blob) — book metadata, page map, bookmarks. Not human-readable. |
| `mobile/` | Mobile flipbook variant (`index.html` + `javascript/` + `style/`). |
| `js/` | `swfobject.js`, `fbscript.js`, `fbendscript.js`, `ActionHtmlWindow.js` — flipbook runtime. |
| `shot.png` | Cover/share thumbnail (present on newer issues 180+; absent on oldest e.g. 166). |

### 2.3 Issue inventory & coverage
- **21 issues**: folders `166`–`186` (sequential, no gaps).
- Title variants seen (inconsistent naming, worth normalizing in redesign):
  - `新保季刊166期全`, `新保季刊167期_電子書`, `新保季刊179期電子書`,
    `新保季刊180期季刊電子書原始檔` (note: "原始檔/source file" leaked into title),
    `新保季刊184期`, `新光保全季刊185期`, `新保季刊186期電子書`.
- Cadence: quarterly. Folder mtimes span 2024→2026, newest issue **186** (May 2025).
- Roughly **~32 pages per issue**, so the library holds on the order of
  **~670 scanned magazine pages + ~670 thumbnails** across all issues.

### 2.4 Content themes (magazine)
The 新保季刊 quarterly is the company's brand/CSR magazine — corporate news,
security & smart-home product features, customer stories, and corporate-social /
disaster-prevention messaging (consistent with the rest of the portal). Exact
article copy is locked inside page-scan images and not text-extractable here.

### 2.5 `ebook/temp/` — NOT eBook content (flag)
`ebook/temp/` (29 files) is **operational/security junk**, not publication content:
firewall block lists (`blockiplist.txt*`, `blockdomainlist.txt*`, `blockurl.txt`),
TLS certs/keys (`*.pfx`, `*.pem`, `*combo*.zip`), an installer (`ReaqtaHive.msi`),
and misc `.txt`. **Exclude from the redesigned section; treat as cleanup / possibly
sensitive (contains private key material).**

---

## 3. Catalog & Document Library — `catalog/`

Flat folder of **PDF downloads** (no listing UI). Themes split into product
collateral, fire/disaster collateral, sales case studies, and a corporate policy.

### 3.1 Inventory
| File | English gloss | Type / theme | Linked from |
|------|---------------|--------------|-------------|
| `SKSFireSystem.pdf` (~1.1 MB) | SKS Fire System brochure | Product/fire-prevention brochure | `Views/Green/Prevention.cshtml` → "Learn More" button to `https://www.sks.com.tw/catalog/SKSFireSystem.pdf` |
| `pms2022/新光保全_物業管理平台型錄.pdf` (~1.6 MB) | "Shin Kong Security — Property Management Platform Catalog" (物業管理平台型錄) | Product catalog (型錄) | `Views/Green/Architecture.cshtml` → "Learn More" button (URL-encoded path) |
| `多元化政策與落實情形.pdf` (~5.3 MB) | "Diversity Policy & Implementation Status" | Corporate ESG/governance policy document | Not referenced in scanned Views (likely linked from a CSR/governance page or admin-driven) |
| `案例1連鎖大賣場.pdf` (~211 KB) | "Case 1 — Chain Hypermarket" | Sales case study | (direct link / sales collateral) |
| `案例2 知名五金百貨案例分享.pdf` (~468 KB) | "Case 2 — Well-known Hardware Dept Store case sharing" | Sales case study | — |
| `案例2話術.pdf` (~464 KB) | "Case 2 — Sales Script (話術)" | Internal sales talk-track | — (likely internal, not public) |
| `案例3 知名國際精品案例分享.pdf` (~135 KB) | "Case 3 — Well-known international luxury brand case sharing" | Sales case study | — |
| `案例3國際精品.pdf` (~131 KB) | "Case 3 — International luxury" | Sales case study (variant) | — |

8 PDFs total (incl. the one inside `pms2022/`).

### 3.2 Content themes (catalog)
- **Product/solution collateral:** fire-prevention system + property-management
  platform (物業管理平台) — supports the security/smart-building product story.
- **Case studies (案例):** retail/hypermarket, hardware department store, and
  international luxury-brand deployments — social-proof sales material.
- **Governance:** diversity policy & implementation (ESG/CSR disclosure).
- Note `案例2話術` ("sales script") and the duplicate case-study variants look like
  **internal sales enablement**, not customer-facing — review before publishing.

---

## 4. Content Types & Data Shapes (for design)

- **eBook = page-flip viewer microsite** per issue (Flash + HTML/JS fallback +
  mobile variant). Data: ordered set of ~32 page-images + thumbnails + cover +
  encrypted config metadata (title, page count, bookmarks).
- **Magazine archive = grid/list of issues**, each: cover image (`shot.png`),
  issue number, title, (implied) publish quarter, link to reader. *This listing
  does not yet exist and should be created.*
- **Catalog = PDF download list/cards**: filename → human title (CN+EN) → size →
  category (Product / Case Study / Policy) → external "Learn More" CTA.
- **Inline CTA pattern (existing):** marketing page paragraph + button
  `btn btn-outline-pill-dark` labeled `@sks.Resources.Layout.Resource.LearnMore`
  (localized "Learn More / 了解更多") linking `target="_blank"` to a catalog PDF.

---

## 5. Media / Assets

| Asset class | Where | Approx. count |
|-------------|-------|---------------|
| Flash flipbook players (`book.swf`, `bookContent.swf`, `page/*.swf`) | each `ebook/<n>/` | ~33 SWF × 21 issues |
| Scanned-page thumbnails (`thumb/*.jpg`) | each `ebook/<n>/files/thumb/` | ~32 × 21 ≈ **670 images** |
| Basic-HTML fallback pages | each `ebook/<n>/files/basic-html/` | ~32 × 21 ≈ **670 HTML files** |
| Cover/share images (`shot.png`) | issues ~180–186 | ~7+ |
| Catalog PDFs | `catalog/` (+ `pms2022/`) | **8 PDFs** (~9.6 MB total) |
| Runtime JS | each `ebook/<n>/js/` | 3–4 files/issue |

**Redesign implication:** all Flash (`.swf`) is dead technology. The redesigned
reader must rebuild on a modern PDF/image viewer (e.g. PDF.js or an image-sequence
flipbook). The `thumb/*.jpg` sequence is the salvageable content source per issue.

---

## 6. Bilingual Coverage

- **eBooks: Chinese only.** All issue titles are Traditional Chinese; the
  basic-HTML chrome has stray English strings ("Basic HTML Version",
  "View Full Version", "Table of Contents") from the FlipPDF template, but the
  **magazine content itself is Chinese-only**. No `EnAdmin`-style English eBook set.
- **Catalog: Chinese only.** All PDFs are Traditional-Chinese filenames/content
  (fire system, 型錄, 案例, 多元化政策). No English catalog variants found.
- The portal as a whole IS bilingual (separate `…EnAdmin` admin areas,
  `sks.Resources.Layout.Resource.LearnMore` localized button), but **this section's
  payloads are not localized** → English-version gap to address in redesign.

---

## 7. Dynamic / Admin-Driven Content

- **Mostly NOT admin-driven / DB-rendered.** The eBook flipbooks and catalog PDFs
  are **static files on disk**, served by direct URL.
- The MVC `Areas/Admin` has admins for News, Events, Products, Banners, Awards, QA,
  Financials, Managers, etc. — but **there is no `EBookAdmin`, `MagazineAdmin`,
  `CatalogAdmin`, or `PublicationAdmin`**. So issues/PDFs are added manually by
  dropping folders/files, not through the CMS.
- Catalog links are **hard-coded** into `Views/Green/Prevention.cshtml` and
  `Views/Green/Architecture.cshtml` (absolute `https://www.sks.com.tw/catalog/...`
  URLs), not resolved from a database.
- **Redesign opportunity:** introduce an admin-managed Publications model (issue
  number, title CN/EN, cover, quarter, PDF source) so the magazine archive and
  document center render dynamically instead of via hand-edited static folders.

---

## 8. Page Inventory (table)

| Page / Asset | Path | Purpose | Content type | Bilingual | Admin-driven |
|---|---|---|---|---|---|
| Quarterly eBook — Issue 166…186 (×21) | `ebook/<166-186>/index.html` | Page-flip reader for one magazine issue | Flash flipbook microsite (SWF) + thumbnails | CN only | No (static folder) |
| eBook basic-HTML fallback | `ebook/<n>/files/basic-html/page1..32.html` | Non-Flash per-page viewer | Image-per-page HTML + prev/next nav | CN (template chrome EN) | No |
| eBook mobile variant | `ebook/<n>/mobile/index.html` | Mobile flipbook reader | JS/HTML viewer | CN | No |
| eBook config | `ebook/<n>/files/config.xml` | Book metadata (encrypted) | Encrypted base64 blob | — | No |
| Magazine archive index | *(does not exist)* | List/grid of all issues | *(to be built)* | — | *(should be)* |
| `ebook/temp/` | `ebook/temp/*` | Ops/security junk (block lists, certs, MSI) | NOT publication content | — | No — **exclude/clean up (contains keys)** |
| Fire System brochure | `catalog/SKSFireSystem.pdf` | Fire-prevention product brochure | PDF download | CN only | No — linked from Prevention.cshtml |
| Property-Mgmt Platform catalog | `catalog/pms2022/新光保全_物業管理平台型錄.pdf` | Product 型錄 | PDF download | CN only | No — linked from Architecture.cshtml |
| Diversity Policy | `catalog/多元化政策與落實情形.pdf` | ESG/governance disclosure | PDF download | CN only | No |
| Case Study 1 — Hypermarket | `catalog/案例1連鎖大賣場.pdf` | Sales case study | PDF download | CN only | No |
| Case Study 2 — Hardware Dept Store | `catalog/案例2 知名五金百貨案例分享.pdf` | Sales case study | PDF download | CN only | No |
| Case 2 — Sales Script | `catalog/案例2話術.pdf` | Internal sales talk-track | PDF (likely internal) | CN only | No — **review before publishing** |
| Case Study 3 — Intl Luxury | `catalog/案例3 知名國際精品案例分享.pdf` / `案例3國際精品.pdf` | Sales case study (2 variants) | PDF download | CN only | No |

---

## 9. Redesign Notes / Flags

- **Kill Flash:** rebuild all flipbooks on PDF.js / modern image viewer; `*.swf`
  cannot run in current browsers. Salvage content from `thumb/*.jpg` sequences.
- **Build the missing hub:** a Publications page with (a) a magazine archive grid
  (cover + issue # + quarter) and (b) a categorized document download center.
- **Normalize metadata:** issue titles are inconsistent (one literally says
  "原始檔/source file"); store clean title + quarter per issue.
- **Bilingual gap:** no English eBook/catalog content despite a bilingual portal.
- **Make it CMS-managed:** no Admin model exists today; adding one would remove the
  hand-edited static-folder workflow.
- **Security/cleanup:** `ebook/temp/` holds firewall lists and **private TLS key
  material (`*.pfx`, `*.pem`)** mixed into a web-served path — exclude and remediate.
- **Internal docs:** `案例2話術` (sales script) and duplicate case variants look
  internal — confirm publish-status before exposing.
