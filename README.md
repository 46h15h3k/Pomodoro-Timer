# Pomodoro Tracker

A Pomodoro Technique timer built with vanilla HTML, CSS, and JavaScript — no framework, no build step, no dependencies. Deploys as a single static file to GitHub Pages, Vercel, or Cloudflare Pages.


**[Live demo](https://46h15h3k.github.io/Pomodoro-Timer/)** _(replace with your deployed URL)_

![Pomodoro Tracker screenshot](./screenshot.png)
_(add a screenshot of the app here)_

## Overview

The [Pomodoro Technique](https://en.wikipedia.org/wiki/Pomodoro_Technique) breaks work into timed intervals (traditionally 25 minutes) separated by short breaks, with a longer break after every four intervals. This app implements that loop as a self-contained web console: start, pause, and resume a session, track completed work sessions and total focus time, and get an audible cue when a session ends.

## Features

- **Start / pause / resume** a running timer with a single control
- **Configurable intervals** — work session, short break, and long break lengths, plus how many work sessions occur before a long break (defaults: 25 / 5 / 15 minutes, long break every 4 sessions)
- **Session type display** — a status badge shows whether the current session is Work, Short break, or Long break, plus which session number you're on
- **Session tracking** — completed work sessions, current position in the cycle, and cumulative focus time
- **Audible notification** on session end, synthesized in-browser via the Web Audio API (no external sound file)
- **Skip** and **reset** controls for adjusting the current session without waiting it out
- **Auto-start** toggle to chain sessions together automatically, or leave off to manually start each one
- Responsive layout for desktop and mobile, with keyboard-operable controls, visible focus states, `aria-live` announcements for session changes, and respect for `prefers-reduced-motion`

## Tech stack

- **HTML5 / CSS3** — no CSS framework; custom properties for theming
- **Vanilla JavaScript (ES5-compatible)** — no build tools, bundlers, or frameworks
- **Web Audio API** — for the session-end chime
- **SVG** — for the circular progress ring

Everything lives in one file (`index.html`), so it can be opened directly in a browser or hosted anywhere that serves static files.

## Project structure

```
pomodoro-tracker/
├── index.html      # markup, styles, and app logic (self-contained)
└── README.md
```

## Getting started

### Run locally

No install step is required.

```bash
git clone https://github.com/46h15h3k/Pomodoro-Timer.git
cd pomodoro-timer
open index.html        # macOS
# or just double-click index.html / drag it into a browser
```

### Deploy to GitHub Pages

1. Push this repo to GitHub.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to `Deploy from a branch`, choose the `main` branch and `/ (root)` folder.
4. Save — GitHub Pages will publish `index.html` at `https://<your-username>.github.io/<repo-name>/`.

### Deploy to Vercel / Cloudflare Pages

Both platforms can deploy a static site with no configuration:

- **Vercel** — import the repo at [vercel.com/new](https://vercel.com/new); leave build settings blank (no build command, output directory `.`).
- **Cloudflare Pages** — connect the repo at [pages.cloudflare.com](https://pages.cloudflare.com/); framework preset `None`, build command empty, output directory `/`.

## How it works

The app holds a small state object (`mode`, `remaining`, `running`, `completed`, `cyclePos`, `focusSeconds`) and a `config` object for the configurable durations. A single `setInterval` tick decrements `remaining` each second and re-renders the UI (timer text, SVG progress ring, status badge, stats). When a session hits zero:

1. A chime plays via two short Web Audio oscillator tones.
2. If a **work** session just ended, the completed-session count and cycle position increment, and the next mode is chosen — `short break` normally, or `long break` once the configured number of work sessions has been reached (cycle position then resets).
3. If a **break** just ended, the next mode is always `work`.
4. If auto-start is enabled, the next session begins immediately; otherwise the timer waits, paused, for the user to press Start.

Settings changes are applied without losing progress on other sessions, and only affect the current session's remaining time if the timer isn't already running.

## Known limitations / possible next steps

- State is kept in memory only — refreshing the page resets the timer, stats, and settings. Adding `localStorage` persistence for `config`, preferences, and running totals would be a natural follow-up.
- No backend, accounts, or cross-device sync — this is a single-page, single-user tool by design.
- Sound is a generated tone rather than a custom audio file; swapping in a custom `.mp3`/`.wav` via `<audio>` would be a simple enhancement if a different notification sound is preferred.
