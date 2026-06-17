# Gaming Dashboard — Design & Architecture

**For:** Codex (implementation) · **Audience:** a 6-year-old · **Architect handoff doc**
**Goal:** A `Dashboard.html` home screen that launches a growing library of self-contained kid games. Each game is its own file that runs by double-clicking on Windows. No server, no npm, no build, no external libraries, no network requests, no `localStorage`.

This doc is the master plan. Each game gets its own copy-paste Codex prompt in `CODEX_PROMPTS.md`. Build the Dashboard first, then one game at a time.

---

## 1. The big picture

```
C:\Claude\Cooking Game\
├── Dashboard.html          ← NEW: the home screen / launcher (build first)
├── CookingGame.html        ← existing game (done)
├── DollDressUp.html        ← NEW (Game 2)
├── MathGame.html           ← NEW (Game 3)
├── SpellingGame.html       ← NEW (Game 4)
├── TicTacToe.html          ← NEW (Game 5)
├── GroceryRegister.html    ← NEW (Game 6)
├── Animation/              ← existing cooking-game chef art
├── *.jpg / *.png / *.json  ← existing cooking assets
├── DESIGN.md               ← cooking game spec
├── GAMING_DASHBOARD_DESIGN.md ← this file
├── CODEX_PROMPTS.md         ← copy-paste prompts for Codex
├── system_state.md / ToChat.md ← handoff files (Codex updates)
```

**Architecture decision (locked): launcher + separate files.** The dashboard is a thin shell. Each game is a fully independent HTML file. Benefits: matches the existing CookingGame pattern, a bug in one game can never break another, and Codex can build/review one game at a time. The folder is named "Cooking Game" today but is now the **Games library** — we don't rename it, the dashboard just sits on top.

---

## 2. The Dashboard (home screen)

A single screen: friendly title ("🎮 Game Time!") and a responsive grid of big game cards, **at most 3 cards per row** (drops to 2 then 1 column on narrow screens). Each card = large icon + game name. Tapping a card opens that game (`<a href="GameFile.html">`). Every game has a **🏠 Home** button (top-left) that links back to `Dashboard.html` via a relative link. Because everything runs from `file://`, plain relative links work — no router, no JS navigation needed for launching.

> **Retrofit note:** `CookingGame.html` was built before the dashboard existed and originally had no Home button. Every game — including the cooking game — must have the 🏠 Home button. This is non-negotiable for the launcher to feel like one product (see Prompt 1.5 in `CODEX_PROMPTS.md`).

**Game registry — the one thing you edit to add a game:**

```js
const GAMES = [
  { id: "cooking",  name: "Cooking",        icon: "🍳", file: "CookingGame.html",     ready: true  },
  { id: "dress",    name: "Doll Dress Up",  icon: "👗", file: "DollDressUp.html",      ready: true  },
  { id: "math",     name: "Math Fun",       icon: "➕", file: "MathGame.html",         ready: true  },
  { id: "spelling", name: "Spelling",       icon: "🔤", file: "SpellingGame.html",     ready: true  },
  { id: "tictactoe",name: "Tic Tac Toe",    icon: "⭕", file: "TicTacToe.html",        ready: true  },
  { id: "grocery",  name: "Grocery Store",  icon: "🛒", file: "GroceryRegister.html",  ready: true  },
];
```

The dashboard renders one card per registry entry. A card with `ready: false` shows a gentle "Coming soon!" badge and doesn't link anywhere (so the dashboard can ship before every game exists). **To add a future game: drop its HTML file in the folder and add one line here.** That is the entire extensibility story — the same philosophy as the cooking game's `RECIPES` block.

Card icon can be an emoji (default) or, later, a small image — same pattern as the cooking game's ingredient `icon` field.

---

## 3. Shared design system (every game obeys this)

These are the house rules so six different games feel like one product. Each game still inlines its own CSS/JS (self-contained), but uses these same values.

**Palette** (warm bakery theme, derived from `Character_JSON.json` — cream / blue / wood):

