# Easy Time Tracker

A lightweight, privacy-first time tracking Progressive Web App built for professional services work. Track billable and non-billable hours across multiple projects throughout the day, review daily summaries, and monitor weekly utilization — all without an account, subscription, or server.

---

## Features

### Dual Timers
Run two independent timers side by side. Starting one automatically pauses the other, so switching between tasks requires a single click. Each timer supports pause, resume, and discard without logging.

### Flexible Logging
- **Round-up billing** — all entries round up to the nearest 15-minute increment
- **Manual entries** — add, edit, or delete log entries for any day of the current week
- **Task notes** — capture what you were working on alongside each time entry

### Four Tabs

| Tab | What it shows |
|---|---|
| **Timers** | Active timers, daily 8-hour progress meter, billable utilization |
| **Log** | Individual entries for any day with add / edit / delete / copy |
| **Daily Summary** | Entries grouped by project with tasks concatenated, PSA Timecard link |
| **Weekly Summary** | Sun–Sat table by project, 40-hour progress meter, weekly utilization |

### Progress Meters
- **Daily meter** — fills toward 8 hours; turns from teal to rose when you hit the target
- **Weekly meter** — fills toward 40 hours across the full week

### Billable Utilization
Billable % is surfaced on every tab, color-coded green (≥ 80%), amber (60–79%), or red (< 60%).

### Project Management
Manage your project list from the ⚙ Settings panel. Add or delete projects, mark each as billable or non-billable. Dropdowns sort A–Z automatically.

### Light & Dark Mode
Toggles in Settings. Automatically follows your OS preference on first load. Choice persists across sessions.

### Data & Privacy
All data lives in your browser's `localStorage`. Nothing is sent to any server. Data persists across sessions and resets weekly (Sunday midnight).

### Export
- **Export CSV** — downloads the full week's log for pasting into billing or project management tools
- **Copy task text** — single-click copy of any entry's task notes to clipboard
- **Print** — clean print layout for paper timesheets

---

## PWA Installation

Easy Time Tracker is a Progressive Web App. Once deployed to HTTPS, it can be installed as a standalone app on desktop and mobile.

**Chrome / Edge (desktop):**
1. Navigate to your deployed URL
2. Click the **⊕ Install App** button in the top bar, or use the browser's install option in the address bar
3. The app opens in its own window with no browser chrome

**iOS Safari:**
1. Navigate to your deployed URL
2. Tap the Share button → **Add to Home Screen**

After installation the app works fully offline.

---

## Deploying to GitHub Pages

### Repository structure

```
your-repo/
├── index.html
├── manifest.json
├── sw.js
├── .nojekyll
├── icon-192.png
├── icon-512.png
├── icon-180.png
└── .github/
    └── workflows/
        └── deploy.yml
```

### Steps

1. Create a new **public** GitHub repository
2. Upload all files, placing `deploy.yml` inside `.github/workflows/`
3. Go to **Settings → Pages → Build and deployment → Source** and select **GitHub Actions**
4. Push to `main` — the workflow deploys automatically
5. Your app will be live at `https://<username>.github.io/<repo-name>/`

### GitHub Actions workflow

The included `deploy.yml` uses Node.js 24-compatible action versions:

```yaml
- uses: actions/checkout@v5
- uses: actions/configure-pages@v5
- uses: actions/upload-pages-artifact@v3
- uses: actions/deploy-pages@v4
```

---

## Running Locally

No build step required. Open `index.html` directly in any modern browser:

```
open index.html          # macOS
start index.html         # Windows
xdg-open index.html      # Linux
```

> **Note:** The service worker and PWA install prompt require HTTPS and will not activate when running from `file://`. All other features work normally.

---

## Tech Stack

| Concern | Approach |
|---|---|
| Framework | None — vanilla HTML, CSS, JavaScript |
| Fonts | IBM Plex Mono (timers), Inter (UI) via Google Fonts |
| Storage | `localStorage` (browser-native, no backend) |
| Offline | Service Worker with cache-first strategy |
| Icons | Programmatically generated PNG (Pillow) |
| Hosting | GitHub Pages via GitHub Actions |

---

## Data Persistence

- **Within a week** — all log entries persist across browser sessions via `localStorage`
- **Week rollover** — on load, entries outside the current Sun–Sat week are automatically cleared
- **Clearing data** — use "Clear this day" on the Log tab, or "Reset week" on the Weekly Summary tab
- **No cloud sync** — data is local to the browser and device it was entered on

---

## Project Structure

```
index.html     Main application (all HTML, CSS, and JS in one file)
manifest.json  PWA manifest (name, icons, display mode, theme color)
sw.js          Service worker (caching, offline support)
.nojekyll      Disables Jekyll processing on GitHub Pages
icon-180.png   Apple touch icon (180×180)
icon-192.png   PWA icon (192×192)
icon-512.png   PWA icon (512×512)
deploy.yml     GitHub Actions workflow for Pages deployment
```

---

## License

MIT — free to use, modify, and self-host.
