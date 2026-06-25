# SKS Portal — Tech Stack & Dependency Inventory

> Source: published build at `/Users/reiser/repo/sks` (新光保全 / Shin Kong Security corporate portal).
> This is a **published/precompiled deployment artifact**, not a source tree: controllers and models are compiled into `bin/sks.dll`. Only Razor `.cshtml` views, config, static assets, and the EDMX diagram remain as files. Inventory derived from `web.config`, `Global.asax`, `NLog.config`, `ApplicationInsights.config`, `bin/*.dll` version strings, and `Scripts/` + `Content/` asset headers.

---

## 1. Platform / Runtime

| Component | Value | Notes |
|---|---|---|
| Application type | ASP.NET MVC 5 (System.Web, IIS-hosted) | `Global.asax` → `sks.MvcApplication` |
| .NET Framework | **4.8** | `sks.dll` `TargetFrameworkAttribute = .NETFramework,Version=v4.8`; `<compilation targetFramework="4.8">` |
| httpRuntime targetFramework | **4.6.1** (mismatch with compilation 4.8) | `<httpRuntime targetFramework="4.6.1">` — behavioral quirks fixed at 4.6.1 level despite compiling for 4.8 |
| Web host | IIS (integrated mode) | `system.webServer` modules, URL Rewrite rules present |
| Compiler | Roslyn (DotNetCompilerPlatform 2.0.1.0) | `bin/roslyn/` ships the in-box C#/VB Roslyn compilers |
| Primary assembly | `bin/sks.dll` (652 KB, built 2025-05-21) | Contains controllers, models, EF context; PDB present |

## 2. Web Framework Stack

| Component | Version | Evidence |
|---|---|---|
| ASP.NET MVC | **5.2.7** (file 5.2.61128.0) | `System.Web.Mvc.dll`; binding redirect → 5.2.7.0 |
| Razor view engine | System.Web.Razor / WebPages **3.0.0** | `webpages:Version = 3.0.0.0`; Areas web.config |
| System.Web.WebPages | 3.0.0 | binding redirect |
| System.Web.Optimization (bundling) | 1.1.0 | uses WebGrease 1.6.5135 |
| WebGrease | 1.6.5135.21930 | bundling/minification |
| Antlr3.Runtime | 3.5.0.2 | Razor/optimization dependency |

Auth: **Forms Authentication** (`<authentication mode="Forms">`), login at `~/Admin/AuthAdmin/Login`, default `~/Admin/homeadmin/Index`, ticket timeout 2880 min (48 h). `httpCookies requireSSL="true"`. Admin area protected; HTTP→HTTPS redirect enforced via URL Rewrite. Two disabled rewrite rules exist for admin IP allow-listing (`192.168.210.123`, `220.135.3.41`).

## 3. Data / ORM

| Component | Version | Evidence |
|---|---|---|
| Entity Framework | **6.2.0** (file 6.2.61023.0) | `EntityFramework.dll` + `EntityFramework.SqlServer.dll` |
| Modeling approach | **Database-First (EDMX)** | `Models/SksDBModel.edmx.diagram`; connection string uses `res://*/Models.SksDBModel.csdl|ssdl|msl` metadata |
| Database | Microsoft SQL Server | `System.Data.SqlClient`; `SqlConnectionFactory` |
| Connection | `SksDBEntities` → `data source=127.0.0.1; catalog=SksDB; user id=webuser` (local SQL, MARS enabled) | **Credentials in plaintext in web.config** |

## 4. Supporting Server-Side Libraries (bin/)

