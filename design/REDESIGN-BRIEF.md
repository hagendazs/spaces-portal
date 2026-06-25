# SKS Corporate Portal — Master Redesign Brief

> **Client:** 新光保全 / Taiwan Shin Kong Security Co., Ltd. (SKS, TWSE 9925)
> **Site:** `https://www.sks.com.tw/` — bilingual (Traditional Chinese zh-TW default · English `/en`)
> **Hand-off target:** Figma (via `use_figma` / generate-design) and Pencil / Claude Design
> **Date:** 2026-06-05
> **Companion artifacts (read alongside this brief):**
> - IA: [`content/00-IA-sitemap.md`](../content/00-IA-sitemap.md)
> - Tech stack: [`content/01-tech-stack.md`](../content/01-tech-stack.md)
> - Section content digests: [`content/section-*.md`](../content/)
> - Design audit: [`design/DESIGN-AUDIT.md`](./DESIGN-AUDIT.md)
> - Design tokens: [`design/DESIGN-TOKENS.md`](./DESIGN-TOKENS.md)
> - Component inventory: [`design/COMPONENT-INVENTORY.md`](./COMPONENT-INVENTORY.md)
> - Security: [`security/SECURITY-ASSESSMENT.md`](../security/SECURITY-ASSESSMENT.md)

---

## 1. Project Context & Goals

SKS is a publicly-listed AIoT / smart-living and security-services conglomerate. Its current portal is **three visually and technically disjoint products stitched under one domain** (per `DESIGN-AUDIT.md` §1):

| Tier | Stack | Era | State |
|---|---|---|---|
| Main marketing site | Bootstrap 4.5 + jQuery 3.4.1 | ~2020 | Functional, visibly dated, a11y gaps |
| `/reservation` microsite | Bootstrap 3.1.1 + jQuery 1.10/1.11 + IE8 shiv | 2014–15 | Abandoned; security & UX liability |
| `/Admin` back-office | AdminLTE 3.0.4 | ~2019 | Off-brand generic template |

There is **no shared design system** — three Bootstrap versions, three font stacks, three icon systems, brand color present on the main site only. The visual language reads as early-2020s corporate Bootstrap: full-bleed background-image carousels, boxy cards, system-font CJK type, a flag-stripe top border.

### Goals

1. **Unify** the three tiers under one modern design system and one set of design tokens (`DESIGN-TOKENS.md`), preserving the load-bearing brand: the **navy → gold → red tri-color stripe** and **navy `#002f82` / gold `#fed01f`** identity.
2. **Modernize** the visual language to a current, trustworthy, security-company aesthetic — without losing the brand equity.
3. **Fix the accessibility floor to WCAG 2.1 AA** — alt-less hero imagery, the non-compliant gold-on-white text contrast (~1.7:1), the admin `user-scalable=no` zoom block, fragile logo accessible name (`DESIGN-AUDIT.md` §7).
4. **Mobile-first, performance-first** — kill `vh`-based hero jank, ship responsive `srcset`/WebP imagery (current assets reach 1.1 MB).
5. **Serve four audiences clearly:**
   - **Prospective customers** (home + business) — product/solution pages that sell, with a fast path to Contact.
   - **Investors / shareholders** — a credible, easy-to-navigate Investor Relations hub with reliable document downloads.
   - **Job seekers** — a clear, visible careers path (today only a buried `104.com.tw` link in the pre-footer tiles).
   - **Existing clients** — self-service hub (`/User`), bill payment, service-location lookup, and contact.
6. **Close critical security gaps during the rebuild** (see §7) — the redesign is the moment to move secrets/certs/uploads out of webroot rather than carry the debt forward.

---

## 2. Target Information Architecture

Principles: **keep what works** (the section taxonomy is sound), **consolidate** the fake single-slide carousels and triple-duplicated nav, **fix orphans** (`/Company`, `/skpos`, `Business/Shop`, `About/Group`, the parked Product/QA admin modules — per `00-IA-sitemap.md` §4), and **add wayfinding** (no breadcrumb exists anywhere today).

