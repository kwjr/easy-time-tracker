# Easy Time Tracker — Changelog

All notable changes to this project are documented here.
Versions are listed from newest to oldest.

---

## v1.8.0 — Default Projects Update

### Added
- **Internal / PTO** added as a third default non-billable project, pre-loaded alongside Internal / Admin and Internal / Professional Development on first run

---

## v1.7.0 — Data Persistence & UX Improvements

### Added
- **Timer persistence** — running and paused timer state (accumulated time, task text, project, and start timestamp) is written to `localStorage` on every state change; timers restore to paused on page reload, with elapsed time added for any time that passed while the page was closed
- **Task history persistence** — autocomplete suggestions now draw from a dedicated history store (up to 300 unique entries, most-recent first) that survives the weekly log rollover; the current week's log entries are included as a supplemental source
- **Configurable daily hour target** — Settings → Targets includes a "Daily hour target" field (default 8h, accepts decimals); the daily progress meter uses this value instead of the hardcoded 480-minute constant
- **Project rename** — project names in Settings are now editable text fields; click a name to edit, press Enter or click away to save; existing log entries update automatically to reflect the new name
- **Weekly Summary shows all projects** — all configured projects are always displayed in the weekly table regardless of whether any time has been logged against them that week; projects with no entries show "—" in all day cells
- **Log tab: oldest-to-newest sort** — log entries for any selected day now display chronologically (oldest at top) rather than reverse-chronological
- **Discard confirmation** — the discard button now uses the same two-click pattern as delete; first click shows "Sure?" in red, second click within 3 seconds confirms; no browser dialog required

### Bug Fixes
- **UTC timezone date mismatch** — `todayKey()` and `weekDays()` previously used `.toISOString()` which returns a UTC date; for US time zones (UTC−5 through UTC−8) this caused entries after 4–7 pm to be attributed to the following day; both functions now use local date methods (`getFullYear()`, `getMonth()`, `getDate()`) so the date is always correct for the full 24-hour local day

---

## v1.6.0 — Daily Summary & PSA Workflow

### Added
- **Daily Summary tab** — a dedicated tab showing time entries grouped by project for any selected day; project names, concatenated task notes, total hours in decimal format (2 decimal places), billable type, and PSA logging status displayed per row
- **Decimal time display** — time on the Daily Summary is shown as decimal hours (e.g. 1.25, 0.50) to match PSA Timecard entry format
- **Grand total row** — a "Day total" footer row at the bottom of the Daily Summary table sums all project hours for the selected day
- **PSA logged checkbox** — each project row on the Daily Summary has a checkbox to mark whether time has been entered in PSA; checked rows dim visually; status persists across reloads and clears on Reset Week
- **Internal-first sort** — projects on the Daily Summary and Weekly Summary tabs sort with Internal projects at the top, then alphabetically by name within each group
- **PSA Timecard Entry link** — moved from the Log tab to the Daily Summary tab where it is more contextually useful
- **Discard timer button** — a ✕ button on each timer card discards a running or paused timer without logging; enabled only when a timer is active

### Bug Fixes
- **Delete entry button unresponsive** — the browser `confirm()` dialog was silently suppressed in some contexts, causing the delete button to appear non-functional; replaced with a two-click inline confirmation (button turns red and shows "Sure?" on first click; second click within 3 seconds confirms deletion)
- **Copy button syntax error** — task text containing apostrophes, quotes, or other special characters caused a `SyntaxError` when embedded directly in the `onclick` attribute; fixed by passing the entry ID and looking up task text from state at click time

---

## v1.5.0 — Progressive Web App

### Added
- **PWA support** — `manifest.json` and `sw.js` service worker enable installation as a standalone desktop or mobile app
- **Offline functionality** — service worker caches the app shell with a cache-first strategy; Google Fonts use network-first with cache fallback
- **App icons** — programmatically generated PNG icons at 192×192, 512×512, and 180×180 (Apple touch icon); navy rounded-square with clock design and rose minute hand accent
- **Install App button** — appears in the top navigation bar when the browser's `beforeinstallprompt` event fires; hidden after installation
- **GitHub Pages deployment** — `deploy.yml` GitHub Actions workflow using Node.js 24-compatible action versions (`checkout@v5`, `configure-pages@v5`, `upload-pages-artifact@v3`, `deploy-pages@v4`)
- **`.nojekyll`** — prevents GitHub Pages Jekyll processing from interfering with static file serving

### Bug Fixes
- **Blank page on GitHub Pages** — the app rendered blank after deployment because `init()` was called at the end of the first `<script>` block while `loadTheme()` (which `init()` calls first) was defined in a second `<script>` block; browsers execute script blocks sequentially so `loadTheme` did not yet exist when `init()` ran; merged all JavaScript into a single `<script>` block with `init()` called last
- **Missing `</style>` tag** — a `</style>` closing tag was inadvertently dropped during an edit, causing the browser to treat the entire HTML body as CSS content and render nothing; restored
- **Service worker fetch handler returning undefined** — the original fetch handler could return a rejected promise to `event.respondWith()` on network failure (particularly for the initial navigation request), causing the browser to show a blank page; all fetch paths now return a valid `Response` object including offline fallback cases

---

## v1.4.0 — Settings, Customization & Autocomplete

