# Section Brief: Reservation Microsite (預約參觀 / Experience Center Booking)

> Standalone HTML + PHP microsite, separate from the main SKS portal. Single-page landing
> for the **新保智慧生活體驗館 (SKS Smart Living Experience Center)**, promoting and
> funneling visitors to an external online booking system.
> Source: `/Users/reiser/repo/sks/reservation/` (`index.html`, `contact.php`).

---

## 1. Purpose

A lightweight, single-page promotional/landing microsite whose sole job is to explain
**how to reserve a guided visit** to the SKS Smart Living Experience Center
(**新保智慧生活體驗館**) and to hand the visitor off to a third-party scheduling tool.
It also surfaces venue logistics (address, hours, transit) and a general inquiry form.

It is **not** part of the main ASP.NET (.cshtml) portal — it is a self-contained
static template (a generic agency theme branded "ECO/Elegance") customized with
SKS Chinese copy. It lives at its own URL.

---

## 2. Page Inventory

| Page | Path | Type | Purpose | Dynamic? |
|------|------|------|---------|----------|
| Landing (single page, 4 scroll sections) | `index.html` | Static one-pager w/ anchor sections | Explain reservation rules, venue info, map, inquiry form | No (static); form POSTs via AJAX to external API |
| Mail handler (orphaned) | `contact.php` | PHP form-to-email script | Template leftover — **not wired** to the live form | Server-side, but unused |
| Backup copy | `index - 複製.html` | Duplicate of landing | Dev artifact, ignore | No |

The entire user-facing experience is **one scrolling page** with four anchor sections:
`#home` (hero), `#about` (reservation steps), `#team` (venue info + map), `#contact` (inquiry form).

---

## 3. Content / Copy Themes (by section)

### A. Hero / Slider (`#home`)
- Bootstrap **carousel** with 3 slides (`slider1.jpg`–`slider3.jpg`), auto-rotating, with indicator dots and prev/next chevrons.
- Slides are image-only — **no caption text or CTA overlay** is populated.
- Fixed top header with SKS logo (`logo.png`); nav menu markup is present but **empty** (no menu items rendered).

### B. Reservation Instructions — 預約參觀說明 ("Reservation / Visit Instructions") (`#about`)
Heading: **預約參觀說明**. Three side-by-side instruction cards, each a numbered step:

1. **預約時間 (Booking time)** — Choose desired date & time, confirm, click 『預約』(Reserve), fill the booking form; the system emails a confirmation to the address provided.
2. **預約人數 (Group size)** — Each time slot admits only **5–15 people** (每場時段僅開放5~15人).
3. **預約方式 (Booking method)** — **Online booking only** (網路預約); phone booking not accepted (恕無法接受電話預約). Changes must be made **at least 3 days before** the visit date.

- Primary CTA button: **立即預約 ("Reserve Now")** → links out to `http://skdemo.simplybook.me/` (external **SimplyBook.me** scheduling service, opens new tab). This is the core conversion action.

### C. Venue Information — (`#team`)
Three info cards with animated hover icons (location / clock / earth):

- **場館地址 (Venue Address):** 104 台北市建國北路一段126號1樓 (1F, No. 126, Sec. 1, Jianguo N. Rd, Taipei).
- **開放時間 (Opening Hours):** 每週一至五 (Mon–Fri); **10:00–11:00** and **15:00–16:00** (two fixed slots/day).
- **交通資訊 (Transit / Directions):** Take MRT to **Songjiang Nanjing (松江南京) Station**, Exit 4; first alley, turn left to the end, turn right.
- Embedded **Google Maps** widget pinned to lat/lng `25.050860, 121.536043`, tooltip "新保智慧生活體驗館".

### D. Inquiry / Consultation — 諮詢服務 ("Consultation Service") (`#contact`)
- Heading **諮詢服務**; intro copy: "您好，如果您有任何問題和建議，請您填寫下列表單，我們將盡快與您聯繫" ("If you have any questions or suggestions, fill out the form and we'll contact you shortly").
- General contact/lead form (see data shape below).

### E. Footer
- SKS logo (`logo1.png`).
- Phone: **0800-668-850** (toll-free); Email: **service@sks.com.tw**.
- Copyright: **©新光保全股份有限公司 / TAIWAN SHIN KONG SECURITY CO., LTD** (link placeholder to www.sks.com.tw).
- Decorative social-media icon set present in assets but not all rendered.

---

## 4. Content Types & Data Shapes

