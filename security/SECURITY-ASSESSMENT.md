# SKS Portal — Consolidated Security Assessment / 新光保全入口網站資安評估報告

**Target:** SKS ASP.NET MVC portal (`/Users/reiser/repo/sks`) — published build, .NET Framework 4.8 / ASP.NET MVC 5.2.7
**Audience:** SKS CISO (王秉麒)
**Assessment type:** Static review of a deployed webroot (config, views, JS/vendor libraries, `bin/` assemblies). Compiled controllers were not fully decompiled.
**Date:** 2026-06-05

---

## 1. Executive Summary / 摘要

The portal carries one **Critical-class systemic failure** — multiple production TLS **private keys** (including the payment domain `*.skspay.com.tw` and a currently-valid 2026 `skgps.com.tw` key) are sitting in the webroot as downloadable `.zip` / `.pfx` / `.txt` / `.pem` artifacts, and the live `web.config` plus a stale dated backup leak cleartext database and SMTP credentials (including a SQL `sa` password). These are not theoretical: the keys are cryptographically verified to match their certificates, and `web.config` defines no request-filtering rules that would block the archive/key extensions. Secondary issues compound the picture: admin passwords are stored in a 50-char column with **no salted hash**, destructive admin operations run over **GET (CSRF)**, the admin area has **no config-layer authorization** (relying solely on compiled attributes), an **end-of-life CKEditor 4.11.3** drives stored content rendered raw to public visitors, and the front-end vendor stack (jQuery 3.4.1, Bootstrap 3/4, AdminLTE) is years out of date. **Governance priority:** treat every exposed key and credential as compromised, rotate/revoke immediately, strip secrets from the webroot, then close the authorization and CSRF gaps before the planned redesign carries this debt forward.

### Severity counts / 風險統計

| Severity | Count |
|----------|-------|
| Critical | 4 |
| High | 7 |
| Medium | 14 |
| Low | 11 |
| Info | 6 |
| **Total** | **42** |

> Note: counts include verdict-adjusted severities. Several findings overlap (the same secrets surface in the live config, the backup config, and the bin assembly); they are listed separately because each represents a distinct artifact/remediation target.

---

## 2. Risk-Ranked Findings / 風險排序總表