| Token | Hex | Use |
|---|---|---|
| Cream background | `#FFF6E6` | page background |
| Warm card | `#FFFFFF` / `#FFF0D4` | cards, panels |
| Sky blue (primary) | `#4FA3E0` | buttons, trim, highlights |
| Deep blue | `#2C6FAE` | button text/borders, headings |
| Wood brown | `#A9784F` | nameplates, frames, register |
| Leaf green | `#6FB36F` | "correct"/go accents |
| Sunshine | `#FFC94D` | stars, celebration |
| Soft coral | `#F08C7A` | gentle "try again" (never harsh red) |
| Ink text | `#3A2E25` | body text |

**Interaction rules (age 6):**
- Tap targets **≥ 96×96 px**, generously spaced. Assume touchscreen.
- Big rounded shapes, ~24px+ font, high contrast (ink on cream).
- **Read-optional:** pair every choice with a picture. A non-reader can play by matching pictures.
- **One decision per screen.** No clutter.
- **Always escapable:** 🏠 Home button (→ `Dashboard.html`) top-left of every game, plus in-game Back where there are sub-screens. No dead ends.
- **Punishment-free:** no timers, no losing, no score going down. Wrong answers get a soft, friendly nudge and let the child try again.
- **Color is never the only signal:** correct/wrong also use icon + sound + words (colorblind-safe).
- **Forgiving input:** debounce taps so a double-tap doesn't double-fire.
- `@media (prefers-reduced-motion: reduce)` disables continuous/celebration motion.

**Sound (reuse the cooking-game pattern):** Web Audio API only, no files. One `playTone(freq, ms, type)` helper + named wrappers (`sfxTap`, `sfxRight`/`sfxWin` bright rising tones, `sfxWrong` a soft low tone — never a buzzer). Init `AudioContext` on first user gesture. Include a 🔈/🔇 mute toggle. Copy this module into each game.

**Reward feel:** stars (⭐) that only ever go up, with a little +1 pop and light confetti on success — the same warm, celebratory feedback the cooking game uses. Stars reset on reload (no storage needed for this age).

**Tech constraints (all games):** one self-contained `.html` file; inline `<style>` + `<script>`; vanilla JS only; no libraries, no bundler, no modules, no external requests, no `localStorage`. Must run by double-clicking on Windows from `file://`.

---

## 4. Game 2 — Doll Dress Up

**Type:** open-ended creative play. No win/fail, no score. Just dress the doll.

**Flow:** `[Home] → Pick Doll (Boy / Girl, two big cards) → Dressing Room`.

**Dressing Room layout:**
```
┌──────────────────────────────────────────────┐
│ [🏠 Home]   [← Pick Doll]            🔈 mute  │
├───────────────┬──────────────────────────────┤
│               │  Category tabs:               │
│   the DOLL    │  ☀️Summer ❄️Winter 😴Sleep    │
│   (SVG, big)  │  ⚽Sports 🛋️Relax             │
│  clothes      │  ┌───┬───┬───┬───┐            │
│  layer onto   │  │👕 │🩳 │👟 │🧢 │  wardrobe   │
│  it here      │  ├───┼───┼───┼───┤  (clothes  │
│               │  │...│...│...│...│   for the  │
│               │  └───┴───┴───┴───┘   chosen   │
│               │                      category)│
│  [🔄 Reset]   │                               │
└───────────────┴──────────────────────────────┘
```

**How dressing works:** the doll is an SVG figure. Clothing items are SVG overlays drawn over the doll body at fixed positions. The child **clicks an article in the wardrobe and it appears on the doll** — no dragging. Clicking another item in the **same slot replaces** the current one. **Reset** clears all clothing back to the base doll (in plain underclothes).

**Slots (z-ordered):** `head` (hat/beanie), `top` (shirt/jacket), `bottom` (shorts/pants/skirt), `feet` (shoes/boots/slippers), `full` (a one-piece like a dress or pajama set that occupies top+bottom — selecting a `full` item clears top & bottom, and vice-versa).

**Data model:**
```js
const DOLLS = {
  girl: { name: "Girl Doll", baseSvg: /* body + underclothes */ },
  boy:  { name: "Boy Doll",  baseSvg: /* body + underclothes */ },
};

// clothes[sex][category] = array of items
const CLOTHES = {
  girl: {
    summer: [ { id:"sundress", slot:"full", label:"Sundress", svg:"...", thumb:"👗" }, ... ],
    winter: [ ... ], sleep: [ ... ], sports: [ ... ], relax: [ ... ],
  },
  boy: { summer:[...], winter:[...], sleep:[...], sports:[...], relax:[...] },
};

const state = { doll:null, category:"summer", worn:{ head:null, top:null, bottom:null, feet:null, full:null } };
```