```
Home  /
│
├── Smart Home  /SmartHome ............... [audience: home customers]
│     ├── Community            /SmartHome/Community
│     ├── Wireless             /SmartHome/Wireless
│     └── Care (elder care)    /SmartHome/Care
│
├── Business  /Business .................. [audience: business customers — flagship]
│     ├── Security             /Business/Security
│     ├── Office Cloud Guard   /Business/Office
│     ├── POS / Store          /Business/POS
│     ├── InfoSecurity         /Business/InfoSecurity   (add EN — currently CN-only)
│     ├── WorkLink             /Business/WorkLink
│     ├── AI Security          /Business/AISecurity
│     ├── Monitor              /Business/Monitor
│     ├── Access Control       /Business/Access
│     ├── GPS Fleet            /Business/GPS
│     └── AI Hotel             /Business/AIHotel        (add EN — currently CN-only)
│     └── (RETIRE/DECIDE: Business/Shop + ProductAdmin/QAAdmin — parked store; keep out unless commerce is in scope)
│
├── Smart Building / Green  /Green ....... [audience: business / property]
│     ├── Green Architecture   /Green/Architecture
│     └── Disaster Prevention  /Green/Prevention
│
├── About  /About ....................... [audience: all]
│     ├── Group / Affiliates   /About/Group   (promote into nav + sitemap — was orphaned)
│     ├── Awards               (merge /AwardAdmin content into About)
│     └── Robots showcase      /Robots → re-home under About as About/Robots
│
├── News & Events  /News ................ [audience: all]
│     ├── News detail          /News/Detail/{id}
│     └── Events / Campaigns   /Event/{...}   (date-gated CMS landing pages)
│
├── Investor Relations  /Shareholder .... [audience: investors/shareholders]
│     ├── Company Profile & Governance
│     ├── Financial Information (reports · revenue · investor conference)
│     ├── Shareholders Column (meetings · annual reports · dividend policy)
│     ├── Investor Q&A
│     └── Investor Contact
│     └── (FOLD ORPHAN: /Company → it duplicates ShareholderView; merge or delete)
│
├── Sustainability / CSR  /CorporateSocial [audience: all, investors, ESG]
│     ├── Peace-of-Mind Classroom   /CorporateSocial/ClassRoom
│     ├── Culture & Art Foundation  /CorporateSocial/CultureArtfoundation
│     └── Social Welfare Foundation /CorporateSocial/WelfareFoundation
│
├── Careers  /Careers ................... [audience: job seekers — NEW first-class section]
│     └── (today only a 104.com.tw link in pre-footer tiles; give it a real landing page
│          even if it deep-links to 104 — culture, benefits, openings, apply CTA)
│
├── Customer / Self-Service  /User ...... [audience: existing clients]
│     ├── Member login hub (8 external self-service systems)
│     ├── Bill Payment → payportal.skspay.com.tw
│     └── Service Locations  /Location
│
├── Stakeholder Engagement  /Stakeholder  [audience: all stakeholders]
│     └── Contact directory + policy/ESG downloads
│
├── Contact  /Contact ................... [all] → /Contact/Success
├── Search  /Search
└── Sitemap  /SiteMap

RETIRE: /reservation microsite (absorb booking into main system) · /skpos (external Wix iframe — decide keep/kill) ·
        legacy static dirs /about(HTML) /catalog(PDF) /ebook /apache (move out of webroot — see §7)
```

**Cross-cutting IA fixes (from `COMPONENT-INVENTORY.md` §3):**
- One **nav config** drives header mega-menu, footer columns, and mobile nav (today hand-duplicated three times).
- Add a global **Breadcrumb** for deep Business / Investor / CSR pages.
- Standardize locale routing so the **language switcher preserves the current page** (today only Home carries an explicit `lang=en`).

---

## 3. Page-Type Templates to Design

Eight templates cover the whole portal. Each lists required content blocks and the source digest.

