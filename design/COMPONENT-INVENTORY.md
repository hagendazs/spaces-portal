# SKS Portal — Component Inventory

Source of truth: `/Users/reiser/repo/sks` (ASP.NET MVC / Razor `.cshtml`, Bootstrap 4 + custom `Content/sks/css/style.css`, SVG sprite icons) and the legacy static microsite `/Users/reiser/repo/sks/reservation` (Bootstrap 3 + Webflow `w-*` classes).

This catalog drives the redesign. It records every recurring UI component, where it appears, its content slots, and its current state, then maps each to a proposed redesigned component.

Legend for **Current state**: 🟢 reusable as-is · 🟡 works but needs refactor · 🔴 broken / brittle / legacy.

---

## 1. Inventory Table

| Component | Appears on | Content slots | Current state | Redesign notes |
|---|---|---|---|---|
| **Header / top nav** (`Shared/_Layout.cshtml` `<header class="fixed-top">`) | Every main-site page (global layout) | Logo (SVG sprite, swaps `icon-sks-text` / `icon-sks-text-en`), 4 dropdown nav items (Services mega-menu, About SKS, Investor Relations, Corporate Governance), flat links (Stakeholder, ESG, Bill Payment), language toggle, social icons (FB, YT), user portal icon, search icon, mobile hamburger (`navbar-toggler`) | 🟡 | Rebuild as `<SiteHeader>` + `<PrimaryNav>`. Move nav model out of the view into data/config (currently hard-coded `Url.Action` lists duplicated between nav, mega-menu, and footer). Fix inline raw-HTML hacks (broken `class="dropdown-item mobile-list...>` attribute on the Stakeholder PDF link, lines ~248). Keep sticky behavior. |
| **Mega-menu (Services dropdown)** | Global header → "Services" item | 4 column groups: Home Users, Business, (overflow column), Smart Building; each a titled list of service links. Width swaps `min-rem-lg-65` (en) / `min-rem-lg-45` (zh). Mobile collapses to accordion (`js-plus`, `jsindex-link`, `data-list`) | 🟡 | Promote to a first-class `<MegaMenu>` with a `columns[] → {title, links[]}` data model. Same link set is hand-duplicated in the footer — unify into one nav source. Remove commented-out items (AIHotel, etc.). |
| **Language switcher** | Global header (desktop dropdown "中/En"; also a mobile nav item) | Two options: 中文 → `/`, English → `Url.Action("Index","Home",{lang="en"})`. Driven by `Resource.LangIso == "en-US"` and `containerCls` (container vs container-fluid) | 🟡 | Extract `<LanguageSwitcher current lang />`. Currently only Home gets an explicit `lang=en` route; other pages prefix `/en` manually (`@(isEn ? "/en":"")`). Standardize locale routing so the switcher preserves the current page. |
| **Footer mega-links** | Every main-site page (global layout `<footer>`) | 7 link columns (Home Users, Business Users, Smart Building, About SKS, ESG, Stakeholder, Member/User services) + logo + address block + customer-service hotline (north/south phone numbers) + copyright/sitemap bar | 🟡 | `<SiteFooter>` driven by the same nav data as header/mega-menu. Hotline + address become a small `<ContactStrip>` sub-block. Eliminate the third duplicated copy of the service link list. |
| **Footer quick-links strip** (`<section class="border-sks">` above footer) | Every main-site page | 4 icon tiles: Service Location, Contact Us, Careers (104.com.tw), Stakeholder Engagement; each = SVG sprite icon + label + link | 🟢 | Keep as `<QuickLinkTiles>` (4 × icon + label). Trivial to re-template. |
| **Hero / banner carousel (CMS-driven)** | `Home/Index` | Per-slide: background image (desktop `BannerPhoto`/`BannerEnPhoto` + mobile `MobileImg`), intro text, title, optional logo SVG, optional CTA button ("Learn More"), text color (white/black), L/R position, vertical offset; OR a YouTube-video slide (thumbnail + play button → opens video modal). Bootstrap carousel w/ indicators | 🟡 | `<HeroCarousel slides[]>` with a discriminated slide type (`image` \| `video`). Heavy per-slide conditional logic (mobile vs desktop, offsets, position) should move into slide data. Keep bilingual image-folder switch. |
| **Banner / page-title hero (static)** | `News/Index`, `News/Detail`, `Contact/Index`, `Search/Index`, `Shareholder/Index`, `Business/*`, most inner pages | Single static slide: fixed bg image (`about-banner.jpg` or page-specific), dark mask overlay, H1 page title, optional subtitle; height variants `vh-33`/`vh-50`/`vh-75` | 🟡 | Collapse into one `<PageHero title subtitle image height />`. It is currently a fake 1-slide Bootstrap carousel copy-pasted across ~12 files — replace with a plain banner component (no carousel needed). |
| **Feature / product spec carousel** | `Business/Office` (`#feature-banner`, `#feature-banner2`), other Business/SmartHome/Green detail pages, `Event/*` | Per-slide: image, heading, description list, prev/next controls (`carousel-control-prev/next` w/ sprite arrows), gray indicators | 🟡 | `<FeatureCarousel>` variant of HeroCarousel with arrow controls + caption. Many near-identical hand-coded copies across product pages → consolidate. |
| **News card / list item** | `Home/Index` (3-up grid teaser), `News/Index` (full divided list) | Date (`OnlineTime` yyyy-MM-dd), title (with zh/en word-splitting + `multiline-ellipsis` clamp on Home), link → `News/Detail?id=`. Home uses `col-lg-4` grid; Index uses stacked `divider-h` list | 🟡 | One `<NewsCard variant="grid\|list">` + `<NewsList>`. Drop the inline regex title word-splitting (lines ~222–246 of Home) into a helper/CSS. |
| **News detail / article** | `News/Detail` | Optional main image (`NewsPhoto`/`NewsEnPhoto`), title H2, push-date, raw HTML body (`@Html.Raw(Model.Content)`), "Back" link. Type-driven title/subtitle/back-link (News / ClassRoom / Welfare / Culture) | 🟡 | `<ArticleDetail>`. Body is trusted raw CMS HTML — sanitize on the redesign. Generalize the `NewsType` switch into a prop. |
| **News year filter (select)** | `News/Index` | `<select>` "Search by year" → years 2015..current; JS reloads page with `?year=` | 🟢 | Keep as `<YearFilter>`; same control reused on every Shareholder financial table. Standardize one component. |
| **Product / solution card (image + copy + CTA)** | `Business/Index` (alternating L/R rows), `Home/Index` (3 category tiles linking Home/Business/SmartBuilding) | Background image, H2 title, 1–2 paragraphs, "Learn More" outline-pill button, link; alternating layout via `flex-row-reverse` | 🟢 | `<SolutionRow image title body cta reverse />`. Clean, repeatable — good redesign anchor. The Home 3-tile block = `<CategoryTiles>` variant. |
| **App download block** | `Home/Index` (SK-PLUS, Smart Life 2s) | Product icon, H3 title, description, App Store + Google Play badge images w/ links | 🟢 | `<AppDownloadCard>` (icon, title, desc, store badges[]). |
| **Sidebar tab navigation (`list-group` aside)** | `News/Index`, `Shareholder/Index`, `About`, `Company`, `CorporateSocial` | Vertical `list-group-item-action` links/tabs; Bootstrap `data-toggle="list"`; desktop-only (`d-none d-lg-block`), mobile equivalent lives in header dropdowns. Drives `tab-pane` content + URL hash | 🟡 | `<SectionSidebarNav items[] activeHash />`. Mobile fallback is inconsistent (sometimes the header dropdown, sometimes a `mobile-list-tab`). Unify into one responsive secondary-nav component. |
| **Inner horizontal tabs (`nav-tabs`)** | `Shareholder/Index` (Company Profile/Org, Financial sub-tabs, Shareholder column sub-tabs) | `nav-tabs tabs-brand-red` items → `tab-pane` panels; plus a custom prev/next "list-controller" scroller for overflow on mobile | 🟡 | `<Tabs>` with optional overflow scroll. Replace bespoke `list-controller` JS with a standard scrollable tab strip. |
| **Financial report / download table** | `Shareholder/Index` (Financial Reports, Income, Investor Conf, Shareholder meeting, Annual reports, Important resolutions, Major shareholders) | Per-year `<table class="table table-download">` with `<thead>` headers + rows; each row = title/date + download icon link (`btn-download` → `FileUploads/Document/{FileName}?v={ticks}`); year `<select>` toggles which table is visible; empty-state placeholder table when no data | 🔴 | `<ReportTable>` + `<YearFilter>` + `<DownloadLink>`. Currently renders ALL years' tables into the DOM and shows/hides via JS class swap — replace with on-demand/AJAX or filtered render. Standardize the file-download row as a reusable `<FileDownloadRow title date href />`. |
| **PDF / download link** | Shareholder tables, header Stakeholder dropdown, footer Stakeholder column, Event pages, ESG report link | Document title, `target="_blank"`, href `FileUploads/Document/{File}?v={DateTime.Now.Ticks}` cache-buster, download-icon variant | 🟡 | `<DownloadLink href label icon?>`. The `?v=ticks` cache-bust pattern is everywhere — bake into the component. One header instance has a corrupted attribute (long URL pasted into `class=`) — fix. |
| **Data / org table (branded)** | `Shareholder/Index` (organization chart rows, major-shareholders table) | `table table-brand-yellow table-borderless` w/ `<thead>`/`<tbody>` rows of plain text | 🟢 | `<DataTable>` simple variant. Distinct from the download table — keep two table styles (data vs download). |
| **Accordion / FAQ (Q&A)** | `Shareholder/Index` (Investor Q&A `#question-list`) | `card-header` button (question + `btn-arrow`) toggling `collapse` panel (answer body); first item open by default | 🟢 | `<Accordion items[{q,a}]>`. Clean Bootstrap pattern, port directly. |
| **Contact form** | `Contact/Index` | Anti-forgery token, hidden `EventId`, Name, preferred Contact Time (`<select>`), Phone, City + Area cascading dropdowns (`city.js`), Message textarea, agree-to-terms checkbox, reCAPTCHA, Submit button. Client-side jQuery validation + custom submit guard | 🟡 | `<ContactForm>` with field components (`TextField`, `SelectField`, `CascadingCityArea`, `Checkbox`, `Recaptcha`, `SubmitButton`). Keep reCAPTCHA + city.js dependency; move validation messages to resources (already partly there). Success state → `Contact/Success`. |
| **Reservation lead form** (microsite) | `reservation/index.html` (`#user_info`) | Name, Tel, Email, Service `<select>`, Submit button (AJAX `btn-submit`) | 🔴 | Legacy standalone (Bootstrap 3 + Webflow). If kept, rebuild as `<ContactForm>` variant; otherwise fold the reservation microsite into the main site theme. |
| **Site search** | `Search/Index`; header search icon links here | Google Custom Search Engine widget (`<gcse:search>` + injected `cse.js`, `cx` key); wrapped in standard `<PageHero>` | 🟡 | `<SiteSearch>`. Currently fully delegated to Google CSE (ad block hidden via CSS). Redesign decision: keep GCSE or build native search. The header magnifier is a link, not an inline expanding search field (the `#showSearchBar` id implies an unfinished inline-search intent). |
| **Pagination** (`Shared/_Page.cshtml` partial) | `News/Index` (and any `PageResult`-backed list) | Prev «, numbered pages (windowed ±2 around current), Next »; `disabled` states; `data-pager` attrs consumed by `Scripts/Admin/page.js` | 🟢 | `<Pagination>` — already a clean reusable partial. Port directly; keep the windowing logic. |
| **Modal — video** | `Home/Index` hero video slides (`#sksVideo`, `data-video` = YouTube id) | YouTube video id → embedded player modal (`modal-trigger`) | 🟡 | `<VideoModal>`. Modal markup is defined in popup.css/script, not inline — confirm the modal container exists in shared markup during redesign. |
| **Modal — reservation CTA** (microsite) | `reservation/index.html` (`.bs-example-modal-lg`) | Triggered by "立即預約" button; links out to external SimplyBook | 🔴 | Legacy; replace with main-site `<Modal>` primitive or remove. |
| **CMS modular landing-page blocks** | `Event/Index`, `Event/Preview` (campaign / event microsites rendered from `EventInformation` model) | Per-block "Arrangement" types (A–E +): full-bleed section w/ bg color/image + min-height, icon, intro, main image, multi-card carousels (`card`: title, image, 4-line clamped text), feature carousels, configurable font-family (KaiSB/MingLiU/JhengHei), date-gated visibility (StartDate/EndDate), bilingual branches | 🔴 | This is a mini page-builder. Redesign as a small set of composable `<EventBlock variant>` components driven by the existing `Arrangement` enum: `<HeroBlock>`, `<CardCarouselBlock>`, `<FeatureBlock>`, `<MediaBlock>`. Huge (1267–1410 line) view with duplicated zh/en branches → de-duplicate via data + i18n. |
| **Back-to-top button** | Global layout | Fixed round button → `#header`, "TOP" label, sprite arrow | 🟢 | `<BackToTop>` utility. Port directly. |
| **Floating side tab** (`.mwt_border` styles in layout) | Global (CSS defined; vertical promo tab) | Vertical-text promo badge w/ arrow | 🟡 | `<FloatingPromoTab>` if still used; otherwise drop the dead CSS. |
| **Breadcrumb** | — *(none found in audited views)* | — | 🔴 (absent) | No breadcrumb exists anywhere. Inner pages rely on sidebar tabs + "Back" links only. **Add a `<Breadcrumb>` component** in the redesign for deep Business/Investor/ESG pages. |