**Content target:** each sex × each of the 5 categories gets **3–5 articles** so there's real variety. Categories: **Summer** (t-shirt/shorts/sundress/sandals/sunhat), **Winter** (coat/sweater/snow pants/boots/beanie/scarf), **Sleep** (pajama top+bottom or nightgown, slippers), **Sports** (jersey/shorts/sneakers/cap, or leotard), **Relax** (hoodie/sweatpants/joggers/comfy socks). Keep clothing SVGs simple, bright, flat shapes — clean over photoreal.

**Art note:** Codex draws the doll and clothes as inline SVG (simple shapes/paths). Wardrobe thumbnails can be emoji or a tiny SVG of the item. No image files needed. Keep doll proportions friendly/chunky (toy-like), neutral skin tone, and make the two dolls clearly boy/girl by hair + base outfit only — both fully clotheable in every category.

**Art style guide (refined after v1 — the standard for the dolls):** the art lives entirely in drawing functions (`girlBaseSvg`, `boyBaseSvg`, and garment helpers like `topSvg`, `pantsSvg`, `dressSvg` on a `240×340` canvas), so the look is upgraded by improving those functions — no data/logic change. Target a **refined chibi** look, not blobby: visible neck, sloped shoulders, torso tapering to the waist, tapered limbs with rounded (not balloon) hands/feet, **arms posed slightly out** so tops layer cleanly. **Face:** eyes with tiny white highlight dots, soft eyebrows, gentle smile, small subtle pink cheeks; hair with a clean outline and highlight strands (girl longer / boy shorter, both clotheable). **Linework:** one consistent ~2px warm dark-brown stroke (`#5A4632`, not pure black) on every shape. **Clothes are fitted & shaded:** garments wrap the body silhouette (sleeves follow arms, legs follow legs, collars + hems), with depth from a vertical `<linearGradient>` derived from the passed-in base color plus one soft fold/shadow; define the gradient and a soft drop-shadow `<filter>` once in a shared `<defs>`. **Stage:** a cozy, low-contrast bedroom/closet scene (rug, clothing rail or window) drawn behind the doll, with a soft ground-shadow ellipse. (See Prompt 7 in `CODEX_PROMPTS.md`.)

---

## 5. Game 3 — Math Fun (addition & subtraction)

**Type:** quiz with celebration. Endless stream of problems.

**Flow:** `[Home] → (optional tiny mode toggle: ➕ / ➖ / Mixed) → Play`.

**Play screen:** one big problem (e.g. `3 + 2 = ?`), **3 answer choice buttons** (one correct, two near-miss distractors). Tap correct → ⭐ + celebration (confetti + `sfxWin`) → **new problem auto-loads**. Tap wrong → soft `sfxWrong` + gentle "Not quite — try again!" (button gives a little shake), **no penalty, problem stays** so they pick again.

**Problem generation rules (age 6):**
- Numbers **0–10**. Addition: `a + b` with `a + b ≤ 10`. Subtraction: `a − b` with `a ≥ b` (never negative).
- Default mode = **Mixed** (randomly + or −). Parent can switch to add-only or subtract-only with the toggle.
- Distractors: correct answer ± 1 or ± 2 (kept within 0–10, no duplicates, never negative). Plausible-but-wrong, not random.
- **Counting aid (recommended for this age):** under the problem, show the quantities as rows of dots/emoji (e.g. `3` = 🔵🔵🔵) so a child who can't yet read numerals can still reason it out. Make this a small toggle if you want it optional; on by default.

**Data model:** none stored — generate each problem on the fly:
```js
function makeProblem(mode){ /* returns {text:"3 + 2", answer:5, choices:[5,4,6]} */ }
const state = { mode:"mixed", problem:null, stars:0 };
```

---

## 6. Game 4 — Spelling (Kindergarten / 1st grade)

**Type:** quiz with celebration. Picture → pick the correctly spelled word.

**Flow:** `[Home] → Play`.

**Play screen:** one **big picture** (emoji, e.g. 🐱), and **3 spelling-choice buttons** (one correct, two plausible misspellings). Tap correct → ⭐ + celebration → next word. Tap wrong → soft nudge, the word stays, try again. No penalty.

