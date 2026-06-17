# Codex Prompts — Gaming Dashboard build

Hand these to Codex **one at a time**, in order. Each is self-contained. The full spec lives in `GAMING_DASHBOARD_DESIGN.md` (referenced as DESIGN below). After each task, Codex flips the game's `ready` flag in the Dashboard registry and updates `system_state.md` + `ToChat.md`.

**Shared rules baked into every prompt:** one self-contained `.html` file (inline `<style>`+`<script>`), vanilla JS only, no libraries/build/modules, no external requests, no `localStorage`, runs by double-click on Windows from `file://`. Palette + interaction rules + sound module per `GAMING_DASHBOARD_DESIGN.md` §3. Tap targets ≥96px, picture+word choices, punishment-free (no timers, no losing, stars only go up), 🔈/🔇 mute, `prefers-reduced-motion` support, 🏠 Home button (→ `Dashboard.html`) top-left.

---

## Prompt 1 — Dashboard.html (build first)

> Build `Dashboard.html` in `C:\Claude\Cooking Game\` — a single self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, runs by double-click on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §2–§3.
>
> It is a kid game launcher. Show a friendly title ("🎮 Game Time!") and a responsive grid of big game cards rendered from a `GAMES` array exactly as in DESIGN §2:
> `[{id,name,icon,file,ready}]` with entries for cooking (🍳, CookingGame.html, ready:true), dress (👗, DollDressUp.html), math (➕, MathGame.html), spelling (🔤, SpellingGame.html), tictactoe (⭕, TicTacToe.html), grocery (🛒, GroceryRegister.html). **Set every game except `cooking` to `ready:false` for now** (the others don't exist yet).
> - A `ready:true` card is a big tappable link (`<a href="file">`) showing its emoji icon + name. A `ready:false` card is dimmed, not clickable, with a small "Coming soon!" badge.
> - Use the shared palette (cream `#FFF6E6`, sky blue `#4FA3E0`, deep blue `#2C6FAE`, wood `#A9784F`, ink `#3A2E25`), big rounded cards, ≥96px tap targets, large friendly type. Light hover/pop animation, calmed under `prefers-reduced-motion`.
> - Include the shared Web Audio sound module (`playTone` + `sfxTap`) and play a soft blip on card tap; add a 🔈/🔇 mute toggle.
> - No back button needed (this is home).
>
> **Constraints:** all in one file; comment the `GAMES` array so new games are one line to add. When done, create/append to `system_state.md` and `ToChat.md`, then report what you built and how to add a game (drop file + add one `GAMES` line + set `ready:true`).

---

## Prompt 1.5 — Fixes (run after the dashboard is accepted)

> Two small fixes in `C:\Claude\Cooking Game\`. Keep both files self-contained vanilla (no libraries/build/network/localStorage, double-click to run). Follow `GAMING_DASHBOARD_DESIGN.md` §2–§3.
>
> **Fix A — Home button in `CookingGame.html`.** The cooking game predates the dashboard and has no way back. Add a persistent **🏠 Home** button in the existing `.top-bar` (top-left, beside the existing Back arrow — they are different: Back moves between in-game screens, Home leaves the game). It must always return to the launcher via `window.location.href = "Dashboard.html"` (a relative link so it works from `file://`). Style it to match the existing top-bar buttons; tap target ≥96px; visible on every screen including Title/Pick Chef and Win. Don't change game logic, recipes, sound, stars, or animations.
>
> **Fix B — 3-column grid in `Dashboard.html`.** Change the game-card grid to show **at most 3 cards per row** (so the current 6 games render as two rows of three) instead of four across. Use a responsive grid that drops to 2 then 1 column on narrow screens. Keep the existing card styling, ≥96px targets, palette, and `ready`/"Coming soon!" behavior unchanged.
>
> When done, update `system_state.md` + `ToChat.md` and report both changes.

---

## Prompt 2 — DollDressUp.html

