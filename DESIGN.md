# Cooking Game — Design & Architecture

**For:** Codex (implementation) · **Audience of the game:** a 6-year-old
**Goal:** One self-contained `CookingGame.html` that runs by double-clicking it on Windows. No server, no npm, no build, no external libraries.

---

## 1. Folder / file structure

```
C:\Claude\Cooking Game\
├── CookingGame.html        ← the whole game (HTML + CSS + JS in one file)
├── Bunny_Character.jpg     ← existing asset
├── Bear_Character.jpg      ← existing asset
├── Character_JSON.json     ← existing art/style reference (NOT loaded at runtime)
└── DESIGN.md               ← this file
```

One file is the target. Recipes live in a JS object at the top of the script so they're trivial to edit. Sounds are **generated in code** (Web Audio API beeps) — no audio files to manage, nothing to break offline. Ingredient icons are **emoji** (🥚🍞🧀🥓) — they render everywhere, need zero downloads, and are big and colorful for a kid.

> Why no `assets/` folder: the only required art (the two characters) already exists, and emoji covers every ingredient. Adding asset files would create more things that can go missing. If you later want painted icons, the recipe model (section 5) already has an `icon` field that can hold an image path instead of an emoji.

---

## 2. Screen flow

```
[Title]  →  [Pick Chef]  →  [Pick Meal]  →  [Pick Recipe]  →  [Cook]  →  [Win!]
                              ↑___________________________________________|
                                        "Cook again" returns here
```

1. **Title** — game name, big "Play" button. (Optional; can auto-skip to Pick Chef.)
2. **Pick Chef** — Bunny photo or Bear photo, two huge cards.
3. **Pick Meal** — three big buttons: ☀️ Breakfast, 🌤️ Lunch, 🌙 Dinner.
4. **Pick Recipe** — cards for that meal, easy ones first. Locked-looking cards are fine but everything stays playable (no hard gating for a 6-year-old).
5. **Cook** — the active screen (layout in section 3).
6. **Win** — stars earned, confetti, "Cook again" + "Pick new meal" buttons.

Every screen has a clear **Back** arrow (top-left). No dead ends.

---

## 3. Cook screen layout

```
┌──────────────────────────────────────────────┐
│ [←Back]        ⭐⭐⭐ Stars: 4      [Chef pic] │   top bar
├──────────────────────────────────────────────┤
│            Let's make:  GRILLED CHEESE         │   recipe title
│                                                │
│        Next:  🧀  "Add the cheese!"            │   current step prompt
│                                                │
│   ┌─────┐  (the plate / pan, fills as you go)  │
│   │ 🍞  │   ← ingredients you've added stack    │
│   └─────┘     here with a little pop animation  │
│                                                │
├──────────────────────────────────────────────┤
│   🥚    🍞    🧀    🥓    🍅    🥛            │   ingredient tray
│  eggs  bread cheese bacon tomato milk          │   (big tap targets)
└──────────────────────────────────────────────┘
```

- Ingredient tray is a row of large square buttons (icon on top, word under it).
- The "Next" prompt always shows the current target ingredient as a hint, so the child can match by picture even if they can't read.
- The plate area visibly fills up — concrete progress a kid can see.

---

## 4. Game state model

A single plain object. No framework, no router — one `render()` function reads state and redraws the screen.

```js
const state = {
  screen: "pickChef",        // title | pickChef | pickMeal | pickRecipe | cook | win
  chef: null,                // "bunny" | "bear"
  meal: null,                // "breakfast" | "lunch" | "dinner"
  recipeId: null,            // e.g. "grilled_cheese"
  stepIndex: 0,              // how many correct ingredients added so far
  stars: 0,                  // total stars this session
  addedIngredients: []       // icons placed on the plate, for the animation
};
```

State changes go through tiny helper functions (`goTo(screen)`, `pickChef(id)`, `addIngredient(id)`), each of which updates `state` then calls `render()`. That's the entire engine. Easy for Codex to implement and for you to reason about.

---

## 5. Recipe data model

One object, grouped by meal. **This is the part you edit to add recipes.**