| Library | Version | Purpose |
|---|---|---|
| Newtonsoft.Json | **12.0.1** | JSON serialization (binding redirect → 12.0.0.0) |
| AutoMapper | **8.1.0** | Object-to-object mapping |
| ClosedXML | **0.95.3** | Excel (.xlsx) generation |
| DocumentFormat.OpenXml | **2.7.2** | OpenXML (Office docs), ClosedXML dependency |
| ExcelNumberFormat | (2020) | ClosedXML dependency |
| NLog | **4.5.11** | File + EventLog logging (see NLog.config) |
| MvcSiteMapProvider | **4.6.26** (+ WebActivator) | Navigation/breadcrumbs from `Mvc.sitemap` |
| BouncyCastle.Crypto | **1.8.6** | Cryptography |
| ICSharpCode.SharpZipLib | **1.2.0** | Zip/compression |
| AntiXssLibrary / HtmlSanitizationLibrary | 4.x (2014) | XSS encoding / HTML sanitization — **very old (2014)** |
| Microsoft.Web.Infrastructure | (2012) | ASP.NET infra |
| System.Net.Http.Formatting | (2018) | Web API formatting types |
| System.ValueTuple / System.IO.* | shims | framework polyfills |

## 5. Telemetry & Logging

| Component | Version | Notes |
|---|---|---|
| Application Insights | **2.9.1** | `Microsoft.ApplicationInsights.dll` + `Microsoft.AI.*` modules; `ApplicationInsights.config`. **No `<InstrumentationKey>` present** in config — telemetry likely inert / set elsewhere |
| NLog | 4.5.11 | Targets: rolling `App_Data/Logs/${shortdate}.txt` (Trace–Error), `App_Data/Logs/${shortdate}/FatalFile.txt` (Fatal), Windows EventLog (Fatal). `App_Data/Logs/` actively written (entries through Jun 2026) |

## 6. Front-End Libraries

### Admin back-office (Content/Admin, Content/AdminLTE-3.0.4, Scripts/Admin)

| Library | Version | Notes |
|---|---|---|
| AdminLTE | **3.0.4** | Admin dashboard theme |
| Bootstrap | **4.4.1** | bundled in AdminLTE plugins |
| jQuery | **3.4.1** | AdminLTE plugins/jquery |
| Font Awesome Free | **5.13.0** | icons |
| DataTables | **1.10.20** | tables (bs4 + responsive) |
| CKEditor | **4.11.3** (2019) | classic CKEditor 4 build, custom configs; rich-text editing |
| Select2 | **4.0.13** | enhanced dropdowns |
| Spectrum Colorpicker | **2.0.0** (folder named `spectrum-2.0.5`) | color picker — folder/version label mismatch |
| Toastr | (css only present) | notifications |
| bootstrap4-duallistbox, bs-custom-file-input, icheck-bootstrap | (AdminLTE plugins) | form widgets |

### Public front-end (Content/sks, Scripts/front)

| Library | Version | Notes |
|---|---|---|
| jQuery | **3.4.1** | bundled in `Content/sks/js/vendors.js` |
| bootstrap-datetimepicker (Eonasdan) | **2.4.4** | `Content/bootstrap-datetimepicker-master` — **legacy, abandoned upstream** |
| Year-Picker-Text-Input (yearpicker) | (dist only) | year picker widget |
| jQuery Validation + Unobtrusive | (Scripts/jquery.validate*, 2024) | client validation; `ClientValidationEnabled`, `UnobtrusiveJavaScriptEnabled` = true |
| Custom JS | `city.js`, `edit.js`, `location.js`, `script.js`, `cusom.js`, `grecaptcha.js` | app logic incl. Google reCAPTCHA |

Localization: bilingual (zh-Hant default + English). Satellite resources `bin/en/sks.resources.dll`, `bin/zh-Hant/*.resources.dll`. Separate EN admin controllers/views (`*EnAdmin`).

## 7. Application Shape / Areas