> Build `DollDressUp.html` in `C:\Claude\Cooking Game\` — one self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, double-click to run on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §4 and the shared rules in §3.
>
> Open-ended dress-up game, no win/fail/score. Flow: 🏠 Home (→Dashboard.html) → **Pick Doll** (Boy / Girl, two big cards) → **Dressing Room**.
> - The doll is a friendly, chunky, toy-like **inline SVG** figure (neutral skin tone, in plain base underclothes). Boy vs girl differ by hair + base outfit only; both are fully clotheable in every category.
> - Dressing room: doll on the left; on the right, **category tabs** (☀️Summer ❄️Winter 😴Sleep ⚽Sports 🛋️Relax) above a **wardrobe grid** of clothing buttons for the selected category. **Clicking an article puts it on the doll** (no dragging). Clothes are SVG overlays drawn over the doll at fixed positions.
> - **Slots & layering** per DESIGN §4: `head, top, bottom, feet, full`. Picking a new item in a slot replaces the old one. A `full` item (dress / pajama set / leotard) occupies top+bottom: selecting it clears top & bottom; selecting a top or bottom clears any `full`.
> - **🔄 Reset** button removes all clothing back to the base doll.
> - Data: `DOLLS` and `CLOTHES[sex][category]` arrays exactly as in DESIGN §4. Provide **3–5 articles per sex per category** (Summer/Winter/Sleep/Sports/Relax). Keep clothing SVGs simple, bright, flat shapes; wardrobe thumbnails can be emoji or tiny SVG.
> - Shared palette, ≥96px targets, mute toggle, reduced-motion support. A 🔈 blip on each clothing tap.
>
> **Constraints:** one file; comment the `CLOTHES` block so new outfits are easy to add. When done, set `dress` to `ready:true` in `Dashboard.html`'s `GAMES` array, update `system_state.md` + `ToChat.md`, and report what you built and how to add a clothing item.

---

## Prompt 3 — MathGame.html

> Build `MathGame.html` in `C:\Claude\Cooking Game\` — one self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, double-click to run on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §5 and shared rules §3.
>
> Addition & subtraction quiz for a 6-year-old. Flow: 🏠 Home → small mode toggle (➕ / ➖ / Mixed, default **Mixed**) → endless play.
> - Show one big problem (e.g. `3 + 2 = ?`) and **3 answer buttons**: one correct + two near-miss distractors (correct ±1 or ±2, kept in 0–10, no negatives, no duplicates).
> - **Problem rules:** numbers 0–10; addition `a+b ≤ 10`; subtraction `a−b` with `a ≥ b` (never negative). Generate fresh each round via a `makeProblem(mode)` returning `{text, answer, choices}` (shuffle choices).
> - Correct tap → ⭐ (stars only go up), confetti + happy `sfxWin`, then auto-load a new problem. Wrong tap → soft `sfxWrong` + gentle "Not quite — try again!" with a little button shake; **no penalty, problem stays.**
> - **Counting aid ON by default:** under the problem, render each number as that many dots/emoji (e.g. 3 = 🔵🔵🔵) so a pre-reader can reason it out. Make it a small toggle.
> - Shared palette, ≥96px targets, mute toggle, reduced-motion support, running ⭐ counter in a top bar.
>
> **Constraints:** one file. When done, set `math` to `ready:true` in `Dashboard.html`, update `system_state.md` + `ToChat.md`, report what you built.

---

## Prompt 4 — SpellingGame.html

> Build `SpellingGame.html` in `C:\Claude\Cooking Game\` — one self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, double-click to run on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §6 and shared rules §3.
>
> Picture-to-spelling quiz, Kindergarten/1st-grade words. Flow: 🏠 Home → endless play.
> - Show one **big picture (emoji)** and **3 spelling-choice buttons** (big, lowercase): the correct word + 2 plausible misspellings. Correct → ⭐ + confetti + `sfxWin` → next word. Wrong → soft `sfxWrong` + gentle nudge, word stays, try again; no penalty.
> - Use a hand-authored `WORDS` array exactly as in DESIGN §6 (`{word, icon, wrong:[...]}`), **~15–20 simple CVC / sight words** (start from the list in DESIGN and extend). Each round: random word, choices = correct + 2 of its `wrong` spellings, **shuffle** button order (Fisher-Yates). Don't repeat the same word twice in a row.
> - Misspellings should be *close* (swapped/missing/doubled letter) so the child actually reads.
> - Shared palette, ≥96px targets, mute toggle, reduced-motion support, running ⭐ counter.
>
> **Constraints:** one file; comment the `WORDS` block so new words are easy to add. When done, set `spelling` to `ready:true` in `Dashboard.html`, update `system_state.md` + `ToChat.md`, report what you built and how to add a word.

---

## Prompt 5 — TicTacToe.html

> Build `TicTacToe.html` in `C:\Claude\Cooking Game\` — one self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, double-click to run on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §7 and shared rules §3.
>
> Kid-themed Tic Tac Toe using **🐰 Bunny vs 🐻 Bear** tokens (not X/O). Flow: 🏠 Home → mode pick (👫 2 Players / 🤖 vs Computer) → board.
> - Big 3×3 grid of tap squares; current-turn indicator ("🐰 Bunny's turn"). Tapping an empty square places the current token with a pop + `sfxTap`. Disable taps once the game is over.
> - Win/draw via `WIN_LINES` (DESIGN §7). On win: highlight the winning three, `sfxWin` + confetti, "🐰 Bunny wins!". On full board no winner: friendly "It's a tie! 🤝" (never "you lose"). **Play Again** resets the board, keeping the chosen mode.
> - **vs Computer AI (easy/beatable):** (1) take a winning move if available; (2) else block the human's winning move; (3) else random open square. Optional 🙂Easy/😼Tricky toggle where Easy = pure random (default Easy).
> - Shared palette, ≥96px squares, mute toggle, reduced-motion support.
>
> **Constraints:** one file. When done, set `tictactoe` to `ready:true` in `Dashboard.html`, update `system_state.md` + `ToChat.md`, report what you built.

---

## Prompt 6 — GroceryRegister.html

> Build `GroceryRegister.html` in `C:\Claude\Cooking Game\` — one self-contained file (inline HTML/CSS/JS, vanilla only, no libraries/build/network/localStorage, double-click to run on Windows). Follow `GAMING_DASHBOARD_DESIGN.md` §8 and shared rules §3.
>
> Open-ended pretend grocery register, no fail state. One screen: 🏠 Home top-left; a **grocery shelf** of big emoji item buttons (price under each) on the left; a **🧾 receipt** panel with running **TOTAL** on the right.
> - Tapping an item plays a **scanner beep** and adds a receipt line; the total updates. Use `ITEMS` array as in DESIGN §8 — **~10–14 items, whole-dollar prices $1–$5** (apple, banana, milk, bread, eggs, cheese, cookies, carrot, chicken, grapes, juice, tomato…).
> - **💵 Pay** → happy cha-ching + confetti + "Thank you! 🎉". **🔄 New** → clears the receipt for the next customer.
> - Whole-dollar prices keep totals kid-friendly (light addition practice). **No change-making in v1** (note as a possible later add).
> - Shared palette, ≥96px buttons, mute toggle, reduced-motion support.
>
> **Constraints:** one file; comment the `ITEMS` block so new items are easy to add. When done, set `grocery` to `ready:true` in `Dashboard.html`, update `system_state.md` + `ToChat.md`, report what you built and how to add an item.

---

## Prompt 7 — DollDressUp.html art refresh (polish pass)

> Polish the artwork in `C:\Claude\Cooking Game\DollDressUp.html`. **This is an art-only change.** Do NOT touch the `CLOTHES` / `DOLLS` data, the slot/layering logic, `renderDollSvg()`'s wiring, navigation, sound, or any game behavior. Keep it one self-contained vanilla file (no libraries/build/network/localStorage, double-click to run). Follow `GAMING_DASHBOARD_DESIGN.md` §4 (the "Art style" guide).
>
> The dolls and clothes are drawn by functions only: `girlBaseSvg()`, `boyBaseSvg()`, and the garment helpers `topSvg, hoodieSvg, coatSvg, shortsSvg, pantsSvg, dressSvg, leotardSvg, overallsSvg, romperSvg, tracksuitSvg, hatSvg, beanieSvg, capSvg, sleepCapSvg, headbandSvg, shoesSvg, bootsSvg, slippersSvg, socksSvg`. Rebuild the *insides* of these functions to a higher art standard while **keeping every function's name and parameter signature exactly the same** (so the `CLOTHES` data and the passed-in colors keep working untouched). The canvas stays `viewBox="0 0 240 340"`.
>
> **Apply this style guide:**
> - **Proportions:** refined chibi, not blobby. Give the doll a visible neck, gently sloped shoulders, a torso that tapers slightly to the waist, and limbs with subtle taper and rounded (not balloon) hands/feet. **Pose the arms slightly out from the torso** so sleeves/tops layer over the upper arms cleanly.
> - **Face:** two eyes each with a tiny white highlight dot, soft eyebrows, a gentle smile, small subtle pink cheeks (smaller than the current ones), optional tiny nose. Hair with a clean outline and a couple of highlight strands. Keep girl (longer hair) and boy (shorter hair) clearly distinct, both fully clotheable.
> - **Linework:** one consistent ~2px stroke in a warm dark brown (e.g. `#5A4632`, not pure black) on every shape for a cohesive storybook look.
> - **Clothes — fitted & shaded:** shape each garment to wrap the body silhouette (sleeves follow the upper arms, pant legs follow the legs, tops have a collar and hem). Add depth with a vertical `<linearGradient>` (lighter at top, slightly darker at bottom) built from the passed-in base color, plus one soft fold/shadow shape. Define a reusable gradient and a soft drop-shadow `<filter>` ONCE in a shared `<defs>` and reference them, rather than redefining per garment.
> - **Stage/background:** replace the plain doll panel with a cozy, low-contrast bedroom/closet scene drawn in SVG/CSS behind the doll (e.g. soft rug, a clothing rail or a window) so the doll pops. Keep it subtle and strictly behind the doll. Keep/improve the soft ground-shadow ellipse under the doll.
> - Preserve the existing `aria-label`, `prefers-reduced-motion` handling, the wardrobe thumbnails (emoji), and all current behavior.
>
> Expect this to take **2–3 passes** — build it in order (base dolls → garments → background) so there's always something viewable, and report after each pass so Lisa can eyeball it. When done, update `system_state.md` + `ToChat.md` and report what changed (and confirm no data/logic was modified).