**Word list (hand-authored for quality — emoji picture + curated wrong spellings):**
```js
const WORDS = [
  { word:"cat",  icon:"🐱", wrong:["kat","cet"] },
  { word:"dog",  icon:"🐶", wrong:["dgo","dawg"] },
  { word:"sun",  icon:"☀️", wrong:["son","snu"] },
  { word:"hat",  icon:"🎩", wrong:["het","hatt"] },
  { word:"bed",  icon:"🛏️", wrong:["bad","bde"] },
  { word:"cup",  icon:"🥤", wrong:["kup","cap"] },
  { word:"fish", icon:"🐟", wrong:["fsh","fich"] },
  { word:"star", icon:"⭐", wrong:["stor","sar"] },
  { word:"tree", icon:"🌳", wrong:["tre","treee"] },
  { word:"ball", icon:"⚽", wrong:["bal","boll"] },
  { word:"frog", icon:"🐸", wrong:["frg","frug"] },
  { word:"book", icon:"📖", wrong:["buk","bok"] },
  // aim for ~15–20 simple CVC / sight words
];
```
- Each round: pick a random word, build 3 choices = correct word + 2 of its `wrong` spellings, **shuffle** the button order (reuse a Fisher-Yates shuffle like the cooking game's tray).
- Show the word in big, clear, lowercase letters. Distractors should be *close* misspellings (swapped/missing/doubled letter), so the child actually reads, not just spots the weird one.
- Don't repeat the same word twice in a row.

---

## 7. Game 5 — Tic Tac Toe

**Type:** classic game, kid-themed. **Bunny 🐰 vs Bear 🐻** tokens (ties into the cooking game's characters) instead of dry X/O.

**Flow:** `[Home] → (mode pick: 👫 2 Players  or  🤖 vs Computer) → Board`.

**Board screen:** big 3×3 grid of tap squares. Current-turn indicator ("🐰 Bunny's turn"). Tapping an empty square places the current player's token with a little pop + `sfxTap`. On win: highlight the winning 3, `sfxWin` + confetti, "🐰 Bunny wins!". On full board with no winner: friendly "It's a tie! 🤝" (never call it a loss). **Play Again** button resets the board (keep the chosen mode).

**vs Computer AI:** keep it **easy and beatable** for a 6-year-old. Simple heuristic: (1) if the computer can win this move, take it; (2) else if the human could win next move, block it; (3) else pick a random open square. That's friendly but not frustrating. (Optional: a "🙂 Easy / 😼 Tricky" toggle where Easy = pure random; default Easy.)

**Data model:**
```js
const state = { mode:"cpu", board:[null,null,null, null,null,null, null,null,null],
                turn:"bunny", winner:null };
const WIN_LINES = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]];
```
Standard win/draw check against `WIN_LINES`. Disable taps once `winner` is set.

---

## 8. Game 6 — Grocery Store (pretend register)

**Type:** open-ended pretend play with a light number/counting bonus. No fail state.

**Flow:** `[Home] → Store/Register (one screen)`.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ [🏠 Home]                            🔈 mute  │
├───────────────────────────┬──────────────────┤
│  Grocery shelf (tap to     │   🧾 RECEIPT      │
│  ring up):                 │   🍎 Apple  $1     │
│  🍎 🍌 🥛 🍞 🥚 🧀         │   🥛 Milk   $2     │
│  🍪 🥕 🍗 🍇 🧃 🍅         │   ...             │
│  (big buttons, price       │  ─────────────    │
│   under each)              │  TOTAL:  $6       │
│                            │  [💵 Pay]  [🔄 New]│
└───────────────────────────┴──────────────────┘
```

**How it works:** each grocery item is a big emoji button with a whole-dollar price under it. Tapping an item plays a **scanner beep** (`sfxTap`/a short blip) and adds a line to the receipt; the **running total** updates. **Pay** plays a happy cha-ching, shows "Thank you! 🎉" with confetti, then **New** clears the receipt for the next customer. Optional gentle extra: a couple of taps to "open the drawer" sound — keep it simple.

**Data model:**
```js
const ITEMS = [
  { id:"apple", name:"Apple", icon:"🍎", price:1 },
  { id:"milk",  name:"Milk",  icon:"🥛", price:2 },
  { id:"bread", name:"Bread", icon:"🍞", price:2 },
  // ~10–14 items, whole-dollar prices $1–$5 so the total is kid-friendly
];
const state = { cart:[], total:0 };
```
Keep prices whole dollars so totals stay simple; the receipt doubles as light addition practice. **Change-making is intentionally out of scope** for v1 (too advanced for 6) — note it as a possible later add.

---

## 9. Build order for Codex

Each step is independently runnable and gets its own prompt in `CODEX_PROMPTS.md`:

1. **Dashboard.html** — the launcher + game registry (mark unbuilt games `ready:false` so it ships immediately). Verify it opens and the Cooking card launches `CookingGame.html`.
2. **DollDressUp.html** — most art-heavy (SVG dolls/clothes); do it deliberately.
3. **MathGame.html** — small, logic-driven; good quick win.
4. **SpellingGame.html** — mirrors the math game's structure (picture + 3 choices).
5. **TicTacToe.html** — self-contained logic + easy AI.
6. **GroceryRegister.html** — single-screen pretend play.

After each game: flip its `ready` flag to `true` in the Dashboard registry, and (per the handoff system) update `system_state.md` and `ToChat.md`.

---

## 10. Verification checklist (every game)

- Opens by double-click from `file://` with **no console errors**.
- **No** external requests, `localStorage`, libraries, or build step (static scan + open in browser).
- 🏠 Home button returns to `Dashboard.html`.
- Tap targets ≥96px; readable; picture+word on choices.
- Wrong/again paths are gentle and never remove stars or block play; no timers, no losing.
- Sound plays after a click and the 🔈/🔇 mute works.
- `prefers-reduced-motion` calms the animation.
- Dashboard registry entry is added and `ready:true`.

---

## 11. Deployment (GitHub + Vercel)

The project is **static vanilla HTML/CSS/JS with no build step**, which makes it ideal for static hosting. Repo: `github.com/dennisherreraETH/childrensgames`. Hosting: Vercel, deployed from that repo with **zero config** (framework preset "Other", no build command, output = repo root). No `package.json`/`vercel.json`/`node_modules` needed.

Two things matter for hosting that don't matter locally:

- **Case-sensitivity.** Vercel/GitHub run on case-sensitive Linux; Windows is not. Every `href`, every `GAMES` registry `file:` value, and every image path must match the real filename's capitalization exactly. (Audited clean as of publish prep: `Dashboard.html`, the six game files, `Bear_Character.jpg`, `Bunny_Character.jpg`, `Animation/bear_*.png`, `Animation/bunny_*.png`.)
- **Root page.** Static hosts serve `index.html` at `/`. The canonical home is `Dashboard.html` (every game's 🏠 Home links to it), so a small `index.html` at the root redirects to `Dashboard.html` (meta-refresh + `location.replace` + a visible fallback link, all relative URLs). `Dashboard.html` is **not** renamed.

See Prompt 8 in `CODEX_PROMPTS.md`. Git is run by the human (the repo's standing rule is no auto-push).

---

## 12. Easter egg — hidden Asteroids (for grown-ups)

A secret arcade game tucked behind the dashboard so an adult can sneak in a quick play. **Deliberately exempt from the kid-usability rules** — it's keyboard-driven and may have lives, a fail state, and a score.

- **Trigger:** a tiny, low-opacity star/asteroid glyph (✦, ~14–18px, `opacity ~0.25`) absolutely positioned in a corner of the dashboard `.page`. It brightens slightly on hover and links to `Asteroids.html`. It is **not** in the `GAMES` registry and renders **no** card — discoverable only if you know it's there.
- **Game (`Asteroids.html`):** classic Asteroids on `<canvas>` — vector ship (← → rotate, ↑ thrust with momentum, Space fire), asteroids that split large→medium→small and wrap the edges, 3 lives with brief respawn invulnerability, endless escalating waves, score + in-memory high score (no `localStorage`), Game Over → restart. Sleek **dark space theme** (near-black, white/neon vectors) — intentionally unlike the kids' cream palette, to signal "different mode." `requestAnimationFrame` + delta-time loop. Small 🏠 Home link back to `Dashboard.html`. Self-contained vanilla, no libraries/network/storage.

See Prompt 9 in `CODEX_PROMPTS.md`.
