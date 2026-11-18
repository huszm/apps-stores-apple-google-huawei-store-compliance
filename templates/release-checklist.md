# Release checklist

Copy into the release ticket. `[C]` = critical (release blocker), `[H]` = high, `[M]` = medium. Skip rows that don't apply and say why.

## All stores

- [ ] [C] Production backend deployed with every endpoint this build calls; uptime monitored during review.
- [ ] [C] Reviewer can reach all features: guest mode, or demo account valid ≥ 2 weeks, no 2FA/SMS.
- [ ] [C] In-app account deletion works end-to-end (if accounts exist).
- [ ] [C] Privacy policy URL public, HTTPS, localized, reachable by `scripts/store_scan.py --urls` (all agents 200).
- [ ] [C] Delete-account web page public (Play/Huawei; if accounts exist).
- [ ] [C] Privacy declarations (Apple labels, Play Data safety, Huawei privacy) match the worksheet, including SDKs and device IDs.
- [ ] [C] No paid-looking UI unless real store billing is implemented.
- [ ] [C] Build number higher than any previous upload to that store.
- [ ] [H] Privacy policy, terms (if any), delete account, contact linked inside the app.
- [ ] [H] UGC/AI content: report, block, moderation, terms.
- [ ] [H] Permissions: only used ones; rationale shown before each prompt.
- [ ] [H] Offline/error states show retry instead of crash/blank.
- [ ] [H] Screenshots from the current build, per language, correct sizes.
- [ ] [H] Listing text within limits, no other-platform or competitor names, no "best/#1".
- [ ] [M] Crash reporting and symbols (dSYM / mapping / native symbols).
- [ ] [M] Release notes written per language.
- [ ] [M] EU DSA trader info decided (provide or exclude EU).

## Apple

- [ ] [C] `PrivacyInfo.xcprivacy` with required-reason APIs; third-party SDK manifests present.
- [ ] [C] Purpose strings for every permission used, specific and localized.
- [ ] [C] Sign in with Apple if any third-party login.
- [ ] [C] Built with the currently required Xcode/SDK.
- [ ] [H] `ITSAppUsesNonExemptEncryption` set.
- [ ] [H] iPad: layouts tested and 13" screenshots provided, or iPhone-only.
- [ ] [H] Age rating questionnaire (new format) answered; App Privacy published.
- [ ] [H] Review notes (≤ 4000) with access, deletion path, purchases, external services.
- [ ] [M] TestFlight tested on a real device.

## Google Play

- [ ] [C] `targetSdk` meets the current requirement.
- [ ] [C] AAB signed with upload key; Play App Signing enrolled.
- [ ] [C] New personal account: 12 testers × 14 days closed test done, production access granted.
- [ ] [C] App content: privacy policy, ads, app access, content rating, target audience, Data safety, government/financial/health declarations — all green.
- [ ] [C] Sensitive permissions declared or removed (incl. ones merged from SDKs — check merged manifest).
- [ ] [H] 16 KB page-size compatible native libs (targetSdk ≥ 35).
- [ ] [H] Feature graphic 1024×500, icon 512, ≥ 2 screenshots (4+ recommended).
- [ ] [H] After saving: **Publishing overview → Send changes for review**, status shows "In review".
- [ ] [M] Native debug symbols / mapping uploaded; pre-launch report clean.

## Huawei AppGallery

- [ ] [C] Huawei build has no hard GMS dependency (Google Sign-In hidden, no FCM/Maps/Play Billing requirement) — tested on a Huawei device or cloud debugging.
- [ ] [C] No "Google Play" text/badges; store links `appmarket://`.
- [ ] [C] Digital goods via HUAWEI IAP (or app is free).
- [ ] [C] "For reviewer" remarks ≤ 300 characters.
- [ ] [C] Privacy URL returns 200 for `Java/` user agent.
- [ ] [H] Countries chosen; mainland China excluded unless ICP/MIIT/copyright ready; re-upload package after changing countries if the draft won't save.
- [ ] [H] Content rating questionnaire reviewed (per region).
- [ ] [H] Personal-data, cross-border and permission purposes filled; Generative-AI question answered accurately.
- [ ] [H] Icon 216×216, 3–5 screenshots per language from the Huawei build.
- [ ] [M] Unused permissions removed in `src/huawei/AndroidManifest.xml`.
