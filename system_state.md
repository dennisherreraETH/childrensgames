# System State - Cooking Game

Authoritative project history. Codex updates this after completing work.

---

## Project summary
Local browser games library for a 6-year-old. `Dashboard.html` is the home launcher. `CookingGame.html` is a playable cooking recall game, `DollDressUp.html` is a playable open-ended dress-up game, `MathGame.html` is a playable addition/subtraction quiz, `SpellingGame.html` is a playable picture-to-spelling quiz, `TicTacToe.html` is a playable Bunny-vs-Bear game, and `GroceryRegister.html` is a playable pretend grocery register. Gentle, punishment-free interactions. Self-contained vanilla HTML/CSS/JS files, no server/npm/build/external libraries.

## Current status
**Phase: Dashboard, Cooking Game, Doll Dress Up, Math Fun, Spelling, Tic Tac Toe, and Grocery Store are ready.**

## Assets in folder
- `Bunny_Character.jpg` - Bunny chef image.
- `Bear_Character.jpg` - Bear chef image.
- `Character_JSON.json` - art/HUD style reference (cream/blue/wood bakery palette). Not loaded at runtime.
- `DESIGN.md` - full design & architecture spec (screen flow, state model, recipe data model, sound plan, kid-usability, Codex prompt).
- `GAMING_DASHBOARD_DESIGN.md` - games-library launcher and shared design-system spec.
- `CODEX_PROMPTS.md` - build prompts for Dashboard and future games.
- `Dashboard.html` - complete self-contained vanilla HTML/CSS/JS launcher. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, `localStorage`, or backend.
- `CookingGame.html` - complete self-contained vanilla HTML/CSS/JS game. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.
- `DollDressUp.html` - complete self-contained vanilla HTML/CSS/JS dress-up game with inline SVG dolls and clothing overlays. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.
- `MathGame.html` - complete self-contained vanilla HTML/CSS/JS addition/subtraction quiz with stars, counting dots, and synthesized sound. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.
- `SpellingGame.html` - complete self-contained vanilla HTML/CSS/JS picture-to-spelling quiz with emoji art, word choices, stars, and synthesized sound. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.
- `TicTacToe.html` - complete self-contained vanilla HTML/CSS/JS Bunny-vs-Bear tic-tac-toe with mode pick, AI, stars, confetti, and synthesized sound. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.
- `GroceryRegister.html` - complete self-contained vanilla HTML/CSS/JS pretend grocery register with emoji shelf buttons, receipt lines, running total, Pay/New flow, confetti, and synthesized sound. Runs from `file://` / double-click; no build tools, libraries, external requests, audio files, or `localStorage`.

