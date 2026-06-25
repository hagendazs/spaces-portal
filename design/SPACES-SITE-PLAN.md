# SKS SPACES — Product Site Plan (Pencil update)

> **What this is:** plan to update `sks-redesign.pen` into the **SKS SPACES** product-centric
> website, per the planning deck (`SPACES數位網站內容-0506.pptx`, 37 slides) and the
> Reiser / 廖育琴 feedback.
> **Date:** 2026-06-05 (updated 2026-06-08 — absorbed 品牌定位/行銷轉單/差異化 design conclusions) · **Companion:** `REDESIGN-BRIEF.md`, `DESIGN-TOKENS.md`, `COMPONENT-INVENTORY.md`
>
> **Locked decisions:**
> 1. **Same file** — add SPACES top-level page frames inside `sks-redesign.pen`; reuse existing variables/components.
> 2. **Consult-to-quote** — no fixed price cards; every page funnels to 預約免費諮詢. (Removes the NT$599/999/1,599 tiers from the current home.)
> 3. **Homepage first** — build the revised 9-section home now; spec the other 4 pages for the next pass.
> 4. **Brand vision locked (2026-06-08)** — the official 品牌願景 (隱形守護 / 全天候智慧防護網 / 各種場域) is the lead pitch; 企業／店家／住家 are three peer TA entries; 韌性 / 主動式風險管理 / BI are demoted to the technical *proof* layer; 資料主權 / agentic-AI become advanced B2B differentiators, not the headline. (Supersedes the earlier B2B-sovereign North Star.)

---

## 1. Positioning (official brand vision — 2026-06-08)

**官方品牌願景 (locked, verbatim):**

> SPACES 的願景，是打破傳統保全的空間限制，讓「安全」成為無所不在的隱形守護。新光保全致力打造全天候智慧防護網，確保使用者在各種場域中，都能享有更安心的安全保障。

This is the site's lead pitch — universal, warm, and 場域-agnostic ("各種場域 · 更安心"), **not** a B2B-only sovereignty claim. It reframes the earlier plan in two ways:

- **Vision is the headline; resilience is the proof.** 打破空間限制 · 無所不在的隱形守護 · 全天候智慧防護網 is the brand promise at the top. The technical story — 主動式風險管理 (passive→proactive) · 斷網續錄 / 三網備援 / UPS 韌性 · Business Intelligence — becomes *how SPACES keeps that promise*, sitting **under** the vision rather than above it. 資料主權 / agentic-AI context drop to **advanced B2B differentiators** on the Solutions / value-prop layer, no longer the lead.
- **Three peer audiences, three entries.** 企業 · 店家 · 住家 are co-equal TA entrances (not "B2B spine + 住家 sub-branch"). Each needs its own 主要需求 / 痛點 / 對應解決方案 — *content to be supplied by 產品/業務 (open item §7).*

**Hero-tone decision to revisit:** the earlier plan locked the dark "data-graph" hero *because* the lead pitch was sovereignty / agentic-AI. With the warmer 隱形守護 / 各種場域 vision now leading, the hero tone is an **open fork** — the lighter variant (`spaces-home-light-desktop-1440.jpg`) may now read truer than the dark one (`spaces-home-desktop-1440.jpg`). Decide before the build (§7).

---

## 2. Target IA — 5 pages (replaces portal nav)

The current `.pen` carries the **corporate-portal nav** (智慧家庭/企業服務/智慧建築/關於我們/投資人關係/最新消息). SPACES replaces it with a focused 5-page nav + standing CTA:

```
首頁  |  解決方案（含應用場景）  |  產品介紹  |  支援與常見問題  |  媒體報導      [ 預約免費諮詢 ]   中/En
```

| Page | Source slides | Role |
|---|---|---|
| **首頁** Home | 3–8, 36 | North-Star pitch + funnel (built now) |
| **解決方案／應用場景** Solutions | 9–16, **29–34** | B2B vertical stories — *FAQ verticals promoted to solution stories* |
| **產品介紹** Products | 17–23 | 主機 / IP Cam / 感測器 / APP / Web / 雲端錄影 |
| **支援與常見問題** Support & FAQ | 24–28 | *Operational* troubleshooting (APP/硬體故障) lives here, categorized |
| **媒體報導** Media | 36 | Pulls from官網新聞中心 (links back to portal) |

