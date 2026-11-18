# Data declaration worksheet (Apple · Google Play · Huawei)

Fill one row per data type that **leaves the device** (to your server or any SDK). Then translate the rows into each store's form using the mapping below. Re-do this whenever you add an SDK.

## 1. Inventory

| # | Data | Source (code / SDK) | Sent to | Purpose | Required or optional | Linked to user? | Used for tracking/ads? | Retention | Deletable by user? |
|---|---|---|---|---|---|---|---|---|---|
| 1 | Name | Sign-up form | Own API | Account | Optional | Yes | No | Until deletion | Yes (in-app) |
| 2 | Email | Sign-up / social login | Own API | Account, support | Optional | Yes | No | Until deletion | Yes |
| 3 | Device / installation ID | Guest session | Own API | App functionality, fraud prevention | Required for guest | Yes (guest account) | No | Until deletion | Yes |
| 4 | User content (history, favorites…) | App | Own API | App functionality | Optional | Yes | No | Until deletion | Yes |
| 5 | Crash logs / diagnostics | Crash SDK | <vendor> | App functionality | Required | No | No | 90 days | – |
| … | | | | | | | | | |

Checklist of SDKs to look at: auth (Firebase Auth, Google, Facebook, Apple, Huawei ID), analytics (Firebase/GA4, Mixpanel, Amplitude, HMS Analytics), crash (Crashlytics, Sentry), ads (AdMob, Meta, Unity, HUAWEI Ads), push (FCM, APNs, HMS Push, OneSignal), payments (StoreKit, Play Billing, HUAWEI IAP, Stripe, RevenueCat), maps/location, AI APIs (OpenAI, Anthropic, Gemini), customer support chat.

## 2. Mapping to store forms

| Worksheet data | Apple App Privacy category | Google Play Data safety category | Huawei personal data type |
|---|---|---|---|
| Name | Contact Info → Name | Personal info → Name | Name / nickname |
| Email | Contact Info → Email Address | Personal info → Email address | Email address |
| Phone | Contact Info → Phone Number | Personal info → Phone number | Phone number |
| Device / installation / push ID | Identifiers → Device ID | **Device or other IDs** | Device identifier |
| User ID | Identifiers → User ID | Personal info → User IDs | Account information |
| Prompts, posts, history | User Content → Other User Content | App activity → Other user-generated content | User content |
| Search history | Search History | App activity → In-app search history | Usage data |
| App interactions/analytics | Usage Data → Product Interaction | App activity → App interactions | Usage data |
| Crash logs | Diagnostics → Crash Data | App info and performance → Crash logs | Device / log information |
| Performance | Diagnostics → Performance Data | App info and performance → Diagnostics | Log information |
| Purchases | Purchases → Purchase History | Financial info → Purchase history | Transaction records |
| Precise location | Location → Precise Location | Location → Precise location | Location |
| Photos/videos uploaded | User Content → Photos or Videos | Photos and videos | Photos/videos |
| Advertising ID / IDFA | Identifiers → Device ID + **Tracking** | Device or other IDs + Ads purpose | Advertising identifier |

Purposes: Apple (App Functionality, Analytics, Product Personalization, Developer's Advertising, Third-Party Advertising, Other) · Play (App functionality, Analytics, Developer communications, Advertising or marketing, Fraud prevention/security, Personalization, Account management) · Huawei (choose the matching purpose and explain in text).

Extra questions:
- Play: encrypted in transit? users can request deletion? delete-account URL? data shared with third parties?
- Apple: linked to identity? used for tracking? (Tracking = combining with third-party data for ads/measurement or sharing with data brokers.)
- Huawei: cross-border transfer (server location vs. user region)? permission purposes; privacy policy URL; deletion method.

## 3. Consistency check

- [ ] Every row appears in the privacy policy (data, purpose, third parties, retention, deletion).
- [ ] Every row appears in Apple labels and `PrivacyInfo.xcprivacy` (`NSPrivacyCollectedDataTypes`).
- [ ] Every row appears in Play Data safety.
- [ ] Every row appears in Huawei privacy section.
- [ ] No SDK collects anything that is not in the table.
