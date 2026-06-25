# SKS SPACES — AEO-Optimized Site

Static marketing site for **新光保全 SKS SPACES** (次世代韌性保全 / 智慧空間管理平台), built for **AEO (AI Engine Optimization)** — getting cited by AI answer engines (ChatGPT, Perplexity, Google AI Overviews, Gemini), not just ranked in blue links.

Visual style follows the bolt reference (light theme, blue `#1B5CC8` / accent `#0EA5E9`); information architecture and content are our AEO-first structure.

**Live preview:** https://hagendazs.github.io/spaces-portal/ (auto-deployed from `main` via `.github/workflows/pages.yml`).
**Canonical / intended production domain:** `https://spaces.sks.com.tw/` — all `<link rel="canonical">`, OG tags, JSON-LD `url`s, and `sitemap.xml` point here on purpose, so AI citations resolve to the real domain once DNS is pointed. Until then the preview URL above renders identically (paths are relative).

## Run locally

```bash
cd site
python3 -m http.server 8123
# open http://127.0.0.1:8123/
```

## Structure

```
site/
├── index.html              # home — all sections + 5 JSON-LD blocks
├── solutions/finance.html  # per-segment master page (BreadcrumbList + Service + FAQPage)
├── jsonld.json             # standalone copy of the structured-data payload
├── robots.txt              # allows AI crawlers
├── llms.txt                # key-facts digest for LLMs
├── sitemap.xml
└── images/                 # hero, segment, device & resilience mockups (generated in Pencil)
```

## What was done for AEO

### 1. Machine-readable, content in the DOM
- **Static HTML** — every word is in the raw response (verified with JS disabled). The bolt reference is a client-rendered SPA with an empty `#root` and zero structured data; this rebuild fixes exactly that.
- **Semantic markup** — real `<h1>/<h2>/<h3>`, `<article>`, `<table>`, `<ul>`, `<details>`; spec tables as `<table>`.
- Sliders use **native CSS scroll-snap** (no JS) so all slides stay crawlable.

### 2. Structured data (JSON-LD) — what AI engines parse to cite us
- **Home:** `Organization`, `WebSite`, `ItemList`/`Product` (hardware), `HowTo` (install), `FAQPage` (11 Q&A).
- **Finance page:** `BreadcrumbList`, `Service`, `FAQPage` (7 Q&A).
- Mirrored in `jsonld.json` for reuse.

### 3. Answer-first, question-shaped content
- Each section leads with the liftable sentence (the 「一句話」 also used as `<meta description>`).
- FAQ headings use real user phrasing, one self-contained Q per `<h2>`, direct answer in the first sentence.

### 4. Spec → customer value-prop (per segment)
- Every 場域 card / table reads **「規格 — 對你的場域代表什麼」**: the fact translated into the buyer's outcome (e.g. 主機支援 LTE → 沒有固網的偏遠倉，插電就能保全). Fact-dense *and* resonant — what engines lift into answers.

### 5. Funnel strategy — capture problem-aware users
- Target **problem-phrased, jargon-free** queries (e.g. 「分行晚上沒人，怎麼知道有人闖進金庫？」) — *upstream* of category keywords like 保全/監視器, where AI engines actually get consulted. The finance FAQ leads with these situational questions.

### 6. Per-segment pages = one citable URL each
- `solutions/finance.html` is the master template (hero → 痛點/疑慮 → 配置 → spec→value → 效益 → problem-aware FAQ). Replicate for the other 5 verticals to multiply citation surface.

### 7. Honesty guardrails (kept on purpose)
- Only deliverable facts (`30天`, `LTE`, `IP66`, `UPS 4–8H`, `個資法`). AI capabilities framed as 未來藍圖, never unbounded claims (`永不斷線`). Overclaiming gets down-ranked and distrusted.

### 8. Crawler access & discovery
- `robots.txt` explicitly allows `GPTBot`, `OAI-SearchBot`, `ChatGPT-User`, `ClaudeBot`, `PerplexityBot`, `Google-Extended`, `Bingbot`.
- `llms.txt` digest + `sitemap.xml`; per-page `<title>`, `<meta description>`, canonical, OG tags, 新光保全 attribution.

### 9. Visual / UX (supports engagement, not AEO directly)
- Light responsive theme, mobile hamburger (no-JS `<details>`), card hover-lift + image zoom (reduced-motion safe), tables scroll on mobile, 場域 badges on segment imagery, device mockups in the products slider.

## AEO score

Readiness (on-page structure): **≈ 38 → 84 / 100** (see `../design/SPACES-AEO-RESTRUCTURE.md` for the weighted scorecard). Remaining gap is **off-page only**: real deployed domain (SSR), third-party mentions (Wikipedia/news/industry), full English parity, and live measurement.

## Measuring AEO *performance* (vs the readiness score)

Ask the engines, don't just score the page:
- **Answer Presence Rate** — % of a fixed ~40 problem-prompt panel where SPACES is mentioned/cited, run weekly across ChatGPT / Perplexity / Gemini / AI Overviews.
- **Citation Share of Voice** — of vendor-naming answers, % naming SPACES.
- **Funnel split** — problem-query coverage (top) tracked separately from category-query coverage.
- **AI-referral conversions** — sessions from `chat.openai.com` / `perplexity.ai` / `gemini.google.com` reaching the consult form.

## Next

- Replicate the finance master page for 零售 / 倉儲 / 辦公 / 社區 / 住家.
- Add English parity (`/en/…` + `hreflang`).
- Wire the device mockups into the Pencil Products section.
- Stand up the 40-prompt measurement panel.