### Added
- **Settings panel** — slide-in drawer (⚙ top-right) with three sections: Appearance, Targets, and Projects
- **Light / Dark mode toggle** — full dark theme using neutral grays and blacks; automatically follows OS preference on first load; choice persists in `localStorage`
- **Configurable billable utilization target** — replaces the hardcoded 80% threshold; amber zone begins 20 percentage points below the target; red below that; takes effect immediately across all tabs
- **Task autocomplete** — as you type in either timer's task field, a dropdown shows matching entries from the current week's log history (case-insensitive substring match, up to 8 suggestions, most-recent first); `onmousedown` with `preventDefault()` prevents blur from closing the dropdown before a click registers
- **Per-project weekly targets** — each project in Settings has a "Weekly target" hours field; the Weekly Summary adds a Target column showing `Xh / Yh` with a color-coded mini progress bar (green ≥ target, amber ≥ target−20%, red below)
- **Cross-tab timer banner** — a slim strip below the tab row shows any running or paused timer (project, task, live elapsed time) with functional Pause/Resume and Log buttons; banner auto-hides on the Timers tab and when all timers are idle; live elapsed time updates every second via the timer's own tick interval
- **Project management in Settings** — add projects with name and billable type; delete projects (log entries preserved); dropdowns always sort A–Z

---

## v1.3.0 — Log Management & Metrics

### Added
- **Edit log entries** — hover a row on the Log tab and click ✎ to open the entry modal pre-populated with existing values; any field can be changed
- **Manual entry add** — "+ Add Entry" button on the Log tab opens the entry modal for any day of the current week; manually added entries are tagged "manual" in the timestamp column
- **Copy task text** — ⎘ button on each log row copies the task description to clipboard; flashes ✓ for 1.5 seconds to confirm; uses ID-based lookup to avoid quoting issues with special characters
- **Reset week button** — on the Weekly Summary tab; clears all entries for the current week after confirmation
- **Billable utilization** — displayed on all three tabs; color-coded green (≥80%), amber (60–79%), red (<60%); calculated as billable minutes ÷ total logged minutes
- **Daily progress meter** — on Timers tab; fills teal toward 8h target, turns rose when reached; percentage bubble floats at fill edge; billable utilization row below the meter
- **Weekly progress meter** — on Weekly Summary tab; fills toward 40-hour goal
- **PSA Timecard Entry link** — quick link to PSA entry page (opens in new tab)
- **Export CSV** — downloads current week's log as a CSV file

### Bug Fixes
- **`todayEntries` not defined** — the function was accidentally removed during a refactor that added the meters section; restored before the meter render functions that depend on it

---

## v1.2.0 — Hyland Branding & Polish

### Added
- **Hyland brand colors** — primary navy `#191E5E`, rose accent `#EC5CBB`, peppermint-derived teal `#1A8C6F` applied throughout
- **Sticky top navigation bar** — navy bar with clock SVG icon, "Easy Time Tracker" wordmark, Export CSV and Print actions
- **Typography** — Inter (UI) and IBM Plex Mono (timer displays) loaded via Google Fonts
- **Timer card accent stripes** — 3px top border shifts from neutral (idle) → teal (running) → rose (paused)
- **Tab styling** — active tab fills navy; inactive tabs neutral
- **Dark mode variables** — full dark theme CSS block (neutral grays, `#111` background); light/dark toggle in Settings
- **Stat card underline accents** — colored bottom border per card type (teal=billable, rose=non-billable)
- **Modal backdrop** — navy-tinted overlay for all modals

### Changed
- **Rounding changed from nearest to ceiling** — previously used `Math.round()` for quarter-hour increments; changed to `Math.ceil()` so all entries round up; a 1-second task logs as 15 minutes, never rounds down

---

## v1.1.0 — Dual Timers & Weekly View

### Added
- **Pause / Resume** — each timer has a ⏸ pause button that banks accumulated time; paused display turns amber; status line updates to prompt resume; accumulated time never loses banked segments
- **Dual timers** — two independent timer cards side by side; each has its own project selector, task field, and timer state
- **Auto-pause on start** — starting either timer automatically pauses the other if it is running; resuming also auto-pauses the other
- **Weekly log retention** — log entries persist for the full current Sun–Sat week; entries from prior weeks are filtered out on load
- **Log tab day navigation** — pill buttons for each day of the current week; days with no entries shown at reduced opacity
- **Daily Summary tab** — time grouped by project for the selected day with tasks concatenated comma-separated
- **Weekly Summary tab** — Sun–Sat grid with one row per project; day totals footer; billable utilization stat cards
- **"Timer 1" / "Timer 2" labels** — log entries tagged with T1 or T2 to identify which timer generated them
- **startDate capture** — timer records the local date at start time, not stop time, so entries that run past midnight log to the day they began

---

## v1.0.0 — Initial Release

### Added
- **Single-file HTML application** — all HTML, CSS, and JavaScript in one file; no build step, no dependencies, no server
- **Project selector** — dropdown with billable / non-billable designation per project; badge reflects type
- **Timer** — start, stop; elapsed time displayed in `HH:MM:SS` monospace format
- **Quarter-hour rounding** — logged time rounded to nearest 15-minute increment on stop
- **Task description** — free-text field for capturing what was worked on
- **Log tab** — displays all entries for the current day; entry count in tab label
- **Summary tab** — total, billable, non-billable stat cards; breakdown by project with proportional bar; full task list
- **Add project** — "+ New" button in timer dropdown opens a modal for quick project creation
- **localStorage persistence** — all data persists across browser sessions within the current day
- **Export CSV** — week's log downloadable as a CSV file
- **Print** — browser print layout for paper timesheets
- **Default projects** — Internal / Admin (non-billable) and Internal / Professional Development (non-billable) pre-loaded on first run
