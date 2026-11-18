# Google Play — complete compliance reference

Official sources: [Developer Program Policy](https://play.google/developer-content-policy/) · [Policy Center](https://support.google.com/googleplay/android-developer/topic/9858052) · [Target API requirements](https://developer.android.com/google/play/requirements/target-sdk) · [Data safety](https://support.google.com/googleplay/android-developer/answer/10787469) · [Account deletion](https://support.google.com/googleplay/android-developer/answer/13327111)

Last verified: 2026-11. Items marked ⏱ have dates that move — re-check the source.

---

## A. Account-level gates (before the first app)

| Gate | Detail |
|---|---|
| **Developer account type** | Personal or Organization. Organizations need a D-U-N-S number. Some categories (finance, health, government, VPN…) require an organization account. |
| **Identity & contact verification** | Legal name, address, phone, email verified. Public developer email/address appear on the listing. |
| **Android developer verification** ⏱ | From 2026 Google requires developer identity verification for apps installed on certified Android devices, including outside Play (rolling out by region from Sept 2026). Register package names in Play Console / Android Developer Console. |
| **Closed testing for new personal accounts** ⏱ | Personal accounts created after 13 Nov 2023 must run a **closed test with at least 12 testers opted-in for 14 consecutive days** before applying for production access. Testers must stay opted in; uninstalling resets nothing but opting out lowers the count. After the 14 days, answer the production-access questionnaire (how you recruited testers, feedback you received, what you changed). Organization accounts are exempt. |
| **Payments profile** | Required for paid apps / IAP (merchant account). |

## B. Build / technical requirements

| Requirement | Detail |
|---|---|
| **Target API level** ⏱ | New apps and updates must target an API level within one year of the latest major Android release. Since 31 Aug 2025: API 35 (Android 15). From 31 Aug 2026: **API 36 (Android 16)**. Existing apps below the floor stop being offered to new users on newer devices. |
| **App Bundle** | New apps must upload `.aab`. Enroll in **Play App Signing** (required for AAB). Keep the upload key safe; Google can reset it. |
| **64-bit** | Native libs must ship arm64-v8a (and x86_64 if you ship x86). |
| **16 KB memory pages** ⏱ | Since 1 Nov 2025, new apps and updates targeting Android 15+ must support 16 KB page sizes if they include native code (rebuild with NDK r28+/AGP 8.5.1+, update native SDKs). |
| **Play Billing Library** ⏱ | Apps selling digital goods must use a supported PBL version (v7 required since Aug 2025; v8 by 31 Aug 2026). |
| **Foreground services** | Declare `foregroundServiceType` and justify each type in the console (Android 14+). |
| **Exact alarms / full-screen intents** | `USE_EXACT_ALARM` only for alarm/calendar apps; `USE_FULL_SCREEN_INTENT` only for calls/alarms — otherwise declare or remove. |
| **Deobfuscation & native symbols** | Not mandatory; Play warns "This App Bundle contains native code, and you've not uploaded debug symbols". Add `ndk { debugSymbolLevel = "FULL" }` and upload mapping. |
| **Pre-launch report** | Runs automatically on Play test devices; fix crashes and accessibility warnings it reports. |

## C. Policy content checklist

| Policy | Triggers | What to do |
|---|---|---|
| **User Data & privacy policy** | Every app | Privacy policy URL in console **and** in app. Data handling must match the policy and Data safety. |
| **Data safety form** | Every app (even "no data") | See section D. Mismatch with the APK/SDKs = rejection ("Invalid Data safety form"). |
| **Account deletion** | App lets users create accounts | In-app deletion path **and** web URL (works without reinstalling the app) entered in Data safety → "Delete account URL". |
| **Prominent disclosure & consent** | Collecting personal/sensitive data in a way users wouldn't expect | Disclosure screen before collection, with affirmative consent. |
| **Permissions** | SMS/Call log, background location, all-files access, accessibility services, `QUERY_ALL_PACKAGES`, photo/video, exact alarms, health | Each needs a **Permissions declaration** in console + video demo for some. Use Photo Picker; prefer one-time access. Remove what you don't need. |
| **Advertising ID** | Any SDK reading AAID | Answer the Advertising ID declaration; include `com.google.android.gms.permission.AD_ID` only if used. |
| **Ads** | Ads SDKs | "Contains ads" declaration; no disruptive/interstitial-at-launch ads; Families-certified SDKs for kids. |
| **Target audience & content** | Every app | Choose age groups. Selecting under-13 brings Families policy (teacher-approved, ad SDK limits, no AAID for kids). |
| **Content rating (IARC)** | Every app — unrated apps are removed | Answer honestly; AI chat/UGC/web access change the answers. |
| **App access** | Any login or gated feature | Provide credentials or steps (guest mode) in **App content → App access**. Reviewers can't receive SMS/2FA codes. |
| **Generative AI** | App generates text/images/audio/video with AI | Must prevent restricted content (incl. sexual deepfakes/NCII), and offer **in-app reporting of offensive AI output**. |
| **UGC** | Users post content others see | Terms of use, moderation, in-app report & block. |
| **Financial features / Health apps / Government / News / VPN / COVID** | Category-specific declarations in App content | Fill the matching declaration; some need organization accounts or licences. |
| **Payments** | Digital goods/features | Google Play Billing (or alternative billing where permitted). Physical goods/services may use other processors. Donations: link out only where allowed. |
| **Impersonation / IP / metadata** | Listing | No other brands' names in title/keywords, no "Best/#1", no misleading screenshots, no emoji/CAPS spam in title. Max title 30 chars. |
| **Minimum functionality / spam** | Webview wrappers, template apps | Provide real app value; avoid repetitive content. |
| **Child safety standards** | Social & dating apps | Publish CSAE standards page, in-app reporting, named contact. |
| **Deceptive behavior** | Hidden features, fake system dialogs | – |

## D. Data safety form — how to answer correctly

1. **Inventory**: list every data type your code and every SDK (Firebase, Crashlytics, AdMob, analytics, sign-in, maps…) collects or shares. Check each SDK's own Data safety guidance page.
2. **"Collected"** = leaves the device (to you or a third party). Processing only on device is not collected.
3. **"Shared"** = transferred to a third party (not service providers processing on your behalf).
4. **Device or other IDs** — if you create a **guest/installation ID**, send Android ID, Firebase installation ID, push token or advertising ID to a server, declare *Device or other IDs* (purpose: App functionality, maybe Fraud prevention/Analytics). Forgetting this is a common "Invalid Data safety form" rejection (it is detected from SDK/code analysis).
5. Encrypted in transit → Yes if all traffic is HTTPS.
6. Users can request deletion → Yes + **delete account URL** if accounts exist.
7. Re-submit the form whenever you add an SDK or data type.

Use `templates/data-declaration-worksheet.md` to answer Play, Apple and Huawei forms from the same inventory.

## E. Store listing

| Field | Limit / rule |
|---|---|
| App name | ≤ 30 characters |
| Short description | ≤ 80 characters |
| Full description | ≤ 4000 characters |
| App icon | 512×512 PNG (32-bit, ≤ 1 MB), full-bleed square — Play applies the mask |
| Feature graphic | **1024×500** JPG/PNG (no alpha) — required |
| Phone screenshots | 2–8, JPG/PNG, 320–3840 px per side, max aspect ratio 2:1 (e.g. 1080×1920). **4+ at ≥1080 px** to be eligible for featuring |
| Tablet / Chromebook screenshots | Recommended if the app supports large screens (7" and 10") |
| Video | YouTube URL, public or unlisted, no ads |
| Contact | Email required; website and phone optional |
| Category, tags | Choose honestly |

Translations: add listing translations for every language your users read; machine translations are allowed but must be accurate.

## F. Release flow & review

1. Internal testing → closed testing (12×14 rule if applicable) → production.
2. **Send for review**: changes made in the console sit in "Publishing overview" until you click *Send changes for review* (unless managed publishing is on). Many people think they submitted when they didn't — confirm in **Publishing overview** that the state is "In review".
3. Review time: hours to several days; first production releases and policy-sensitive apps take longer (up to 7+ days).
4. Staged rollout is recommended for updates (e.g. 20% → 100%).
5. A rejection lists the policy; fix and resubmit from the same track. Appeals via Policy status → Appeal. Multiple rejected updates or policy strikes can lead to suspension — never resubmit unchanged.

## G. Play warnings that are not rejections

- "Debug symbols not uploaded" — fix next release.
- "Your app is not optimized for tablets / large screens" — recommendation only.
- Pre-launch accessibility suggestions — recommendation, but fix touch-target and contrast issues.
