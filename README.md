# Keepstone — Android build via GitHub Actions

This turns the Keepstone web app into a real installable Android app,
using [Capacitor](https://capacitorjs.com/) to wrap the HTML page in a
native WebView shell, and GitHub Actions to compile the APK in the
cloud — you don't need Android Studio or any local build tools.

## What's in this folder

- `www/index.html` — the vault app itself (uses `localStorage`, so it
  actually persists data once installed on a phone)
- `capacitor.config.json` — tells Capacitor the app's ID/name and that
  the web content lives in `www/`
- `package.json` — the two Capacitor packages needed
- `.github/workflows/build-apk.yml` — the GitHub Actions workflow that
  does the actual build
- `.gitignore` — keeps the generated `android/` project and
  `node_modules/` out of your repo (the workflow regenerates them
  fresh on every run)

## Step 1 — Create a GitHub repository

1. Go to [github.com/new](https://github.com/new)
2. Name it something like `keepstone-vault`
3. Public or Private both work
4. Click **Create repository** — don't add a README/gitignore, since
   you already have those here

## Step 2 — Upload these files

**Important:** this project contains a hidden folder, `.github/`. Most
file browsers (Finder/Explorer) hide dot-folders, so dragging this
folder into GitHub's web uploader can silently skip it — which means
the workflow file never arrives and nothing builds. Two reliable ways
around this:

**Option A — git from a terminal (most reliable):**
```bash
cd keepstone-app
git init
git add -A
git commit -m "Initial Keepstone app"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/keepstone-vault.git
git push -u origin main
```
`git add -A` picks up dot-folders automatically, so this avoids the
hidden-file problem entirely.

**Option B — GitHub's web UI, file by file:**
On your repo page, click **Add file → Create new file**, and in the
"Name your file" box type the *full path* (GitHub creates the folders
for you when you type a `/`):
- `.github/workflows/build-apk.yml` — paste in that file's contents
- `package.json`
- `capacitor.config.json`
- `www/index.html`

Commit each one to the **main** branch.

## Step 3 — Let GitHub build it

Pushing to `main` triggers the workflow automatically. To check on it
or run it manually:

1. Open your repo on GitHub → **Actions** tab
2. Click **Build Android APK**
3. If it hasn't started, click **Run workflow** (top right) → **Run workflow**
4. Wait 3–6 minutes for the green checkmark

## Step 4 — Download the APK

1. On the finished workflow run's page, scroll to **Artifacts**
2. Click **keepstone-debug-apk** to download a zip
3. Unzip it — inside is `app-debug.apk`

## Step 5 — Install it on your phone

1. Transfer `app-debug.apk` to your Android phone (email, Drive, USB —
   whatever's easiest)
2. Tap the file to install it
3. Android will prompt about "unknown apps" the first time — allow
   installs from that source when asked
4. Open **Keepstone** from your app drawer

## Notes and limitations

- This is a **debug-signed APK** — fine for installing on your own
  device, not suitable for the Play Store (that needs a release
  keystore and Play Console setup — a separate, heavier process).
- Data is stored locally via `localStorage`, still encrypted the same
  way as the browser version (AES-GCM, key derived from your PIN via
  PBKDF2). Uninstalling the app deletes the vault.
- No native fingerprint/biometric unlock yet — that needs a Capacitor
  plugin and some glue code. Ask if you want it added.
- If the build fails, open the failed step in the Actions log and
  paste me the error text — that tells me exactly what to fix.
