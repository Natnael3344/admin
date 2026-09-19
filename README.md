# ScrapifyAdmin

ScrapifyAdmin is the admin/back-office web app for **Scrapify** (Firebase project `scrapify-8e990`), built with **Flutter** and compiled to a static web bundle. This repository holds the **production build output** of that app (the files produced by `flutter build web`), not the Dart/Flutter project source — there is no `pubspec.yaml` or `lib/` folder here, only `index.html`, the compiled `main.dart.js`, the CanvasKit renderer, and the app's static assets.

> Note for contributors: because only the build output is committed, this repo can be *served* and *deployed* as-is, but it cannot be built, run in `flutter run`, or modified feature-by-feature without the original Flutter source project, which lives elsewhere.

## What's actually in this repo

| Path | Purpose |
|---|---|
| `index.html` | App shell; loads the Firebase JS SDK (Analytics), then boots the compiled app via `main.dart.js` |
| `main.dart.js` | The entire compiled Flutter application (Dart → JS via `dart2js`) |
| `flutter.js` / `flutter_service_worker.js` | Flutter's web bootstrap loader and PWA service worker (asset caching/offline support) |
| `canvaskit/` | The CanvasKit (Skia-on-WebAssembly) renderer Flutter uses to draw the UI in the browser |
| `assets/` | Bundled fonts, icons, images, the Flutter `AssetManifest.json`/`FontManifest.json`, and `NOTICES` (auto-generated OSS license file listing every package compiled into the app) |
| `manifest.json`, `icons/`, `favicon.png` | Web app manifest and icons (PWA installability) |
| `version.json` | Build identity: `{"app_name":"admin","version":"1.0.0","build_number":"1","package_name":"admin"}` |

The repository history consists of two manual "Add files via upload" commits — this build was uploaded directly through the GitHub UI rather than produced by a CI pipeline.

## Features (verified from the shipped assets)

Since the Dart source isn't in this repo, `main.dart.js` is minified/tree-shaken and not practically readable. The features below are grounded in concrete evidence in the repo (bundled navigation icons and the package license manifest at `assets/NOTICES`), not guesses about business logic:

- **Firebase-backed backend.** `assets/NOTICES` shows the build includes `firebase_core`, `firebase_auth`, `cloud_firestore`, `firebase_database` (Realtime Database), and `firebase_storage` — i.e. auth, two kinds of Firebase data storage, and file storage.
- **Firebase Analytics**, initialized directly in `index.html` against the `scrapify-8e990` project.
- **Admin navigation sections.** `assets/assets/icons/menu_*.svg` includes icons named `menu_dashbord`, `menu_doc`, `menu_notification`, `menu_profile`, `menu_setting`, `menu_store`, `menu_task`, and `menu_tran` — consistent with a sidebar covering Dashboard, Documents, Notifications, Profile, Settings, Store, Tasks, and Transactions.
- **Document/file browsing.** A large set of file-type icons is bundled (`doc_file`, `excle_file`, `pdf_file`, `media_file`, `sound_file`, `unknown`, plus `google_drive`, `drop_box`, `one_drive`, `Figma_file`, `xd_file`), suggesting a document-management view that shows different file/cloud-source icons.
- **Data tables and charts**, via the bundled `data_table_2` and `fl_chart` packages — typical for an admin dashboard's listing/reporting screens.
- **SMS/telephony integration**, via the bundled `twilio_flutter` package.
- **Installable PWA**, via `manifest.json` and `flutter_service_worker.js` (caches all assets listed in `RESOURCES` for offline use).

## Tech stack

- **Flutter/Dart**, compiled for the web (`flutter build web`) using the **CanvasKit** renderer.
- **Firebase**: Core, Auth, Cloud Firestore, Realtime Database, Cloud Storage, Analytics (client config is inline in `index.html`).
- Other Flutter packages bundled in this build (from `assets/NOTICES`): `provider` (state management), `http`, `fl_chart`, `data_table_2`, `dropdown_button2`, `dropdown_textfield`, `flutter_svg`, `google_fonts`, `twilio_flutter`, `path_provider`, `intl`, `loading_indicator`, `equatable`.
- This is **not** a Node/JavaScript project — there is no `package.json` and no npm/yarn scripts anywhere in the repo.

## Running this build locally

There's no source to compile, so "running" it means serving the static bundle over HTTP (opening `index.html` directly via `file://` will not work correctly — the service worker and asset manifest require a real origin).

```bash
git clone https://github.com/Natnael3344/admin.git
cd admin

# any static file server works, e.g.:
npx serve .
# or
python3 -m http.server 8080
```

Then open the printed local URL in a browser. The app will connect to the live `scrapify-8e990` Firebase project (see `index.html`), so data shown will reflect that project's real Firestore/Realtime Database/Storage content.

## Deploying

Because this repo *is* a `flutter build web` output directory, it can be deployed as-is to any static host (Firebase Hosting, Netlify, S3 + CloudFront, GitHub Pages, etc.) by pointing the host at the repository root. There is no build step to run here — updating the app means rebuilding from the original Flutter source project and re-uploading the new `build/web` output over these files.

See `GUIDE.md` for configuration details and how the pieces fit together.
