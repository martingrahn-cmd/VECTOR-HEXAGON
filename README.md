# VECTOR HEXAGON

A twitch survival game in the spirit of **Super Hexagon**: one hexagonal core,
six lanes, and walls closing in from the edge of the screen. Slip your cursor
into the gap before each wall reaches the centre. Miss, and you start over.

One self-contained `index.html` — HTML canvas, no build step, no dependencies.
Plays on desktop and mobile.

## Play

Open `index.html` in a browser, or serve the folder:

```sh
python3 -m http.server 8791
# open http://localhost:8791
```

Published via GitHub Pages once enabled (Settings → Pages → deploy from
`main`): `https://martingrahn-cmd.github.io/vector-hexagon/`.

## Install as an app (PWA)

Over HTTPS (i.e. from GitHub Pages, not a plain `file://`), the game is an
installable Progressive Web App: it runs full-screen, has its own home-screen
icon, and **plays fully offline** — a service worker (`sw.js`) caches the shell,
icons and the soundtrack on first visit.

- **Desktop / Android (Chrome/Edge):** an **INSTALL APP** button appears on the
  title screen (browser install icon works too).
- **iOS Safari:** tap **Share → Add to Home Screen** (the title screen shows a
  hint). Launches standalone, no browser chrome.

`manifest.webmanifest` uses relative paths, so it installs correctly from the
project subpath. Bump `CACHE` in `sw.js` when cached files change to push an
update to installed clients.

## Controls

| Input | Action |
|---|---|
| `←` / `→` or `A` / `D` | rotate the cursor around the core |
| **Tap & hold** left / right half (mobile, mouse) | rotate that way |
| `SPACE` / `Enter` / tap | begin · retry |
| `Esc` | back to menu |
| `M` | toggle sound |

## Clearing a tier (60s) and climbing

Survive **60 seconds** on a tier and the run **auto-advances into the next one,
seamlessly, in the same run** — the field flips its spin, snaps to the tier's
colour and the announcer calls the new level, while the timer keeps running. The
shape milestones (LINE/TRIANGLE/SQUARE/PENTAGON/HEXAGON at 10s steps) mark
progress toward each clear. Start on any tier; clearing the last one
(**HEXAGONEST**) is a **YOU WIN**, and each tier you clear from earns a ★ on the
menu (persisted). The HUD shows the tier you're currently in.

## Feel

- **Announcer** — a spoken "BEGIN" / "GAME OVER" (Web Speech) plus on-screen
  callouts, and survival-milestone shouts as you last longer: LINE (10s),
  TRIANGLE, SQUARE, PENTAGON, HEXAGON, PERFECT (60s).
- **Close-call feedback** — thread a wall by a hair and the cursor flares, the
  screen flashes and a high tick fires (with a subtle phone buzz on razor-thin
  passes). The closer the pass, the stronger the cue.
- **Haptics** — mobile devices vibrate on a crash (`navigator.vibrate`).

## Music (Suno-ready)

The field pulses, flashes and flips its spin **in time with whatever is
playing**, because both soundtrack sources feed one beat engine:

- **SYNTH** (default) — a small procedural WebAudio groove, always available.
- **Your own MP3** — click **＋ LOAD MP3** and pick a track (e.g. one you made
  in [Suno](https://suno.com)). The game runs an `AnalyserNode` over it and
  detects beats live from the bass energy, so the visuals dance to *any* song —
  no need to enter a BPM.

To ship a soundtrack with the game, commit it as **`assets/track.mp3`**; it is
picked up automatically on the first run. Missing file → the synth groove plays,
no error.

## Modes

Three difficulties, each with its own best time saved to `localStorage`:

- **HEXAGON** — approachable pace, calmer patterns
- **HEXAGONER** — faster walls, tighter spin
- **HEXAGONEST** — brutal

## How it works

The player is an angle orbiting the core at a fixed radius; walls are hexagonal
rings that shrink inward, each blocking a subset of the six lanes and leaving at
least one gap. Collision is a pure polar-domain check — is the player's centre
lane blocked while the ring crosses the orbit radius — so there are no colliders
and no tunnelling. Walls come from a difficulty-weighted pattern spawner
(tunnel · spiral · zig-zag · spike · C-shape) with a short grace window at the
start of each run. The field spins, pulses on the beat and drifts through hues;
the soundtrack is a small procedural WebAudio groove.

Built with Claude Code.
