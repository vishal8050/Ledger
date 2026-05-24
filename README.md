# Daily Ledger — PWA

A minimal offline-first personal finance ledger. Track expenses, salary credits,
budgets, recurring entries, and download monthly/full PDF statements.

This folder is a complete **Progressive Web App**:

- `index.html` — the whole app (~92 KB, no build step)
- `manifest.webmanifest` — PWA manifest
- `sw.js` — service worker (offline cache)
- `icon.svg` + 4 PNG icons — app icons (any + maskable, 192 + 512)

## Run it locally

PWA features (service worker, install prompt) require **a real HTTP origin**.
Opening `index.html` from `file://` will work for normal use but won't enable
"Install app" or offline caching.

Easiest local server (any one of these):

```powershell
# Python (built into many systems)
python -m http.server 8080

# Node
npx serve .

# PHP
php -S localhost:8080
```

Then open <http://localhost:8080>.

## Install on Android (manual, no APK needed)

1. Host this folder anywhere with HTTPS (see below) and open the URL in Chrome on
   your phone.
2. Tap the **⋮** menu → **Install app** (or **Add to Home screen**).
3. The app gets an icon on your home screen, runs full-screen, and works offline.

This already behaves like a real app. The steps below are only if you specifically
want a `.apk` file (e.g. to sideload or distribute outside Play Store).

## Get a real `.apk` from this folder

You need three steps: **host → package → install**.

### 1. Host (free)

Pick any one — all are free and give you an HTTPS URL:

- **GitHub Pages** (recommended):
  1. Create a new public repo, upload these files to the root.
  2. Settings → Pages → Source: `main` branch, `/` (root) → Save.
  3. Wait ~1 min. URL: `https://<your-user>.github.io/<repo>/`.
- **Netlify Drop**: drag this folder onto <https://app.netlify.com/drop>. Instant URL.
- **Vercel**: `npx vercel` inside this folder.
- **Cloudflare Pages**: connect the GitHub repo.

### 2. Package with PWABuilder

1. Open <https://www.pwabuilder.com>.
2. Paste your hosted URL → **Start**.
3. The PWA score should be ≥ 80 (manifest + SW + HTTPS = green).
4. Click **Package for Stores** → **Android**.
5. Choose **Signed APK** (PWABuilder generates a key for you on first use; save
   the `.keystore` file it gives you — needed for future updates).
6. Download the ZIP — it contains `app-release-signed.apk` and an AAB.

### 3. Install on your phone

1. Transfer the `.apk` to your phone (USB, Drive, etc.).
2. Open it. Android will warn about unknown sources — allow your file manager to
   install apps once.
3. Done — opens like any installed app, with its own icon and full-screen UI.

## What the APK actually is

PWABuilder wraps your PWA in a Trusted Web Activity (TWA). The APK is small
(~few MB) because it doesn't bundle the web content — it loads your hosted URL
inside a Chrome-powered shell. Updates happen automatically when you push to
your host (no Play Store review).

If you want a fully offline-bundled APK (no hosted URL needed), use:

- [Bubblewrap CLI](https://github.com/GoogleChromeLabs/bubblewrap) for more
  control over the TWA build
- [Capacitor](https://capacitorjs.com) to wrap as a native app with full Android
  Studio access (bigger APK, more native features)

## Data storage & privacy

Everything is stored in your browser's `localStorage`. Nothing is sent anywhere.
Use **Menu → Export JSON** to back up, **Import JSON** to restore. The PDF
export is also fully local (uses the browser's print engine).

## Keyboard shortcuts (desktop)

| Key | Action |
|-----|--------|
| `←` `→` | Move selected day ± 1 |
| `↑` `↓` | Move selected day ± 1 week |
| `N` | New expense |
| `S` | Add salary credit |
| `T` | Jump to today |
| `M` | Open menu |
| `/` | Search |
| `P` | Download PDF |
| `Esc` | Close modal / sheet / search |

## Quick-add syntax (the bar at the bottom)

| Input | Result |
|-------|--------|
| `200 coffee` | ₹200 expense, category **Drinks** |
| `1500 swiggy dinner` | ₹1500 expense, category **Food orders** |
| `45 cigarettes` | ₹45 expense, category **Cigarettes** |
| `+50000 salary` | ₹50000 income, source **Salary** |
| `+2000 freelance` | ₹2000 income, source **Freelance** |

A built-in keyword dictionary maps ~100 common words to the right category.
Falls back to **Misc** if nothing matches.
