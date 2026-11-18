# Huawei AppGallery — complete compliance reference

Official sources: [AppGallery Review Guidelines](https://developer.huawei.com/consumer/en/doc/app/50104) · [AppGallery Connect help](https://developer.huawei.com/consumer/en/doc/AppGallery-connect-Guides/agc-get-started-0000001100239806) · [Releasing an app](https://developer.huawei.com/consumer/en/doc/AppGallery-connect-Guides/agc-release-app-0000001100206512) · [HMS Core](https://developer.huawei.com/consumer/en/hms) · [Privacy statement requirements](https://developer.huawei.com/consumer/en/doc/app/50127)

Last verified: 2026-11. AppGallery Connect changes its UI often; field names below are as seen in the English console. Items marked 🧪 come from real submissions (field experience), not from published docs.

---

## A. Why a separate Huawei build matters

Huawei phones sold since 2019 (and all HarmonyOS/HarmonyOS NEXT-era Android-compatible devices that still run APKs) **have no Google Mobile Services (GMS)**. Anything that calls Google Play services fails at runtime and in review:

| Google feature | Breaks on Huawei | Replace with |
|---|---|---|
| Google Sign-In / Credential Manager Google ID | Yes — button does nothing or crashes | Email/password, Huawei Account Kit (HUAWEI ID), or hide the button in the Huawei build |
| Firebase Cloud Messaging | Yes — no push | HMS Push Kit (or a non-GMS push provider) |
| Google Maps SDK | Yes | HMS Map Kit, or Mapbox/OSM |
| Fused Location Provider | Yes | HMS Location Kit or `LocationManager` |
| Google Play Billing | Yes, and AppGallery requires its own IAP | **HUAWEI IAP** (In-App Purchases Kit) for virtual goods |
| Play Integrity / SafetyNet | Yes | HMS Safety Detect |
| Google Play In-App Review / In-App Updates | Yes | AppGallery equivalents or remove |
| Firebase Crashlytics / Analytics | Mostly work (pure HTTP) but some features depend on GMS | Test on a Huawei device; HMS Analytics/APM optional |
| AdMob | Ads won't serve reliably | HUAWEI Ads Kit |
| `market://` links, "Get it on Google Play" badges | Open nothing or the wrong store | `appmarket://details?id=<package>` / `https://appgallery.huawei.com/app/C<appId>` |

**Rule:** build a `huawei` product flavor (see `project-setup.md`) that removes GMS dependencies, hides Google-only UI, removes unused permissions, and points store links to AppGallery. A review on a Huawei device of a button that cannot work = "App Functions" rejection.

## B. Account and app setup

| Step | Detail |
|---|---|
| Developer account | Individual or enterprise, real-name verified (ID / business licence; bank card for individuals in some regions). Verification can take 1–3 working days. |
| Create app | My apps → New app → Android, package name **identical** to the package you upload (cannot be changed later). |
| Ownership 🧪 | If the same package already exists on Google Play under your name, AppGallery may ask for proof of ownership (e.g. Play Console screenshot or a signed copyright statement). If another party already claimed the package name on AppGallery, file an appeal. |
| Signing | Upload an APK/AAB signed with your key. *App signing* (Huawei manages the key) is optional; if you enable it you can't turn it off. Keep one key per app. |
| Agreements | Accept the distribution agreement; paid apps/IAP need merchant service (bank info). |

## C. Version information page — every field

AppGallery Connect → My apps → *app* → Version information → *Draft*.

| Section | Field | Requirement & pitfalls |
|---|---|---|
| **Country/Region** | Distribution countries | Choose countries. **Mainland China** needs ICP/MIIT filing, a Chinese entity, software copyright certificate and simplified-Chinese material — exclude it unless prepared. 🧪 Changing the country list after uploading the package can require **re-uploading the APK** before the draft saves. |
| | EU DSA info | To distribute in EU/EEA countries you must provide **trader status** (trader vs non-trader) and, for traders, legal name, address, phone, email (DUNS or business registration). Without it the EU countries are blocked — either add it or exclude the EU. |
| **App package** | Software package | APK or AAB, `versionCode` higher than any previous AppGallery upload. Package name must match. Huawei scans it: malware, permissions vs declared features, GMS calls, 64-bit libs, `targetSdkVersion` (keep it current, same as Play). |
| **Payment** | Free / Paid / IAP | Free apps that sell digital content must use HUAWEI IAP. |
| **Content rating** | Questionnaire per region | Produces ratings for China, Brazil (ClassInd), and "Other regions". 🧪 Answers about *web access, user interaction, AI-generated content* can push "Other regions" to 16+; re-check if the rating looks too high — it reduces reach. |
| **Privacy** | Privacy statement URL | Public HTTPS URL. 🧪 AppGallery's checker fetches it with a **Java user agent such as `Java/1.8.0_x`** (Cloudflare blocks the Java 8 signature even when `Java/17` passes): Cloudflare Bot Fight Mode / WAF rules that 403 non-browser agents cause "AppGallery Connect fails to access the URL". Allow that UA (WAF *Skip* rule for the policy and deletion paths, or disable Bot Fight Mode). Verify with `scripts/store_scan.py --urls`. |
| | Account deletion URL / method | If the app has accounts. |
| | Personal data collection | Yes/No + data types + purposes; cross-border data transfer (if your server is outside the user's region, say Yes). Must match the privacy policy. |
| | Privacy tags / permission explanations | Each sensitive permission needs a purpose; unused permissions get flagged. |
| **Generative AI** 🧪 | Does the app provide AI-generated content? | If the app itself runs or calls an AI model to generate content, answer Yes and expect extra material (model filing in China). If it only builds text the user copies elsewhere, answer according to what the app actually does. |
| **Review information** | **Remarks "For reviewer"** | **Max 300 characters** 🧪 — longer text gives a generic **"Invalid parameter"** on Save/Submit with no field highlighted. Keep it short: how to enter (guest/skip), test account, where account deletion is, notable behavior. |
| | Test account | Username/password if login is required; must work for the whole review; no SMS 2FA. |
| | Attachments | Optional: copyright documents, qualification certificates (e.g. for finance, health, VPN, news). |
| **Release** | Release time | "Immediately once approved" or scheduled; phased release available for updates. |

Store listing (App information page, per language):

| Field | Limit (🧪 as enforced) |
|---|---|
| App name | ≤ 64 characters (shorter is better; must match the app's displayed name closely) |
| Brief introduction | ≤ 80 characters |
| Description | ≤ 8000 characters |
| New features (release notes) | ≤ 500 characters |
| Icon | **216×216 PNG**, ≤ 2 MB, square, no rounded-corner pre-rendering issues |
| Screenshots | **3–5** per language, same orientation, JPG/PNG; portrait 450×800 or larger (e.g. 1080×1920), ≤ 2 MB each. No Google Play badges, no other-platform UI (iPhone frames) |
| Category | App category + sub-category |
| Customer service | Email (required), phone/website optional |

## D. Review guideline themes that reject most often

AppGallery's guidelines are grouped into **App Information · App Security · App Functions · App Content**.

| Theme | Common rejection | Fix |
|---|---|---|
| App Information | Name/icon/screenshots differ from the actual app; mentions of other stores; screenshots not from the app | Make listing match the installed app; remove "Google Play" text and badges. |
| App Functions | Crash on launch on Huawei devices; buttons that need GMS; login required but no test account; blank screens offline | Test the Huawei build on a Huawei device or AppGallery cloud debugging; provide guest mode or test account. |
| App Security | Excess permissions; permission requested at launch without explanation; cleartext HTTP; data collection before consent; outdated vulnerable SDKs | Remove unused permissions (biometric, contacts, storage…) in the Huawei manifest; request in context; HTTPS only; consent before collection. |
| Privacy | Policy unreachable, not localized, missing data types, no deletion method; policy not accessible in app | Public URL reachable by Java user agents (`Java/1.8`); link in app settings; list deletion path. |
| App Content | Gambling, adult, political, unlicensed news/finance/health/VPN content; AI content without controls | Exclude regions or provide qualifications. |
| Payments | Virtual goods without HUAWEI IAP; external payment links for digital content | Integrate IAP Kit or make the app free. |
| Duplicate / low quality | Multiple near-identical apps, web wrappers | Ship real native value. |

## E. China-specific (only if distributing in mainland China)

- MIIT app filing number (displayed in app/about), ICP filing for every backend domain.
- Software copyright certificate (or electronic version) for the app name.
- Chinese legal entity or licensed publisher; games need ISBN/approval.
- Simplified-Chinese name, description, screenshots, privacy policy.
- **First-launch privacy popup**: before any data collection or SDK init, show summary + links to privacy policy and user agreement; "Disagree" must not force-close without option and must not collect data.
- Permission requests only when the feature is used; explain each one in a pre-dialog.
- Generative AI services need algorithm/model filing.

## F. Review timing and after submit

- Review usually 1–5 working days; first submissions and sensitive categories longer.
- Status: *Under review → Approved / Rejected*. Rejection reasons appear in the version page and email; fix and resubmit as a new draft.
- After approval with "Immediately", the app appears in AppGallery in a few hours (search indexing can lag a day).
- Keep the Huawei `versionCode` sequence independent from Play's but always increasing within AppGallery.
