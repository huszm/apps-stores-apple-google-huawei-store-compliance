# Project setup for multi-store releases

How to structure one codebase so the same app ships to the App Store, Google Play and Huawei AppGallery without per-store hacks.

---

## 1. Android product flavors (Play vs Huawei)

```kotlin
// app/build.gradle.kts
android {
    flavorDimensions += "store"
    productFlavors {
        create("play") {
            dimension = "store"
            isDefault = true
            buildConfigField("String", "STORE", "\"play\"")
            buildConfigField("String", "GOOGLE_WEB_CLIENT_ID", "\"${providers.gradleProperty("GOOGLE_WEB_CLIENT_ID").getOrElse("")}\"")
        }
        create("huawei") {
            dimension = "store"
            buildConfigField("String", "STORE", "\"huawei\"")
            buildConfigField("String", "GOOGLE_WEB_CLIENT_ID", "\"\"") // hides Google Sign-In
        }
    }
}

dependencies {
    // GMS-only libraries go to the play flavor only
    "playImplementation"("androidx.credentials:credentials-play-services-auth:<ver>")
    "playImplementation"("com.google.firebase:firebase-messaging:<ver>")
    // HMS equivalents for huawei
    "huaweiImplementation"("com.huawei.hms:push:<ver>")
}
```

- `app/src/huawei/AndroidManifest.xml` can remove permissions the Huawei build doesn't use:
  ```xml
  <manifest xmlns:android="http://schemas.android.com/apk/res/android" xmlns:tools="http://schemas.android.com/tools">
      <uses-permission android:name="com.google.android.gms.permission.AD_ID" tools:node="remove" />
  </manifest>
  ```
- Hide UI that depends on GMS at runtime as well: `if (BuildConfig.GOOGLE_WEB_CLIENT_ID.isNotBlank()) GoogleButton()`.
- Store links from one place:
  ```kotlin
  val storePageUri = if (BuildConfig.STORE == "huawei") "appmarket://details?id=$pkg" else "market://details?id=$pkg"
  val storePageWebUrl = if (BuildConfig.STORE == "huawei") "https://appgallery.huawei.com/app/C$appGalleryId" else "https://play.google.com/store/apps/details?id=$pkg"
  ```
- Build commands: `./gradlew bundlePlayRelease` (AAB for Play), `./gradlew assembleHuaweiRelease` (APK for AppGallery) or `bundleHuaweiRelease`.
- Unit-test the flavor config (e.g. a test asserting the Huawei build has no Google client ID and uses `appmarket://`).
- HMS Core kits need `agconnect-services.json` + the AGConnect Gradle plugin applied **only** to the huawei flavor.

**Flutter**: use `--flavor play|huawei` with matching Android flavors and a Dart `--dart-define=STORE=huawei`. **React Native**: Gradle flavors + `react-native-config`. Keep GMS plugins conditional.

## 2. Signing and secrets

- Keystore outside the repo (e.g. `~/keys/app-upload.jks`), referenced from a gitignored `keystore.properties`. Back it up in two places; losing it means you can't update on Huawei (unless Huawei App signing) and need a Play upload-key reset.
- iOS: automatic signing with the team ID in the project file; never commit `.p8`/`.p12`.
- API keys for production in CI secrets or local properties, not in source.

## 3. Versioning

| Store | Marketing version | Build number |
|---|---|---|
| Apple | `MARKETING_VERSION` / `CFBundleShortVersionString` | `CURRENT_PROJECT_VERSION` / `CFBundleVersion` — unique per upload |
| Play | `versionName` | `versionCode` — strictly increasing per app |
| Huawei | `versionName` | `versionCode` — strictly increasing within AppGallery |

Bump build numbers on **every** upload attempt, even a failed one that reached the store.

## 4. Backend contract for store features

The app's server must support, in production, before the build goes to review:

| Endpoint | Purpose |
|---|---|
| `DELETE /me` (or equivalent) | Account deletion; revoke Sign in with Apple token; delete or anonymize data; return 403 for protected admin accounts. |
| `PATCH /me` | Edit name/email; don't require a current password for social-login users without one. |
| Guest / device session | If guest mode exists; declare the device/installation ID in privacy forms. |
| Social sign-in verification | Accept iOS bundle ID **and** web Services ID as Apple audiences; Google audiences for each client. Log verification failures. |
| Health / status | For your own uptime monitoring during review. |
| Public pages | `/privacy-policy`, `/delete-account`, `/terms`, `/contact`, reachable by store crawlers. |

Deploy the backend **before** submitting; reviewers hit production.

## 5. CDN / WAF (Cloudflare, etc.)

Store URL checkers are not browsers. Make sure the public policy pages return **200** for:
- Browser UA, `Googlebot`, `curl/*`, and `Java/*` (AppGallery).
- No JavaScript challenge, no country block on those paths.
In Cloudflare: disable **Bot Fight Mode**, or create a WAF custom rule `URI Path in {"/privacy-policy" "/delete-account" …}` → **Skip** (Bot Fight Mode, managed rules). Test with `scripts/store_scan.py --urls`.

## 6. Debug symbols and crash reporting

- Android: `isMinifyEnabled = true`, upload `mapping.txt` (AAB includes it), `ndk { debugSymbolLevel = "FULL" }` for native code.
- iOS: upload dSYMs (Xcode does it for App Store builds); crash reporter SDK (Crashlytics/Sentry) must be declared in privacy forms.

## 7. Localization

Store reviewers check that app language matches the listing language. If the listing is Arabic, the app must be usable in Arabic (RTL correct); add English listing if the app supports English. Generate store strings from the same source as app strings to keep wording consistent.