---

## Prompt 7b — DollDressUp.html girl-hair fix (small art pass)

> Small art-only fix in `C:\Claude\Cooking Game\DollDressUp.html` — `girlBaseSvg()` only. Touch nothing else (no data, logic, boy doll, garments, background). Keep it one self-contained vanilla file.
>
> **Problem:** the girl's hair currently reads as a beard. The head is a circle at `cx=120, cy=75, r=38` (face bottom ≈ y=113), but the back-hair shape (the `<path ... fill="#7A5236" ...>` around line 653) closes straight across the chin at about y=147–151, wrapping under the jaw. The side strands (the two `#6D4A30` strokes near lines 656–657) also hug the cheeks down to ~y=144.
>
> **Fix — make it long hair that frames the face, not a beard:**
> - Rework the back-hair path so its **lower edge does NOT cross under the chin.** Leave the chin/jaw area (roughly x=100–140 below y=110) as **bare skin** — the smile and chin should sit on open face, no hair beneath them.
> - Let the hair fall as **two locks down the OUTSIDE of the face/neck**, past the shoulders (down to roughly y=210–230), sitting *behind* the body/arms (drawn before the body, or clearly outside the face silhouette) so it frames the cheeks rather than covering them.
> - Keep the crown/top and the bangs across the forehead as they are (they look good). Keep the same brown hair colors and the `lineColor()` stroke.
> - Result: clearly a girl with long hair down the sides, full open face, no beard.
>
> Verify the dress-up layering still works (put on a dress → it clears top/bottom; Reset → bare doll) since this is base-doll art. When done, update `system_state.md` + `ToChat.md` and report what changed.