## Major decisions
- 2026-06-14 - Single self-contained `CookingGame.html`; no build tools or libraries.
- 2026-06-14 - Ingredient icons = emoji (no image files); `icon` field can later hold an image path.
- 2026-06-14 - Sound effects synthesized via Web Audio API (no audio files). "Wrong" sound is a soft low tone, never a buzzer.
- 2026-06-14 - Both chefs play identically; chef choice is cosmetic.
- 2026-06-14 - Wrong taps never remove stars and never block play. Stars only increase.
- 2026-06-14 - Easiest recipes use `ordered: false`; harder recipes default to ordered step-by-step play.
- 2026-06-14 - `RECIPES` block is commented in `CookingGame.html`; add ingredients in `INGREDIENTS`, then add recipe objects to the desired meal array.
- 2026-06-14 - Recipe-pick screen uses a dedicated responsive, scrollable grid sorted by difficulty ascending so each meal's 6-card ramp reads easy-to-hard.
- 2026-06-14 - Close emoji stand-ins used where Unicode has no exact item: ketchup/sauce use a can icon, dough uses flatbread, and chips use a potato icon.
- 2026-06-14 - Cook screen keeps the small top-bar portrait and also shows the active chef as a larger framed figure in the cooking workspace beside/above the plate, depending on viewport width.
- 2026-06-14 - Chef presentation uses transform/opacity-only CSS motion: Bunny pose frames swap for idle/stir/cheer, while fallback photos use calm sway/nudge/hop motion. `prefers-reduced-motion: reduce` disables continuous and reaction motion while still allowing static pose swaps.
- 2026-06-14 - Cook flow is now recall-based: recipe start shows a full ingredient memory card with a Start Cooking button, then active cooking hides the exact next ingredient prompt.
- 2026-06-14 - Frustration guardrail uses `HINT_AFTER_MISSES = 3`; after three wrong taps on the same step, the correct tray button is gently highlighted and the feedback reveals that one step.
- 2026-06-14 - Only the three easiest recipes are unordered: `eggs_and_toast`, `cereal`, and `banana_yogurt_bowl`. All other recipes are ordered.
- 2026-06-14 - Bunny and Bear cook/Win animation use pose-frame PNGs from `Animation/`: idle, stir, cheer. The existing JPGs remain defensive fallbacks.
- 2026-06-14 - Pose-frame fallback is defensive: if a pose PNG fails to load, `onerror` switches that chef to the existing JPG and marks the pose set failed to avoid broken-image icons.
- 2026-06-14 - Removed the emoji stove and steam props from the chef station. The station is now a simple framed bakery counter/background.
- 2026-06-14 - Ingredient trays use a Fisher-Yates shuffle when a recipe play starts. The shuffled order is stored in `shuffledTray` and remains stable for the whole play; correctness and hints still use ingredient ids, not positions.
- 2026-06-16 - Games library architecture starts with `Dashboard.html` as a thin launcher. Add future games by dropping a self-contained HTML file, adding one `GAMES` line, and setting `ready: true`.
- 2026-06-17 - Dashboard registry now has Cooking, Doll Dress Up, Math Fun, Spelling, Tic Tac Toe, and Grocery Store marked `ready: true`.
- 2026-06-16 - Cooking Game top bar now includes a persistent `🏠 Home` button beside the in-game Back arrow. Home always leaves the game with `window.location.href = "Dashboard.html"`; Back remains in-game navigation.
- 2026-06-16 - Dashboard game grid is capped at 3 cards per row on wide screens, drops to 2 columns on medium screens, and 1 column on narrow screens.
- 2026-06-16 - Doll Dress Up uses a two-screen flow: Pick Doll, then Dressing Room. Clothes are inline SVG overlays with slot rules for `head`, `top`, `bottom`, `feet`, and `full`; `full` clears top/bottom and selecting top/bottom clears `full`.
- 2026-06-16 - Doll Dress Up is open-ended with no score, win, fail, timers, storage, or penalties. Reset clears clothing back to the base doll.
- 2026-06-17 - Doll Dress Up artwork was refreshed as an art-only pass. The base dolls now use refined chibi proportions, visible necks, tapered limbs, angled arms, highlighted eyes, smaller cheeks, warm-brown linework, and distinct girl/boy hair. Garments now use fitted silhouettes, collars/hems, soft folds, shared SVG depth shading, and a subtle closet/bedroom backdrop behind the doll. `DOLLS`, `CLOTHES`, `renderDollSvg()`, navigation, sound, and behavior wiring were left unchanged.
- 2026-06-17 - Doll Dress Up girl hair received a narrow art-only correction inside `girlBaseSvg()` so the long hair no longer wraps under the chin. The crown/bangs remain, the face and jaw are open skin, and long side locks fall outside the face/neck behind the body/arms. Boy doll, garments, background, data, layering logic, navigation, sound, and behavior were left unchanged.
- 2026-06-16 - Math Fun uses endless generated problems from `makeProblem(mode)` with modes `add`, `sub`, and default `mixed`. Addition stays at sums <= 10; subtraction never goes negative.
- 2026-06-16 - Math Fun stars only increase. Wrong answers play a soft sound and keep the same problem; correct answers add one star, show confetti, then auto-load a fresh problem.
- 2026-06-16 - Spelling uses hand-authored `WORDS` entries with 15-20 simple CVC/sight words, three shuffled choices per round, no repeat of the same word twice in a row, and stars only increasing.
- 2026-06-16 - Spelling wrong answers are gentle and keep the same word on screen; correct answers add a star, show confetti, then auto-load a new word.
- 2026-06-16 - Tic Tac Toe uses Bunny vs Bear tokens, a 2 Players or vs Computer mode pick, and an easy/beatable AI that first seeks a win, then blocks, then picks randomly.
- 2026-06-16 - Tic Tac Toe stars only increase on wins; ties are friendly and no-penalty, and Play Again keeps the chosen mode.
- 2026-06-17 - Grocery Store uses a commented `ITEMS` block with 12 emoji groceries and whole-dollar prices from $1-$5. Change-making is intentionally left as a possible later v2 addition; v1 focuses on scanning items and simple running totals.
- 2026-06-17 - Grocery Store is open-ended pretend play with no score, fail state, timer, storage, or penalties. Pay plays a happy cha-ching and confetti; New clears the receipt for the next customer.

