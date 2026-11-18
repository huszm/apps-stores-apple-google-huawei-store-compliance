# Delete-account web page — template

Required by Google Play (URL in the Data safety form) for any app with account creation, and asked by Huawei AppGallery. Must be public (no login needed to read it), name the app, and give a way to request deletion **without reinstalling the app**.

---

# Delete your <App name> account

You can delete your <App name> account and its data at any time.

## Option 1 — In the app (fastest)

1. Open <App name> and sign in.
2. Go to **<Account>** → **<Delete account>**.
3. Confirm. Your account is deleted <immediately>.

## Option 2 — Without the app

Send a request from the email address of your account to **<privacy@example.com>** with the subject "Delete my account", or use the form below. We confirm and complete the deletion within <7> days.

<Optional form: email field + confirmation. If you add a form, verify ownership (e.g. emailed link) before deleting.>

## What is deleted

- Your profile (name, email, sign-in links).
- <Content you created: history, favorites, collections…>
- <Guest/device identifiers linked to the account.>

## What we may keep

- <Records we must keep by law (e.g. invoices) for <N> years.>
- <Anonymous, aggregated statistics that no longer identify you.>
- Backups are purged within <30> days.

Questions: <contact email>. See also our [privacy policy](<URL>).

---

Implementation notes:
- Route example: `GET /delete-account` (+ localized `/<lang>/delete-account`). Return 200 for bots (no JS challenge).
- Link it from the privacy policy and from the app's account screen.
- Localize for each listing language.
