# G10 Schedule Tracker

Personal hour-by-hour tracker for the G10 End-of-Year Plan v2 (Apr 25 – Jun 5, 2026).

## Files

- **[index.html](index.html)** — the tracker. Live "now" indicator, subject pills, week grid, stats dashboard, JLEC pipeline, exam countdowns, search, notes per day, export/import, keyboard shortcuts. Single file, vanilla JS.
- **[defunct-index.html](defunct-index.html)** — original minimal MVP. Kept for reference; shares the same `localStorage` keys so checkbox state carries between them.
- **[G10_End_of_Year_Plan_v2.md](G10_End_of_Year_Plan_v2.md)** — source of truth for the schedule.

## How to open

Double-click `index.html`. Opens in your default browser via `file://`. No server, no internet needed.

On iPhone: AirDrop / iCloud Drive the file, open with Safari (Files app → tap → Share → Open in Safari). For repeat use, "Add to Home Screen" from the Safari share sheet.

## Feature tour

### Today
- **Live "now" card** highlights the block you should be in right now, with a progress bar through it and "Next at HH:MM" preview. Auto-refreshes every minute.
- **Subject pills** auto-tagged from task text: JLEC, Crit C, Crit D, Math, AP CSP, INS, SCI, LLE, Chinese, WSC, Piano. Each gets a distinct color.
- **Duration pill** on each trackable row (parsed from the time range).
- **Day completion ring** (top right of the day-nav row) — visual % done.
- **Day notes** textarea — autosaves to localStorage per day.
- **Mark all done / Clear day / Print** quick actions.
- **Confetti + toast** when all of today's tasks are checked off.

### Deadline timeline
A horizontal strip in the header shows all 13 hard deadlines as pins, with a today-marker and a soft progress fill. Hover a pin for the full label. Click any pin (or any of the cards above it) to jump to that day.

### Week tab
Mon–Sun grid for the current week. Each cell shows the day's subject mix, deadline name if any, and a mini progress bar. Click a cell to open it. Prev/next-week buttons.

### Stats tab
- 4 top-line tiles: overall %, hours done vs planned, current streak, days complete.
- **Subject hours bar chart**: planned vs done per subject across the entire 38-day plan.
- **Daily completion heatmap**: 6 weeks × 7 days, GitHub-style intensity. Click any cell to jump there.

### Projects tab
- **JLEC pipeline**: 17 stages from source reading → submit, each auto-derived from checked tasks matching its date range and pattern.
- **Exams & submissions**: every exam/deliverable with countdown and prep-task completion (auto-counted from earlier days tagged with the matching subject).
- **Subject legend / filter**: click a subject pill to filter today's view to only that subject's tasks.

### List tab
Compact 38-row table (Day · Event · Focus · Done %), with a mini progress bar per day.

### Search
Top-bar search box. Live-filters across all 38 days × all blocks. Click a result to jump.

### Keyboard shortcuts (press `?`)
- `←` `→` — prev / next day (Today tab)
- `t` — jump to today
- `1`–`5` — Today / Week / Stats / Projects / List
- `/` — focus search
- `?` — show shortcut help
- `Esc` — close overlay / clear search

### Export / import
Top-bar buttons. Export drops a JSON file with every check + every note. Import replaces all current state from such a file. Use this to back up before a destructive change, or to move state between Mac and iPhone manually.

## Where the schedule lives

Everything is hardcoded in the `schedule` JS object inside `index.html` (search for `// SOURCE:`).

```js
schedule.deadlines  // [{ date, label, kind }]
schedule.days       // [{ date, label, note, focus, blocks: [{ time, task, trackable?, deadline? }] }]
```

A row becomes a checkbox if `trackable: true`. Add `deadline: true` for the red highlight.

Two adjacent arrays drive the Projects tab:
- `jlecPipeline` — milestone definitions for the project tracker.
- `examList` — exam definitions for the countdown cards.

## Subject auto-tagging

`SUBJECTS` in `index.html` maps regex patterns to colored pill keys. Adding a subject is a one-line change in that array — pick a key, label, regex, and add a `--s-<key>` color in the CSS `:root`.

## Updating when the plan changes

1. Edit `G10_End_of_Year_Plan_v2.md` as source of truth.
2. Mirror the change in `index.html` — find the day in `schedule.days` and edit its `blocks`. (If you also use `defunct-index.html`, mirror there too — same shape.)
3. Save, refresh the browser.

The `// SOURCE:` comment is the reminder that both must stay in sync. No runtime markdown parser — too fragile.

## Reset / clear state

DevTools → Application → Local Storage → clear keys starting with `g10:`. Or in the console:
```js
Object.keys(localStorage).filter(k => k.startsWith("g10:")).forEach(k => localStorage.removeItem(k));
```
Or **Export** to back up, then clear; **Import** to restore.

## Storage keys

- `g10:YYYY-MM-DD:N` — checkbox state for block N on a given date
- `g10:note:YYYY-MM-DD` — day notes
- `g10:filter` — current subject filter

All keys are scoped under `g10:` for clean cleanup and export.
