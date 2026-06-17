# ToChat - Games Library

Short handoff for ChatGPT. Updated by the architect/Codex after each session.

## Just completed
- Project expanded from a single Cooking Game into a multi-game library with `Dashboard.html` as the launcher.
- `Dashboard.html` is complete with responsive game cards, shared sound/mute controls, and ready links for all six kid games.
- `CookingGame.html` has a persistent Home button beside its in-game Back control.
- `DollDressUp.html` is complete and had two art-only passes: a refined chibi SVG refresh and a `girlBaseSvg()` hair fix so the girl's hair no longer reads as a beard.
- `MathGame.html`, `SpellingGame.html`, `TicTacToe.html`, and `GroceryRegister.html` are complete and marked ready in the Dashboard registry.
- Static-hosting prep is complete: root `index.html` redirects to `Dashboard.html`, `README.md` documents the repo, and no build/dependency config was added.
- `Asteroids.html` is complete as a hidden adult-only fullscreen canvas arcade game with vector ship controls, bullets, wrapping/splitting asteroids, endless waves, lives, score, in-memory high score, pause, mute, synthesized sound, and Home navigation.
- The secret Dashboard trigger is a tiny low-opacity `✦` at the bottom-right of `.page` that launches `Asteroids.html`. It is not in the `GAMES` registry and does not render a game card.

## Current status
- Dashboard: done (`Dashboard.html`).
- Cooking Game: done and ready (`CookingGame.html`).
- Doll Dress Up: done and ready (`DollDressUp.html`).
- Math Fun: done and ready (`MathGame.html`).
- Spelling: done and ready (`SpellingGame.html`).
- Tic Tac Toe: done and ready (`TicTacToe.html`).
- Grocery Register: done and ready (`GroceryRegister.html`).
- Hidden Asteroids: done and ready (`Asteroids.html`), reachable only through the secret Dashboard glyph.

## Architecture
- Each visible kid game is its own self-contained `.html` file and is launched from the Dashboard `GAMES` registry.
- `Asteroids.html` is the exception to the visible registry rule: it is intentionally hidden from `GAMES` and launched only by the Dashboard `✦` Easter egg.
- Constraints remain: vanilla HTML/CSS/JS, no server/build/libraries/network/localStorage, double-click to run on Windows.
- Kid games keep the shared cream/blue/wood palette, big tap targets, Home navigation, synthesized Web Audio, and punishment-free rules.
- Hidden Asteroids intentionally uses a dark arcade style and adult keyboard gameplay; the kid-usability rules do not apply there.
- Grocery Register v1 intentionally has no change-making. A later version could add simple paid/change practice after the register play is accepted.

## Recommended next steps
1. Human commits and pushes to GitHub so Vercel can auto-deploy.
2. Optional later add: simple change-making mode for the Grocery Register.

## Verification just run
- Static and script checks passed for `Dashboard.html` and `Asteroids.html`.
- Dashboard registry still has exactly 6 entries and all six are `ready:true`: `cooking`, `dress`, `math`, `spelling`, `tictactoe`, and `grocery`.
- `Asteroids.html` is not present in the `GAMES` registry and does not render a card.
- No external requests, libraries, build hooks, or storage references were found in the changed runtime files.
- `Asteroids.html` mechanics harness passed: initial/reset state, lives, waves, asteroid split scoring, game-over state, and in-memory high score.
- Headless Edge `file://` screenshots rendered both the Asteroids start screen and the Dashboard secret glyph.

## Known issues / blockers
- None blocking. Dashboard + 6 kid games complete; hidden Asteroids complete; static-hosting prep (`index.html` + `README.md`) done.