**Key reclassification (from feedback):**
- P29–34 industry FAQs (餐飲/咖啡/倉儲/辦公/商場/金融) → **升級為「解決方案」分種故事**, not buried Q&A.
- P24–28 operational issues (安裝/斷網/APP/硬體故障) → **降為「支援與常見問題」**, with a category index.

---

## 3. Homepage section stack (build now)

Top→bottom, mapping the feedback's block order to content + which component to reuse vs. build new.
Existing component to reuse is from the current home shown in `home-v2-desktop-1440.jpg`.

| # | Section | Content (from deck/feedback) | Component action |
|---|---|---|---|
| 1 | **Hero — 無所不在的隱形守護** | H1 e.g. 打造無所不在的隱形守護 / 全天候智慧防護網 · sub "打破傳統保全的空間限制，整合智慧監控、遠端控制、防災預警與雲端管理，在各種場域守護你的安全" · EN tag *Intelligent Technology That Simplifies Your World* · CTAs 立即了解 / 預約諮詢 · proof chips 30天雲端儲存 · 24H守護 · 三網備援 | Hero photo/tone **TBD** (dark vs light — §1/§7); swap copy to vision-led; add **TA entry chips 企業／店家／住家** (peer entrances → filtered Solutions) |
| 2 | **品牌願景 — NEW** | Official vision statement (verbatim, §1) + 3 pillars: 打破空間限制 · 無所不在的隱形守護 · 全天候智慧防護網（各種場域更安心）. 主動式風險管理 / BI / agentic-AI context move down to value-prop/Solutions as the technical "how". | **Build new** `VisionBlock` (statement + 3 pillars) |
| 3 | **SPACES 是什麼** | 服務客製化 · 設備多元化 · 安全互動化 (24H APP推播/雙向語音/與管制中心連動). 30-sec intro line: "SPACES 是新光保全旗下智慧雲端安防平台，整合分析、IoT設備與24小時人員管制中心" | **Build** 3-pillar block (repurpose card grid styling) |
| 4 | **為何韌性** | 斷網續錄 (本地SD續錄+斷線通知，復網自動同步) · 三網備援 (WiFi/有線/LTE) · UPS 備援 (4–8H) | **Build new** `ResilienceBlock` — promoted from FAQ to a standalone selling section |
| 5 | **智慧安防生態系／服務架構** | 客戶 → SPACES雲端平台 → 設備 → 新光保全管制中心(勤務員/工程師). Capabilities: 遠端操控/即時推播/事件分析/設備集中管理/24H監控/現場派遣 (slides 6–7) | **Build new** `EcosystemDiagram` (illustrative; deck has placeholder 圖.jpg) |
| 6 | **解決方案／應用場景** | Grouped by the three TA entries 企業 · 店家 · 住家 (verticals: 金融 · 商場零售 · 倉儲物流 · 辦公 · 社區 · 住家). Each tile teases 痛點/需求 → SPACES 解決方案 → 解決方案 page. Full story on Solutions = 痛點 → 解決方案 → **安裝實例 → 導入效益 → 客戶成功案例** (conclusion #4; content pending 產品/業務). | **Build** `ScenarioGrid` (6 tiles, filterable by 企業/店家/住家) |
| 7 | **產品介紹** | 系統主機(SGW-10) · IP Cam(P1/D1/B1/B2) · 感測器 · APP · Web後台 · 雲端錄影方案 | **Build** `ProductOverview` 6-up; deep specs live on Products page |
| 8 | **媒體報導** | 3 cards from官網新聞 ("Safe City, Smart Spaces" 智慧城市展…) → links back to portal | **Reuse** NewsCard 3-up (already in `COMPONENT-INVENTORY`) |
| 9 | **預約免費諮詢／聯絡我們** | "全台服務數超過數萬場域信賴…" + lead-capture form (公司/姓名/電話/Email/問題) **串接客服中心 E-mail** (行銷#2). Standing conversion CTAs: **免費試用 · 產品 Demo · 下載型錄 PDF · LINE 即時客服** (行銷#1). 0800-668-850 · service@sks.com.tw. Form fields/tracking depend on whether 業務追蹤/客戶分級/成效分析 exists (§7). | **Reuse** CTA band + **build** `ConsultForm` + LINE/Demo/Download CTAs |

**Removed from current home:** the fixed-price tier section (基礎守護/智慧家庭/全方位守護 NT$…) — replaced by consult-to-quote CTAs. The current 3-step process becomes a **5-step process** (§4).

---

## 4. Conversion / differentiation assets (weave into home + Solutions)

The transactional + persuasion layers the deck lacks. Status reflects the 2026-06-08 design conclusions.

- **TA entry points** — 企業 / 店家 / 住家 selector (hero chips → filtered Solutions). *Structure confirmed; per-segment 需求/痛點/解決方案 content pending 產品/業務 (§7).*
- **價值主張 + 差異化** *(conclusion #3 + 差異化內容)* — a 核心優勢 statement + a **`ComparisonTable`: SPACES vs 傳統監視/保全系統 vs 同業平台** (韌性, 管制中心連動, 主動式風險管理, 客製化 …) + a **`PainPointBlock`** (SPACES 主要解決的客戶痛點). Lives on home (condensed) + Solutions (full). *Committed: 產品/業務 to supply 核心優勢 / vs傳統 / vs同業 + 痛點 content; publish only legal/marketing-signed claims.*
- **案例與導入效益** *(conclusion #4)* — Solutions vertical stories carry 安裝實例 · 導入效益說明 · 客戶成功案例. *Content pending 產品/業務.*
- **5-step process — CONFIRMED** *(conclusion #6)*: 諮詢 → 場勘 → 規劃 → 安裝 → 維運 (extend the existing 3-step `ProcessSteps`).
- **Lead funnel extras — CONFIRMED** *(行銷#1)*: 免費試用 · 產品 Demo · 下載型資源 (型錄 PDF) · **LINE 即時客服**, surfaced as standing CTAs (downloads also on Products). *Existing mechanisms / 申請流程 / 素材 pending confirmation (§7).*
- **Lead capture & tracking** *(行銷#2)*: 「聯絡我們」表單 **串接客服中心 E-mail** (confirmed). Form fields + 行為追蹤/分級/二次行銷 depend on whether 業務追蹤 / 客戶分級 / 成效分析 mechanisms exist — *pending confirmation (§7).*
- **FAQ restructure** *(conclusion #5)*: the Support & FAQ page re-categorized (operational: 安裝 / 斷網 / APP / 硬體故障 …). *客戶最常詢問/反映的問題 list pending 產品/業務/客服.*

---

## 5. Component reuse map

| Reuse as-is / restyle | Repurpose | Build new |
|---|---|---|
| Design tokens (navy/gold/red, dark theme, type scale) · SiteHeader shell · StatStrip · ProcessSteps (3→5) · AppPromo (→ APP+Web) · TestimonialRow (→ B2B quotes) · CTABand · Footer shell · NewsCard 3-up · dark data hero | Price cards → **ScenarioGrid / 3-pillar** · portal nav → **SPACES 5-page nav** · StatStrip values → SPACES proofs (數萬場域 · 30天雲端 · 三網備援 · UPS 4–8H · 24H管制中心) | `VisionBlock` · `ResilienceBlock` · `EcosystemDiagram` · `ScenarioGrid` tiles (TA-filterable) · `ProductOverview` 6-up · `ComparisonTable` · `PainPointBlock` · `ConsultForm` (lead capture) · LINE/Demo/Download standing CTAs |

---

## 6. Execution phases

> Gated on Pencil reconnecting — the live MCP app (`antigravity_ide`) is currently disconnected, so Phase 0 can't run yet.

- **Phase 0 — Inspect (blocked on connection).** `get_editor_state(include_schema:true)`, `get_variables`, `batch_get(document)` to map current frames/components/variables in `sks-redesign.pen`. Confirm the reuse map against actual node IDs.
- **Phase 1 — Tokens.** Confirm token set present; add any SPACES-only vars (scenario accent, vision block). No hardcoding.
- **Phase 2 — Global chrome.** Build SPACES `SiteHeader` (5-page nav + 預約諮詢 CTA + 中/En) and `SiteFooter` columns (解決方案/產品/支援/媒體 + 0800-668-850 + service@sks.com.tw + 內湖區行愛路128號).
- **Phase 3 — Homepage.** New page frame, desktop 1440 + mobile 390, sections 1–9 (§3). Reuse first, build new where marked.
- **Phase 4 — Verify.** `snapshot_layout` for structure/sizing, then `export_nodes` → JPG for visual review. *(Note: `get_screenshot` returns blank in this env — see memory `pencil-env-quirks`; rely on snapshot_layout + JPG export.)*
- **Phase 5 — Spec the other 4 pages.** Write `content/section-spaces-*.md` digests (Solutions/Products/Support-FAQ/Media) so the next build pass is content-ready. Solutions page absorbs P29–34 as vertical stories.

---

## 7. Open items / dependencies

### Resolved by the 2026-06-08 design conclusions
- **Brand vision** — official 品牌願景 locked (§1, verbatim): 隱形守護 / 全天候智慧防護網 / 各種場域.
- **TA entries** — 企業 / 店家 / 住家 confirmed as three peer entrances (per-segment content still pending, below).
- **Onboarding flow** — 諮詢 → 場勘 → 規劃 → 安裝 → 維運 (5 steps) confirmed.
- **Contact form routing** — 「聯絡我們」串接客服中心 E-mail confirmed.
- **Conversion features** — 免費試用 · Demo · 下載型資源 · LINE 即時客服 confirmed in-scope.
- **FAQ** — re-categorized architecture confirmed.

### Still pending — need input / assets (產品 / 業務 / 客服 / marketing)
1. **Per-TA content** — 企業 / 店家 / 住家 each: 主要需求 · 痛點 · 對應解決方案. *(conclusion #2)*
2. **Value prop & differentiation copy** — SPACES 核心優勢 · vs 傳統保全/監視系統 · vs 同業平台 + the 客戶痛點 list, with legal/marketing sign-off on published competitor claims. *(conclusion #3 + 差異化內容)*
3. **Case content** — 安裝實例 · 導入效益說明 · 客戶成功案例 for the application scenarios (deck p9–15). *(conclusion #4)*
4. **FAQ content** — 客戶最常詢問/反映的問題 to populate the new categories. *(conclusion #5)*
5. **Conversion mechanisms** — existing 免費試用/Demo 申請流程 · 下載素材 (型錄 PDF) · LINE 帳號/客服 mechanism, if any. *(行銷#1)*
6. **Lead tracking** — whether 業務追蹤 / 客戶分級 / 成效分析 exists, to finalize form fields + tracking flow. *(行銷#2)*
7. **3D simulation video** — deck slide 4 placeholder ("請先留版為，後續補上"). Hero §1 / vision §2 may reference it; need asset or keep a poster placeholder.
8. **Real product photos** — IP Cam P1/D1/B1/B2, 主機, 感測器, 讀卡機 (Products + ProductOverview); ecosystem diagram art (deck uses 圖.jpg placeholders).
9. **Media feed** — 媒體報導 live from 官網新聞中心 vs a static 3-card snapshot.
10. **Hero tone fork — RESOLVED 2026-06-08: LIGHT chosen.** The SPACES home is now being built on the light frame (`PA6QP` "SPACES Home (Light) — Desktop 1440") in the warm linen/clay/sage palette. Applied so far: vision-led Hero + 企業/店家/住家 TA chips, Vision block (official statement + 3 pillars), Differentiation (PainPoint + SPACES vs 傳統 vs 同業 comparison table), 5-step Process, Consult section (lead form→客服 E-mail + 免費試用/Demo/型錄下載/LINE CTAs). Still to relabel on light: existing Scenarios/Products/Cases sections → TA-grouped 企業/店家/住家 + 安裝實例/導入效益/客戶成功案例 story (content pending, item #3). The dark frame (`m1t1tf`) keeps the updated Vision block but is no longer the lead.
11. **EN coverage** — deck is zh-only; confirm whether SPACES home ships bilingual at launch (brief mandates first-class EN).
