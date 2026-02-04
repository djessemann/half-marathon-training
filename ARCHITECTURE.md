# Half Marathon Training Web App

A single-file progressive web app (PWA) for tracking a 12-week half marathon training plan. Designed for mobile-first use with iOS home screen support.

## Overview

- **File**: `hm_training.html` (single self-contained HTML file)
- **Target**: Sub-2:10 half marathon on April 26, 2026
- **Duration**: 12 weeks (Feb 2 - Apr 26, 2026)
- **Storage**: Browser localStorage (no backend)
- **Hosting**: GitHub Pages or any static file host

## Architecture

### Single-File Structure

The entire app is contained in one HTML file with embedded CSS and JavaScript:

```
hm_training.html
├── <head>
│   ├── Meta tags (viewport, PWA support)
│   ├── Apple touch icon (👟 emoji)
│   ├── Google Fonts (DM Sans, JetBrains Mono)
│   └── <style> (all CSS ~700 lines)
├── <body>
│   ├── Header (title, countdown, month nav)
│   ├── Week summary bar
│   ├── Calendar container
│   ├── Backup buttons (export/import)
│   ├── Plan summary
│   ├── Profile button
│   ├── Info overlay (guidance panel)
│   ├── Day detail modal
│   └── <script> (all JavaScript ~430 lines)
```

### Design System

CSS custom properties defined in `:root`:

| Variable | Value | Usage |
|----------|-------|-------|
| `--bg` | `#0e0e10` | Page background |
| `--surface` | `#1a1a1f` | Card backgrounds |
| `--surface-raised` | `#222228` | Elevated elements |
| `--border` | `#2a2a32` | Border color |
| `--text` | `#e8e8ec` | Primary text |
| `--text-dim` | `#7a7a88` | Secondary text |
| `--text-muted` | `#4a4a55` | Disabled/muted text |
| `--green` | `#1db954` | Easy runs, success states |
| `--orange` | `#e67e22` | Tempo runs |
| `--blue` | `#3498db` | Cross-training |
| `--red` | `#e74c3c` | Race day |
| `--rest-color` | `#555566` | Rest days |

### Workout Types

Each workout type has associated colors and notes:

| Type | Color | CSS Class | Description |
|------|-------|-----------|-------------|
| Easy | Green | `tag-easy` | Conversational pace runs |
| Long | Green | `tag-long` | Weekly long runs |
| Tempo | Orange | `tag-tempo` | Race-pace training |
| Cross-train | Blue | `tag-cross` | Non-running activity |
| Rest | Gray | `tag-rest` | Recovery days |
| Shakeout | Green | `tag-shakeout` | Pre-race easy run |
| RACE DAY | Red | `tag-race` | The half marathon |

## Data Structures

### Training Plan (`PLAN` object)

The training plan is stored as a JavaScript object keyed by date strings:

```javascript
PLAN = {
  "2026-02-03": { type: "Easy", dist: "3.5mi" },
  "2026-02-04": { type: "Cross-train", dist: "" },
  // ...
}
```

The plan is generated from the `weeks` array at initialization:

```javascript
const weeks = [
  ["2026-02-02", {Tue:["Easy","3.5mi"], Wed:["Cross-train",""], ...}],
  // 12 weeks total
];
```

Each week starts on Monday. Rest days (Mon/Fri) are auto-filled if not specified.

### User Logs (localStorage)

Logs are stored in localStorage with the prefix `hm_`:

```javascript
// Key format: "hm_YYYY-MM-DD"
// Value format:
{
  "distance": "4.2",      // Actual distance run
  "time": "42:30",        // Duration
  "feel": "good",         // great|good|ok|rough|skipped
  "notes": "Felt strong"  // Free text
}
```

Storage functions:
- `getLog(key)` - Retrieve log for a date
- `setLog(key, data)` - Save log for a date
- `deleteLog(key)` - Remove log for a date

## Key Components

### 1. Header
- Title: "13.1 Training"
- Race countdown: Days until April 26, 2026
- Month navigation: Feb / Mar / Apr buttons