- **Banner carousel:** 3 static image slides, no per-slide structured data (no title/subtitle/CTA fields used).
- **Instruction cards:** 3 fixed numbered "step" cards (title + paragraph). Hard-coded, not data-driven.
- **Venue info cards:** 3 fixed cards (icon + title + text) — address, hours, directions.
- **Map embed:** single lat/lng + zoom + tooltip (Google Maps widget attributes).
- **Inquiry/contact form** (`#user_info`), client-side validated, submitted via AJAX:
  | Field | Name | Type | Validation | Notes |
  |-------|------|------|------------|-------|
  | 姓名 (Name) | `name` | text | required | placeholder "請輸入姓名" |
  | 聯絡電話 (Phone) | `tel` | text | required, `\d{7,}` | placeholder "市話請含區碼" (include area code) |
  | E-Mail | `email` | text | required, email regex | placeholder "請輸入E-Mail" |
  | 洽詢服務 (Service of interest) | `service` | select | required (≠ null) | dropdown, options below |

  **Service dropdown options** (option labels are the product lines; underlying values are mislabeled in source as 智慧居家/智慧節能/etc., a template bug):
  - 無線保全系統 (Wireless Security System)
  - iHome智慧家庭 (iHome Smart Home)
  - Care U健康照護 (Care U Health Care)
  - SK POS
  - 新保機器人 (SKS Robot)
  - 其它 (Other)

  **Submission flow:** On submit, JS builds `{Name, Tel, Email, Service}` JSON and `POST`s to
  **`http://www2.sks.com.tw/msg/api/msg`** (the main portal's message API). Success → alert
  "感謝您的填寫，客服人員將盡速與您聯絡！"; error → alert pointing to service@sks.com.tw.
  → The form is a **lead-capture/inquiry form**, reusing the main portal's contact API.

- **`contact.php`** is an unrelated template artifact: reads `cf_name/cf_email/cf_message`,
  emails `newthinks@live.com` (the template author), redirects to `index.html`. **Not connected
  to the live form** — safe to drop in redesign.

---

## 5. Media / Assets

Roughly **40+ image assets** ship in the template, but only a handful are actually used:

**Used by the page:**
- Carousel: `images/slider/slider1.jpg`, `slider2.jpg`, `slider3.jpg` (3)
- Branding: `images/logo.png` (header), `images/logo1.png` (footer)
- Google Maps embed (live, via widget)
- Font Awesome + custom icon webfonts (location/clock/phone/envelope icons)

**Shipped but unused (generic template leftovers, candidates for deletion):**
- `images/portfolio/1–9.jpg`, `images/services/*` (UX-UI, Web-Design, Branding, etc.),
  `images/team/image1–3.png`, `images/clients/client-logo1–6.png`,
  `images/social/*` (14 social icons), `about.png/jpg`, `who-we-are.png`, `parlex.png`,
  `team.png`, `map-image.png`, duplicate "複製" logos.
- **No PDFs / no downloads** in this section.
- Fonts: FontAwesome, Glyphicons, plus template `ecoicons` & `codropsicons`.
- JS: jQuery (two versions bundled), Bootstrap, Modernizr, carousel + animation/effects scripts.

---

## 6. Bilingual Coverage

- **Chinese only (Traditional / zh-TW).** All user-facing copy — headings, instructions,
  venue info, form labels, alerts — is in Traditional Chinese.
- **No English version of this microsite exists.** The only English strings are:
  - The company legal name in the footer ("TAIWAN SHIN KONG SECURITY CO., LTD").
  - Hidden CSS-anchor icon labels ("Location", "Time", "Partners") and `contact.php`'s
    English alerts — both are template residue, not a real EN locale.
- **Redesign implication:** if the portal is bilingual, an EN version of this microsite must be
  authored from scratch.

---

## 7. Dynamic / Admin-Driven Content

- **Essentially none.** All copy (steps, hours, address, directions, dropdown options) is
  **hard-coded in static HTML** — not rendered from the Admin/DB.
- The **only** dynamic touchpoints are outbound:
  1. CTA → external **SimplyBook.me** booking system (`skdemo.simplybook.me`) — the actual
     reservation engine lives entirely off-site.
  2. Inquiry form → `POST www2.sks.com.tw/msg/api/msg` (main portal's message API; leads land
     in the portal's Admin message store).
- Carousel images and content are not Admin-managed here; updates require editing the HTML.

---

## 8. Redesign Notes / Flags

- This is a **detached single-page micro-landing** built on a stale generic agency theme;
  ~90% of bundled assets are unused.
- Core reservation logic is **delegated to SimplyBook.me** — redesign should decide whether to
  keep that external dependency or bring booking in-house/into the portal.
- The instruction copy embeds **policy data** (5–15 pax/slot, Mon–Fri 10–11 & 15–16,
  online-only, 3-day change notice) — these are business rules to preserve.
- Two forms of "contact": a **booking CTA** (primary) and a **general inquiry form** (secondary,
  reuses portal API). Hero slides and nav are empty shells.
- `contact.php` and `index - 複製.html` are dead artifacts.