### 3.1 Home (`section-home.md`)
- **Hero carousel** — admin-driven slides, image **and** video slide types; per-slide intro/title/CTA, separate mobile image, light/dark text. **Replace `vh` heights with `dvh`/aspect-ratio; real `<img>`/`<picture>` with alt, not inline `background-image`.**
- **Three business pillars** — Smart Home · Business · Smart Building image tiles (primary nav into the catalog).
- **Service / self-service strip** (navy band) — customer hotline (北區 `0800-097668` / 南區 `0800-897668`) + two pill CTAs: Bill Payment, Member Area.
- **News Center teaser** — 3-up latest news cards (date + clamped title) → News list.
- **App download** — SK-PLUS / Smart Life 2s icons + App Store / Google Play badges.
- Pre-footer **quick-link tiles** (Service Location · Contact · Careers · Stakeholder) + **footer**.

### 3.2 Section Landing (`section-business-products.md`)
For Business / Smart Home / Green index pages.
- **PageHero** (static title banner — kills the fake 1-slide carousel).
- **Solution rows** — alternating image/text rows, heading + 1–2 lines + "Learn More" pill.
- **Advantage / feature grid** — icon + title + short copy (3–8 cells).
- **Stat / counter block** (e.g. 600 personnel · 350 engineers · 250 sales · 250 vehicles).
- **Use-case switcher / gallery**, **app-download** block, floating **"Dedicated Service" contact pill** → Contact.

### 3.3 News List + Detail (`section-news-events.md`)
- **List:** PageHero + **YearFilter** select (2015→current) + **NewsList** (date + title rows) + **Pagination**. Optional left **SectionSidebarNav** (About-area tabs).
- **Detail:** **ArticleDetail** — optional main image, H2 title, push-date line, rich HTML body (**must be sanitized — see §7**), polymorphic by `NewsType` (News / ClassRoom / Welfare / CultureAndArts) with type-aware back link.

### 3.4 Event / Campaign (`section-news-events.md` §3)
A CMS page-builder driven by the `Arrangement` enum. Design a composable **EventBlock** family: `HeroBlock`, `CardCarouselBlock` (cards: title/image/4-line clamp), `FeatureBlock`, `MediaBlock`. Date-gated visibility, bilingual, per-page font selector, full SEO meta. De-duplicate the zh/en branches via data + i18n.

### 3.5 Product / Solution Detail (`section-business-products.md` §4)
- PageHero (single or multi-slide), **FeatureCarousel** (spec slides w/ arrows), spec/feature **bullet lists**, advantage grid, use-case gallery, PDF catalog **DownloadLink**, app-download, contact pill. Tune heading sizes for the longer English copy (today done via `@if(en)` H2/H3 swaps — make it a token-driven responsive type rule instead).

### 3.6 Investor Relations Hub (`section-investor.md`)
The most structured template. One page, secondary navigation:
- PageHero "Investor Relations / 投資人關係".
- **SectionSidebarNav** (left rail, 5 tabs): Company Profile & Governance · Financial Information · Shareholders Column · Q&A · Investor Contact. Responsive → top tabs / accordion on mobile.
- **DataTable** (branded) for company-profile key-values and org structure (~19 rows).
- **ReportTable** + **YearFilter** + **FileDownloadRow / DownloadLink** for financial reports, revenue, investor conference, shareholder meetings, annual reports — **render on demand / AJAX, not all-years-into-DOM** (`COMPONENT-INVENTORY.md`). ROC year in zh, +1911 CE in en.
- **Accordion** for the shareholder Q&A.
- Investor **contact block** (TEL `(02)2311-8787`, address, stock agency).
- *(Stakeholder page reuses: contact directory grid + icon DownloadCard grid for policy/ESG PDFs.)*

### 3.7 Contact / Reservation (`section-contact-misc.md`)
- PageHero + intro block.
- **ContactForm** — 8 fields: Name, ContactTime (select), Phone, **CascadingCityArea** (city → district via `city.js`), Message textarea, consent **Checkbox** (raw-HTML legal copy — sanitize), **reCAPTCHA**, hidden `EventId`, **SubmitButton**. jQuery validate; resource-driven messages.
- **Success** state — confirmation + countdown redirect (**add the missing English copy**).
- Reservation booking folds into this `ContactForm` system (retire the Bootstrap 3 microsite).