---

## 2. Proposed redesigned component set (grouped)

**Global chrome**
- `SiteHeader` → `Logo`, `PrimaryNav`, `MegaMenu`, `LanguageSwitcher`, `SocialIcons`, `UtilityIcons (user, search)`, `MobileNavToggle`
- `SiteFooter` → `FooterLinkColumns`, `ContactStrip` (hotline + address), `LegalBar` (copyright + sitemap)
- `QuickLinkTiles` (the 4-icon pre-footer band)
- `BackToTop`, `FloatingPromoTab`

**Navigation & wayfinding**
- `Breadcrumb` *(new)*
- `SectionSidebarNav` (responsive secondary nav, replaces `list-group` aside + scattered mobile dropdowns)
- `Tabs` (horizontal, with overflow scroll) — replaces `nav-tabs` + custom `list-controller`
- `Pagination`, `YearFilter`

**Heroes & carousels**
- `PageHero` (static title banner — kills ~12 fake single-slide carousels)
- `HeroCarousel` (Home; image/video slide union) + `VideoModal`
- `FeatureCarousel` (product spec slides w/ arrows)

**Content cards & lists**
- `NewsCard` (grid/list variants), `NewsList`, `ArticleDetail`
- `SolutionRow` (Business alternating rows), `CategoryTiles` (Home 3-up)
- `AppDownloadCard`
- `Accordion` (FAQ/Q&A)

