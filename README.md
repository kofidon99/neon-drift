# Neon Drift

A one-thumb neon lane racer, built to YouTube Playables spec. Single HTML file,
no build step, no dependencies, no external network requests at runtime.

## Controls

| Action | Touch | Keyboard |
|---|---|---|
| Change lane | Tap or swipe left / right | `←` `→` or `A` `D` |
| Boost | Tap the ring (when charged) | `Space` or `W` |
| Pause | Pause button | `Esc` or `P` |
| Mute | Speaker button | `M` |

## How it plays

Traffic comes at you down four lanes and never stops. Squeeze past a car and
you bank a **near miss**, which adds score and builds a combo up to ×9. Energy
gems charge the **boost** ring; fire it and you become briefly invincible at
1.55× speed with a doubled multiplier, smashing through anything in the way.
A shield gem shows up occasionally and absorbs one hit.

Mechanics arrive one at a time rather than all at once: traffic first, then
energy, then boost, then lane-changing drifters at ~35s, then two-lane barriers
at ~60s. Speed and spawn density rise continuously.

## Playables compliance notes

Built against the published requirements:

- **Initial load ~65 KB**, one file. Limit is 30 MB, so there's enormous headroom.
- **Zero external requests.** No CDN, no web fonts, no analytics, no multiplayer.
  All art is drawn procedurally on canvas; all audio is synthesised with Web
  Audio. Verified automatically in `test/run.js`.
- **No copyrighted assets.** Nothing is sampled, traced or licensed — there is
  no image or audio file in the bundle at all.
- **Scales to 1:1, 16:9 and 9:16.** Scale is driven by height so the road keeps
  a constant on-screen size; extra width reveals more city rather than
  stretching gameplay. Screenshots of all three in `test/shots/`.
- **`ytgame.game.firstFrameReady()` then `ytgame.game.gameReady()`**, in that
  order, and `gameReady` only once the menu is actually interactive.
- **Pause and mute are obeyed immediately.** `ytgame.system.onPause` cancels the
  animation frame outright — no rendering, no gameplay — and zeroes the audio
  master gain. `onAudioEnabledChange` is honoured and in-game mute cannot
  override the platform setting.
- **Progress is saved through `ytgame.game.saveData` / `loadData`**, falling back
  to `localStorage` only when the SDK is absent.
- **No ads wired up yet.** Interstitial and rewarded hooks are deliberately left
  out rather than stubbed — add them once the channel is onboarded and the
  portal tells you which placements are supported.

Every SDK call is feature-detected and wrapped in try/catch, so the same file
runs identically on GitHub Pages, in an artifact, and inside Playables.

## Uploading to the Playables developer portal

`neon-drift-playables.zip` is the bundle to upload once your channel is
onboarded — it contains `index.html` at the archive root, which is the layout
the portal expects.

## Repo layout

```
index.html                    the whole game
.nojekyll                     tells GitHub Pages to serve files as-is
neon-drift-playables.zip      bundle for the developer portal
src/body.html                 source of truth (title + style + markup + script)
build.js                      wraps src/body.html into index.html
test/run.js                   aspect ratios, external requests, pause, perf
test/sdk.js                   integration test against a mocked ytgame SDK
test/shot.js                  screenshot capture at 9:16, 1:1, 16:9
```

`node build.js` regenerates `index.html`. `node test/run.js && node test/sdk.js`
runs the checks.