| # | Severity | Title | Category | Location |
|---|----------|-------|----------|----------|
| 1 | Critical | Wildcard TLS private key + cert archive in webroot (`2022wildcard.sks.com.tw.zip`) | Secrets | `apache/2022wildcard.sks.com.tw.zip` |
| 2 | Critical | `apache.zip` in webroot contains wildcard cert + private key | Secrets | `apache.zip` |
| 3 | Critical | PFX (cert+key) archives in webroot — payment domain `skspay` + `sks` | Secrets | `ebook/temp/wildcard.skspay.com.tw.zip` ; `apache/wildcard_sks.com.tw/IIS/wildcard.sks.com.tw.pfx` |
| 4 | Critical | Additional PEM/private-key archives (current 2026 `skgps.com.tw` key) | Secrets | `ebook/temp/20260115jay.zip` ; `ebook/temp/2026sks.combo.zip` |
| 5 | High | Unzipped wildcard RSA private key served as `.txt` | Secrets | `apache/wildcard_sks_com_tw.txt` |
| 6 | High | Hardcoded SQL Server connection-string credentials (`webuser`) | Secrets | `web.config:156` |
| 7 | High | Old config backup in webroot leaks `sa` password + SMTP secret | Exposed-Files | `20200803Web.config` |
| 8 | High | Backup `Web.config` leaks DB `sa` + SMTP creds (EXP-006, dup artifact) | Secrets | `20200803Web.config` |
| 9 | High | Admin passwords stored without strong hash (nvarchar(50), no salt) | Crypto | `bin/sks.dll` (EF `Admin.Password`) |
| 10 | High | State-changing Delete operations via GET links (CSRF) | CSRF | 13 `Areas/Admin/Views/*Admin/Index.cshtml` |
| 11 | High | CKEditor 4.11.3 (EOL) in admin — multiple known XSS CVEs | Vuln-Dependency | `Content/Admin/ckeditor/ckeditor.js` |
| 12 | Medium | Hardcoded SMTP credentials in `web.config` + backup | Secrets | `web.config:30` ; `20200803Web.config:26` |
| 13 | Medium | Live `web.config` exposes DB + SMTP creds (defense-in-depth) | Config | `web.config` |
| 14 | Medium | No URL-authorization rule protects `/Admin` in `web.config` | AuthZ | `web.config:54-61` |
| 15 | Medium | Compiled assembly + PDB debug symbols present in `bin` | Exposed-Files | `bin/sks.dll` ; `bin/sks.pdb` |
| 16 | Medium | Stored XSS: CKEditor news content rendered unencoded | XSS | `Views/News/Detail.cshtml:82` |
| 17 | Medium | Stored XSS: shareholder policy content rendered unencoded | XSS | `Views/Shareholder/Index.cshtml:930-937` |
| 18 | Medium | Stored XSS: event content/titles/link labels rendered unencoded | XSS | `Views/Event/Index.cshtml` ; `Views/Event/Preview.cshtml` |
| 19 | Medium | Unencoded model data in href/style attributes (attribute XSS) | XSS | `Views/Event/Index.cshtml` ; `Views/Event/Preview.cshtml` |
| 20 | Medium | Forms auth not restricted to SSL; 48h sliding timeout | Config | `web.config:50-52` |
| 21 | Medium | Missing HTTP security response headers (HSTS, CSP, XFO, nosniff) | Config | `web.config:98-142` |
| 22 | Medium | File-upload forms rely only on client-side `accept` | Config | multiple `Areas/Admin/Views/.../Edit.cshtml` |
| 23 | Medium | jQuery 3.4.1 — CVE-2020-11022 / 11023 XSS | Vuln-Dependency | `Content/AdminLTE-3.0.4/...jquery.js` ; `Content/sks/js/vendors.js` |
| 24 | Medium | jQuery Validation 1.11.1 — outdated, ReDoS-class issues | Vuln-Dependency | `Scripts/jquery.validate.js` |
| 25 | Medium | Bootstrap 4.4.1 + 3.1.1 — XSS CVEs / EOL | Vuln-Dependency | `Content/AdminLTE-3.0.4/...bootstrap.bundle.min.js` ; `reservation/js/bootstrap.min.js` |
| 26 | Medium | AdminLTE 3.0.4 — outdated, ships vulnerable jQuery/Bootstrap | Vuln-Dependency | `Content/AdminLTE-3.0.4/dist/js/adminlte.js` |
| 27 | Medium | Newtonsoft.Json 12.0.1 — CVE-2024-21907 (deep-nesting DoS) | Vuln-Dependency | `bin/Newtonsoft.Json.dll` |
| 28 | Medium | Source-backup HTML files in `/about/backup` served from webroot | Exposed-Files | `about/backup/` |
| 29 | Medium | Mobile app APK published at webroot (`2sapp.apk`) | Exposed-Files | `2sapp.apk` |
| 30 | Medium | `FileUploads/Document` hosts 639 corporate/financial docs (PII risk) | PII | `FileUploads/Document/` |
| 31 | Low | Internal IPs / admin allowlist / infra hostnames leaked in config | Config | `web.config:136-137,156,30` |
| 32 | Low | `httpCookies requireSSL` set but `httpOnlyCookies` not explicit | Config | `web.config:46` |
| 33 | Low | App logs accumulate under `App_Data/Logs` (PII / availability) | Exposed-Files | `App_Data/Logs/` ; `App_Data/Logs.zip` |
| 34 | Low | Destructive GET on LogsAdmin/BannerAdmin (CSRF + audit-evidence loss) | CSRF | `Areas/Admin/Views/LogsAdmin/Index.cshtml` ; `BannerAdmin/Edit.cshtml` |
| 35 | Low | Login-record viewer exposes account names + source IPs; deletable | PII | `Areas/Admin/Views/LogsAdmin/Index.cshtml` |
| 36 | Low | Sitemap role-visibility typos weaken intended gating | AuthZ | `Mvc.sitemap:38,75,82` |
| 37 | Low | Disabled IP-allowlist rules leave `/Admin/AuthAdmin` internet-exposed | Config | `web.config:116-139` |
| 38 | Low | File-upload admin views write to web-served `~/FileUploads` | Config | `Areas/Admin/Views/.../Edit.cshtml` |
| 39 | Low | `robots.txt` confirms sensitive directories (recon aid) | Config | `robots.txt` |
| 40 | Low | Self-XSS: selected filename inserted via jQuery `.html()` | XSS | `Areas/Admin/Views/AwardAdmin/Edit.cshtml:109` (+2) |
| 41 | Low | select2 4.0.13 + legacy 3.1.12 copy | Vuln-Dependency | `Content/Admin/select2/js/` |
| 42 | Low | Spectrum 2.0.0, datetimepicker 2.4.4, EF6 6.2.0, MVC 5.2.7, AI SDK 2.9.1, MvcSiteMapProvider 4.6.26 — stale libs | Vuln-Dependency | `bin/` ; `Content/` |

---

## 3. Detailed Findings by Severity / 詳細風險

### CRITICAL

