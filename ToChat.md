# ToChat — Games Library

Short handoff for ChatGPT. Updated by the architect/Codex after each session.

## Just completed (architecture phase)
- Project expanded from a single Cooking Game into a **multi-game library** with a launcher.
- Architect wrote `GAMING_DASHBOARD_DESIGN.md` — master spec: launcher + separate-files architecture, game registry, shared design system (palette, ≥96px tap targets, Web Audio sound, 🏠 Home navigation, punishment-free rules), and a full spec for each of the 5 new games.
- Architect wrote `CODEX_PROMPTS.md` — six copy-paste, build-ready Codex prompts (Dashboard + 5 games), to be run one at a time in order.
- Codex built `Dashboard.html` as Prompt 1: a self-contained launcher with responsive big cards, Cooking active, and all unbuilt games dimmed with "Coming soon!".
- Dashboard includes the shared Web Audio `playTone` + `sfxTap` module and a mute toggle.
- Codex added the retrofit `🏠 Home` button to `CookingGame.html`; it sits beside Back in the top bar and returns to `Dashboard.html`.
- Codex changed the Dashboard grid cap to 3 columns wide, 2 columns medium, and 1 column narrow.
- Codex built `DollDressUp.html`: self-contained dress-up game with Boy/Girl selection, inline SVG dolls, category tabs, wardrobe buttons, slot/layer rules, Reset, Home, Back, tap sound, and mute.
- Dashboard `dress` entry is now `ready:true`.
- Codex built `MathGame.html`: self-contained addition/subtraction quiz with Mixed/Add/Subtract modes, generated 0-10 problems, 3 answer choices, counting dots, stars, confetti, Home, tap/win/wrong sounds, and mute.
- Dashboard `math` entry is now `ready:true`.
- Codex built `SpellingGame.html`: self-contained picture-to-spelling quiz with hand-authored words, three lowercase choices, stars, confetti, Home, tap/win/wrong sounds, and mute.
- Dashboard `spelling` entry is now `ready:true`.
- Codex built `TicTacToe.html`: self-contained Bunny-vs-Bear tic-tac-toe with 2 Players / vs Computer mode pick, easy/tricky AI, stars, confetti, Home, tap/win/wrong sounds, and mute.
- Dashboard `tictactoe` entry is now `ready:true`.
- Codex built `GroceryRegister.html`: self-contained pretend grocery register with a big emoji grocery shelf, whole-dollar prices, scanner beep, receipt lines, running TOTAL, Pay/New buttons, happy cha-ching, confetti, Home, and mute.
- Dashboard `grocery` entry is now `ready:true`.
- Codex polished `DollDressUp.html` artwork as an art-only pass. The SVG dolls now have refined chibi proportions, warm-brown linework, highlighted eyes, smaller cheeks, distinct girl/boy hair, fitted shaded garments, and a subtle bedroom/closet backdrop. No `DOLLS` / `CLOTHES` data, `renderDollSvg()` wiring, navigation, sound, slot logic, or game behavior was changed.
- Codex made a small `girlBaseSvg()`-only art fix in `DollDressUp.html`: the girl's hair no longer closes under the chin like a beard. The crown/bangs remain, the face and jaw stay open, and long side locks fall outside the face/neck behind the body/arms. Boy doll, garments, background, data, logic, navigation, and sound were untouched.
- Codex completed static-hosting prep for GitHub/Vercel: added root `index.html` redirecting to `Dashboard.html`, added public `README.md`, and re-verified case-sensitive `href`, dashboard `GAMES.file`, and image-path references.

## Current status
- Dashboard: done (`Dashboard.html`).
- Cooking Game: done and ready (`CookingGame.html`), with both in-game Back and launcher Home controls in the top bar.
- Doll Dress Up: done and ready (`DollDressUp.html`).
- Math Fun: done and ready (`MathGame.html`).
- Spelling: done and ready (`SpellingGame.html`).
- Tic Tac Toe: done and ready (`TicTacToe.html`).
- Grocery Register: done and ready (`GroceryRegister.html`).

