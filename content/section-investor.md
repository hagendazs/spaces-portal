# Section Brief — Investor Relations & Shareholders (投資人關係 / 利害關係人)

> AI-readable content digest for redesign. Source: SKS bilingual ASP.NET MVC corporate portal.
> Files digested:
> - `Views/Shareholder/Index.cshtml` (the entire Investor Relations hub — single page, multi-tab)
> - `Views/Stakeholder/Index.cshtml` (Stakeholder Engagement / 利害關係人溝通)
> - `Areas/Admin/Views/ShareholderPolicyAdmin/Index.cshtml` (admin editor for the dividend policy)
> - Supporting: `Mvc.sitemap` (admin routing), `FileUploads/Document/` (the actual downloadable assets).

---

## 1. Section Purpose

This section is the company's **Investor Relations (投資人關係 / InvestorRelations)** and **Stakeholder Engagement (利害關係人溝通)** hub. It serves two audiences:

- **Investors / shareholders** — corporate profile, governance structure, financial reports, shareholder-meeting materials, dividend policy, regulatory disclosures, an extensive shareholder FAQ, and IR contacts.
- **All stakeholders** (customers, shareholders, suppliers, staff, spokesperson) — a contact directory plus downloadable policy statements (privacy, personal-data, anti–sexual-harassment, occupational safety, ESG).

Architecturally it is **two pages**, not many. The Investor Relations page is one large page driven by left-rail vertical tabs + nested horizontal tabs; the Stakeholder page is a flat contact grid + a download-card grid.

---

## 2. Page Inventory

| Page | Route (view) | Audience | Primary content type | Bilingual | DB-driven |
|------|--------------|----------|----------------------|-----------|-----------|
| Investor Relations hub | `Shareholder/Index` | Investors / shareholders | Tabbed hub: static info tables + dynamic PDF download tables + FAQ accordion + contact blocks | Yes (zh-TW / en) | Partly (financial/meeting/annual/conference tables + dividend policy) |
| Stakeholder Engagement | `Stakeholder/Index` | All stakeholders | Contact directory grid + icon download-card grid | Yes (zh-TW / en) | No (static markup; files via shared resource keys) |
| Dividend Policy admin | `Admin/ShareholderPolicyAdmin/Index` | Internal editors | Dual-language WYSIWYG (CKEditor) form | n/a (edits both langs) | Writes the dividend-policy record |

---

## 3. Investor Relations Hub (`Shareholder/Index.cshtml`)

### Layout shape
- **Banner**: full-width fade carousel (single slide) over `Content/sks/img/about-banner.jpg`, dark mask, centered H2 = "Investor Relations / 投資人關係". (Same banner image is reused by the Stakeholder page.)
- **Left vertical rail** (`list-group`, desktop only `d-lg-block`) = 5 top-level tabs. The right column swaps `tab-pane`s. URL `#hash` is synced (`change-hash` JS), and `?governance` query can deep-link.

### Top-level tabs (left rail)
1. **公司治理 / Company Profile** (`CompanyProfile`)
2. **財務資訊 / Financial Information** (`FinancialInformation`)
3. **股東專欄 / Shareholders Column** (`ShareholdersColumn`)
4. **常見問題 / Q&A** (`Q_A`)
5. **投資人聯絡資訊 / Investor Contact Information** (`InvestorContactInformation`)

### Tab 1 — Company Profile (公司治理)
Nested horizontal sub-tabs:
- **基本資料 / Company Profile** — static `<ul>` key-value list: company info line, Chairperson (董事長), CEO (總經理), Spokesperson (發言人), Deputy Spokesperson (代理發言人), incorporation date (成立日期), listing date (上市日期), paid-in capital (實收資本額), stock agency (股務代理), TEL `(02)2311-8787`, address, website `https://www.skis.com.tw/`.
- **組織架構 / Organizational** — static 2-column table (Department 部門 / Responsibilities 職掌) with ~19 rows: Auditorial Room (審計室), CEO Room (執行長室), Affair / Smart Affair / HealthCare / Strategic / Product / Service / Security / Finance / Analysis / Business-Group-Finance / Management / Planning / Information / Information-Security / Legal-Affairs / HR / Training rooms. (An older SVG org chart `公司基本資料組織架構2024.11.14.svg` is commented out — replaced by the table.)

### Tab 2 — Financial Information (財務資訊)
Nested horizontal sub-tabs, each a **year-filtered PDF download table** (year `<select>` dropdown reveals one `<table>` at a time via jQuery; ROC year shown in zh, +1911 → CE year in en):
- **財務報告 / Financial Statements** (`FinancialStatementsList`)
- **營業收入 / Operating Revenue** (`OperatingRevenue` → `IncomeStatementList`)
- **法人說明會 / Investor Conference** (`InvestorConferenceList`)

