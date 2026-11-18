---
name: mobile-store-compliance
description: Make a mobile app pass review on the Apple App Store, Google Play and Huawei AppGallery — from the first line of code to the submit button. Use when building, preparing, auditing or submitting an iOS, Android, Flutter, React Native, Expo or Capacitor app; when the user mentions App Store Connect, Play Console, AppGallery Connect, HMS/GMS, Data safety, privacy labels, privacy manifest, account deletion, review notes, store listing, screenshots, content rating, 12 testers, target API, or a rejection / "changes needed" / "Invalid parameter" message; or when asking which pages, screens or files an app must have before publishing.
---

# Mobile Store Compliance (Apple · Google Play · Huawei AppGallery)

Goal: the app is **built compliant from the start** and **every store submission passes on the first try**. This skill tells you what the project must contain (code, screens, web pages, files), what each store form expects, and what to warn the user about *before* it becomes a rejection.

All paths below are relative to this skill's folder.

## How to use this skill

Pick the entry point that matches the situation:

| Situation | Do |
|---|---|
| Starting a new app / adding a store | Step 1 → Step 2 → Step 3 (build the musts in), then Step 5 before release |
| About to submit / release | Steps 1, 4, 5, 6 |
| Already rejected | Step 7 |
| Only the question "what pages/screens do I need?" | Read `references/required-pages-and-screens.md` and answer from it |

### Step 1 — Profile the app (ask only what you can't detect)

Detect from the repo first, then confirm the gaps with the user:

- **Targets:** iOS / Android (Play) / Android (Huawei) — which stores, which countries (EU? mainland China? kids?).
- **Stack:** native SwiftUI/UIKit, Kotlin/Compose, Flutter, React Native/Expo, Capacitor/Cordova, KMP.
- **Accounts:** can users create an account? which sign-in methods (email, Google, Apple, Huawei ID, phone)? guest mode?
- **Money:** free / ads / in-app purchases / subscriptions / physical goods / donations / external payments.
- **Data:** what is collected (name, email, device ID, location, contacts, photos, health, analytics, crash logs) and which SDKs collect it.
- **Content:** user-generated content, chat, AI-generated output, mature themes, gambling, news, health/medical, finance, VPN.
- **Platform services:** push, maps, location, billing, sign-in, analytics, crash reporting — and whether each depends on Google Mobile Services (breaks on Huawei).

Write the profile as a short table at the top of your answer; it drives every later step.

### Step 2 — Run the scanner

```bash
python3 scripts/store_scan.py /path/to/project                      # all detected platforms
python3 scripts/store_scan.py /path/to/project --stores play,huawei  # limit stores
python3 scripts/store_scan.py /path/to/project --urls https://example.com/privacy https://example.com/delete-account
python3 scripts/store_scan.py /path/to/project --json > scan.json   # machine-readable
```

- Pure Python 3.9+ standard library; reads files only, never modifies the project.
- Prints findings `CRITICAL / HIGH / MEDIUM / INFO` with file, rule id and fix. Exit code `2` if any CRITICAL, `1` if HIGH, else `0`.
- `--urls` fetches each public URL with a browser, Googlebot, curl **and Java 8 (`Java/1.8.0_x`), Java 17 and okhttp user agents** (AppGallery's checker uses Java; Cloudflare blocks `Java/1.8` specifically). A 403 only for some agents means a CDN/WAF (e.g. Cloudflare Bot Fight Mode) is blocking store crawlers — a real cause of "AppGallery Connect fails to access the URL" and of Play "privacy policy URL not reachable".

The scanner covers what code can show. It cannot see store-console answers, backend uptime or listing text — Step 4 covers those.

### Step 3 — Build in the musts

Read `references/required-pages-and-screens.md` and `references/project-setup.md`, then make sure the project has **every row that applies to the profile**. The most common gaps, in order of how often they reject apps:

1. **Account deletion** inside the app (all three stores) **and** a public web deletion page (Google requires the URL; Huawei asks for it too).
2. **Privacy policy** public URL (no login, HTTPS, not a PDF/Google Doc, reachable by bots) **and** linked inside the app.
3. **Reviewer access**: guest mode or a working demo account that stays valid for the whole review; live production backend.
4. **Declarations match runtime**: Apple privacy labels + `PrivacyInfo.xcprivacy`, Play Data safety, Huawei privacy tags — including every SDK. A device/install ID used for guest accounts counts as "Device or other IDs".
5. **Payments**: digital goods → Apple IAP / Google Play Billing / HUAWEI IAP. No paid-looking UI if the app is actually free (Apple 2.1(b) business-model questions).
6. **Store-specific builds**: Huawei build with no hard GMS dependency, no Google Sign-In button that can't work, `appmarket://` store links, no "Google Play" wording.
7. **Sign in with Apple** (or another privacy-focused login) on iOS whenever a third-party login (Google, Facebook…) is offered.
8. **UGC / AI output**: report, block, moderation, terms acceptance.

When you add something, follow the project's own conventions (localization, tests). Templates for pages and texts are in `templates/`.

### Step 4 — Walk the store consoles (human checks)

For each target store open its reference and go through its **console checklist** with the user:

- Apple → `references/apple-app-store.md`
- Google Play → `references/google-play.md`
- Huawei AppGallery → `references/huawei-appgallery.md`
- Data/privacy form answers for all three from one worksheet → `templates/data-declaration-worksheet.md`
- Listing text limits, icon and screenshot sizes → `references/store-listing-assets.md`

Flag anything the user has to decide or sign themselves (legal declarations, DSA trader status, content-rating answers, pricing, countries). **Do not submit, publish, accept agreements or answer legal declarations on the user's behalf without explicit confirmation.**

### Step 5 — Pre-release gate

Go through `templates/release-checklist.md`. Every unchecked CRITICAL item is a release blocker. Also confirm:

- Version/build number is higher than anything already uploaded **to that store** (each store keeps its own history; Apple rejects a reused `CFBundleVersion`, Play a reused `versionCode`).
- Release build is signed with the right key (Play upload key / Huawei signing), minified, with debug symbols (Play native symbols, iOS dSYM).
- Production backend has every server change the build depends on deployed (a missing endpoint = crash or 404/405 in review).

### Step 6 — Write the review notes

Use `templates/review-notes.md`. Respect the field limits — **Huawei "For reviewer" remarks max 300 characters** (longer text gives a generic "Invalid parameter" error), Apple notes max 4000, Play App access instructions per entry. Lead with how to reach the core features without friction.

### Step 7 — After a rejection

Read `references/rejection-playbook.md`: find the store message, apply the mapped fix, change the build or the metadata (never resubmit unchanged), and reply to the reviewer with what changed. On Play, repeated rejected updates count against the account.

## Output contract

End every audit with:

1. **App profile** table (Step 1).
2. **Findings table**: `ID | Store | Severity | What | Fix | Where (file / console page)`, CRITICAL first.
3. **Missing artifacts**: pages, screens, files to add — with the template to use.
4. **User actions**: console answers, legal/DSA info, accounts, things only the user can do.
5. **Verdict**: `READY` or `BLOCKED (n critical)`. Never say ready while a CRITICAL stands.

## Accuracy rules

- Store policies change several times a year. Dates and numbers in `references/` carry a "last verified" note; when a decision depends on one (target API level, SDK deadline, tester count), re-check the linked official page if you have web access, and say when you couldn't.
- Quote guideline numbers only from the references or the official source. Never invent a guideline number.
- Distinguish **required** (store will reject) from **recommended** (reduces risk) in every recommendation.
