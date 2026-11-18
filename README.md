# mobile-store-compliance

An AI-agent skill (Claude Code, Codex, Cursor, Gemini CLI… any tool that reads `SKILL.md`) that makes mobile apps pass review on **Apple App Store, Google Play and Huawei AppGallery** — and tells you, while you build, which pages, screens, files and store answers your app must have.

- **Three stores, one checklist**: Apple, Google Play *and* Huawei AppGallery (GMS→HMS, AppGallery console fields, 300-char reviewer remarks, DSA, China gates).
- **Required pages & screens**: privacy policy, delete-account page, support, terms, in-app deletion, consent, report/block, paywall rules — with ready templates.
- **Scanner** (`scripts/store_scan.py`, Python stdlib only): native iOS/Android, Flutter, React Native/Expo, Capacitor. Finds missing privacy manifest, purpose strings, Sign in with Apple, account deletion, target SDK, sensitive permissions, exported components, GMS deps leaking into the Huawei build, Google-only store links, paid UI without billing, secrets, cleartext — and tests your public URLs with the **user agents store crawlers really use** (catches Cloudflare blocking AppGallery's `Java/1.8` checker).
- **Field-tested**: entries marked 🧪 come from real submissions of a production app to all three stores.

## Install

Clone it into your project's skills folder (project-scoped) or your home skills folder (all projects):

```bash
# project
git clone https://github.com/ibadwi/apps-stores-apple-google-huawei-store-compliance .claude/skills/mobile-store-compliance
# or global
git clone https://github.com/ibadwi/apps-stores-apple-google-huawei-store-compliance ~/.claude/skills/mobile-store-compliance
```

Update later with `git -C .claude/skills/mobile-store-compliance pull`.

Other agents: point them at `SKILL.md` (e.g. add "Follow `.claude/skills/mobile-store-compliance/SKILL.md` before any store submission" to `AGENTS.md`).

## Use

Ask your agent, e.g.:
- "Prepare this app for App Store, Google Play and AppGallery."
- "What pages and screens does my app need before publishing?"
- "Huawei says *Invalid parameter* / *fails to access the URL*."

Or run the scanner yourself:

```bash
python3 .claude/skills/mobile-store-compliance/scripts/store_scan.py . --urls https://example.com/privacy-policy https://example.com/delete-account
```

Exit code `2` = critical findings (release blocker), `1` = high, `0` = clean. `--json` for CI, `--stores play,huawei` to limit.

Run the scanner's tests:

```bash
cd .claude/skills/mobile-store-compliance/scripts && python3 -m unittest test_store_scan
```

## Contents

| Path | What |
|---|---|
| `SKILL.md` | Workflow the agent follows (profile → scan → build the musts → consoles → gate → review notes → rejections) |
| `references/required-pages-and-screens.md` | Every page, screen and project file, per store, required vs recommended |
| `references/apple-app-store.md` | Guidelines that reject most apps, privacy labels/manifest, App Store Connect fields |
| `references/google-play.md` | Account gates (12 testers × 14 days), target API, Data safety, policies, listing, release flow |
| `references/huawei-appgallery.md` | GMS→HMS table, AppGallery Connect version page field by field, review themes, China |
| `references/project-setup.md` | Play/Huawei flavors, signing, versioning, backend contract, CDN/WAF, symbols |
| `references/store-listing-assets.md` | Text limits and image sizes for the three stores |
| `references/rejection-playbook.md` | Store messages → cause → fix |
| `templates/` | Privacy policy, delete-account page, terms, review notes (3 formats), data-declaration worksheet, release checklist |
| `scripts/store_scan.py` | Scanner + URL crawler check; `test_store_scan.py` tests |

## Disclaimer

Store policies change often. Each reference has a "last verified" date and links to the official source; check it before a decision that depends on a date or number. Templates are not legal advice.

## License

[MIT](LICENSE)

---

## بالعربي

مهارة لوكلاء الذكاء الاصطناعي تساعد أي مطوّر على بناء تطبيق يُقبل من أول مرة في **متجر Apple وGoogle Play وHuawei AppGallery**. تنبّهك أثناء البناء إلى الصفحات والشاشات والملفات الإلزامية، مثل سياسة الخصوصية وصفحة حذف الحساب والحذف داخل التطبيق وتسجيل الدخول بحساب Apple والإفصاح عن البيانات ونسخة هواوي بدون خدمات Google. وفيها سكربت يفحص المشروع ويختبر روابطك بنفس طريقة زواحف المتاجر.

**التثبيت:**

```bash
git clone https://github.com/ibadwi/apps-stores-apple-google-huawei-store-compliance .claude/skills/mobile-store-compliance
```

ثم اطلب من الوكيل: «جهّز التطبيق للنشر على المتاجر الثلاثة».