#### C-1. Wildcard TLS private key + certificate archive exposed in webroot
- **Location:** `/Users/reiser/repo/sks/apache/2022wildcard.sks.com.tw.zip` (and the unzipped tree under `apache/wildcard_sks.com.tw/`)
- **Evidence:** `unzip -l` shows `wildcard.sks.com.tw.crt` (2484 B) + `wildcard.sks.com.tw.key` (1679 B). The `.key` is an **unencrypted** RSA private key (`openssl rsa -check` = OK, no passphrase). Key modulus MD5 `e50e2d59…` matches the certificate modulus exactly. Cert subject `CN=*.sks.com.tw`, SAN `*.sks.com.tw` + `sks.com.tw`, issued to 台灣新光保全股份有限公司.
- **Impact:** Whoever downloads this key can impersonate / decrypt TLS for **every** `*.sks.com.tw` host (full MITM across all subdomains). Wildcard key compromise is the most severe TLS failure. The cert expired 2023-04-14, but (a) wildcard renewals routinely reuse the same key pair — so the current production cert may be compromised; (b) the key still decrypts any historically captured non-PFS sessions.
- **Recommendation:** Remove from webroot and source. Assume the key is compromised: **revoke and reissue** `*.sks.com.tw`. Confirm whether the current production cert reuses this key pair; if so it must be reissued with a fresh key.
- **Concrete step:** `rm /Users/reiser/repo/sks/apache/2022wildcard.sks.com.tw.zip` and delete the `apache/wildcard_sks.com.tw/` directory; then open a CA ticket to revoke + reissue with a newly generated key.

#### C-2. `apache.zip` at site root contains wildcard cert + private key
- **Location:** `/Users/reiser/repo/sks/apache.zip`
- **Evidence:** `unzip -l` → `apache/wildcard.sks.com.tw.crt`, `apache/wildcard.sks.com.tw.key` (1679 B), `apache/SO3-Bundle.crt`. The `.key` modulus MD5 `e50e2d59…` cryptographically matches the Sectigo-issued `*.sks.com.tw` wildcard. `.zip` is a default IIS static MIME type and `web.config` has **no** requestFiltering/hiddenSegments rule blocking it — `GET /apache.zip` is served to any anonymous requester.
- **Impact:** A top-level guessable archive (`/apache.zip`) hands the wildcard private key + chain to anyone. Same full-subdomain TLS impersonation/decryption as C-1.
- **Recommendation:** Remove `/apache.zip` immediately, revoke/reissue the wildcard cert, and audit web/access logs for prior downloads of `/apache.zip`.
- **Concrete step:** `rm /Users/reiser/repo/sks/apache.zip`; pull IIS logs and grep for `apache.zip`, `2022wildcard`, `.pfx`, `.key`, `wildcard_sks_com_tw.txt` to gauge prior exposure.

#### C-3. PFX (cert+key) bundles in webroot — payment domain `skspay` + `sks`
- **Location:** `/Users/reiser/repo/sks/ebook/temp/wildcard.skspay.com.tw.zip` → `wildcard.skspay.com.tw.pfx` (4437 B, dated 2025-08-15) ; `/Users/reiser/repo/sks/apache/wildcard_sks.com.tw/IIS/wildcard.sks.com.tw.pfx` (6668 B)
- **Evidence:** Both files have PKCS#12 magic bytes and SHA1/2048-iter MAC — valid cert+private-key bundles (a PFX always carries the private key). No `requestFiltering`/`fileExtension` denials in `web.config`, and no `web.config` inside `ebook/`, so IIS serves `.zip`/`.pfx` as static content. A sibling `ebook/temp/2026sks.combo.pem.txt` also contains a plaintext, unencrypted `-----BEGIN PRIVATE KEY-----` requiring no cracking at all.
- **Impact:** Leaked production wildcard private key for the **payment** domain `skspay.com.tw` enables TLS impersonation and passive decryption of payment-related traffic — especially severe. The PFX is not blank-password (blank MAC verify failed), but an exposed wildcard PFX is brute-forceable offline, and the sibling PEM needs no password.
- **Recommendation:** Remove both PFX artifacts and the plaintext PEM from the webroot. Treat the `*.skspay.com.tw` and `*.sks.com.tw` keys as compromised — revoke/reissue. If a PFX password is reused elsewhere, rotate it.
- **Concrete step:** `rm -rf /Users/reiser/repo/sks/ebook/temp` after confirming nothing live depends on it; revoke/reissue `*.skspay.com.tw`.

#### C-4. Additional PEM/private-key archives — current 2026 `skgps.com.tw` key
- **Location:** `/Users/reiser/repo/sks/ebook/temp/20260115jay.zip` ; `/Users/reiser/repo/sks/ebook/temp/2026sks.combo.zip`
- **Evidence:** `20260115jay.zip` → `file.nokey.pem` (Sectigo cert `CN=skgps.com.tw`, valid **Jan 9 2026 – Feb 9 2027**) + `file.key`, a valid RSA private key whose modulus MD5 `a3055cd8…` matches the cert. `2026sks.combo.zip` → a 4-cert chain plus a matching RSA private key for the same cert. These are **current, live** keys.
- **Impact:** Unlike the expired wildcards, this is a **currently-valid** production private key for `skgps.com.tw`, directly usable for TLS impersonation and traffic decryption until revoked. This is the most operationally dangerous of the key leaks.
- **Recommendation:** Remove the entire `ebook/temp` staging area from the webroot. Revoke and reissue the `skgps.com.tw` certificate now (do not wait for 2027 expiry). Stop staging cert material inside web-served directories.
- **Concrete step:** Revoke `skgps.com.tw` today, reissue with a fresh key, then `rm -rf /Users/reiser/repo/sks/ebook/temp`.

