# Required pages, screens and files

What an app project must contain to pass the Apple App Store, Google Play and Huawei AppGallery. **R** = required (store rejects or blocks the listing without it), **C** = required *if the condition applies*, **Rec** = recommended (reduces rejection risk or support load).

Last verified: 2026-11. Re-check the store links in each store reference when a decision hinges on a row.

---

## 1. Public web pages (on your own domain, HTTPS)

These URLs are pasted into the store consoles. Store crawlers fetch them, so each one must load **without login, without a cookie wall that hides the text, without geo-blocking, and without bot protection that blocks non-browser agents** (Google's checker and AppGallery's `Java/…` agent). Use `scripts/store_scan.py --urls …` to verify.

| Page | Apple | Play | Huawei | Notes |
|---|---|---|---|---|
| **Privacy policy** | R | R (every app, even with no data) | R | Must name the app/developer, list each data type, purpose, SDK/third parties, retention, deletion method, contact. HTML page, not PDF/Google Doc. Localize for the listing languages (at minimum the default language). |
| **Account / data deletion page** | Rec | **C: R if users can create an account** (URL goes in the Data safety form) | R (asked in privacy section) | Explain *in-app path* (e.g. Account → Delete account) **and** a way to request deletion without the app (form or email). State what is deleted, what is kept and for how long. |
| **Support / contact page** | R (Support URL) | R (contact email; website Rec) | R (customer service email/phone) | A real page with an email or form. A bare homepage with no contact info gets rejected by Apple (1.5). |
| **Terms of use / EULA** | C: R with auto-renewing subscriptions (link in app **and** in description, or use Apple's standard EULA) | C: Rec with subscriptions/UGC | C: R with IAP/subscriptions in China | UGC apps: terms must forbid objectionable content (Apple 1.2). |
| **Marketing / website** | Rec (Marketing URL) | Rec | Rec | Must not contradict the listing. |
| **Open-source licenses** | Rec | Rec | Rec | Can live in-app instead. |
| **Child safety standards page** | C: social/dating apps | **C: R for social & dating apps** (published CSAE standards + contact) | Rec | Play "Child Safety Standards" policy. |

Per-region extras:

| Region | Requirement |
|---|---|
| EU (all stores) | DSA **trader status** declaration: legal name, address, phone, email (published on the listing for traders). Apple and Huawei block EU distribution until provided; Play shows it on the listing. |
| EU / UK | GDPR: lawful basis, DPO/representative if applicable, right of access/erasure, cookie/analytics consent if you track. |
| Mainland China (Huawei/other CN stores) | ICP filing for every domain the app calls, MIIT app filing, software copyright certificate, domestic entity, simplified-Chinese privacy policy + first-launch consent popup. Most foreign developers **exclude mainland China** in AppGallery distribution. |
| South Korea, Japan, US states, Brazil… | Age assurance / data laws; see store references. |

---

## 2. In-app screens and flows

| Screen / flow | Apple | Play | Huawei | How to do it right |
|---|---|---|---|---|
| **Delete account** (in app, easy to find) | **C: R** if account creation (5.1.1(v)) | **C: R** | **C: R** | Settings/Account → "Delete account" → confirm → actually delete (or start deletion) server-side → sign out. Not just "email us". Deactivation alone does not count. Super-admin/staff accounts may be blocked with a clear message. |
| **Privacy policy link** inside the app | R | R | R | Settings/About row that opens the public URL. Also on sign-up screen. |
| **Terms link** | C (subscriptions, UGC) | C | C | Shown at sign-up and paywall. |
| **Guest / no-login use** | Rec (5.1.1: don't force login for features that don't need an account) | Rec | Rec | Greatly simplifies review. If login is mandatory, provide a demo account (see review notes). |
| **Sign in with Apple** | **C: R** if any third-party/social login on iOS (4.8, or an equivalent privacy-focused login) | – | – | Same placement/size as other social buttons. Handle the "hide my email" relay address and name only on first sign-in. |
| **Edit profile** (name/email) | Rec | Rec | Rec | Social-login users usually have no password: don't require "current password" for them. |
| **First-launch privacy consent** | – | C: if you collect personal data before any user action and in regions requiring consent | **C: R for China; Rec elsewhere** | Short dialog: what is collected, link to policy, Agree / Disagree (app must stay usable or exit gracefully on Disagree in CN). |
| **Tracking consent (ATT)** | **C: R** before any cross-app tracking / IDFA | – | – | `NSUserTrackingUsageDescription` + `ATTrackingManager.requestTrackingAuthorization`. Without tracking: don't show it and answer "No tracking". |
| **GDPR/UMP consent** | C: ads/analytics for EU | C: AdMob in EEA/UK (Google certified CMP) | C | Use a certified CMP if serving personalized ads. |
| **Permission rationale** before each runtime prompt | R (purpose strings) | R (runtime permissions, prominent disclosure for sensitive data) | R (explain purpose; request when needed, not at launch) | Request in context. Sensitive (location in background, SMS, call log, accessibility, all-files) need Play declarations. |
| **Prominent disclosure** | – | **C: R** if collecting personal/sensitive data not obviously expected | C | In-app screen *before* the runtime prompt, describing data + use, with explicit accept. |
| **Report / block / filter** content | **C: R** for UGC (1.2) and chat | **C: R** UGC; **R** for AI-generated content (report offensive output) | C: R | Report button on each item/user, block user, filtering, published contact, timely moderation. |
| **AI output report / disclaimer** | C (1.2, 5.1.2(i) if sending personal data to third-party AI — disclose and get consent) | **C: R** generative AI: in-app reporting of offensive output | C | Show which AI service processes data; offer reporting. |
| **Paywall details** | C: subscription price, period, auto-renew terms, Restore Purchases button, links to Terms + Privacy | C: Play Billing UI, clear price | C: HUAWEI IAP UI | Never hide close button; no misleading "free" claims. |
| **Restore purchases** | **C: R** for non-consumable/subscriptions | Rec | Rec | Visible button. |
| **Age gate / kids mode** | C: Kids category, mature content | C: target audience includes children (Families policy) | C | Neutral age screen; no ads SDKs that aren't Families-certified for kids. |
| **Offline / error states** | R (2.1 — no crash, no blank screen) | R (quality) | R (App Functions) | Show a retry state when offline; reviewers sometimes test on poor networks. |
| **Contact / feedback** | Rec | Rec | Rec | Mailto or form; reduces 1-star reviews. |
| **About / version** | Rec | Rec | Rec | Version + build shows reviewers which build they test. |
| **Licenses** | Rec | Rec | Rec | Open-source notices. |

---

## 3. Project files and settings

### iOS

| File / setting | Status | Notes |
|---|---|---|
| `PrivacyInfo.xcprivacy` (app + each SDK) | **R** | Declare required-reason APIs (`UserDefaults` CA92.1, file timestamp C617.1, system boot time 35F9.1, disk space E174.1…), collected data types, tracking flag, tracking domains. Must match the App Privacy labels. |
| `Info.plist` purpose strings | **C: R** per permission used | `NSCameraUsageDescription`, `NSPhotoLibraryUsageDescription`, `NSLocationWhenInUseUsageDescription`, `NSMicrophoneUsageDescription`, `NSContactsUsageDescription`, `NSFaceIDUsageDescription`, `NSUserTrackingUsageDescription`… Specific, user-facing, localized; vague text ("needs camera") is a 5.1.1 rejection. |
| `ITSAppUsesNonExemptEncryption` | Rec (saves an export-compliance question per build) | `NO` if only HTTPS/standard OS crypto. |
| Sign in with Apple entitlement | C | `com.apple.developer.applesignin`; backend must accept the bundle ID as audience. |
| Associated domains | C | Universal links / password autofill. |
| App icon 1024×1024, no alpha | **R** | |
| Launch screen | **R** | |
| Build SDK | **R** | Built with the Xcode/SDK version Apple currently requires (Apple raises it each spring). |
| Version / build | **R** | `CFBundleShortVersionString` + unique, increasing `CFBundleVersion`. |
| Device families / orientations | R consistency | If iPad is supported, iPad screenshots are required and the UI must work on iPad (2.4.1). Supporting all orientations on iPad requires `UIRequiresFullScreen` false and working layouts. |

### Android (both stores)

| File / setting | Status | Notes |
|---|---|---|
| `targetSdk` | **R** | Play: within the required level (see `google-play.md`). Keep Huawei build on the same. |
| Release signing | **R** | Upload key in a keystore outside VCS (`keystore.properties` gitignored). Back it up. |
| `versionCode` / `versionName` | **R** | Strictly increasing per store. |
| Android App Bundle (`.aab`) | **R** for Play new apps | Huawei accepts APK or AAB. |
| R8/minify + mapping, native debug symbols | Rec (Play warns without them) | `isMinifyEnabled`, `ndk { debugSymbolLevel = "FULL" }`. |
| 16 KB page-size compatible native libs | **C: R** (Play, targetSdk ≥ 35 with native code) | Update NDK/AGP and native SDKs. |
| Permissions minimized | **R** | Remove unused; sensitive ones need Play declarations. Use Photo Picker instead of `READ_MEDIA_*` when possible. |
| `android:usesCleartextTraffic="false"` / network security config | Rec | Cleartext is flagged in security review (Huawei especially). |
| `android:allowBackup` / data extraction rules | Rec | Exclude tokens/secrets from backup. |
| Exported components | **R** (`android:exported` explicit on components with intent filters) | |
| `AD_ID` permission | **C: R** if using advertising ID (and declare it) | Remove it if no ads (`tools:node="remove"`). |
| Store flavor (`play` / `huawei`) | **C: R** for Huawei if any GMS dependency | See `project-setup.md`. |
| Store links | **R per store** | Play `market://details?id=` / `https://play.google.com/store/apps/details?id=`; Huawei `appmarket://details?id=` / `https://appgallery.huawei.com/app/C<appId>`. |

### Cross-platform frameworks

- **Flutter**: same native files under `ios/` and `android/`; check `pubspec.yaml` plugins (e.g. `google_sign_in`, `firebase_messaging`, `in_app_purchase`) for GMS/IAP impact per store.
- **React Native / Expo**: `app.json`/`app.config.*` (`ios.infoPlist`, `ios.privacyManifests`, `android.permissions`, `android.blockedPermissions`); run `npx expo prebuild` before auditing native files.
- **Capacitor/Cordova**: thin web wrappers risk Apple 4.2 (minimum functionality) — add native value (offline, push, native navigation, share, widgets).