## Completed tasks
- 2026-06-14 - Design & architecture written to `DESIGN.md`.
- 2026-06-14 - Handoff files (`system_state.md`, `ToChat.md`) stubbed.
- 2026-06-14 - Built `CookingGame.html` as one self-contained file with Pick Chef, Pick Meal, Pick Recipe, Cook, and Win screens.
- 2026-06-14 - Seeded 6 recipes: 2 each for breakfast/lunch/dinner, with one unordered easy recipe and one ordered harder recipe per meal.
- 2026-06-14 - Implemented Web Audio API sound helpers (`playTone`, `sfxTap`, `sfxRight`, `sfxWrong`, `sfxWin`) plus mute toggle.
- 2026-06-14 - Implemented forgiving cook logic: correct taps animate ingredients onto the plate and advance; wrong taps play a soft low tone and show a picture hint with no penalty.
- 2026-06-14 - Verified inline script parsing, no external/localStorage references, headless Edge `file://` load, and recipe logic harness across all 6 recipes.
- 2026-06-14 - Added 12 recipes: 4 new breakfast, 4 new lunch, 4 new dinner. Totals are now 6 recipes per meal / 18 recipes overall.
- 2026-06-14 - Added requested ingredient ids for the expanded recipe set, reusing existing `egg`, `bread`, `cheese`, `pan`, `milk`, `tomato`, `bacon`, `chicken`, `broccoli`, and `pasta`.
- 2026-06-14 - Verified 18-recipe expansion: inline script parses; no external/localStorage references; no duplicate emojis within any recipe tray; Edge loads the `file://` page; logic harness completed all 18 recipes and confirmed wrong taps never deducted stars.
- 2026-06-14 - Added animated chef station to Cook using existing `Bunny_Character.jpg` / `Bear_Character.jpg`; later revised to remove stove/steam props and use Bunny pose frames with photo fallback. No libraries, external requests, storage, recipe data, cook logic, sounds, stars, or punishment-free behavior changes.
- 2026-06-14 - Verified animated-chef pass: inline script parses; no external/localStorage references; Edge loads the `file://` page; harness confirmed chef station/reaction classes and completed all 18 recipes with 18 wrong taps and no star deductions.
- 2026-06-14 - Made gameplay harder: removed always-visible next-ingredient hints, added recipe memory card, expanded every tray to 3+ distractors, and enforced ordered play on 15 of 18 recipes while preserving no-penalty wrong taps.
- 2026-06-14 - Verified harder-mode pass: inline script parses; no external/localStorage references; Edge loads the `file://` page; no duplicate emojis within recipe trays; all recipes have at least 3 distractors; harness completed all 18 recipes, tested wrong taps for all 18, verified delayed hint after 3 misses, and confirmed no star deductions.
- 2026-06-14 - Replaced Bunny photo wiggle with real pose-frame art: `Animation/bunny_idle.png`, `Animation/bunny_stir.png`, `Animation/bunny_cheer.png`. Correct taps show stir for about 600ms; wrong taps keep idle with a soft nudge; completion holds cheer through Win with one gentle hop.
- 2026-06-14 - Verified pose-frame pass: all three Bunny frame files exist; code references relative `Animation/...` paths; Bear renders `Bear_Character.jpg` with `is-photo-fallback`; fallback handler swaps failed Bunny frames to `Bunny_Character.jpg`; inline script parses; Edge loads the `file://` page; clean logic harness completed all 18 recipes and confirmed wrong taps never deducted stars.
- 2026-06-14 - Activated Bear pose frames: `Animation/bear_idle.png`, `Animation/bear_stir.png`, `Animation/bear_cheer.png`, using the same idle/stir/wrong/cheer mapping as Bunny with defensive JPG fallback intact.
- 2026-06-14 - Added per-play tray shuffling. Harness confirmed the same recipe can produce different tray orders across plays, correct ingredients are no longer locked to the first canonical slots, and the tray order does not move after wrong or correct taps within a play.
- 2026-06-14 - Verified Bear/shuffle pass: all Bear frame files exist; inline script parses; no external/localStorage references; Edge loads the `file://` page; clean logic harness completed all 18 recipes and confirmed wrong taps never deducted stars.
- 2026-06-16 - Built `Dashboard.html` as one self-contained launcher with responsive big game cards, shared cream/blue/wood palette, Web Audio `playTone`/`sfxTap`, mute toggle, and disabled coming-soon cards for unbuilt games.
- 2026-06-16 - Verified Dashboard static constraints: single file, inline CSS/JS only, no external requests, no libraries, no build step, no `localStorage`; current ready links are Cooking and Doll Dress Up, with four inactive coming-soon games.
- 2026-06-16 - Added persistent Home navigation to `CookingGame.html` without changing game logic, recipes, sounds, stars, or animations.
- 2026-06-16 - Updated `Dashboard.html` grid CSS to enforce 3 / 2 / 1 responsive columns while preserving card styling and ready/coming-soon behavior.
- 2026-06-16 - Built `DollDressUp.html` as one self-contained game with Boy/Girl doll selection, category tabs, 50 clothing items across sex/category groups, Web Audio tap blip, mute toggle, Reset, Home, and Pick Doll navigation.
- 2026-06-16 - Set the Dashboard `dress` registry entry to `ready:true` so Doll Dress Up launches from `Dashboard.html`.
- 2026-06-16 - Verified Doll Dress Up with static self-contained scans, Node behavior harness, Dashboard registry check, and headless Edge `file://` smoke loads for `DollDressUp.html` and `Dashboard.html`.
- 2026-06-17 - Polished `DollDressUp.html` SVG art only: rebuilt the interiors of `girlBaseSvg()`, `boyBaseSvg()`, and the garment helper drawing functions while preserving function names/signatures, data blocks, slot/layering behavior, `renderDollSvg()` wiring, navigation, sound, and storage/network constraints.
- 2026-06-17 - Fixed `DollDressUp.html` girl hair/beard issue as a `girlBaseSvg()`-only art edit. Verified protected hashes for `DOLLS`, `CLOTHES`, `boyBaseSvg()`, garments, stage background, `renderDollSvg()`, and behavior wiring were unchanged.
- 2026-06-16 - Built `MathGame.html` as one self-contained game with mode toggle, generated addition/subtraction problems, three answer choices, counting-aid toggle, stars, confetti, Web Audio feedback, Home, and mute.
- 2026-06-16 - Set the Dashboard `math` registry entry to `ready:true` so Math Fun launches from `Dashboard.html`.
- 2026-06-16 - Verified Math Fun with static self-contained scans, Node behavior harness for problem generation and answer handling, Dashboard registry check, and headless Edge/CDP `file://` render validation.
- 2026-06-16 - Built `SpellingGame.html` as one self-contained game with emoji prompts, lowercase spelling choices, hand-authored words, stars, confetti, Web Audio feedback, Home, and mute.
- 2026-06-16 - Set the Dashboard `spelling` registry entry to `ready:true` so Spelling launches from `Dashboard.html`.
- 2026-06-16 - Built `TicTacToe.html` as one self-contained game with Bunny-vs-Bear tokens, mode pick, easy vs tricky AI, win/tie handling, stars, confetti, Web Audio feedback, Home, and mute.
- 2026-06-16 - Set the Dashboard `tictactoe` registry entry to `ready:true` so Tic Tac Toe launches from `Dashboard.html`.
- 2026-06-17 - Built `GroceryRegister.html` as one self-contained game with a grocery shelf, scanner beep, receipt line items, running TOTAL, Pay/New buttons, happy cha-ching, confetti, Home, and mute.
- 2026-06-17 - Set the Dashboard `grocery` registry entry to `ready:true` so Grocery Store launches from `Dashboard.html`.

## Milestones
- [x] Design & architecture
- [x] v1 playable prototype (CookingGame.html)
- [x] Recipe content pass (6+ recipes across meals)
- [x] Polish (animations, styling, sound)
- [x] Self-test pass
- [x] Dashboard launcher
- [x] Doll Dress Up game
- [x] Math Fun game
- [x] Spelling game
- [x] Tic Tac Toe game
- [x] Grocery Store game