**Data & documents**
- `DataTable` (branded plain table)
- `ReportTable` (financial/download table) + `FileDownloadRow` + `DownloadLink`

**Forms & input**
- `ContactForm` composed of `TextField`, `SelectField`, `CascadingCityArea`, `Checkbox`, `Recaptcha`, `SubmitButton`
- `SiteSearch` (GCSE wrapper or native)
- `Modal` primitive (video, CTA, alerts)

**CMS / campaign**
- `EventBlock` family: `HeroBlock`, `CardCarouselBlock`, `FeatureBlock`, `MediaBlock` (driven by the existing `Arrangement` enum)

---

## 3. Cross-cutting issues to fix in the redesign
- **Triple-duplicated nav data**: the service link list is hand-maintained in the header mega-menu, the footer, and (partly) mobile dropdowns. Drive all three from one nav config.
- **Fake carousels**: ~12 pages wrap a static title banner in a 1-slide Bootstrap carousel. Replace with `PageHero`.
- **Bilingual branching in markup**: zh/en handled with inline `@if (isEn)` and string `.Replace()` hacks (and whole duplicated foreach blocks in `Event/Index`). Move to proper i18n + data.
- **No breadcrumb / weak wayfinding** on deep pages.
- **Broken markup**: corrupted `class` attribute on a Stakeholder download link in `_Layout.cshtml` (~line 248).
- **Render-everything tables**: Shareholder renders every year's table to the DOM and toggles via JS — move to filtered/AJAX render.
- **Trusted raw HTML**: `News/Detail` and Event blocks emit `@Html.Raw(...)` CMS content unsanitized.
- **Legacy reservation microsite** (Bootstrap 3 + Webflow) is stylistically disconnected from the main site — re-theme or absorb.