### 3.8 Search (`section-contact-misc.md` §3)
- PageHero + **SiteSearch**. Decision point: keep Google CSE wrapper or build native search. The header magnifier should become an **inline expanding search field** (the existing `#showSearchBar` hints at an unfinished intent), not just a link.

---

## 4. Design Direction

### Tone
Trustworthy, modern, **safety/security-grade** — precise, calm, confident. Not flashy. The audience spans home consumers, enterprise buyers, and institutional investors, so the system must feel both **approachable** (product pages) and **authoritative** (IR/governance). Bilingual zh-TW + en is first-class, not an afterthought.

### Modern visual language
- **Keep verbatim** the navy/gold/red **tri-color stripe** — the single strongest brand signal (`DESIGN-TOKENS.md` §1). Reuse it as a top accent bar, section divider, or focus underline.
- Retire the heavy "flag banner" feeling: use the stripe as a **thin, deliberate accent**, not a full-width government-style header band.
- Generous whitespace (`space.6 = 72px`, `space.7 = 96px` section padding), confident type scale, restrained shadows.
- Photography: real `<img>`/`<picture>` with measured dark overlay for legibility (the current overlay instinct is correct — just make contrast measurable). Consistent treatment; no mixed stock + product-render grab-bag.
- One **icon system** (SVG sprite) across all tiers; drop FontAwesome/Glyphicons from the legacy surfaces.

### Design-token foundation (`DESIGN-TOKENS.md` §8 — ingest the JSON as Figma Variables / Claude Design tokens)
- **Color** — brand: `navy #002f82`, `navy-dark #012a55`, `navy-logo #003d9c`, `blue #0066b1`, `gold #ffc129`, `gold-alt #fed01f`, `red #c90e2d`. Neutrals `#fff → #212529`. State: success `#28a745`, warning `gold`, danger `red`, info `blue`.
- **Gradient** — `brandStripe` (navy 33% / gold / red), `logoIcon`.
- **Type** — modernized CJK stack `"Microsoft JhengHei","PingFang TC","Noto Sans TC",-apple-system,…` (fixes the leading-`sans-serif` ordering bug and the non-Windows zh-TW gap). Scale `xs 12 → displayLg 70`. Weights 400 / **500 (new, for UI)** / 700. Line-height base 1.5, heading 1.25, **cjk 1.7**.
- **Spacing** 4px step (0–96). **Radius** none→pill. **Shadows** xs / md / lg / focus rings. **Motion** 200ms `cubic-bezier(.4,0,.2,1)`. **Breakpoints** sm 576 / md 768 / lg 992 / xl 1200.

### Responsive / mobile-first
- Design **mobile-first**, then md / lg / xl. Mobile uses a **hamburger → full-screen / accordion** nav (replace the dense 30+-link mega-menu on small screens).
- **No `vh` hero heights** → `dvh`/`svh` or `aspect-ratio` (kills mobile-Safari address-bar jump).
- Responsive imagery: `srcset` + WebP/AVIF, `<picture>` for art-direction (separate mobile banner image already exists in data).
- Tap targets ≥ 44px.

### Accessibility — WCAG 2.1 AA (mandatory; from `DESIGN-AUDIT.md` §7)
- **Never use `#ffc129` gold as a text/link color on white** (fails ~1.7:1). Reserve gold for large surfaces/icons; pair text with navy or `#212529`. Verify every text pairing ≥ 4.5:1 (3:1 for large text).
- Hero/banner imagery must carry **alt text** (real `<img>`, not inline `background-image`).
- **Never block zoom** (remove `user-scalable=no`).
- Logo link needs a proper accessible name (`<title>`/`aria-label` on the SVG; drop deprecated `text-hide`).
- Visible **focus rings** on all interactive elements (`shadow.focusBrand`). Keyboard-operable nav, dropdowns, tabs, carousel (with a **visible pause control** + `prefers-reduced-motion` respected).
- Real anchors, not `href="#"`/`href=".#"` placeholders.