```js
const RECIPES = {
  breakfast: [
    {
      id: "eggs_and_toast",
      name: "Eggs & Toast",
      difficulty: 1,
      reward: 1,                               // stars for finishing
      steps: ["egg", "bread"],                 // correct order of ingredients
      tray: ["egg", "bread", "cheese", "milk"] // buttons shown (includes distractors)
    }
  ],
  lunch: [
    {
      id: "grilled_cheese",
      name: "Grilled Cheese",
      difficulty: 2,
      reward: 2,
      steps: ["bread", "cheese", "pan"],
      tray: ["bread", "cheese", "pan", "egg", "tomato"]
    }
  ],
  dinner: [ /* ... */ ]
};

// Ingredient lookup: id → display info (one place, reused everywhere)
const INGREDIENTS = {
  egg:    { word: "egg",    icon: "🥚" },
  bread:  { word: "bread",  icon: "🍞" },
  cheese: { word: "cheese", icon: "🧀" },
  pan:    { word: "pan",    icon: "🍳" },
  milk:   { word: "milk",   icon: "🥛" },
  tomato: { word: "tomato", icon: "🍅" },
  bacon:  { word: "bacon",  icon: "🥓" }
  // add new ingredients here once; recipes reference them by id
};
```

**Rules of the model**
- `steps` defines the correct sequence. Cooking checks the tapped ingredient against `steps[state.stepIndex]`.
- `tray` is what's shown. It always contains every `steps` ingredient plus 1–2 distractors. More distractors = harder.
- Difficulty scales naturally by recipe: difficulty 1 = 2 steps no distractors, difficulty 2 = 3 steps + 1 distractor, difficulty 3 = 4 steps + 2 distractors.
- To add a recipe: add one object to a meal array. To add an ingredient: add one line to `INGREDIENTS`. Nothing else changes.

