# Stretch Timer

A lightweight, single-file web application that guides users through a 10-pose yoga/stretching routine with timed intervals and audio cues.

## Project Structure

```
stretchtimer/
├── index.html   - Entire application (HTML + CSS + JS, single-file)
├── poses.jpg    - Composite sprite sheet of pose illustrations (512x640)
└── CLAUDE.md    - This file
```

## Tech Stack

- **Pure HTML/CSS/JavaScript** — no frameworks, no build tools
- **Google Fonts** — DM Sans (body) and Space Mono (headers/timer)
- **Web Audio API** — procedurally generated tick and ding sounds
- **SVG** — circular timer progress ring
- **localStorage** — persists per-pose duration multipliers

## How It Works

The app has three screens, all rendered dynamically into a single `<div id="app">`:

1. **Intro** (`renderIntro`) — lists all 10 poses with effective durations and per-pose multiplier +/− controls, total time, and "Begin Routine" button
2. **Active Pose** (`renderPose`) — shows pose image, name, multiplier controls (when timer is not running), circular countdown timer, play/pause/resume controls, and nav bar (prev/home/redo/skip)
3. **Complete** (`renderComplete`) — shows total time, "All Done!" message, and "Do It Again" button

### Poses

10 poses defined in the `poses` array, each with `name`, `duration` (base seconds), optional `subtitle`, and an image `key`. Base durations are 30 or 45 seconds. Base total: 6:15 (375 seconds), adjusted by multipliers.

### Pose Images

The `poseImages` object maps keys (e.g., `cat`, `cow`, `downdog`) to CSS `background-position` values. The external file `poses.jpg` (512x640) is a composite sprite sheet with a 3x3 grid of pose illustrations below a title area. Each pose card uses a `<div class="pose-sprite">` with `background-image: url('poses.jpg')` and `background-size: 450px auto`, positioned via the values in `poseImages` to show the correct 150x140px region.

### Timer Flow

1. User clicks **Start** → 5-second countdown begins (`countingDown = true`)
2. Countdown: ticks each second, orange ring, "get ready" label
3. Countdown hits 0 → ding plays, `countingDown` clears, pose timer begins with effective duration
4. Pose timer: ring animates down, turns orange in final 5 seconds with ticks
5. Timer hits 0 → ding plays, "Next Pose" button appears
6. User can **Pause** at any point (during countdown or pose); **Resume** continues from where it left off without re-triggering the countdown

### Audio

- `ensureAudio()` — lazy-initializes `AudioContext`, handles suspended state (iOS)
- `playTick()` — 660Hz sine, 0.15s duration, gain 0.1 (countdown ticks and final 5 seconds)
- `playDing()` — 880→1100Hz sine sweep, 0.8s duration, gain 0.3 (countdown end and pose completion)

### Per-Pose Duration Multiplier

- Each pose has an adjustable multiplier (default ×1.0, min ×0.5, step ±0.1, no hard upper bound)
- Effective duration = `Math.round(baseDuration × multiplier)`
- Multipliers stored in `localStorage` key `"stretchTimerMultipliers"` as JSON keyed by pose ID
- Pose ID = `pose.key + (pose.subtitle ? '-' + subtitle : '')` — distinguishes e.g. the two "oldd" poses (Right Leg vs Left Leg)
- Controls (+/− buttons) appear on both the intro pose list and the active pose card (when timer is not running)
- Adjusting a multiplier only updates the stored value and re-renders; it does not change `timeRemaining` or any other timer state
- The new effective duration takes effect the next time the pose is loaded (via Start, Redo, Prev, Next)

### State

Global variables:
- `currentPose` — index into `poses` array (-1 = intro screen)
- `timeRemaining` — seconds left in current countdown or pose timer
- `timerInterval` — `setInterval` ID (null when stopped)
- `running` — true when timer is actively counting down
- `countingDown` — true during the 5-second pre-pose countdown
- `paused` — true when user has paused mid-timer (distinguishes from "ready" state for button display)
- `audioCtx` — Web Audio API context (lazy-initialized)

### Key Functions

- **Multiplier helpers**: `getPoseId()`, `loadMultipliers()`, `getMultiplier()`, `setMultiplier()`, `getEffectiveDuration()`, `getTotalTime()`, `adjustMultiplier()`
- **Rendering**: `renderIntro()`, `renderPose()`, `renderComplete()`
- **Timer**: `startTimer()`, `countdownTick()`, `poseTimerTick()`, `pauseTimer()`, `resumeTimer()`
- **Navigation**: `startRoutine()`, `nextPose()`, `prevPose()`, `redoPose()`, `resetApp()`
- **Audio**: `ensureAudio()`, `playDing()`, `playTick()`
- **Utility**: `formatTime()`

### Design

- Dark theme with teal accent (`#3ecfb4`)
- Mobile-first, max-width 420px
- CSS variables for all colors defined in `:root`
- No external CSS files — all styles in `<style>` block

## Running

Open `index.html` in any modern browser. No server or build step required. Works offline after initial font load.