---

## 5. Component System to Build

Build from `COMPONENT-INVENTORY.md` §2. Each is token-driven, bilingual-ready, AA-compliant.

**Global chrome**
- `SiteHeader` → `Logo`, `PrimaryNav`, `MegaMenu` (`columns[] → {title, links[]}`), `LanguageSwitcher` (preserves current page), `SocialIcons`, `UtilityIcons` (user, search), `MobileNavToggle`. Sticky.
- `SiteFooter` → `FooterLinkColumns`, `ContactStrip` (hotline + address), `LegalBar`.
- `QuickLinkTiles` (4 icon tiles), `BackToTop`, `FloatingPromoTab` (keep or drop dead CSS).

**Navigation & wayfinding**
- `Breadcrumb` *(new)*, `SectionSidebarNav` (responsive secondary nav), `Tabs` (horizontal w/ overflow scroll — replaces bespoke `list-controller` JS), `Pagination`, `YearFilter`.

**Heroes & carousels**
- `PageHero` (static banner — replaces ~12 fake 1-slide carousels), `HeroCarousel` (image|video slide union) + `VideoModal`, `FeatureCarousel` (arrow controls + caption).

**Content cards & lists**
- `NewsCard` (grid|list), `NewsList`, `ArticleDetail`, `SolutionRow` (alternating), `CategoryTiles` (Home 3-up), `AppDownloadCard`, `Accordion`.

**Data & documents**
- `DataTable` (branded plain), `ReportTable` (on-demand/AJAX) + `FileDownloadRow` + `DownloadLink` (bake in the `?v=ticks` cache-bust; fix the corrupted `class` attr).

**Forms & input**
- `ContactForm` = `TextField` · `SelectField` · `CascadingCityArea` · `Checkbox` · `Recaptcha` · `SubmitButton`. `SiteSearch`. `Modal` primitive.

**CMS / campaign**
- `EventBlock` family: `HeroBlock`, `CardCarouselBlock`, `FeatureBlock`, `MediaBlock` (driven by the `Arrangement` enum).

**Primitives** (define first): `Button` (primary navy / secondary / outline-pill), `Link`, `Icon`, `Badge`, `Tag`, `FormField`, `Card`, `Container`/`Section`.

---

## 6. Hand-off Prompts

> Both prompts assume the token JSON from `DESIGN-TOKENS.md` §8 is loaded as variables/tokens. Paste that JSON into the tool's variable/token store first.

### 6.a Figma — Home page + design system

