# Stretch Timer

A simple, guided yoga/stretching routine timer. 10 poses, audio cues, and a clean dark-themed UI — all in a single HTML file with no dependencies.

**[Try it live](https://wolfdenpublishing.github.io/stretchtimer/)**

## Features

- 10-pose flexibility routine with visual and audio guidance
- 5-second countdown before each pose so you can get into position
- Per-pose duration multiplier — adjust difficulty for individual poses and it remembers your settings
- Circular progress ring with color changes in the final seconds
- Procedurally generated audio (tick and ding sounds via Web Audio API)
- Fully offline-capable after first load
- Mobile-friendly, responsive design

## Usage

Open `index.html` in any modern browser. No server, build step, or installation required.

Adjust pose durations on the intro screen or during the routine using the +/− buttons next to each pose. Your settings are saved automatically in your browser.

## Tech

Pure HTML, CSS, and JavaScript. No frameworks. Pose illustrations loaded from a single sprite sheet (`poses.jpg`).