---

### HIGH

#### H-1. Unzipped wildcard RSA private key served as `.txt`
- **Location:** `/Users/reiser/repo/sks/apache/wildcard_sks_com_tw.txt` (byte-identical copy at `apache/wildcard_sks.com.tw/Apache/wildcard.sks.com.tw.key`)
- **Evidence:** Complete valid RSA private key (`openssl rsa -check` = OK), modulus MD5 `e50e2d59…` matches the `*.sks.com.tw` cert. `.txt` is in the default IIS MIME map and served verbatim as `text/plain` — no archive extraction needed.
- **Impact:** The wildcard key in the most trivially fetchable form possible. Cert is expired (notAfter Apr 2023), which is the only reason this is High not Critical, but key reuse on renewal and retrospective decryption of captured non-PFS traffic keep it serious.
- **Recommendation:** Delete the `.txt` and `.key` copies; revoke/reissue as in C-1; move all cert material out of the served tree.
- **Concrete step:** `rm /Users/reiser/repo/sks/apache/wildcard_sks_com_tw.txt` and the nested `apache/wildcard_sks.com.tw/` copies.

#### H-2. Hardcoded SQL Server connection-string credentials in `web.config`
- **Location:** `/Users/reiser/repo/sks/web.config:156`
- **Evidence:** `…user id=webuser;password=UmkvBQseknew;…initial catalog=SksDB…` in cleartext. No XDT transform tokens, env-var placeholders, or config-builder references — the password is baked in literally. Section is not encrypted via `aspnet_regiis`.
- **Impact:** Any indirect disclosure (backup leak, path traversal, source disclosure, repo/file-share access) yields working DB credentials for `SksDB` (admin Manager accounts, contact PII, login logs). `data source=127.0.0.1` means the DB is loopback-bound, so exploitation needs a secondary file-disclosure vector — capping this at High not Critical.
- **Recommendation:** Encrypt `<connectionStrings>` (`aspnet_regiis -pe "connectionStrings"`) or use Integrated Security / a secret store. Rotate the `webuser` password.
- **Concrete step:** Rotate `webuser`, then run `aspnet_regiis -pe "connectionStrings" -app "/"` (or move to env vars) on the deployed host.

#### H-3 / H-4. Old config backup in webroot leaks DB `sa` password + SMTP secret
- **Location:** `/Users/reiser/repo/sks/20200803Web.config:109,26`
- **Evidence:** Line 109: `…user id=sa;password=@trustno1;…catalog=SksDB`. Line 26: SMTP `host=192.168.1.139 userName=Notify@notesmtp.com.tw password=1qaz2wsx`. This backup sits in the same webroot as the live `web.config`.
- **Impact:** Discloses the SQL Server **`sa` (sysadmin)** password `@trustno1` — strictly more privileged than anything in the live config — plus a still-live SMTP credential (identical to the live config) and internal IPs. The live DB account now uses `webuser`, so `sa` appears partially rotated, but it reveals a reused weak-password pattern and may still be valid. **Note:** the headline "served as plaintext over HTTP" claim does **not** hold on a default IIS — request filtering denies the `.config` *extension*, not just the literal `web.config` name, so `/20200803Web.config` returns 404 by default. The real exposure vector is source-control / filesystem / backup access. This caps it at High, not Critical.
- **Recommendation:** Delete the backup from the webroot. Rotate the `sa` password and disable/limit the `sa` account. Never keep dated config backups in a deployable directory.
- **Concrete step:** `rm /Users/reiser/repo/sks/20200803Web.config`; rotate `sa` and confirm `sa` is disabled for remote login.

