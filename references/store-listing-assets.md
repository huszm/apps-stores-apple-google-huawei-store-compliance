# Store listing assets — sizes and text limits

Last verified: 2026-11. Stores occasionally change limits; when an upload is refused, read the error text for the exact rule.

## Text limits

| Field | Apple App Store | Google Play | Huawei AppGallery |
|---|---|---|---|
| App name | 30 | 30 | 64 |
| Subtitle / short description | Subtitle 30 | Short description 80 | Brief introduction 80 |
| Full description | 4000 | 4000 | 8000 |
| Promotional text | 170 | – | – |
| Keywords | 100 bytes | – (no field; use description) | Tags in console |
| Release notes | 4000 | 500 per language | 500 |
| Reviewer notes | 4000 | App access instructions (per entry) | **300** ("For reviewer" remarks) |

## Graphics

| Asset | Apple | Google Play | Huawei |
|---|---|---|---|
| Icon | 1024×1024 PNG, no alpha, no rounded corners | 512×512 PNG 32-bit, ≤ 1 MB | 216×216 PNG, ≤ 2 MB |
| Phone screenshots | 6.9": 1320×2868 or 1290×2796 (portrait), 1–10 | 2–8, 320–3840 px, ratio ≤ 2:1 (1080×1920 recommended), 4+ for featuring | 3–5, same orientation, ≥ 450×800 (1080×1920 recommended), ≤ 2 MB |
| Tablet screenshots | 13" iPad 2064×2752 / 2048×2732 if iPad supported | 7" and 10" recommended | Optional |
| Feature graphic | – | **1024×500 required** | – (optional promo banner in some regions) |
| Video | App preview 15–30 s, device resolution | YouTube link | Optional video upload |

## Screenshot rules shared by all stores

- Show the **app in use**: real screens from the current build, not just splash/login.
- No other store's badges or device frames of another platform (no iPhone frame on Play/Huawei, no "Google Play" on Huawei).
- Captions are fine; avoid claims ("#1", "best") and prices that may change.
- Localize per listing language (RTL screenshots for Arabic listings).
- Remove status-bar clutter (simulator `xcrun simctl status_bar … override`, Android demo mode).

## Producing them

- iOS: capture on a 6.9" simulator (`xcrun simctl io booted screenshot`), and 13" iPad if supported.
- Android: emulator at 1080×1920/1080×2400 (`adb exec-out screencap -p > s.png`); crop or pad to 9:16 for Play.
- Huawei: reuse Android screenshots from the Huawei build (no Google button visible) and export a 216×216 icon.
- Keep sources under `docs/<store>/` in the repo so updates are reproducible.
