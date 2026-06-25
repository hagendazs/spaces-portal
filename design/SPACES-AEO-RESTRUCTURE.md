# SKS SPACES — AEO Restructure + Score

> **What this is:** restructures the SPACES site around the **new** content set and
> quantifies its AEO (AI Engine Optimization) readiness.
> **Goal (verbatim, deck slide 1):** AEO — *讓 AI「直接引用你的答案」*.
> **Date:** 2026-06-25 · **Supersedes content-mapping in** `SPACES-SITE-PLAN.md` (built off the older 0506 deck).
>
> **Sources ingested:**
> - `SPACES數位網站內容-0616.pptx` — 52 slides; locks the **5-page nav** + all page copy, FAQ bank, product specs.
> - `SPACES_場景應用_中間語氣文案_v1.docx` — 6 scenario stories (金融/零售/倉儲/辦公/社區/住家), each with a one-line hook + spec→benefit table.
> - `SPACES_RX_五大場景應用介紹.docx` — same 6 scenarios with 痛點 / 產品配置 / 系統效益 + product-line glossary.
>
> **Scope note:** the site today is a `.pen` design + markdown content, not a deployed HTML site. So the AEO score below measures **content/structure readiness** (what's in our control now), and flags which points are gated on the eventual build/deploy.

---

## 1. AEO Score — the quantification

**Method:** 8 weighted dimensions, each scored 0–100, weighted to a single AEO Index. Two states scored: the raw source set as-is vs. the proposed restructure.

| # | Dimension (what AI engines reward) | Weight | Now (raw sources) | After restructure | Why the gap |
|---|---|---:|---:|---:|---|
| 1 | **Crawlability & rendering** — static/SSR HTML, robots allows AI bots, `llms.txt`, sitemap | 15 | 10 | 85 | Now: `.pen`/slides, no live SSR, portal `robots.txt` blocks `/Content` & names no AI bots. After: static build + allow `GPTBot/ClaudeBot/PerplexityBot/Google-Extended`, add `llms.txt`. |
| 2 | **Structured data (JSON-LD)** — `FAQPage`, `Product`, `HowTo`, `Organization`, `BreadcrumbList` | 15 | 5 | 90 | Now: none. After: every Q&A → `FAQPage`; products → `Product`; 安裝 → `HowTo`; 新光保全 → `Organization`. |
| 3 | **Answer-first extractable chunks** | 15 | 55 | 90 | The 「一句話」 hooks + Q&A are already answer-first — restructure leads every section with the liftable sentence. |
| 4 | **Question-shaped headings (real-query match)** | 12 | 60 | 88 | FAQ slides 35–49 + 痛點 are real questions; restructure makes them the `<h2>` text verbatim. |
| 5 | **Factual density & specificity** | 15 | 80 | 88 | **The content's biggest AEO asset** — 30天, LTE, IP66, UPS 4–8H, 2K/3K, 8-Port, 個資法. Keep, tag, don't dilute. |
| 6 | **Entity & authority (E-E-A-T)** | 12 | 35 | 70 | 新光保全 brand + 0800/email/address strong; missing author/dates, off-site mentions, EN parity. Ceiling here is gated on **off-site** work. |
| 7 | **Semantic HTML & modularity** | 10 | 30 | 88 | spec→benefit + product-config tables are ideal `<table>`/list material; render as real semantic markup. |
| 8 | **Measurement & bilingual reach** | 6 | 15 | 55 | AI-referral tracking + QR analytics (slide 1) planned; EN parity staged, not day-1. |
| | **AEO Index (weighted)** | **100** | **≈ 38 / 100** | **≈ 84 / 100** | |

**Read:** the restructure roughly **doubles AEO readiness (38 → 84)**. The remaining 16 pts are *not* content problems — they're gated on the actual deploy (SSR), **off-site presence** (Wikipedia/news/industry mentions), full **English parity**, and **live measurement**. Don't expect content edits alone to close them.

### Highest-leverage moves (ranked by Δ-points ÷ effort)
1. **Render Q&A + scenarios as static HTML with `FAQPage`/`Product` JSON-LD** → dimensions 1+2+7, ~+30 weighted pts. Single biggest lever.
2. **Lead every block with its liftable sentence** (reuse the existing 「一句話」) → dim 3, low effort.
3. **Make every 痛點/FAQ an `<h2>` phrased as the user's actual question** → dim 4.
4. **Allow AI crawlers + ship `llms.txt`** pointing at the scenario & FAQ pages → dim 1, ~1 hour.
5. **Add `Organization` schema + per-page `dateModified` + author/「新光保全」 attribution** → dim 6.

---

## 2. Restructured IA — 5 pages (deck slide 2, AEO-shaped)

```
首頁  |  解決方案／場景  |  產品介紹  |  常見問題 FAQ  |  媒體報導      [ 預約免費諮詢 ]   中／En
```

Each page below notes its **AEO job** + the **schema** it carries.

| Page | Source | AEO job | Schema |
|---|---|---|---|
| **首頁 Home** | slides 3–16 | Define the entity ("SPACES 是什麼") in one liftable paragraph + link hub to all answer pages | `Organization`, `WebSite` (+ `SearchAction`) |
| **解決方案／場景** | both DOCX + slides 14–25 | The citation engine — 6 self-contained scenario answers, each a 痛點→配置→效益 chunk | `FAQPage` per 痛點, `BreadcrumbList` |
| **產品介紹** | slides 28–33 | Spec-table pages AI quotes for "which camera / does it support LTE" | `Product` per device, `ItemList` |
| **常見問題 FAQ** | slides 35–49 | Highest-yield AEO surface — direct Q→A pairs | `FAQPage` (every page) |
| **媒體報導 Media** | slides 51 | Third-party authority signal; links to news | `NewsArticle` / `Article` |

---

## 3. The scenario module (Solutions page) — AEO pattern

Six scenarios, **one URL each** (`/solutions/finance`, `/retail`, `/warehouse`, `/office`, `/community`, `/home`). Each page is built from the two DOCX in this fixed, extractable order:

```
H1:  <場景> — <中間語氣 標題>           e.g. 金融安防 — 金庫的門關上之後，守護才正要開始
[answer-first ¶]  the 中間語氣 paragraph (白話 + 場景結果)
H2:  這個場景的風險是什麼？             ← 痛點 (RX docx), phrased as a question
H2:  SPACES 怎麼解？                    ← 產品配置 table (產品 | 部署位置與用途)
<table>  spec → 對你的場域代表什麼      ← 中間語氣 spec/benefit table (the fact-dense gold)
H2:  導入後的效益                       ← 系統效益 bullets
[pull-quote]  「一句話」                ← the liftable one-liner, also the meta description
```

Why this wins AEO: every page answers one entity-rich question, leads with the quotable sentence, backs it with a numeric table, and is self-contained (no "如上所述"). The 「一句話」 doubles as `<meta name="description">` and the JSON-LD answer text.

**Honesty guardrail (from the DOCX 使用備註 — keep it):** ship only deliverable facts (`30天`,`LTE`,`個資法友善`). Never rewrite to unbounded claims (`永不斷線`/`永不遺失`). AI engines down-rank — and users distrust — overclaiming. **AI 語意理解 stays in a 「未來藍圖」 block, not stated as current capability.**

---

## 4. FAQ page — the highest-yield AEO surface

Slides 35–49 already give ~40 real Q&A pairs across 安裝/韌性/APP/硬體/餐飲/咖啡/倉儲/辦公/商場/金融. Restructure:

- One H2 per question, **verbatim user phrasing** ("斷網後設備還能繼續運作嗎？").
- Direct answer in the **first sentence**, detail after.
- Group by category but keep each Q self-contained.
- Wrap the whole page in `FAQPage` JSON-LD (this is what lands you in AI Overviews / ChatGPT citations).
- Industry Q&A (餐飲/咖啡/etc.) cross-links to the matching scenario page.

---

## 5. Build-time AEO checklist (gates the remaining 16 pts)

- [ ] Static/SSR render — content present in raw HTML, not JS-only.
- [ ] `robots.txt`: allow `GPTBot`, `OAI-SearchBot`, `ClaudeBot`, `PerplexityBot`, `Google-Extended`, `Bingbot`.
- [ ] `llms.txt` at root → links to Solutions + FAQ pages.
- [ ] JSON-LD: `Organization` (sitewide) · `FAQPage` (FAQ + each scenario) · `Product` (each device) · `HowTo` (安裝) · `BreadcrumbList`.
- [ ] Every page: `<title>`, `<meta description>` (= the 「一句話」), `dateModified`, 新光保全 attribution.
- [ ] Semantic markup: spec/benefit tables as `<table>`, 效益 as `<ul>`, questions as `<h2>`.
- [ ] `sitemap.xml` with real `lastmod`.
- [ ] Measurement: tag referrals from `chat.openai.com`/`perplexity.ai`/`gemini.google.com`; reuse slide-1 QR tracking.
- [ ] (Staged) English parity for the 6 scenario + FAQ pages — biggest lever on dim 8.
```