---

## Prompt 8 — Publish prep for GitHub + Vercel

> Prepare `C:\Claude\Cooking Game\` for static hosting on Vercel (deployed from the GitHub repo `dennisherreraETH/childrensgames`). The whole project is static vanilla HTML/CSS/JS with no build step — keep it that way (no frameworks, no bundler, no network, no `localStorage`). Do NOT change any game's behavior or art.
>
> **1. Add a root `index.html`.** Static hosts serve `index.html` at the site root (`/`), but the home page is currently `Dashboard.html`. Create a small `index.html` in the folder that sends visitors straight to the dashboard, and that ALSO still works when opened locally by double-click. Use all three so it's bulletproof: a `<meta http-equiv="refresh" content="0; url=Dashboard.html">`, a `<script>location.replace("Dashboard.html")</script>`, and a visible fallback link ("Tap here to play 🎮") pointing to `Dashboard.html`. Use a **relative** URL (`Dashboard.html`, no leading slash) so it works at any path. **Do NOT rename `Dashboard.html`** — every game's 🏠 Home button links to it.
>
> **2. Case-sensitivity guard.** Vercel/GitHub run on case-sensitive Linux. A prior audit found all current references already match the on-disk filenames exactly (`Dashboard.html`, the six game files, `Bear_Character.jpg`, `Bunny_Character.jpg`, `Animation/bear_*.png`, `Animation/bunny_*.png`). Re-verify this still holds: every `href`, every `GAMES` registry `file:` value, and every image path string must match the real filename's capitalization character-for-character. Report any mismatch; if one exists, fix the reference to match the actual file on disk.
>
> **3. Add a `README.md`** at the folder root for the public repo: one-paragraph description (a small offline-friendly collection of games for young kids), the list of games, how to run locally (open `index.html` or `Dashboard.html` in a browser — no install/build needed), how to deploy (Vercel: import the repo, framework preset "Other", no build command, output = repo root), and the tech note (vanilla HTML/CSS/JS, no dependencies, no network, no storage).
>
> **4. Do NOT add** a build config, `package.json`, `node_modules`, or `vercel.json` unless required — a static repo deploys on Vercel with zero config. Don't commit or push (the human handles git).
>
> When done, update `system_state.md` + `ToChat.md` and report exactly which files you added/changed and the result of the case-sensitivity re-check.