Each row is either a **file** (`isFile==1`: title + download icon linking `~/FileUploads/Document/{FileName}`) or a **link** (external URL + play icon). Empty state renders an empty Item/Download table.

### Tab 3 — Shareholders Column (股東專欄)
Seven nested horizontal sub-tabs:
- **股東會 / Shareholders Meeting** (`ShareholderMeetingList`) — year-filtered download table.
- **年報 / Annual Report** (`AnnualStatementsList`) — year-filtered download table.
- **重大訊息 / Material Information** — static list pointing to TWSE MOPS `https://mops.twse.com.tw/mops/web/t146sb05` + a note. (Legacy DB-driven `ShareholderNewsList` news-list code is commented out.)
- **主要股東名單 / List of Major Shareholders** — **static table**, dated `115/03/31`, columns Name / Shares / % — top-10 holders (anonymized as A–J resource keys), e.g. 35,778,823 (9.23%) down to 5,962,329 (1.54%).
- **股務代理 / Stock Agency** — static contact `<ul>` (agency name, TEL, address, website).
- **股利政策 / Stock (Dividend) Policy** — **DB-driven rich HTML** via `Model.ShareholderPolicy.Content` (zh) / `Content_EN` (en), rendered with `@Html.Raw`. This is what the admin editor below maintains.
- **聯絡窗口 / Contact** — static block: Finance Department, TEL `(02)7719-9888#28010`, address, email `kitty@sks.com.tw`.

### Tab 4 — Q&A (常見問題)
Bootstrap **accordion FAQ** with **23 hard-coded Q/A cards** (`Q1/A1` … `Q23/A23`, sourced from resource strings, several with multi-paragraph or sub-bullet answers). Topics center on shareholder/stock procedures; Q22–Q23 link to TDCC e-voting `https://stockservices.tdcc.com.tw/evote/index.html`. Not DB-driven — content lives in resource files.

### Tab 5 — Investor Contact Information (投資人聯絡資訊)
Static block: heading "Investor Relations", Deputy Spokesperson, TEL `(02) 7719-9888#20200`, address, email `arther@sks.com.tw`.

---

## 4. Stakeholder Engagement Page (`Stakeholder/Index.cshtml`)

### Layout shape
- **Banner**: same carousel pattern, H2 = "Stakeholder Engagement / 利害關係人溝通" over `about-banner.jpg`.
- **Section A — Contact directory grid** (`bg-stakeholder`): 5 columns, one per stakeholder group, each with heading + phone/email links:
  - **顧客 / Customers** — `0800-668-850`, `service@sks.com.tw`
  - **股東 / Shareholders** — Finance Dept, `(02)7719-9888#28010`, `kitty@sks.com.tw`
  - **供應商 / Suppliers** — Procurement Dept, `(02)7719-9888#25022`, `zaihua.feng@sks.com.tw`
  - **員工 / Staff** — HR Dept, `(02)7719-9888#20163`, `0264972@sks.com.tw`
  - **發言人 / Spokesperson** — Spokesperson + Deputy, `(02)7719-9888#20200`, `arther@sks.com.tw`
- **Section B — Policy download cards**: centered grid of 6 cards, each = inline **SVG sprite icon** (`sprite.svg#icon-J_05/06/07/12/13/14`) + heading + a "Learn More / 了解更多" pill button. Five open a PDF in `FileUploads/Document/`; the sixth links to the external ESG microsite.
  1. **隱私權保護聲明 / Privacy Policy Statement** → PDF (`PrivacyPolicyFile`)
  2. **個人資料保護 / Personal Data Protection** → PDF (`PersonalProfileFile`)
  3. **性騷擾防治 / Sexual Harassment Prevention** → PDF (`MeasuresonPreventionofSexualHarassmentFile`)
  4. **利害關係人溝通 / Stakeholder Communication** → PDF (`StakeholderFile`)
  5. **職業安全衛生 / Occupational Safety & Health** → PDF (`OccupationalSafetyandHealthFile`)
  6. **ESG 永續網站 / ESG Website** → external `https://esg.sks.com.tw`

Note: zh headings are split on spaces and rendered as stacked H3 lines; en renders the full string. PDF filenames come from shared `Layout.Resource` keys (so the filename itself differs by language — bilingual files exist, e.g. `..._EN.pdf`).

---

## 5. Content Types & Data Shapes (for design system)

