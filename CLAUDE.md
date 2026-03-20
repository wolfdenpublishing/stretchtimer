# Rise & Stretch

A lightweight, single-file web application that guides users through a 16-pose yoga/stretching routine with timed intervals and audio cues.

## Project Structure

```
stretchtimer/
├── index.html   - Entire application (HTML + CSS + JS, single-file)
├── poses.json   - Master pose list (ordering, durations, image paths, sides)
├── poses/       - Individual pose illustration PNGs (transparent background)
│   ├── camel_pose.png
│   ├── cat_pose.png
│   ├── caterpillar_pose.png
│   ├── child_pose.png
│   ├── cow_pose.png
│   ├── downward_dog.png
│   ├── forward_bend.png
│   ├── one_legged_downward_dog_l.png
│   ├── one_legged_downward_dog_r.png
│   ├── pigeon_pose_l.png
│   ├── pigeon_pose_r.png
│   ├── triangle_l.png
│   ├── triangle_r.png
│   ├── upward_dog.png
│   ├── warrior_i_l.png
│   └── warrior_i_r.png
└── CLAUDE.md    - This file
```

## Tech Stack

- **Pure HTML/CSS/JavaScript** — no frameworks, no build tools
- **Google Fonts** — DM Sans (body) and Space Mono (headers/timer)
- **Web Audio API** — procedurally generated tick and ding sounds
- **SVG** — circular timer progress ring
- **localStorage** — persists per-pose duration multipliers

## How It Works

The app has four screens, all rendered dynamically into a single `<div id="app">`:

1. **Landing** (`renderLanding`) — app title, cycling pose image slideshow (3s interval, 0.2s fade), description of the routine and how it works, "View Routine" button
2. **Intro** (`renderIntro`) — lists all 16 poses with effective durations and per-pose multiplier +/− controls, total time, and "Begin Routine" button
3. **Active Pose** (`renderPose`) — shows pose image, name, side label (if applicable), multiplier controls with base duration display (when timer is not running), circular countdown timer, play/pause/resume controls, and nav bar (prev/home/redo/skip)
4. **Complete** (`renderComplete`) — shows total time, "All Done!" message, "Do It Again" button, and credits/attribution

### Poses

16 poses defined in the `poses` array (embedded from `poses.json`), each with `name`, `duration` (base seconds), `image` (path to PNG), and optional `side` ("Left"/"Right"). 12 unique poses, 4 of which have left/right variants (one legged downward dog, pigeon, warrior I, triangle).

Base durations: 30s for harder/active poses, 45s for easier/rest poses. Base total: 9:30 (570 seconds), adjusted by multipliers.

### Pose Data Management

- **`poses.json`** is the master reference file for pose ordering, names, durations, and image paths
- The same data is embedded directly in `index.html` as a JS `const poses` array (since `file://` can't fetch local JSON)
- When updating poses, edit `poses.json` first, then sync the embedded copy in `index.html`

### Pose Images

Individual PNG files with transparent backgrounds in the `poses/` folder. Files ending in `_l` or `_r` are left/right variants of the same pose. Each pose card uses an `<img>` tag referencing the `image` path from the pose data. Illustrations by [brgfx on Freepik](https://www.freepik.com/author/brgfx).

### Landing Page

The landing page displays the app name ("Rise & Stretch"), a tagline, and a cycling slideshow of all pose images in routine order. Images transition every 3 seconds with a 0.2s CSS opacity fade. The slideshow wraps around indefinitely. Descriptive text explains the purpose and features of the routine. The interval is cleared when navigating away.

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
- Pose ID = `pose.name + (pose.side ? '-' + side : '')` — distinguishes e.g. "One Leg Downward Dog-Right" from "One Leg Downward Dog-Left"
- Controls (+/− buttons) appear on both the intro pose list and the active pose card (when timer is not running)
- On the active pose card, the multiplier display shows base duration in accent color: `30s ×1.0 = 30s`
- Adjusting a multiplier only updates the stored value and re-renders; it does not change `timeRemaining` or any other timer state
- The new effective duration takes effect the next time the pose is loaded (via Start, Redo, Prev, Next)

### State

Global variables:
- `currentPose` — index into `poses` array (-1 = intro/landing screen)
- `timeRemaining` — seconds left in current countdown or pose timer
- `timerInterval` — `setInterval` ID (null when stopped)
- `running` — true when timer is actively counting down
- `countingDown` — true during the 5-second pre-pose countdown
- `paused` — true when user has paused mid-timer (distinguishes from "ready" state for button display)
- `audioCtx` — Web Audio API context (lazy-initialized)
- `landingImageInterval` — `setInterval` ID for the landing page image slideshow (null when not on landing)

### Key Functions

- **Multiplier helpers**: `getPoseId()`, `loadMultipliers()`, `getMultiplier()`, `setMultiplier()`, `getEffectiveDuration()`, `getTotalTime()`, `adjustMultiplier()`
- **Rendering**: `renderLanding()`, `renderIntro()`, `renderPose()`, `renderComplete()`
- **Timer**: `startTimer()`, `countdownTick()`, `poseTimerTick()`, `pauseTimer()`, `resumeTimer()`
- **Navigation**: `showIntro()`, `startRoutine()`, `nextPose()`, `prevPose()`, `redoPose()`, `resetApp()`
- **Audio**: `ensureAudio()`, `playDing()`, `playTick()`
- **Utility**: `formatTime()`, `scaleApp()`

### Design

- Dark theme with teal accent (`#3ecfb4`)
- Mobile-first, max-width 420px
- CSS variables for all colors defined in `:root`
- No external CSS files — all styles in `<style>` block

### Credits

Displayed on the completion screen:
- App Design by [Wolfden Publishing](https://github.com/wolfdenpublishing/stretchtimer) — MIT License
- Created with [Anthropic Claude Code](https://claude.com/claude-code)
- Pose illustrations by [brgfx](https://www.freepik.com/author/brgfx) on Freepik

## Running

Open `index.html` in any modern browser. No server or build step required. Works offline after initial font load.