#### H-5. Admin passwords stored without a strong hash (nvarchar(50), no salt)
- **Location:** `/Users/reiser/repo/sks/bin/sks.dll` — EF model `Admin.Password Type="nvarchar" MaxLength="50"`
- **Evidence:** EF storage + conceptual model define `Password` as `nvarchar(50) NOT NULL`. A full string scan of `sks.dll` returned **zero** occurrences of MD5/SHA1/SHA256/SHA512/BCrypt/PBKDF2/Rfc2898/Salt/PasswordHash. Auth is custom Forms auth (`GetByAccount` + `FormsAuthenticationTicket`), not ASP.NET Identity. A 50-char column cannot hold any standard salted hash (BCrypt = 60, SHA-256 hex = 64, PBKDF2 base64 ~88), consistent with plaintext or a reversible cipher.
- **Impact:** Any DB read (SQLi, backup leak, or the cleartext config creds above) yields directly usable or trivially reversible admin credentials with no per-user salt → full admin/content/PII compromise. OWASP A02/A07 cryptographic failure.
- **Recommendation:** Store admin passwords with a salted adaptive hash (PBKDF2/BCrypt/Argon2). Widen the column, migrate existing accounts on next successful login, remove any reversible scheme.
- **Concrete step:** Confirm storage by decompiling `AuthAdminController.ValidateUser`/`GetByAccount`; implement PBKDF2 (Rfc2898) hashing and a one-time on-login migration. (Requires source access — flagged for the dev team.)

#### H-6. State-changing Delete operations performed via GET links (CSRF)
- **Location:** 13 views, e.g. `Areas/Admin/Views/AwardAdmin/Index.cshtml:117`, `NewsAdmin/Index.cshtml:151`, `ProductAdmin/Index.cshtml:151`, `ManagerAdmin/Index.cshtml:140`, `LogsAdmin/Index.cshtml:38`, plus Banner/Event/FinancialShareholder admins.
- **Evidence:** `@Html.ActionLink("刪除","Delete", new { id = item.ID })` renders a plain `<a href="/Admin/.../Delete?id=…">` GET link; `LogsAdmin` builds `href='/Admin/LogsAdmin/Delete?createTime='+date` in JS. The `js-delete-confirm` handler only calls `confirm()` — it does **not** convert to POST. No POST form / token-bearing AJAX exists for these entities, so Delete must accept GET for the live UI to work. The team uses `@Html.AntiForgeryToken()` on 19 Edit forms, so this is an inconsistency, not a capability gap.
- **Impact:** A GET link can never carry an anti-forgery token. Any page an authenticated admin visits (or a crawler/prefetcher/`<img>`) can trigger deletion of CMS records — textbook CSRF, plus accidental-deletion exposure. Blast radius is content records, not funds/account-takeover → High not Critical.
- **Recommendation:** Convert Delete to POST: per-row form with `@Html.AntiForgeryToken()`, controller action decorated `[HttpPost][ValidateAntiForgeryToken]`. Confirm no `[HttpGet] Delete` remains.
- **Concrete step:** Replace each `@Html.ActionLink("刪除","Delete",…)` with a mini POST form + token, and add `[HttpPost][ValidateAntiForgeryToken]` server-side.

#### H-7. CKEditor 4.11.3 (EOL) bundled in admin area — multiple known XSS CVEs
- **Location:** `/Users/reiser/repo/sks/Content/Admin/ckeditor/ckeditor.js` (`version:"4.11.3"`), `CHANGES.md`, `README.md` (©2003-2019)
- **Evidence:** Version string confirmed. CKEditor 4 is end-of-life (post-4.22 fixes are behind the paid Extended Support Model); 4.11.3 predates CVE-2021-33829, 2021-37695, 2021-41164/41165, 2023-28439, 2024-24815/24816. It is actively loaded in admin edit views (e.g. `NewsAdmin/Edit.cshtml:105-114` → `CKEDITOR.replace` on `Content`), and that content is rendered raw to public visitors (`Views/News/Detail.cshtml:82`).
- **Impact:** The editor is missing essentially the entire post-2019 XSS patch set. Stored HTML that bypasses CKEditor 4's outdated sanitizer leads to admin-session XSS and persisted XSS served to all public visitors.
- **Recommendation:** Migrate to CKEditor 5 (actively maintained). Short-term: buy the CKEditor 4 LTS/ESM build (≥4.22.x), disable HTML source mode for low-trust roles, and re-sanitize editor output server-side before render.
- **Concrete step:** Plan CKEditor 5 migration in the redesign; meanwhile add server-side HTML sanitization on the save path for all CKEditor-backed fields.

---

### MEDIUM

#### M-1. Hardcoded SMTP credentials in `web.config` (and backup)
`web.config:30` / `20200803Web.config:26`: `<network defaultCredentials="true" host="192.168.1.139" port="25" userName="Notify@notesmtp.com.tw" password="1qaz2wsx" />`. **Impact:** Cleartext weak keyboard-walk SMTP password + internal IP leaked. `defaultCredentials="true"` means `System.Net.Mail` ignores the explicit creds at runtime, so the mail-relay-abuse vector is largely negated — but the secret is disclosed and likely reused. **Recommendation/step:** Remove the password, move SMTP config to a protected/encrypted store, and rotate `1qaz2wsx` (it is now compromised and trivially guessable).

