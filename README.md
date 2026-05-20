# Easy Time Tracker

A lightweight, privacy-first time tracking Progressive Web App built for professional services work. Track billable and non-billable hours across multiple projects throughout the day, review daily summaries, and monitor weekly utilization — all without an account, subscription, or server.

---

## Features

### Dual Timers
Run two independent timers side by side. Starting one automatically pauses the other, so switching between tasks requires a single click. Each timer supports pause, resume, discard (with two-click confirmation), and survives a browser refresh or accidental close — elapsed time is restored on reload.

### Flexible Logging
- **Threshold-based rounding** — entries round to the nearest 15-minute increment with a 5-minute grace: ≤5 min over a boundary rounds down, >5 min rounds up
- **Manual entries** — add, edit, or delete log entries for any day of the current or previous week
- **Task notes** — capture what you were working on alongside each time entry
- **Task autocomplete** — suggestions drawn from a persistent cross-week history as you type
- **Timezone-safe** — entries are stamped with local date, not UTC, so late-evening work always lands on the correct day

### Four Tabs

| Tab | What it shows |
|---|---|
| **Timers** | Active timers, configurable daily progress meter, billable utilization |
| **Log** | Individual entries for any day of the current or previous week, sorted oldest-to-newest, with add / edit / delete / copy |
| **Daily Summary** | Entries grouped by project, Internal-first then A–Z, with decimal hours, day total, task copy, and PSA logged checkboxes |
| **Weekly Summary** | All projects Sun–Sat (including those with no entries), 40-hour progress meter, weekly utilization, per-project targets |

### Progress Meters
- **Daily meter** — fills toward a configurable hour target (default 8h); color shifts from teal to rose at goal
- **Weekly meter** — fills toward 40 hours across the full week

### Billable Utilization
Billable % is shown on every tab, color-coded green / amber / red against a configurable target. Set your target in Settings; amber begins 20 points below the target, red below that.

### Project Management
Manage your project list from the ⚙ Settings panel:
- **Add** projects with a name and billable type
- **Rename** projects inline — click the name field to edit, press Enter or click away to save
- **Delete** projects (existing log entries are preserved)
- **Set weekly targets** per project — progress shown in the Weekly Summary target column
- **Dropdowns sort A–Z** with Internal projects always grouped at the top

### Summary Views
- **Daily Summary** — one row per project; tasks concatenated and comma-separated; time shown as decimal hours to 2 d.p.; day-total footer row; Internal-first sort
- **Weekly Summary** — all configured projects shown regardless of whether time has been logged; per-project target column with color-coded mini progress bar

### Cross-Tab Timer Banner
A slim persistent banner below the tab row shows any running or paused timer — elapsed time, project, task description — with fully functional Pause/Resume and Log buttons usable from any tab.

### Timer Persistence
Timer state (running or paused) is written to `localStorage` on every state change. If the browser closes or refreshes mid-task, the timer restores to paused with all accumulated time intact, including elapsed time while the page was closed.

### Task History
Autocomplete suggestions persist across weekly resets in a dedicated history store (up to 300 unique entries, most-recent first). Every timer stop and manual entry add contributes to the history. The current week's log entries are also included.

### Configurable Targets
- **Daily hour target** — controls the daily progress meter (default 8h, accepts decimals)
- **Billable utilization target** — controls color thresholds across all tabs (default 80%)
- **Per-project weekly targets** — set per project in Settings; progress tracked in the Weekly Summary

### File System Backup
Link a local JSON file as an automatic backup target (Chrome / Edge only). Once linked, the app writes your full state — log entries, projects, settings, PSA status, and task history — to the file automatically 2 seconds after any data change. If localStorage is cleared, a restore banner appears on next load offering to recover from the backup file. Manage the backup link in Settings → Backup.

### Previous Week View
A week navigation bar on the Log, Daily Summary, and Weekly Summary tabs lets you browse the previous week's data. Entries from both the current and previous week are retained in storage, so missed PSA entries can be reviewed and marked off without a CSV export.

### Auto-Refresh on Deploy
The service worker uses a network-first strategy for the main HTML document, so new deployments are picked up automatically on the next page visit. If the service worker itself updates, the page reloads silently to activate the new version.
Toggle in Settings. Automatically follows OS preference on first load. Choice persists across sessions.

### Data & Privacy
All data lives in your browser's `localStorage`. Nothing is sent to any server.

### Export & Copy
- **Export CSV** — downloads the full week's log
- **Refresh** — reloads the page; active timers restore automatically from saved state
- **Copy task text** — single-click copy per row (Log tab: individual entry; Daily Summary: concatenated project tasks)

---

## PSA Timecard Workflow

The Daily Summary tab is designed to match the PSA entry flow:

1. Open the **Daily Summary** tab and select today using the day-filter pills
2. For each project row: copy task text (⎘), enter time and tasks in PSA, check the **PSA** checkbox to mark it done
3. Click **Open PSA Timecard Entry** at the bottom of the card to jump directly to the PSA entry page
4. PSA checkbox state persists across reloads and clears automatically on Reset Week

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

| Data | Scope |
|---|---|
| Log entries | Current week + previous week (2 weeks); older entries cleared on load |
| Task autocomplete history | Indefinitely, up to 300 entries; survives weekly reset |
| Active timer state | Until stopped or discarded; restores after page reload |
| PSA logged status | Per week; clears on Reset Week for that week |
| Project list, names & targets | Indefinitely |
| Settings (targets, theme) | Indefinitely |
| File backup (optional) | Persists until you unlink or delete the file; survives browser history clears |

- **Clearing entries** — "Clear this day" (Log tab) or "Reset week" (Weekly Summary)
- **Optional backup** — link a local JSON file in Settings → Backup (Chrome / Edge); the app writes to it automatically on every save
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