- **Banner carousel** — single-slide, full-bleed background image, dark overlay, centered title. Reused across both pages.
- **Vertical tab rail + nested horizontal tabs** — 2-level navigation on the IR hub; deep-linkable via hash; mobile collapses the rail.
- **Year-filtered download tables** — dropdown `<select>` of fiscal years → reveals one table; rows are `{Title, download-icon → PDF}` or `{Title → external link, play-icon}`. Empty state = headers only. (5 instances: financial statements, operating revenue, investor conference, shareholder meeting, annual report.)
- **Static key-value lists** — company profile, stock agency, contact windows.
- **Static data tables** — org-structure (dept/duties) and top-10 major shareholders (name/shares/percent).
- **FAQ accordion** — 23 collapsible cards.
- **Rich-text panel** — dividend policy, raw HTML from DB.
- **Contact directory grid** — 5 stakeholder groups (phone + email).
- **Icon download cards** — 6 SVG-icon cards with pill CTA.
- **Admin form** — dual-pane CKEditor (zh + en) with save.

---

## 6. Media / Assets

- **Banner image**: `Content/sks/img/about-banner.jpg` (~228 KB) — shared by both pages.
- **SVG sprite**: `Content/sks/img/sprite.svg` — 6 icons used on Stakeholder cards (`icon-J_05/06/07/12/13/14`); plus extensive inline `.stN` SVG styling block in IR page `@section head` (legacy org-chart styling, mostly unused now).
- **Download library**: `FileUploads/Document/` contains **~639 files** — **~555 PDFs**, **~76 .doc/.docx**. These back the financial/annual/meeting/conference download tables and the stakeholder policy cards. Filenames are ROC-year prefixed (e.g. `107年度財務報告.pdf`, `106年報.pdf`, `107股東常會議事錄.pdf`) with **~53 English variants** (`*_eng.pdf`, `*_E.pdf`, `*_EN.pdf`). Examples confirmed: dividend/policy & stakeholder docs (`利害關係人溝通與經營(2025).pdf` + `_EN.pdf`, `性騷擾防治措施申訴及懲戒辦法(20230307).pdf` + `_EN.pdf`, `隱私權保護聲明.pdf`, `職業安全衛生...pdf`).
- All file links are cache-busted with `?v={DateTime.Now.Ticks}`.

---

## 7. Bilingual Coverage

- **Full zh-TW + en** support. The view code branches on `Model.Language` / `Resource.LangIso == "en-US"`:
  - Years display as **ROC year** in zh and **+1911 CE year** in en.
  - Dividend policy serves `Content` (zh) vs `Content_EN` (en).
  - Stakeholder PDF filenames swap by language resource (English `_EN` documents exist).
- All UI labels, the 23 FAQ Q/A, profile fields, and headings come from `.resx` resource classes (`sks.Resources.Shareholder.Index`, `sks.Resources.Stakeholder.Index`, `sks.Resources.Layout.Resource`) — so English equivalents exist for every label.

---

## 8. Dynamic / Admin-Driven Content

Driven from the **Admin area** (per `Mvc.sitemap`):

- **`FinancialShareholderAdmin` (zh)** and **`FinancialShareholderEnAdmin` (en)** manage the download tables via a `?type=` code:
  - `type=1` 財務報告 / Financial Reports
  - `type=2` 營業收入 / Operating Revenue
  - `type=3` 年報 / Annual Report
  - `type=4` 股東會 / Shareholders Meeting
  - `type=5` 法說會 / Investor Conference
  - (`type=6–10` belong to adjacent Governance/foundation sections, not this page's tabs.)
  - Each managed item = `{FiscalYear, Title, isFile (file vs external link), FileName / Link}`.
- **`ShareholderPolicyAdmin`** (sitemap title 股利政策 / Dividend Policy) — single record, edited via the dual-pane CKEditor form (`Content` + `Content_EN`), with image upload to `/Admin/ShareholderPolicyAdmin/Upload`. Output renders into the IR hub's "Stock Policy" sub-tab.

**Static (not admin-driven)**: company profile, org-structure table, major-shareholders table (hard-coded, dated 115/03/31), the 23-item FAQ, all contact blocks, and the entire Stakeholder page (its PDFs are swapped by editing resource keys / replacing files, not via a CRUD admin screen).

---

## 9. Redesign Notes / Flags

- The IR hub is **one monolithic ~1,280-line page** doing 5 top tabs × up to 7 sub-tabs — a candidate to split into discrete routed pages or a cleaner tab component.
- **Major-shareholders table and the date `115/03/31` are hard-coded** — no admin screen; stale-data risk.
- The legacy SVG org-chart and `ShareholderNewsList` "Material Information" news list are **commented out** — current org info is a table, and Material Information is now just an external TWSE/MOPS link.
- Heavy reliance on **ROC-year filenames** and `?v=ticks` cache-busting; a redesign should formalize document metadata (year, type, language) rather than infer from filenames.