```
Use the SKS design tokens (loaded as Figma Variables) to build the SKS corporate
portal design system and home page in this file.

BRAND: 新光保全 / Shin Kong Security — a bilingual (zh-TW + English) security &
smart-living company. Tone: trustworthy, modern, security-grade, calm.

TOKENS (use Variables, do not hardcode):
- Color: brand/navy #002f82, brand/navy-logo #003d9c, brand/blue #0066b1,
  brand/gold #ffc129, brand/gold-alt #fed01f, brand/red #c90e2d;
  neutral white→#212529; state success/warning/danger/info.
- Gradient brandStripe: linear-gradient(to right, #002f82 33.33%, #ffc129 33.33%,
  #ffc129 66.67%, #c90e2d 66.67%)  — use as a THIN top accent bar only.
- Type: family "Microsoft JhengHei","PingFang TC","Noto Sans TC", system fallback;
  scale xs12 sm14 base16 h5-18 h4-20 h3-24 h2-32 h1-40 displayMd-48 displayLg-70;
  weights 400/500/700; line-height base1.5 heading1.25 cjk1.7.
- Space 4px step (0,4,8,16,24,48,72,96). Radius: base .25rem, md .3rem, lg 10px,
  xl 25px, pill 50rem. Shadow xs/md/lg + focusBrand ring. Motion 200ms cubic-bezier(.4,0,.2,1).
- Breakpoints sm576 md768 lg992 xl1200. Design MOBILE-FIRST, then desktop frame at 1440.

STEP 1 — Design-system page. Create component sets with variants for:
SiteHeader (sticky; Logo, PrimaryNav, MegaMenu, LanguageSwitcher 中/En, social FB/YT,
user + search utility icons, mobile hamburger), SiteFooter (link columns + ContactStrip
hotline/address + LegalBar), Button (primary navy / secondary / outline-pill; default/
hover/focus/disabled), Link, Icon, Badge, Card, PageHero, HeroCarousel (image+video
slide), NewsCard (grid+list), SolutionRow, CategoryTiles, AppDownloadCard, QuickLinkTiles,
Breadcrumb, SectionSidebarNav, Tabs, Accordion, DataTable, ReportTable+FileDownloadRow,
DownloadLink, ContactForm fields (TextField, SelectField, CascadingCityArea, Checkbox,
Recaptcha, SubmitButton), Pagination, Modal, BackToTop.

STEP 2 — Home page (desktop 1440 + mobile 390), top to bottom:
1. Sticky SiteHeader with a thin navy/gold/red stripe accent (NOT a heavy banner).
2. Hero carousel — full-width, aspect-ratio based (NO 100vh), real image with overlay,
   overlaid intro (h3) + title (h1, white) + "Learn More" outline-pill CTA; dot indicators
   + a visible pause control; one video slide with play button.
3. Three business pillars — equal image tiles (Smart Home · Business · Smart Building)
   with dark-mask hover, each a link.
4. Service strip — navy band: customer hotline (北區 0800-097668 / 南區 0800-897668) +
   two pill CTAs (線上繳費 Bill Payment, 會員專區 Member Area).
5. News Center — section heading + 3-up NewsCard grid (date + 3-line-clamp title) → News list.
6. App download — SK-PLUS + Smart Life 2s icons with App Store + Google Play badges.
7. QuickLinkTiles (Service Location · Contact · Careers · Stakeholder).
8. SiteFooter.

ACCESSIBILITY (WCAG AA, enforce): all text ≥4.5:1 — NEVER gold #ffc129 text on white
(use navy or #212529 on gold instead); every image has alt; visible focus rings
(focusBrand); 44px min tap targets; carousel pausable + reduced-motion friendly.
Provide both a zh-TW and an English variant of the hero and nav.
```

### 6.b Pencil / Claude Design — Home page + design system

