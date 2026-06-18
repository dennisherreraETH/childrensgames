# ToChat - Games Library

Short handoff for ChatGPT. Updated by the architect/Codex after each session.

## Just completed
- Project expanded from a single Cooking Game into a multi-game library with `Dashboard.html` as the launcher.
- `Dashboard.html` is complete with responsive game cards, shared sound/mute controls, and ready links for all six kid games.
- `CookingGame.html` has a persistent Home button beside its in-game Back control.
- `DollDressUp.html` is complete and had two art-only passes: a refined chibi SVG refresh and a `girlBaseSvg()` hair fix so the girl's hair no longer reads as a beard.
- `MathGame.html`, `SpellingGame.html`, `TicTacToe.html`, and `GroceryRegister.html` are complete and marked ready in the Dashboard registry.
- Static-hosting prep is complete: root `index.html` redirects to `Dashboard.html`, `README.md` documents the repo, and no build/dependency config was added.
- `Asteroids.html` is complete as a hidden adult-only fullscreen canvas arcade game with vector ship controls, touch-only iPad/iPhone controls, bullets, wrapping/splitting asteroids, endless waves, lives, score, in-memory high score, pause, mute, synthesized sound, and Home navigation.
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
- Hidden Asteroids intentionally uses a dark arcade style and adult arcade gameplay; keyboard controls remain for desktop, and touch controls are shown only on touch devices.
- Grocery Register v1 intentionally has no change-making. A later version could add simple paid/change practice after the register play is accepted.

## Recommended next steps
1. Human commits and pushes to GitHub so Vercel can auto-deploy.
2. Optional later add: simple change-making mode for the Grocery Register.

## Verification just run
- `Asteroids.html` inline script parses with Node.
- `Asteroids.html` static checks passed: no external requests, libraries, build hooks, storage references, `stopPropagation`, or non-vanilla additions.
- `Asteroids.html` `update()` is unchanged from `HEAD`; touch movement/fire drives only `keys.ArrowLeft`, `keys.ArrowRight`, `keys.ArrowUp`, and `keys.Space`.
- Headless Edge/CDP `file://` desktop check: `navigator.maxTouchPoints` was `0`, no `body.touch` class was set, `.move-pad` / `.fire-pad` stayed `display: none`, and keyboard help text remained visible.
- Headless Edge/CDP `file://` touch-emulation check: touch controls displayed, touch overlay text used "Tap" wording, controls were below overlay z-index, canvas/buttons had `touch-action: none`, thrust+fire held together set `keys.ArrowUp` and `keys.Space`, release cleared both flags, pause showed "Tap to resume", and mute toggled on/off.

## Known issues / blockers
- None blocking. Dashboard + 6 kid games complete; hidden Asteroids complete; static-hosting prep (`index.html` + `README.md`) done.
