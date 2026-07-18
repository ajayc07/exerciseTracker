# Exercise Tracker (PWA)

Camera-based movement tracker for your 15-exercise program. Pose detection (MediaPipe) runs fully **on-device** — no video ever leaves your phone/laptop, and tracking works offline.

## Features
- **Rep counting** from joint angles (squats, push-ups, rows, curls, raises, flyes, presses, glute bridges, dead bugs, shoulder taps)
- **Hold timer** for plank (starts only when your body line is straight) and **timed mode** for jump rope (with automatic jump counting)
- **Form feedback**: "keep body straight", "pin your elbows", "stop at shoulder height", tempo warnings, etc.
- **Online/Offline toggle** (top-right): Online shows rotating coaching tips; Offline is tracking-only. It follows your real connection but you can override it manually.
- **Session history** saved on-device (localStorage)
- **Weekly schedule** from your poster (Mon/Tue/Thu/Fri workout, Wed recovery, Sat light, Sun rest)
- Tap ✎ on any exercise to change its target reps/seconds

## Running it

The camera API requires **HTTPS or localhost**.

### Laptop
```
cd exercise-tracker
python -m http.server 8000
```
Open http://localhost:8000 in Chrome/Edge, allow camera.

### Android
Easiest options (pick one):
1. **Host it free** (recommended): drag this folder onto https://app.netlify.com/drop or push to GitHub Pages. Open the HTTPS URL in Chrome on your phone → menu → **Add to Home screen**. It installs like an app.
2. **Local network**: serve from your laptop and use a tunneling tool (e.g. `npx localtunnel --port 8000`) to get an HTTPS URL.

### Offline use
First visit must be online (it caches the app + pose model, ~6 MB). After that everything — including tracking — works with no internet. Tips are built-in, so "Online" mode tips also work; the toggle controls the mode per your design.

## Controls
- **Start Camera** → a get-ready countdown (default 5 s, configurable) runs before any counting starts, so you have time to get into position.
- **⚙ Settings** (header): change target reps/seconds and rest per exercise, countdown lengths, voice/gesture options, camera resolution.
- **↺ Reset Set**: restart the current set if you messed up a rep.
- **From a distance** (no need to walk to the device):
  - Hold **one hand above your head for 2 s** → pause / resume (works offline)
  - Hold **both hands above your head for 3 s** → reset the current set
  - Optional **voice commands** (enable in settings, needs online): say "pause", "go", "reset", or "finish"
  - **Voice announcements** speak your rep count and cues out loud so you can hear progress from across the room.
- **Export/Import JSON** (History tab): back up workout history or move it between laptop and phone.

## Usage tips for accurate tracking
- Place the device so your **whole body** (or the working joints) is in frame; side view works best for push-ups, plank, glute bridges, dead bugs, squats.
- Good lighting, plain background, 2–3 m distance.
- 🔄 button switches front/back camera on phones.
- A beep = rep counted; triple beep = target reached → 45 s rest timer starts automatically.

## Files
- `index.html` — the whole app (UI + pose logic)
- `sw.js` — service worker (offline caching)
- `manifest.webmanifest`, `icon-192.png`, `icon-512.png` — install/PWA metadata
