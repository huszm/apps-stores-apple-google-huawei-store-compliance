# Rejection & error playbook

Find the message you got, apply the fix, then **change something** (build, metadata or answer) before resubmitting. Entries marked 🧪 are from real submissions.

## Apple

| Message | Cause | Fix |
|---|---|---|
| Guideline 2.1(b) — Information Needed, "business model" questions (who pays, what can be unlocked, where is it purchased) 🧪 | UI shows PRO/credits/balance/upgrade but no IAP in binary | If free: remove every paid signal (badges, balance, upgrade CTAs, server-side quotas that block use), ship a new build, then reply "The app is completely free; no content or features are paid. Build N removes the remaining PRO labels." If paid: implement IAP. |
| 2.1 — App Completeness: "error when signing in", "service unavailable" with Sign in with Apple 🧪 | Backend rejects the Apple identity token (audience/bundle ID not configured, wrong env) | Add the bundle ID to accepted Apple audiences in production config; log verification failures; test on device. |
| 2.1 — crash / bug on iPad | Universal app not tested on iPad | Fix layout or set iPhone-only. |
| 4.8 — Login Services | Google login without Sign in with Apple | Add Sign in with Apple. |
| 5.1.1(v) — Account deletion | No in-app deletion | Add Account → Delete account flow + backend. |
| 5.1.1 — Purpose string | Vague `NS…UsageDescription` | Rewrite: what is accessed, why, example. |
| 3.1.1 — IAP | External payment for digital goods | StoreKit, or remove. |
| 2.3.3 — Screenshots | Screenshots don't show app in use | Replace. |
| ITMS-90062/90186 "bundle version must be higher" 🧪 | Reused build number | Bump `CURRENT_PROJECT_VERSION`; with XcodeGen edit `project.yml`, not Xcode UI. |
| ITMS-91053 Missing API declaration | Required-reason API without manifest entry | Add to `PrivacyInfo.xcprivacy`. |
| "Unable to Add for Review — App Privacy" 🧪 | Privacy labels not published | App Privacy → answer & **Publish**. |
| Export compliance question every build | Missing `ITSAppUsesNonExemptEncryption` | Set `NO` (if exempt). |
| "Waiting for review" for days | Normal queue; sometimes a pending question | Check App Review messages; don't resubmit. |

## Google Play

| Message | Cause | Fix |
|---|---|---|
| **Invalid Data safety form — "Device or other IDs" not declared** 🧪 | SDK/code sends a device/installation ID (guest accounts, push token, analytics) | Declare *Device or other IDs* → collected, purpose App functionality (+ Fraud prevention/Analytics if true); update privacy policy; resubmit. |
| Account deletion requirement — missing URL | App allows sign-up, no web deletion link | Publish `/delete-account` page; add URL in Data safety. |
| Privacy policy — URL not reachable / not a privacy policy 🧪 | Login wall, PDF, 403 for crawlers (Cloudflare), wrong page | Public HTML page, test with `--urls`. |
| Target API level | targetSdk below requirement | Raise `targetSdk`, retest behavior changes. |
| "You need 12 testers opted-in for 14 days" / can't apply for production 🧪 | New personal account | Run closed test; count only opted-in testers on the closed track; wait full 14 days; then answer the production-access questions. Note third-party "tester community" counters ≠ Play Console count — trust Play Console. |
| Changes not reviewed / status unchanged 🧪 | Changes saved but not **sent for review** | Publishing overview → *Send changes for review*. |
| "Debug symbols not uploaded" warning 🧪 | Native code without symbols | `ndk { debugSymbolLevel = "FULL" }` next release (warning only). |
| Permissions declaration required | Sensitive permission in manifest (even from SDK) | Remove (`tools:node="remove"`) or declare with video. |
| Misleading claims / metadata | Brand names, "best", fake features | Rewrite listing. |
| Broken functionality | Login requires credentials not provided | App access → add credentials/guest steps. |
| Minimum functionality / webview | Thin wrapper | Add native value. |

## Huawei AppGallery

| Message | Cause | Fix |
|---|---|---|
| **"Invalid parameter"** on Save draft / Submit, no field highlighted 🧪 | A text field over its limit — typically **"For reviewer" remarks > 300 chars** | Shorten remarks (≤ 300), check name/intro/notes limits. |
| **"AppGallery Connect fails to access the URL"** 🧪 | Privacy/deletion URL returns 403/challenge for `Java/` UA (Cloudflare Bot Fight Mode / WAF), redirects, or login | Allow non-browser agents on those paths; verify with `--urls`. |
| Country changes not saving / package error after editing countries 🧪 | Distribution change invalidated the package step | Re-upload the same APK/AAB, then save. |
| EU countries cannot be selected / DSA prompt 🧪 | Trader info missing | Provide DSA trader info (or exclude EU). |
| Content rating unexpectedly high (16+) 🧪 | Questionnaire answers (web access, user interaction, AI) | Re-answer accurately; ratings are per region. |
| Rejected: function unavailable / crashes on Huawei device | GMS dependency (Google sign-in, FCM, Maps, Billing) | Huawei flavor without GMS; HMS kits or hide features. |
| Rejected: mentions other app store | "Google Play" text, badges, `market://` links | Remove; use `appmarket://`. |
| Rejected: permission not explained / excessive | Unused permissions, requested at launch | Remove in Huawei manifest; request in context with rationale. |
| Rejected: privacy policy incomplete | Missing data types, SDK list, deletion method, contact | Update policy (see template). |
| Rejected: virtual goods without HUAWEI IAP | Other payment for digital content | IAP Kit or make free. |
| Ownership / copyright request | Package exists elsewhere or brand concerns | Upload ownership proof / copyright statement. |

## Universal reply template (to a reviewer)

```text
Thank you for the review. We have addressed the issue:
- <what was wrong> → <what we changed> (build <N> / metadata).
How to verify: <2–3 steps>.
Test access: <guest mode: tap "Skip"> / <demo account>.
```
Keep it short, factual, in English (or the reviewer's language), and only claim what the build actually does.
