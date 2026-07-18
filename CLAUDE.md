# Exercise Tracker — Project Guide

## What this is
A static, single-page PWA (no build step, no dependencies) that tracks exercise movements via the device camera using MediaPipe pose detection (loaded from CDN, runs on-device). Rep counting, plank hold timer, jump-rope timer, form feedback, online/offline tips toggle, and localStorage session history.

## Files
- `index.html` — entire app (UI, styles, pose logic). Everything is inline.
- `sw.js` — service worker; cache-first so app + pose model work offline after first online visit.
- `manifest.webmanifest`, `icon-192.png`, `icon-512.png` — PWA install metadata.
- `README.md` — user-facing usage notes.

## Key behaviors (v2)
- **Get-ready countdown**: after Start/Resume/Reset/rest, a configurable countdown (default 5s start / 3s resume) runs before any counting begins — prevents false reps while getting into position.
- **Settings panel (⚙ in header)**: per-exercise target + rest seconds, countdown durations, voice announcements, voice commands, gesture control, camera resolution. Stored in localStorage keys `et_set`, `et_targets`, `et_rests`.
- **Reset Set (↺)**: zeroes the current set counters and re-runs the countdown.
- **Distance control** (user exercises away from device):
  - Hand gestures (pose-based, works offline): one hand held above head 2s = pause/resume; both hands 3s = reset set. 4s cooldown after each trigger. Progress bar shown at bottom of camera stage.
  - Voice commands (optional, Web Speech API, needs online + mic): "pause/stop", "go/start/resume", "reset", "finish/done".
  - Voice announcements (speechSynthesis, offline-capable): counts each rep aloud, "Go", "Set complete. Rest."
- **Performance**: adaptive frame skipping when pose detection is slow (>45ms skip 1, >80ms skip 2), FPS badge on stage, default 480p capture (720p optional in settings). Timed/hold timers ignore gaps >500ms so walking out of frame or pausing doesn't eat time.
- **History backup**: Export/Import JSON buttons in History tab (merge + dedupe on import).

## Important constraints
- `getUserMedia` (camera) requires HTTPS or localhost — the app cannot run from `file://`.
- No bundler/framework. Keep it dependency-free and single-file. Do not add npm/node tooling.
- The service worker cache name is `extracker-v1` in `sw.js`. **Bump this version string on every deploy** or users will keep the old cached app.

## Task: Deploy to GitHub Pages

Goal: publish this folder to a new public GitHub repo named `exercise-tracker` and serve it via GitHub Pages.

Steps:
1. Initialize git in this folder (if not already):
   ```
   git init
   git add .
   git commit -m "Exercise tracker PWA - initial version"
   git branch -M main
   ```
2. Create the repo and push (gh CLI is easiest; fall back to manual remote if gh isn't installed/authenticated):
   ```
   gh repo create exercise-tracker --public --source=. --push
   ```
   or manually:
   ```
   git remote add origin https://github.com/<USERNAME>/exercise-tracker.git
   git push -u origin main
   ```
3. Enable GitHub Pages, serving from the `main` branch root:
   ```
   gh api repos/<USERNAME>/exercise-tracker/pages -X POST -f "source[branch]=main" -f "source[path]=/"
   ```
   (or via web UI: repo → Settings → Pages → Source: Deploy from a branch → main / root)
4. Verify: wait ~1–2 min, then confirm `https://<USERNAME>.github.io/exercise-tracker/` returns 200 and loads the app.
5. Report the live URL. The user will open it on their Android phone in Chrome and use "Add to Home screen" to install it.

## Redeploying after changes
1. Bump `extracker-v1` → `extracker-v2` (etc.) in `sw.js`.
2. `git add . && git commit -m "<change>" && git push`
3. Pages redeploys automatically in ~1 min.

## Testing locally
```
python -m http.server 8000
```
Open http://localhost:8000 — allow camera. Rep counting logic can be smoke-tested by extracting the inline `<script type="module">` from `index.html` and running `node --check` on it.
