# Stretch Timer

A lightweight, single-file web application that guides users through a 10-pose yoga/stretching routine with timed intervals and audio cues.

## Project Structure

```
stretchtimer/
├── index.html   - Entire application (HTML + CSS + JS, single-file)
├── poses.jpg    - External pose illustration image
└── CLAUDE.md    - This file
```

## Tech Stack

- **Pure HTML/CSS/JavaScript** — no frameworks, no build tools
- **Google Fonts** — DM Sans (body) and Space Mono (headers/timer)
- **Web Audio API** — procedurally generated tick and ding sounds
- **SVG** — circular timer progress ring

## How It Works

The app has three screens, all rendered dynamically into a single `<div id="app">`:

1. **Intro** — lists all 10 poses with durations, "Begin Routine" button
2. **Active Pose** — shows pose image, name, circular countdown timer, play/pause controls, and nav (prev/home/redo/skip)
3. **Complete** — summary screen with "Do It Again" button

### Poses

10 poses defined in the `poses` array, each with `name`, `duration` (base seconds), optional `subtitle`, and an image `key`. Base total: ~6 minutes (varies with multipliers).

### Pose Images

The `poseImages` object maps keys (e.g., `cat`, `cow`, `downdog`) to CSS `background-position` values. The external file `poses.jpg` (512x640) is a composite sprite sheet with a 3x3 grid of pose illustrations below a title area. Each pose card uses a `<div>` with `background-image: url('poses.jpg')` and `background-size: 450px auto`, positioned via the values in `poseImages` to show the correct 150x140px region.

### Timer & Audio

- Clicking "Start" triggers a 5-second countdown (with ticks) before the pose timer begins, giving the user time to get into position
- Countdown shows orange ring and "get ready" label; ding plays when countdown ends and pose begins
- Pose timer uses `setInterval` at 1-second intervals
- SVG circle stroke-dashoffset animates the progress ring
- Ring turns orange (warning color) in the last 5 seconds
- `playTick()` — short 660Hz beep during countdown and final 5 seconds
- `playDing()` — 880-1100Hz sweep when countdown ends or pose completes
- Pausing during countdown or pose preserves state; "Resume" continues from where it left off (no re-countdown)

### Per-Pose Duration Multiplier

- Each pose has an adjustable multiplier (default ×1.0, min ×0.5, step ±0.1)
- Effective duration = `Math.round(baseDuration × multiplier)`
- Multipliers are stored in `localStorage` under key `"stretchTimerMultipliers"` as a JSON object keyed by pose ID (`pose.key` + optional `-subtitle`)
- Controls (+/− buttons) appear on both the intro pose list and the active pose screen (when timer is not running)
- Total time on intro and completion screens reflects adjusted durations
- Helper functions: `getPoseId()`, `getMultiplier()`, `setMultiplier()`, `getEffectiveDuration()`, `getTotalTime()`

### State

Global variables: `currentPose`, `timeRemaining`, `timerInterval`, `running`, `countingDown`, `paused`, `audioCtx`

### Design

- Dark theme with teal accent (`#3ecfb4`)
- Mobile-first, max-width 420px
- CSS variables for all colors
- No external CSS files — all styles in `<style>` block

## Running

Open `index.html` in any modern browser. No server or build step required. Works offline after initial font load.