## Architecture (locked decisions)
- `Dashboard.html` is a thin launcher; each game is its own self-contained `.html` file. A `GAMES` registry array drives the dashboard — add a game = drop a file + add one line + set `ready:true`.
- Same constraints as the cooking game: vanilla HTML/CSS/JS, one file each, no server/build/libraries/network/localStorage, double-click to run.
- Art = emoji + inline SVG/CSS (no image files). Shared cream/blue/wood palette.
- Codex builds one game per prompt. After a game exists, flip its Dashboard `ready` flag to `true`.
- Grocery Register v1 intentionally has no change-making. A later version could add simple paid/change practice after the register play is accepted.

## Recommended next steps
1. Human runs git review/commit/push to `dennisherreraETH/childrensgames`.
2. Import the repo on Vercel with framework preset `Other`, no build command, and repo root as the output.
3. Optional later add: simple change-making mode for the Grocery Register.

## Verification just run
- Static and script checks passed for `Dashboard.html`.
- Dashboard registry has exactly 6 entries and all six are `ready:true`: `cooking`, `dress`, `math`, `spelling`, `tictactoe`, and `grocery`.
- The Cooking, Doll Dress Up, Math Fun, Spelling, Tic Tac Toe, and Grocery Store cards are active links.
- No external requests, libraries, build hooks, or `localStorage` references found.
- Latest pass also verified `CookingGame.html` has one shared Home button renderer, all five screens render Home, `goHome()` uses `window.location.href = "Dashboard.html"`, and Dashboard CSS has 3 / 2 / 1 grid breakpoints.
- Latest Doll Dress Up pass verified static self-contained constraints, script parse, 50 clothing items, 5 categories for each doll, Home to `Dashboard.html`, Back to Pick Doll, Reset, slot replacement, `full` clearing top/bottom, top/bottom clearing `full`, Dashboard `dress` ready link, and headless Edge `file://` smoke loads.
- Latest Doll Dress Up art-refresh pass verified `DOLLS`, `CLOTHES`, `renderDollSvg()`, and behavior wiring hashes stayed byte-identical; all requested drawing helper signatures stayed unchanged; no external/network/storage patterns were introduced; script parse and behavior harness passed; headless Edge `file://` screenshot render passed for the pick screen.
- Latest girl-hair fix verified only `girlBaseSvg()` changed: protected hashes for `DOLLS`, `CLOTHES`, `boyBaseSvg()`, garments, stage background, `renderDollSvg()`, and behavior wiring stayed unchanged; no external/network/storage patterns were introduced; behavior harness confirmed dress clears top/bottom and Reset clears the doll; headless Edge `file://` screenshot render passed.
- Latest static-hosting prep verified root `index.html` has meta refresh, `location.replace("Dashboard.html")`, and a visible relative fallback link; `README.md` documents games, local run, Vercel deploy, and vanilla/no-dependency constraints; no `package.json`, `node_modules`, or `vercel.json` exists; no external/network/storage patterns were introduced; case-sensitive audit checked 21 `href`, dashboard `GAMES.file`, and image-path references with zero mismatches or missing files.
- Latest Math Fun pass verified static self-contained constraints, script parse, 0-10 add/subtract problem rules, near-miss answer choices, wrong-answer no-penalty behavior, correct-answer star increment/new problem behavior, counting-aid toggle, Dashboard `math` ready link, and headless Edge/CDP `file://` render validation.
- Latest Spelling pass verified static self-contained constraints, script parse, hand-authored WORDS list, shuffled lowercase choices, no-repeat word selection, wrong-answer no-penalty behavior, correct-answer star increment/new word behavior, Dashboard `spelling` ready link, and headless Edge/CDP `file://` render validation.
- Latest Tic Tac Toe pass verified static self-contained constraints, script parse, Bunny/Bear tokens, 2 Players and vs Computer modes, easy/tricky AI, win/tie handling, winning-line highlight, Dashboard `tictactoe` ready link, and headless Edge/CDP `file://` render validation.
- Latest Grocery Register pass verified static self-contained constraints, script parse, 12 whole-dollar ITEMS entries from $1-$5, scan behavior, receipt total updates, Pay thank-you/confetti behavior, New receipt clearing, mute state, Home link, Dashboard `grocery` ready link, and headless Edge `file://` screenshot render validation.

## Known issues / blockers
- None blocking. Project is feature-complete (dashboard + 6 games, dolls polished).
- Awaiting human git push to GitHub and Vercel import.
