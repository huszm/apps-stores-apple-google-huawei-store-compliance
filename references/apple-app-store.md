# Apple App Store — compliance reference

Official sources: [App Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) · [App privacy details](https://developer.apple.com/app-store/app-privacy-details/) · [Privacy manifest files](https://developer.apple.com/documentation/bundleresources/privacy-manifest-files) · [Required reason APIs](https://developer.apple.com/documentation/bundleresources/describing-use-of-required-reason-api) · [Offering account deletion](https://developer.apple.com/support/offering-account-deletion-in-your-app/) · [Screenshot specifications](https://developer.apple.com/help/app-store-connect/reference/screenshot-specifications/) · [Upcoming requirements](https://developer.apple.com/news/upcoming-requirements/)

Last verified: 2026-11. ⏱ = moving date.

---

## A. Guidelines that reject most apps

| Guideline | What triggers it | Fix |
|---|---|---|
| **2.1 App Completeness** | Crash, placeholder content, broken links, backend down, login with no demo account, features unreachable | Live backend; demo account or guest mode; test the exact build on device (TestFlight). |
| **2.1(b) Information needed / business model** | Reviewer sees paid-looking UI (PRO badges, credits, balance, "upgrade") but no IAP in the binary | Answer the questions precisely (who pays, what is unlocked, where purchased) — or remove paid signals if the app is free. |
| **2.3 Accurate metadata** | Screenshots not showing the app in use, features not in the app, other platform names ("Android"), price in name | Real in-app screenshots; describe only existing features. |
| **2.3.3 / 2.3.7** | Screenshots that are only splash/login; keyword stuffing, competitor names in subtitle/keywords | – |
| **2.4.1 iPad** | App runs on iPad (universal) but layout breaks | Test on iPad or set iPhone-only. |
| **2.5.x Software requirements** | Private APIs, downloading executable code, deprecated APIs | – |
| **3.1.1 In-App Purchase** | Digital content/features unlocked with non-IAP payment, links/buttons to external purchase (outside allowed regions/entitlements) | StoreKit IAP; reader apps / US storefront link rules have special entitlements. |
| **3.1.2 Subscriptions** | Missing price/period/terms, no Restore, no Terms (EULA) link in app and metadata | Add to paywall and description. |
| **4.0 Design / 4.2 Minimum functionality** | Web-view wrappers, little native value | Native navigation, offline, share, widgets, notifications. |
| **4.3 Spam** | Template/clone apps, many similar apps | One rich app. |
| **4.8 Login services** | Third-party login (Google, Facebook…) without an equivalent privacy-focused option | Add **Sign in with Apple**. |
| **5.1.1(i) Privacy policy** | No policy link in App Store Connect and inside the app | Add both. |
| **5.1.1(ii)/(iii) Permission & data minimization** | Vague purpose strings, requiring data not needed, forcing login for non-account features | Specific purpose strings; optional login. |
| **5.1.1(v) Account deletion** | Account creation without in-app deletion | In-app delete that deletes the account and associated data (may require re-auth; Sign in with Apple users: revoke token via REST API). |
| **5.1.2 Data use and sharing** | Tracking without ATT; sharing with AI providers without disclosure/consent | ATT prompt; disclose third-party AI processing and get consent. |
| **1.2 User-generated content** | No filter/report/block/contact | Add moderation tools. |
| **1.4 / 1.5** | Physical harm; developer information missing (support URL without contact) | Support page with real contact. |

## B. Privacy: labels + manifest

1. **App Privacy (nutrition labels)** in App Store Connect: data types, linked to user or not, used for tracking or not. Include SDK data.
2. **`PrivacyInfo.xcprivacy`** in the app target (and each third-party SDK must ship its own): `NSPrivacyTracking`, `NSPrivacyTrackingDomains`, `NSPrivacyCollectedDataTypes`, `NSPrivacyAccessedAPITypes` with reasons. Missing required-reason declarations produce an email (ITMS-91053) and can block uploads.
3. A guest/device identifier sent to a server = **Device ID** (App Functionality, linked or not linked depending on use).
4. Labels, manifest, privacy policy and actual behavior must agree.

## C. Build and upload

| Item | Detail |
|---|---|
| SDK minimum ⏱ | Apple requires uploads built with a recent Xcode/SDK each spring (e.g. Xcode 26 / iOS 26 SDK from April 2026). |
| Version numbers | `CFBundleShortVersionString` (marketing) + `CFBundleVersion` (build) — build must be unique and increasing for that version. "The bundle version must be higher than the previously uploaded version" = bump the build. |
| Encryption | `ITSAppUsesNonExemptEncryption = NO` when only exempt encryption (HTTPS) is used. |
| Bundle ID & capabilities | Match the App ID; Sign in with Apple, push, associated domains configured on developer portal. Backend audience for Apple sign-in = bundle ID (and Services ID for web). |
| Age rating ⏱ | New age-rating questionnaire (4+, 9+, 13+, 16+, 18+) — answer the updated questions in App Store Connect. |
| EU DSA ⏱ | Trader status required to distribute in the EU. |
| TestFlight | Internal testers need no review; external testing needs a Beta App Review. Human testers before submission are **not** required by Apple. |

## D. App Store Connect page checklist

- Name (≤ 30), Subtitle (≤ 30), Promotional text (≤ 170), Description (≤ 4000), Keywords (≤ 100 bytes, comma separated, no spaces needed, no competitor names), What's New.
- Support URL (required), Marketing URL (optional), Privacy Policy URL (required).
- Screenshots: **6.9" iPhone** (1320×2868 or 1290×2796 portrait) required — smaller sizes scale down; **13" iPad** (2064×2752 or 2048×2732) required if iPad is supported. 1–10 per size, PNG/JPG, no alpha.
- App previews optional (15–30 s).
- Category, content rights (third-party content?), age rating, pricing & availability, App Privacy, App Review Information (contact, demo account, notes ≤ 4000 chars, attachment).
- Version release: manual / automatic / scheduled; phased release for updates.

## E. Review process

- Typical review < 24–48 h. "Waiting for Review" → "In Review" → "Pending Developer Release"/"Ready for Distribution" or "Rejected".
- Reply in **App Review → Messages** (Resolution Center) for questions like 2.1(b); a reply without a new build is fine when the fix is metadata/explanation.
- Expedited review request is available for critical fixes.
- Appeals: App Review Board form.