#### M-2. Live `web.config` exposes DB + SMTP creds (defense-in-depth)
`web.config:30,156` store DB and SMTP secrets in cleartext, unencrypted. IIS blocks direct `web.config` download, so the live file leaks only via a secondary defect (source disclosure/LFI, the `sks.pdb` path leak, or the downloadable backup H-3). **Step:** Encrypt the `connectionStrings` + `mailSettings` sections (`aspnet_regiis -pe`) or move to env/secret store; rotate both passwords.

#### M-3. No URL-authorization rule protects `/Admin`
`web.config:54-61` — the `<location path="Admin">` block contains **only** `<customErrors>`; no `<authorization><deny users="?"/>` anywhere (confirmed by grep across all `*.config`). Two IP-based admin rules exist but are `enabled="false"`. Authorization depends entirely on compiled `[Authorize]`/`SystemManagerAuthorize` attributes; a single missing attribute (or a `NoAuthorize` action) exposes admin functionality anonymously. **Step:** Add `<system.web><authorization><deny users="?"/></authorization></system.web>` to the Admin `<location>`, with an explicit allow-carve-out for `AuthAdmin/Login` + `Logout`.

#### M-4. Compiled assembly + PDB debug symbols in `bin`
`bin/sks.dll` (652 KB .NET assembly, trivially decompilable) + `bin/sks.pdb` (1.3 MB, maps to absolute source paths like `C:\GitHub\sks\…`); 7 `.pdb` total. IIS blocks `/bin` over HTTP by default, so this is a conditional source-disclosure/hardening issue. **Step:** Ship Release builds without `.pdb`; remove `.pdb` from the deployed `bin`; treat the assembly as if source were public (no hardcoded secrets).

#### M-5 to M-8. Stored / attribute XSS sinks (admin-authored content rendered raw)
- **M-5** `Views/News/Detail.cshtml:82` — `@Html.Raw(Model.Content)` (CKEditor news HTML).
- **M-6** `Views/Shareholder/Index.cshtml:930-937` — `@Html.Raw(...ShareholderPolicy.Content/_EN)`.
- **M-7** `Views/Event/Index.cshtml` + `Preview.cshtml` — `@Html.Raw` of Title/Content/LinkName; `LinkName` regex `<(?!span).*?>` strips non-span tags but **preserves `<span onmouseover=…>`**, so it is still XSS-able.
- **M-8** `Views/Event/{Index,Preview}.cshtml` — unencoded model values in `href="@…Link1"` / `style="background:@…"`; a `javascript:` Link or crafted color breaks out of the attribute.

All four are admin-authored CMS fields gated behind admin auth (Forms login), so weaponizing requires admin compromise or a chained admin-side injection — hence Medium. But the output reaches **all public visitors**. **Step:** Add allow-list HTML sanitization (HtmlSanitizer/Ganss) on save and/or render; for plain-text fields (Title, LinkName) drop `@Html.Raw` and use default encoding; validate Link schemes (reject non-`http(s)`) and color/filename fields against strict patterns; add a CSP as defense-in-depth.

#### M-9. Forms auth not restricted to SSL; 48h sliding timeout
`web.config:50-52` — `<forms … timeout="2880" />` (48 h), no `requireSSL="true"`, sliding expiration on. **Step:** Add `requireSSL="true"`, reduce timeout to 30-60 min, and set `slidingExpiration="false"` for the admin area.

#### M-10. Missing HTTP security response headers
`web.config:98-142` — no `<httpProtocol><customHeaders>`, no HSTS, CSP, X-Frame-Options, X-Content-Type-Options, or Referrer-Policy. Site is exposed to clickjacking, MIME sniffing, and first-request SSL-stripping. **Step:** Add a `customHeaders` block with `Strict-Transport-Security`, `X-Content-Type-Options: nosniff`, `X-Frame-Options: SAMEORIGIN` (or CSP `frame-ancestors`), `Content-Security-Policy`, and `Referrer-Policy`.

#### M-11 / M-12. File-upload validation relies only on client-side `accept`
`Areas/Admin/Views/Shared/EditorTemplates/_UploadImage.cshtml` + Product/News/Banner/FinancialShareholder Edit views use only `accept="image/*"` etc., bypassable client-side hints. Uploads land in web-served `~/FileUploads`. DLL strings show server-side use of `Path.GetExtension` + `Guid.NewGuid` + `SaveAs` (GUID renaming mitigates predictable names/traversal), but extension/magic-byte/size validation is not visible in the views. **Step:** Confirm server-side handlers enforce an extension + magic-byte allow-list and size cap; store uploads outside the executable root or disable script execution for `/FileUploads`; serve with `Content-Disposition: attachment`.

