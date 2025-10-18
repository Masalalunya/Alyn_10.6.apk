app-game-release-1 — minimal Android project skeleton
=====================================================

What this contains
- Minimal Android Studio project skeleton (app module).
- versionName set to "10.6" and versionCode set to 10600.
- A simple MainActivity and layout styled to resemble Alyn_10.6 UI cues.

Important: This is a source skeleton — it is NOT a compiled APK. To produce an APK you must build it locally using Android Studio or the command-line SDK.

Quick build steps (Android Studio)
1. Open Android Studio -> Import project and pick the folder.
2. Let Gradle sync (you need Android SDK installed).
3. Build -> Generate Signed Bundle / APK.
4. Use a keystore (see below to create a test keystore).

Create a test keystore (command line)
- Run:
  keytool -genkeypair -v -keystore test_keystore.jks -alias testkey \
    -keyalg RSA -keysize 2048 -validity 10000

Signing via command line (after building an unsigned APK)
- jarsigner -verbose -sigalg SHA256withRSA -digestalg SHA-256 -keystore test_keystore.jks \
    app-release-unsigned.apk testkey
- Then align:
  zipalign -v -p 4 app-release-unsigned.apk app-release-signed.apk

Upload & share
- Option A: Upload the signed APK to Google Drive and share link.
- Option B: Use GitHub Releases to upload the APK (recommended for versioning).
- Option C: Use a simple file-hosting command-line like transfer.sh:
  curl --upload-file app-release-signed.apk https://transfer.sh/app-release-signed.apk

If you want, reply **"buat zip"** and aku akan buatkan file ZIP project ini supaya bisa kamu download langsung.


---
## GitHub Actions: automatic build & release (CI)

I've added a GitHub Actions workflow at `.github/workflows/android-build.yml` that will attempt to build `assembleRelease` and optionally sign the APK if you provide a keystore as a **base64-encoded secret**.

How to use the CI workflow:
1. Push this repository to GitHub (create a new repo and push).
2. Add the following repository secrets (Settings -> Secrets & variables -> Actions):
   - `KEYSTORE_BASE64` — the keystore file encoded in base64 (or leave empty to skip signing).
     Create it locally with: `base64 -w0 your_keystore.jks` (Linux/macOS) and copy the output.
   - `KEYSTORE_PASSWORD` — password for the keystore.
   - `KEY_ALIAS` — alias name for the key (e.g., `testkey`).
   - `KEY_PASSWORD` — key password (often same as keystore password).
3. Push to `main` (or `master`) or run the workflow manually from the Actions tab.
4. After the workflow runs, download the `app-release-apk` artifact from the workflow run — it will contain the built APK (and the signed aligned APK if keystore was provided).

Notes & troubleshooting:
- The CI installs Android SDK components and Gradle on the runner. If the build fails due to missing Gradle tasks, consider adding a Gradle wrapper or adjusting Gradle plugin versions in `app/build.gradle`.
- This workflow uses secrets for signing; never commit your keystore file directly to the repo.

If kamu mau, aku bisa:
- tambah Gradle wrapper files ke project supaya CI lebih konsisten, atau
- bantu kamu push ke GitHub (kalau mau beri akses), atau
- jelaskan langkah build lokal step-by-step (Android Studio).

