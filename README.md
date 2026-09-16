# NERV // EVA Chrono-Timer

An Evangelion-inspired **watch + chronograph + countdown timer**, styled after the EVA units' internal-power activity-limit display. Amber-on-black tactical UI with CRT scanlines, warning states, and beeps.

- **Watch** — live `HH:MM:SS` clock
- **Chronograph** — stopwatch with millisecond precision (`HH:MM:SS.mmm`), Start / Stop / Reset
- **Countdown** — set up to `24:00:00` by typing or presets; millisecond display; turns **orange under 5 min**, **pulsing red under 1 min**, beeps in the final 10 s, and sounds an alarm at zero

The whole app lives in [`www/`](www/) — a self-contained static web app (no build step, no external dependencies).

---

## 1. Run it in a browser (right now)

Open [`www/index.html`](www/index.html) directly in any browser, **or** serve the folder:

```bash
# Python 3
python -m http.server 8123 --directory www
# then open http://localhost:8123
```

## 2. Install it on your phone as a PWA (no build tools)

The app is a full Progressive Web App (manifest + offline service worker + icons).

1. Host the `www/` folder over **HTTPS** (a service worker only runs on HTTPS or `localhost`). Easiest free options:
   - **GitHub Pages** — push `www/` to a repo and enable Pages.
   - **Netlify** — drag-and-drop the `www/` folder at https://app.netlify.com/drop.
   - **Cloudflare Pages / Vercel** — point at the repo, set the output/root to `www`.
2. Open the hosted URL in **Chrome on Android**.
3. Tap the **⋮ menu → "Install app"** (or "Add to Home screen").

You now get a home-screen icon, fullscreen (no browser bar), and full offline use.

> iOS: open in Safari → **Share → Add to Home Screen**.

## 3. Build a real Android APK with Capacitor

This produces an installable `.apk` you can sideload or publish to the Play Store.

### Prerequisites (install once)
- **Node.js 18+** — https://nodejs.org
- **JDK 17** — https://adoptium.net
- **Android Studio** (includes the Android SDK) — https://developer.android.com/studio

### Steps
```bash
cd eva-chrono-timer

# 1. install Capacitor
npm install

# 2. create the native Android project (reads capacitor.config.json -> webDir: "www")
npx cap add android

# 3. copy the web app into the native project
npx cap sync

# 4a. open in Android Studio to run on a device/emulator or build a signed release:
npx cap open android

# 4b. …or build a debug APK from the command line:
cd android
./gradlew assembleDebug        # Windows: gradlew.bat assembleDebug
# -> android/app/build/outputs/apk/debug/app-debug.apk
```

Install the APK on a phone with USB debugging on:
```bash
adb install android/app/build/outputs/apk/debug/app-debug.apk
```

### App identity
- **App ID:** `com.nerv.evatimer`
- **App name:** `EVA Timer`

Change these in [`capacitor.config.json`](capacitor.config.json) before your first `cap add android`.

### Whenever you edit the app
After changing anything in `www/`, re-sync the native project:
```bash
npx cap sync
```

### App launcher icons
Capacitor uses its own icon set under `android/app/src/main/res/`. The quickest way to apply the amber NERV mark to the native launcher icons is `@capacitor/assets`:
```bash
npm install -D @capacitor/assets
# place a 1024x1024 icon at resources/icon.png, then:
npx capacitor-assets generate --android
```

---

## Project layout
```
eva-chrono-timer/
├─ www/                      # the app (PWA + Capacitor webDir)
│  ├─ index.html
│  ├─ manifest.webmanifest
│  ├─ sw.js                  # offline service worker
│  └─ icons/{icon-192,icon-512}.png
├─ package.json              # Capacitor tooling
├─ capacitor.config.json     # appId / appName / webDir
└─ README.md
```
