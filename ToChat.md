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
- `PacMan.html` is complete as a hidden adult-only Maze Muncher canvas arcade game with responsive maze scaling, keyboard/WASD movement, swipe movement, ghosts, dots, power pellets, lives, score, in-memory high score, pause, mute, synthesized sound, and Home navigation.
- The second secret Dashboard trigger is a tiny low-opacity `•` pellet at the bottom-left of `.page` that launches `PacMan.html`. It is not in the `GAMES` registry and does not render a game card.

## Current status
- Dashboard: done (`Dashboard.html`).
- Cooking Game: done and ready (`CookingGame.html`).
- Doll Dress Up: done and ready (`DollDressUp.html`).
- Math Fun: done and ready (`MathGame.html`).
- Spelling: done and ready (`SpellingGame.html`).
- Tic Tac Toe: done and ready (`TicTacToe.html`).
- Grocery Register: done and ready (`GroceryRegister.html`).
- Hidden Asteroids: done and ready (`Asteroids.html`), reachable only through the secret Dashboard glyph.
- Hidden Maze Muncher: done and ready (`PacMan.html`), reachable only through the secret Dashboard pellet.

## Architecture
- Each visible kid game is its own self-contained `.html` file and is launched from the Dashboard `GAMES` registry.
- `Asteroids.html` and `PacMan.html` are exceptions to the visible registry rule: they are intentionally hidden from `GAMES` and launched only by the Dashboard secret glyphs.
- Constraints remain: vanilla HTML/CSS/JS, no server/build/libraries/network/localStorage, double-click to run on Windows.
- Kid games keep the shared cream/blue/wood palette, big tap targets, Home navigation, synthesized Web Audio, and punishment-free rules.
- Hidden Asteroids intentionally uses a dark arcade style and adult arcade gameplay; keyboard controls remain for desktop, and touch controls are shown only on touch devices.
- Hidden Maze Muncher intentionally uses a dark arcade style and adult arcade gameplay; keyboard/WASD controls remain for desktop, and touch users use canvas swipes plus touch-only pause/mute.
- Maze Muncher movement is boundary-accurate for low/irregular frame rates: player and ghosts sub-step to tile centers before resolving walls, turns, and ghost intersections.
- Maze Muncher now has the verified 198-pellet open center aisle, compact row-10 magenta ghost pen, wider frightened-ghost eat distance, slower frightened ghosts, single-ghost eyes return/pen hold, longer post-death respawn grace, respawn blinking, and a frozen 3-2-1 countdown before each life/level.
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
- `PacMan.html` and `Dashboard.html` inline scripts parse with Node.
- `PacMan.html` static checks passed: uniform 21x21 maze, 4 power pellets, ghost-house tiles, side tunnel row, no external requests/libraries/build hooks/storage references, and no restricted title/metadata wording.
- Dashboard static check passed: `GAMES` still has exactly 6 visible entries and does not include `PacMan.html`; `.secret-pac` and `setupPacTrigger()` exist alongside the Asteroids trigger.
- Headless Edge/CDP `file://` desktop check for `PacMan.html`: touch controls hidden, overlay used keyboard wording, Enter started play, Arrow input changed desired direction, dots scored, four ghosts existed, and P paused.
- Headless Edge/CDP `file://` touch-emulation check for `PacMan.html`: touch controls displayed, overlay used swipe wording, canvas/buttons had `touch-action: none`, maze was centered/scaled to portrait, swipe changed desired direction, pause/mute worked, and no runtime exceptions were captured.
- Headless Edge/CDP `file://` Dashboard check: bottom-left `•` trigger and bottom-right `✦` trigger did not overlap, the pellet click navigated to `PacMan.html`, and no runtime exceptions were captured.
- Maze Muncher movement fix checks passed: VM and headless Edge/CDP large-`dt` cases confirmed the player stops at the last open tile before a wall, buffered up-turns apply at/near centers, ghosts stay on open maze tiles and aligned to corridors, desktop keyboard direction still works, pointermove swipes update direction before lift, and touch mute toggles `🔈` / `🔇`.
- Maze Muncher latest tuning checks passed: script syntax is valid; `MAZE` is 21x21 with row 9 as the full center aisle, row 10 as the compact `HHH` pen, and all 198 pellets reachable from the player start; all `ghostStarts` use y=10; player base speed is `7.4`; ghost base speed is `4.8`; frightened speed factor remains `0.5`; eating one frightened ghost mutates only that ghost to eyes; eaten eyes hold in the pen for `2.0s`; death reset returns player/ghosts to starts with `respawnTimer = 2.2` and `readyTimer = 3`; capped-`dt` movement crosses the center aisle cleanly.

## Known issues / blockers
- None blocking. Dashboard + 6 kid games complete; hidden Asteroids and hidden Maze Muncher complete; static-hosting prep (`index.html` + `README.md`) done.