### 2. Calendar Grid
- 7-column grid (Sun-Sat)
- Each day cell shows:
  - Date number
  - Workout type tag (color-coded)
  - Distance (planned or logged)
  - Green dot indicator (top-right) if logged
  - Green border glow if today

### 3. Week Summary Bar
- Shows current week number (1-12)
- Planned miles for the week
- Logged miles for the week

### 4. Day Detail Modal
- Bottom sheet that slides up
- Shows workout details and coaching notes
- Log form: distance, time, feel, notes
- Save/Cancel/Clear buttons
- X button in top-right to close

### 5. Info Panel (Guidance)
- Full-screen slide-in from right
- Contains:
  - User profile (age, weight, goals)
  - Baseline activity assessment
  - Plan rationale
  - Prep & recovery tips
  - Nutrition guidance
  - Race day tips

### 6. Backup System
- Export: Downloads all logs as JSON file
- Import: Restores logs from JSON file
- Toast notifications for feedback

## Key Functions

### Rendering
| Function | Purpose |
|----------|---------|
| `renderNav()` | Month navigation buttons |
| `renderCalendar()` | Calendar grid for current month |
| `renderWeekSummary()` | Current week stats bar |
| `renderPlanSummary()` | Training phases overview |
| `updateCountdown()` | Days until race |

### Modals & Panels
| Function | Purpose |
|----------|---------|
| `openDay(key)` | Open day detail modal |
| `closeModal()` | Close day detail modal |
| `openInfo()` | Open guidance panel |
| `closeInfo()` | Close guidance panel |

### Data Management
| Function | Purpose |
|----------|---------|
| `saveLog(key)` | Save workout log |
| `clearLog(key)` | Delete workout log |
| `exportData()` | Download logs as JSON |
| `importData(event)` | Restore logs from JSON |

### Helpers
| Function | Purpose |
|----------|---------|
| `dateKey(d)` | Convert Date to "YYYY-MM-DD" |
| `todayKey()` | Get today's date key |
| `tagClass(type)` | Get CSS class for workout type |
| `cardClass(type)` | Get modal card CSS class |
| `typeColor(type)` | Get color for workout type |

## PWA Features

### iOS Home Screen Support
```html
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<link rel="apple-touch-icon" href="data:image/svg+xml,...">
```

### Responsive Design
- Max-width: 500px for content
- Mobile-first CSS
- Touch-optimized interactions (`:active` states)
- Dynamic viewport height (`100dvh`)

## Modifying the Training Plan

### To change workout schedule:
Edit the `weeks` array (line ~838):

```javascript
const weeks = [
  ["2026-02-02", {  // Week start date (Monday)
    Tue: ["Easy", "3.5mi"],
    Wed: ["Cross-train", ""],
    Thu: ["Tempo", "4mi"],
    Sat: ["Easy", "3mi"],
    Sun: ["Long", "6mi"]
  }],
  // ... more weeks
];
```

### To change race date:
Update the countdown function (line ~926):
```javascript
const race = new Date("2026-04-26T00:00:00");
```

### To change workout notes:
Edit the `NOTES` object (line ~828):
```javascript
const NOTES = {
  Easy: "Keep it conversational...",
  Tempo: "Warm up 0.5 mi easy...",
  // ...
};
```

### To change user profile:
Edit the info-card HTML in the info-overlay section (line ~742).

## Common Modifications

### Add a new workout type
1. Add to `NOTES` object with description
2. Add CSS class in `.tag-*` and `.type-*` sections
3. Add case to `tagClass()` and `cardClass()` functions
4. Add case to `typeColor()` function
5. Add modal card style `.modal-workout-card.type-*`

### Change color scheme
Modify CSS custom properties in `:root` (line ~13).

### Add new log fields
1. Add input in `openDay()` function HTML
2. Add to `saveLog()` function data object
3. Update `clearLog` button condition if needed

## File Dependencies

**External:**
- Google Fonts: DM Sans, JetBrains Mono

**Internal:**
- No external JS libraries
- No build process required
- No backend/API calls

## Browser Support

- Modern browsers (Chrome, Safari, Firefox, Edge)
- iOS Safari (primary target)
- Requires localStorage
- Requires CSS custom properties
- Requires CSS Grid and Flexbox