```
Create a new SKS corporate portal .pen design (web, responsive). First call
get_editor_state(include_schema:true) and get_variables; load these as variables, then
batch_design the system + home page. NEVER hardcode values that exist as variables.

PROJECT: 新光保全 / Shin Kong Security — bilingual (Traditional Chinese + English)
security & smart-living company portal. Tone: trustworthy, modern, security-grade.

VARIABLES (set_variables):
- color: navy #002f82, navyLogo #003d9c, blue #0066b1, gold #ffc129, goldAlt #fed01f,
  red #c90e2d; neutrals #ffffff #f8f9fa #dee2e6 #6c757d #212529 #000000;
  state success #28a745 / warning #ffc129 / danger #c90e2d / info #0066b1.
- gradient brandStripe: navy 33.33% | gold 33.33–66.67% | red 66.67% (thin accent bar only).
- font: "Microsoft JhengHei","PingFang TC","Noto Sans TC", system; sizes
  12/14/16/18/20/24/32/40/48/70; weight 400/500/700; line-height 1.5 body, 1.25 heading,
  1.7 CJK.
- space 0/4/8/16/24/48/72/96; radius 4/5/10/25/pill; shadow xs/md/lg + navy focus ring;
  motion 200ms ease(.4,0,.2,1). Breakpoints 576/768/992/1200. Build mobile (390) first,
  then desktop (1440).

COMPONENTS to define (reusable): SiteHeader (sticky: logo, primary nav, Services mega-menu,
中/En switcher, FB/YT, user+search icons, mobile hamburger), SiteFooter (link columns +
hotline/address ContactStrip + legal bar), Button (primary navy / outline-pill /
secondary, with hover+focus+disabled), PageHero, HeroCarousel (image+video, pausable),
NewsCard (grid/list), SolutionRow, CategoryTiles, AppDownloadCard, QuickLinkTiles,
Breadcrumb, SectionSidebarNav, Tabs, Accordion, DataTable, ReportTable + FileDownloadRow,
DownloadLink, ContactForm (Name, ContactTime select, Phone, City→District cascade,
Message, consent checkbox, reCAPTCHA, submit), Pagination, Modal, BackToTop.

HOME PAGE (mobile + desktop), sections top→bottom:
1) Sticky header with a THIN navy/gold/red stripe accent (not a heavy government banner).
2) Hero carousel: aspect-ratio framed (NOT 100vh), photo with dark overlay, intro line +
   big title (white) + outline-pill "Learn More"; indicators + visible pause; one video slide.
3) Three pillars: Smart Home / Business / Smart Building image tiles, dark-mask hover, linked.
4) Navy service strip: hotline 北區 0800-097668 / 南區 0800-897668 + two pill CTAs
   (Bill Payment, Member Area).
5) News Center: heading + 3 NewsCards (date + 3-line clamped title) → News list link.
6) App download row (App Store + Google Play badges).
7) QuickLinkTiles: Service Location, Contact, Careers, Stakeholder.
8) Footer.

ACCESSIBILITY (WCAG AA — required): text contrast ≥4.5:1, so NEVER place gold #ffc129 text
on white (use navy/#212529 on gold); every image needs alt; visible focus ring on all
interactive nodes; min 44px tap targets; carousel pausable. Produce zh-TW and English
variants of the hero/nav. After building, call snapshot_layout + get_screenshot to verify.
```

---

## 7. Security Note — Redesign + Rebuild Must Close the Critical Gaps

The rebuild is the moment to fix the systemic security failures in `security/SECURITY-ASSESSMENT.md` (4 Critical, 7 High). Carrying this debt into the new build is not acceptable. The design/IA decisions in this brief already assume:

- **Move all secrets, certs, backups, and uploads OUT of webroot.** Today the webroot serves TLS **private keys** (`apache/2022wildcard.sks.com.tw.zip`, `apache.zip`, `ebook/temp/wildcard.skspay.com.tw.zip`, current 2026 `skgps.com.tw` key), a stale `20200803Web.config` leaking the SQL `sa` + SMTP passwords, an APK, and 639 corporate/financial docs under `FileUploads/Document`. **Treat every exposed key/credential as compromised — rotate/revoke now.** In the new architecture, `FileUploads/Document` (the IR/financial PDFs referenced by `ReportTable`/`DownloadLink`) must be served through an authenticated/streamed endpoint from outside the webroot, not as static files.
- **Secrets management** — no DB/SMTP credentials in `web.config`; use a secret store / environment config. Salt-hash admin passwords (currently `nvarchar(50)`, unhashed).
- **Sanitize all CMS rich-text** rendered to the public — `ArticleDetail` (`News/Detail`), Investor dividend-policy content, and `EventBlock` bodies are emitted via `@Html.Raw(...)` today (stored-XSS risk; the editor is EOL CKEditor 4.11.3). The redesign components must HTML-sanitize on render.
- **CSRF / AuthZ** — replace GET-based destructive admin actions with POST + anti-forgery; add config-layer URL authorization on `/Admin`.
- **Modernize the vendor stack** the design system replaces — jQuery 3.4.1, Bootstrap 3/4, AdminLTE 3.0.4 all carry known CVEs; the new token-driven component system should not reintroduce them.
- **Retire the `/reservation` microsite and legacy static dirs** (`/about` HTML backups, `/catalog`, `/ebook`, `/apache`) — they are both UX debt and exposed-file risk.

The redesign and the security remediation are one program: a modern, accessible portal on top of an unpatched, secret-leaking webroot is still a breach. Sequence the secret rotation and webroot cleanup **before** launch.