- **Areas**: single `Admin` area (CMS back office). Controllers compiled in `sks.dll`; views under `Areas/Admin/Views/*`.
- Admin view modules: News, Event, Product, ProductCategory, Banner, Award, Contact, QA, Manager, Logs, FinancialShareholder, ShareholderPolicy, Auth, Home — most with parallel `*En` (English) variants.
- **Public sections** (folders at root, served as content/views): `about`, `apache`, `catalog`, `ebook`, `reservation`, `images`.
- **File uploads**: `~/FileUploads` (configurable via appSetting `FileUploads`); upload dirs present.
- **App_Data**: holds `Logs/` (active NLog output) and archived `Logs.zip`.
- Misc deployed assets at web root: `2sapp.apk` (~48 MB Android app), `sitemap.xml`/`ror.xml`, `robots.txt`, Google site-verification HTML, `apache.zip`, legacy `20200803Web.config` backup.

## 8. Notable Config / Security Observations

- **Plaintext secrets in `web.config`**: SQL connection password (`UmkvBQseknew`), SMTP password (`1qaz2wsx`) — should be moved out / rotated.
- **SMTP**: internal relay `192.168.1.139:25`, sender `Notify@notesmtp.com.tw` ("新光保全").
- `customErrors mode="On"` globally and for Admin (404/500 redirects) — good.
- `maxRequestLength = 1024000` KB (~1 GB) upload cap — large; review.
- Application Insights configured but **no instrumentation key** in the file.
- `httpRuntime targetFramework="4.6.1"` while compiling against 4.8 — intentional quirks-mode pin, worth revisiting.

---

## 9. Outdated / Risk Flags (for redesign)

| Item | Installed | Latest / Status | Risk |
|---|---|---|---|
| .NET Framework 4.8 | 4.8 | Windows-only, in maintenance; no new features. Migrate to .NET 8/9 | High (modernization) |
| ASP.NET MVC 5 | 5.2.7 | End of feature life; superseded by ASP.NET Core MVC | High |
| Entity Framework | 6.2.0 | EF6 latest 6.5.x; EF Core is current. EDMX (Database-First) **not supported in EF Core** | High (migration blocker) |
| Newtonsoft.Json | 12.0.1 (2018) | 13.0.x; 12.x has known DoS CVE considerations | Medium |
| AutoMapper | 8.1.0 (2019) | 13.x+ | Medium |
| CKEditor | 4.11.3 (2019) | CKEditor 4 **EOL (security support ended)**; CKEditor 5 current | **High (security)** |
| jQuery | 3.4.1 (2019) | 3.7.x; 3.4.1 has known XSS CVEs (CVE-2020-11022/11023) | **High (security)** |
| Bootstrap | 4.4.1 (2019) | 5.x; BS4 EOL | Medium |
| AdminLTE | 3.0.4 | 3.2.x / 4.x | Low/Medium |
| bootstrap-datetimepicker | 2.4.4 (Eonasdan) | Abandoned; replaced upstream by Tempus Dominus | Medium |
| Select2 | 4.0.13 | 4.1.x | Low |
| Spectrum | 2.0.0 | 1.8.x current line / largely unmaintained | Low |
| Font Awesome | 5.13.0 | 6.x | Low |
| DataTables | 1.10.20 | 1.13.x / 2.x | Low |
| AntiXss / HtmlSanitization libs | 4.x (2014) | Legacy Microsoft Web Protection Library (deprecated) | Medium |
| Application Insights | 2.9.1 (2018) | 2.22.x; or OpenTelemetry | Low |
| SQL/SMTP plaintext credentials | in web.config | Move to secrets store; rotate | **High (security)** |

### Redesign migration call-outs
1. **EDMX → EF Core**: Database-First EDMX has no EF Core path; the data layer must be re-scaffolded (DbContext scaffolding from existing `SksDB`).
2. **CKEditor 4 + jQuery 3.4.1** are the most urgent security items regardless of framework migration.
3. **Forms Auth** must be re-platformed to ASP.NET Core Identity / cookie auth if moving to .NET Core.
4. Secrets in `web.config` must be externalized (User Secrets / Key Vault / env vars).
