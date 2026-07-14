# FX Trainer — MA2 Effect Engine Training Game

## What this is
A personal web game for Kadmon (lighting designer, MA2 operator). A target
lighting effect animates on screen; the player recreates it using a faithful
mimic of the grandMA2 Effect Value window. Trains real console muscle memory.
Personal use + a few friends. No monetization, no accounts, no analytics.

## Current state
`fx-trainer-v1.html` is the complete, playable v1 — a single self-contained
HTML file developed as a Claude.ai artifact (PoC phase). Feature-complete:
- 8 fixtures on canvas (target row + player row), sine dimmer effect
- MA2-accurate parameters: Speed (BPM), Low/High values, Phase from..to
  (degrees, "thru" syntax), Direction forward/backward
- UI mimics the real MA2 Effect Value window (fader strips, grayed
  Width/Attack/Decay) and MA2 calculator windows (numpad, Thru key, green
  input field, preset buttons, "Please" confirm)
- Equivalence-aware scoring (Speed 30 / Size 30 / Phase 40): effects are
  canonicalized before comparison so console-equivalent inputs (reversed
  direction + mirrored phase, swapped low/high, global phase offsets,
  ±360 wraps) score as correct
- 10 named cues, locked progression, stars (60/80/95), per-cue best scores,
  in-memory progress, "STORED" win stamp, Sequence Complete summary + ranks

## Core math (do not change without checking against real MA2)
Per fixture i of N (N=8):
  phase_i = phaseFrom + (phaseTo - phaseFrom) * i/(N-1)   // "from Thru to"
  dim_i(t) = clamp(low + (high-low) *
             (sin(2*pi*(BPM/60)*dir*t + phase_i*pi/180)+1)/2, 0, 100)
- Speed is BPM (60 BPM = 1 Hz), NOT Hz — MA2 convention
- Size is low/high VALUES, not amplitude — MA2 convention
- OPEN VALIDATION: travel direction of "0 thru 360" was guessed. Kadmon will
  verify on MA2 onPC (dimmer sine, 8 fixtures, phase 0 thru 360, note travel
  direction vs selection order). If real MA2 is opposite, negate one sign in
  dimmerAt() — and mirror the same fix inside canonical().

## This session's tasks (v1 deployment) — in order
1. Init git repo. Keep single-file architecture (it's a feature: easy deploy).
   Optionally split levels into levels.json fetched at load, so future level
   packs are content updates, not code updates.
2. Persistence: localStorage for stars/best/current cue. (Forbidden in the
   Claude.ai artifact sandbox; on a real server it's fine and sufficient.
   No backend/database — out of scope.)
3. PWA: manifest.json + minimal cache-first service worker so it installs to
   the phone home screen and works offline. App name "FX Trainer", dark theme
   (bg #14100d, accent #f2c14e).
4. Deploy to Kadmon's Linode (Apache, he has root and vhost experience).
   Set up so `git pull` on the server updates the live site.
5. Apply Kadmon's playtest tuning notes if he brings any.

## Definition of done for v1
Playable from an icon on Kadmon's phone home screen, 10 cues, progress
survives closing the app. NOTHING ELSE. Claude acts as scope guardian.

## Roadmap (logged, NOT in scope this session)
- v1.5 "Chase Pack": unlock Select Form window — PWM form + Width fader
  (one-lamp-at-a-time chases), Attack/Decay functional for PWM, Mirror phase
  preset (0 thru 360 thru 0 triangular distributions)
- v2: pan/tilt position effects (needs 2D stage view)
- v3: combined dimmer+position scenarios
- Someday: hardcore mode with typed MA2 syntax (the parameter object was
  designed so a syntax parser bolts on without rewrites)

## Working style
- Kadmon is time-poor: Claude decides autonomously, explains briefly;
  Kadmon playtests and answers yes/no/weird
- One milestone per interaction, testable at each step
- MA2 authenticity beats prettiness when they conflict
- New ideas get logged into the roadmap above, never built before v1 ships