#### M-13. Vulnerable / outdated front-end + server libraries
- **jQuery 3.4.1** (`AdminLTE`, `vendors.js`) — CVE-2020-11022 / 11023 (DOM-manipulation XSS sanitizer bypass). Loaded by `_Layout.cshtml` and admin layout. Upgrade ≥3.5.0 (latest 3.7.x).
- **jQuery Validation 1.11.1** — far behind 1.21.x; ReDoS-class regex issues. Upgrade; enforce all validation server-side.
- **Bootstrap 4.4.1 + 3.1.1** — CVE-2024-6531, CVE-2019-8331 (4.x), CVE-2018-14040/14041/14042 (3.x EOL). Upgrade AdminLTE Bootstrap ≥4.6.2 / replace 3.1.1 in `reservation/`.
- **AdminLTE 3.0.4** — drags in the above; upgrade to 3.2.x and re-pull plugins.
- **Newtonsoft.Json 12.0.1** — CVE-2024-21907 deep-nesting DoS. Upgrade ≥13.0.3 and set `MaxDepth` for untrusted JSON.

**Step:** Establish a dependency-upgrade backlog item; these are best addressed wholesale during the redesign on a maintained vendor baseline.

#### M-14. Exposed files in webroot (recon / PII)
- **`about/backup/`** — dated backup HTML (`apple - 20241226.html`) directly fetchable; advertises a predictable backup-naming convention.
- **`2sapp.apk`** (48 MB, site root) — reverse-engineerable for hardcoded API endpoints/keys; should not sit unmanaged at the document root.
- **`FileUploads/Document/`** — 639 corporate/financial PDFs incl. donation rosters (`106年度捐款名冊.pdf`, `愛心米捐款芳名錄`) and top-10 shareholder relationship tables → potential PII. Many are likely public regulatory filings, but donor/contact rosters warrant review.

**Step:** Remove `about/backup/` from webroot; serve the APK via a controlled channel and audit it for embedded secrets; review the donor/roster PDFs and gate genuinely sensitive ones behind authentication.

---

### LOW

- **L-1** (`web.config:136-137,156,30`) Internal IPs + admin allowlist (`192.168.210.123`, public `220.135.3.41`) + infra hostnames leaked; reveals admin IP restriction is currently OFF. *Move allowlists to network-layer controls; remove dead rules.*
- **L-2** (`web.config:46`) `httpOnlyCookies` not explicit (defaults true, but fragile). *Set `<httpCookies httpOnlyCookies="true" requireSSL="true" />` explicitly.*
- **L-3** (`App_Data/Logs/`, `Logs.zip`) 50 daily log files + 2.3 MB archive; PII/secret sink + unbounded growth. *Confirm App_Data non-servable; scrub sensitive fields; rotate/retain with limits; move archive out of webroot.*
- **L-4** (`LogsAdmin/Index.cshtml`, `BannerAdmin/Edit.cshtml`) Destructive GET on log/banner deletion — CSRF + destroys login-audit evidence. *Convert to POST + anti-forgery; make log deletion audited/append-only.*
- **L-5** (`LogsAdmin/Index.cshtml`) Login-record viewer exposes admin account names + source IPs and is deletable from the UI. *Restrict to SystemAdmin (controller + config); make deletion POST-only/audited.*
- **L-6** (`Mvc.sitemap:38,75,82`) Role-visibility typos: line 38 `Event,SystemAdmin!*` (malformed, deny-all not applied); line 75 `EventContactAdmin` (PII contact export) has **no** visibility attribute → shown to every authenticated role. *Normalize to `<roles>,!*`; verify controller-side `[Authorize(Roles=…)]` mirrors it.*
- **L-7** (`web.config:116-139`) Disabled `Block Admin URL` + IP-allowlist rules leave `/Admin/AuthAdmin` internet-exposed; combined with weak password storage maximizes brute-force exposure. *Enable allowlist or front with VPN/WAF; add login rate-limiting/lockout.*
- **L-8** (`Areas/Admin/Views/.../Edit.cshtml`) Upload admin views write to web-served `~/FileUploads`; rely on client `accept` only (GUID renaming helps). *Enforce server-side extension+magic-byte allow-list; disable script exec on the upload path.*
- **L-9** (`robots.txt`) Disallow entries advertise `/FileUploads/`, `/Event/Access` — a roadmap, not protection. *Apply real authorization to sensitive paths; don't rely on robots.txt.*
- **L-10** (`AwardAdmin/Edit.cshtml:109`, `EventAdmin/Edit.cshtml:438`, `EventEnAdmin/Edit.cshtml:438`) Self-XSS: selected filename written via jQuery `.html()`. Same-user only → Low. *Use `.text(cleanFileName)`.*
- **L-11** (stale libs) select2 4.0.13 + legacy 3.1.12; Spectrum 2.0.0; datetimepicker 2.4.4 (ships EOL jQuery 1.7/1.8 samples); EntityFramework 6.2.0; ASP.NET MVC 5.2.7; AppInsights SDK 2.9.1; MvcSiteMapProvider 4.6.26 (abandoned XML parser — latent XXE). *Upgrade/replace during redesign; delete bundled sample/test trees; ensure `.sitemap` XML parsing disables external entity/DTD resolution.*