**Order-independent option (recommended for easiest recipes):** for difficulty-1 recipes you may treat `steps` as a set rather than a sequence (any correct ingredient that hasn't been added yet counts). Implement a per-recipe flag `ordered: false` if you want this. Default `ordered: true`.

---

## 6. Character selection model

```js
const CHEFS = {
  bunny: { name: "Bunny Chef", img: "Bunny_Character.jpg", cheer: "Hop hop, yum!" },
  bear:  { name: "Bear Chef",  img: "Bear_Character.jpg",  cheer: "Beary tasty!" }
};
```

Picking a chef sets `state.chef` and shows that photo in the cook-screen top bar and on the Win screen. Purely cosmetic — both chefs play identically (no reason to split gameplay for this age). The chef's `cheer` line is used in positive feedback.

---

## 7. Star / points system

- Each completed recipe awards its `reward` stars (1–3 by difficulty).
- Stars **only ever go up.** Wrong taps never remove stars — punishment-free by design.
- `state.stars` shows in the top bar the whole time and animates (+1 pop) on each award.
- Win screen shows stars earned *this dish* plus the running total.
- No score-saving needed for a prototype (no localStorage — not supported here and unnecessary). Stars reset on page reload; that's fine for a 6-year-old session.
- Optional sticker/medal at 5 and 10 stars as a gentle long-term goal. Easy to add later.

---

## 8. Sound effect plan

All sounds are **synthesized with the Web Audio API** — short tones, no files, works offline forever.

| Event | Sound | Feel |
|---|---|---|
| Ingredient tapped | soft single "blip" | responsive |
| Correct ingredient | bright rising two-note "ding!" | yes! |
| Wrong ingredient | gentle low "boop" (NOT a buzzer) | oops, try again |
| Recipe complete | happy little 3-note jingle | celebration |

Implementation: one tiny `playTone(freq, durationMs, type)` helper plus four named wrappers (`sfxTap`, `sfxRight`, `sfxWrong`, `sfxWin`). First user click initializes the `AudioContext` (browsers require a gesture). Include a 🔈/🔇 mute toggle in the top bar for the grown-up.

> The "wrong" sound is deliberately soft and low, never a harsh buzz — paired with the friendly text correction in section 11.

---

## 9. Accessibility / kid-usability (age 6)

- **Tap targets:** minimum ~96×96 px, generously spaced. Little fingers, possibly a touchscreen.
- **Read-optional:** every choice pairs a big picture with a short word. A non-reader can play entirely by matching pictures. The "Next" hint always shows the target icon.
- **One decision at a time:** each screen asks exactly one thing.
- **Always escapable:** a big Back arrow on every screen; no way to get stuck.
- **High contrast, big type:** dark text on light warm backgrounds, ~24px+ font, rounded friendly shapes.
- **Color is never the only signal:** correct/wrong also use icon + sound + words, so it works for colorblind kids.
- **Forgiving input:** debounce taps slightly so a double-tap doesn't skip a step. No timers, no fail state, no losing.
- **No reading-required failure:** wrong taps explain with a picture ("Let's find the 🧀 cheese!").

---

## 10. Implementation plan for Codex

Build in this order so there's always something runnable:

1. **Skeleton** — single HTML file, `<style>` and `<script>` blocks, the `state` object, an empty `render()` that swaps a `#screen` div by `state.screen`.
2. **Data** — paste `INGREDIENTS`, `CHEFS`, `RECIPES` (start with 2 breakfast, 2 lunch, 2 dinner: one easy + one harder each).
3. **Screens** — write a render function per screen (`renderPickChef`, `renderPickMeal`, `renderPickRecipe`, `renderCook`, `renderWin`). Wire Back + navigation.
4. **Cook logic** — tap handler compares to `steps[stepIndex]`; right → advance + `sfxRight` + plate animation; wrong → `sfxWrong` + friendly hint, no penalty. Last correct step → award stars, go to Win.
5. **Sound** — add the `playTone` helper and four wrappers; mute toggle.
6. **Polish** — pop/confetti animations (CSS only), star counter animation, cheer lines, warm bakery styling matching `Character_JSON.json` (cream backgrounds, blue trim, wood tones).
7. **Self-test** — open the file, play one recipe of each meal, confirm sounds, wrong-tap, and stars work. No console errors.

Keep it all vanilla. No bundler, no modules — just one file you can open with a double-click.

---

## 11. Feedback copy (friendly correction)

- **Right:** "Yum! ⭐", "Perfect!", chef cheer line.
- **Wrong:** never "No"/"Wrong." Use redirection: "Oops! Let's find the 🧀 cheese next!" or "Not yet — try the 🍞 bread!"
- **Win:** "You made Grilled Cheese! 🎉 You earned ⭐⭐."

---

## Codex prompt (copy-paste to build v1)

> Build `CookingGame.html` in `C:\Claude\Cooking Game\` — one self-contained file (HTML + CSS + JS inline), vanilla only, no libraries, no build tools, runs by double-clicking on Windows. Follow `DESIGN.md` in that folder.
>
> **Implement:**
> - A `state` object exactly as in DESIGN section 4, and a single `render()` that shows one screen at a time based on `state.screen`.
> - Screens: Pick Chef → Pick Meal → Pick Recipe → Cook → Win, each with a big Back arrow. Use `Bunny_Character.jpg` and `Bear_Character.jpg` (already in the folder) on the chef-pick and top bar.
> - Data objects `INGREDIENTS`, `CHEFS`, `RECIPES` as in DESIGN section 5–6. Seed **6 recipes**: 2 each for breakfast/lunch/dinner, one easy (2 steps, no distractor) and one harder (3 steps + 1 distractor) per meal. Use the examples (eggs & toast, grilled cheese) plus simple ones you choose.
> - Cook logic: tapping an ingredient checks it against `steps[state.stepIndex]`. Correct → play "ding", animate it onto the plate, advance. Wrong → play a soft low "boop" and show a friendly picture hint, NEVER remove stars or block play. Completing the last step awards `reward` stars and goes to Win with confetti.
> - Sound via Web Audio API only (no audio files): `playTone()` + `sfxTap/sfxRight/sfxWrong/sfxWin`, initialized on first click, plus a 🔈/🔇 mute toggle.
> - Kid-usability per DESIGN section 9: tap targets ≥96px, picture+word on every button, big warm bakery styling (cream/blue/wood per `Character_JSON.json`), no timers, no fail state.
>
> **Constraints:** all in one file; no localStorage; no external requests; comment the RECIPES block so new recipes are easy to add. When done, update `system_state.md` and `ToChat.md`, then report what you built and how to add a recipe.
```
```