---

### INFO (documented for scope completeness)

- **No `<machineKey>` defined** — acceptable for single instance; define an explicit generated key for a web farm (keep out of source).
- **ApplicationInsights instrumentation key NOT in config** — clean; keep supplying via env var.
- **`customErrors mode="On"`, no `debug="true"`/`trace`** — positive; note the backup `20200803Web.config` has neither protection (another reason to delete it).
- **Admin login form has `@Html.AntiForgeryToken()`** (good); lacks `autocomplete="off"` and offers persistent RememberMe with the 48h ticket.
- **Transitive libs dated 2018-2019** incl. deprecated Microsoft AntiXSS / Web Protection Library (2014) — replace with a maintained sanitizer (HtmlSanitizer/Ganss).

---

## 4. Quick Wins / Immediate Actions / 立即處置清單

Ordered by priority. Items 1-3 should be treated as an incident, not a backlog item.

- [ ] **Revoke + reissue every exposed certificate with a fresh key pair** — `*.sks.com.tw` (C-1/C-2/H-1), `*.skspay.com.tw` payment domain (C-3), and the **currently-valid** `skgps.com.tw` 2026 key (C-4). Assume all are compromised.
- [ ] **Remove all key/cert artifacts from the webroot:** `apache.zip`, `apache/2022wildcard.sks.com.tw.zip`, `apache/wildcard_sks_com_tw.txt`, the `apache/wildcard_sks.com.tw/` tree (incl. `.pfx`), and the entire `ebook/temp/` staging directory (`.pfx`, `.zip`, `.pem`, `.pem.txt`).
- [ ] **Delete the backup config** `20200803Web.config` from the webroot.
- [ ] **Rotate compromised credentials:** SQL `sa` (`@trustno1`) → and disable/limit `sa`; SQL `webuser` (`UmkvBQseknew`); SMTP (`1qaz2wsx`). All are now in this report and any prior artifact reader's hands.
- [ ] **Audit IIS/access logs** for prior downloads of `/apache.zip`, `*.pfx`, `*.key`, `wildcard_sks_com_tw.txt`, and `ebook/temp/*` to scope actual exposure.
- [ ] **Encrypt config secrets:** `aspnet_regiis -pe "connectionStrings"` and the `mailSettings`/SMTP section (or move to env vars / secret store).
- [ ] **Add the `/Admin` deny rule** (`<deny users="?"/>`) and **add HTTP security headers** (HSTS, CSP, X-Content-Type-Options, X-Frame-Options) to `web.config`.
- [ ] **Convert all destructive admin Delete actions to POST + `[ValidateAntiForgeryToken]`** (13 views + LogsAdmin/BannerAdmin).
- [ ] **Remove `bin/*.pdb`** from the deployed build.
- [ ] **Re-hash admin passwords** with PBKDF2/BCrypt/Argon2 (widen column, migrate on next login).
- [ ] **Plan CKEditor 4 → 5 migration** and add server-side HTML sanitization on all CMS save paths; remove `2sapp.apk` and `about/backup/` from the webroot.

---

## 5. Notes & Limitations / 範圍與限制

- This was a **static + view/code review of a published (deployed) build** at `/Users/reiser/repo/sks`, not a live penetration test. No requests were issued against a running server; HTTP-reachability claims are inferred from default IIS behavior and the absence of `requestFiltering`/`hiddenSegments` rules in `web.config`.
- **Compiled controllers in `bin/sks.dll` were not fully decompiled.** Authorization enforcement, the upload validation path, the password-comparison logic, and server-side sanitization were assessed from EF model metadata, exported strings, and view-layer code. Several findings (H-5 hashing, M-3 attribute coverage, M-11/L-8 upload validation, M-5..M-8 save-path sanitization) should be **confirmed against controller source** by the development team.
- **IIS-blocked-by-extension nuance:** The `.config` and `/bin` HTTP-retrieval vectors are blocked by default IIS request filtering. Two original "served as plaintext over HTTP" claims for the backup config were therefore down-rated; the real exposure path for those is source-control / filesystem / backup access, not the public web surface. The certificate/key `.zip`/`.pfx`/`.txt`/`.pem` extensions are **not** blocked by default and remain directly downloadable.
- **Certificate expiry:** The `*.sks.com.tw` (2023) and older wildcards are expired; the `skgps.com.tw` key (valid to Feb 2027) is current. Expiry reduces direct TLS-impersonation impact but does **not** eliminate it (key reuse on renewal, retrospective decryption of captured non-PFS traffic). All exposed keys are treated as compromised regardless of expiry.
- Finding counts include overlapping artifacts (the same secret in live config, backup config, and the assembly) listed separately because each is a distinct remediation target.
