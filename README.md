# CAN CRUSH — Indoor Arcade

A 3D can-knockdown game for desktop and mobile browsers. Three.js renders the scene and cannon-es simulates every can and ball as a rigid body. All art, textures and sounds are generated in code, so there are no image or audio files.

## Run it

**Option A: open the file.** Double-click `index.html`, or drag it into Chrome, Edge, Firefox or Safari. The first launch needs internet access to load Three.js, cannon-es and the fonts from jsDelivr and Google Fonts.

**Option B: local server** (recommended for phones on your Wi-Fi, or if your browser blocks local files):

```bash
powershell -ExecutionPolicy Bypass -File serve.ps1
```

Then open http://localhost:8765. Any static server works, for example `npx serve .` or `python -m http.server 8765`.

There is nothing to install and no build step.

## Controls

| Action | Desktop | Touch |
| --- | --- | --- |
| Aim | Press and drag down/back. Further down = higher arc, sideways = left/right | Same, with your finger |
| Throw | Release | Lift finger |
| Cancel a throw | Drag back to where you started, then release | Same |
| Pause | Esc or P, or the pause button | Pause button |
| Restart level | R | Pause → Restart level |

## Features

- **Physics:** each can is its own cylinder rigid body with real mass, friction, restitution and damping. Collisions alone decide what falls. The game never fakes can movement.
- **Stable stacks:** cans start asleep and wake only when hit or when the can beneath them moves. Stacks never collapse on their own before the first throw, and they settle without jitter.
- **Can look:** tall, slim soda cans with tapered silver tops and bases, glossy vertical sheen bands, and condensation droplets (drawn into the colour and bump maps). The default "Soda Pop" skin uses blue/green split, orange, yellow, pink-red, magenta and lime.
- **Ball:** one green tennis ball for every throw (classic mode, no shop).
- **Energy Booster Can ⚡ (levels 6–15):** an electric-blue energy-drink can with silver bands, icy cyan pinstripes and a white lightning bolt that glows. It always faces the player so the bolt is visible.
  - **Activation:** when the ball genuinely hits it (impact over 1.0 m/s), the bolt charges for 0.12 s and then releases one radial energy pulse.
  - **Physics:** every can within 1.35 m gets one outward-and-upward impulse through cannon-es (`Body.applyImpulse`), applied slightly above its centre so it tumbles. The force is scaled by `(1 − distance / radius)^1.1`, so near cans are hit hard and far cans barely move, and speed is capped at 4.6 m/s.
  - **Once only:** the pulse fires once per level load; Restart and Next Level reset it.
  - **Chain:** pushed cans, and any can they hit hard, join the energy chain. Each chain can that falls pays +50 × a growing multiplier (×1 up to ×5), and only once. Two or more show "ENERGY BOOST!".
  - **Sound** (synthesized with Web Audio, no audio file; `Sfx.energyCharge` / `Sfx.energyRelease`): three layers, about 1.0 s in total. (1) A rising electric buzz that lasts exactly as long as the real charge. (2) On the actual pulse: a crackling discharge, a clean bass punch (120 → 46 Hz, low-passed, with a 2nd harmonic for phone speakers) and a filtered energy whoosh panned left → right. (3) A soft metallic clatter 0.4–0.95 s after the pulse. It plays once per activation, follows the master sound setting, and its loudness is set by `Sfx.energyVolume`.
  - **Effects:** a camera-facing ring and a flat ring, a cyan light flash, sparks, flickering arcs to the four nearest pushed cans, and a small camera shake. No fire, smoke or explosion imagery.
  - **Tutorial and HUD:** a one-time tutorial appears on the first level that has one, and those levels show a bolt icon in the HUD.
  - **Tuning:** values live in the `ENERGY` object; a level can override them with `energy: { radius, push, lift }`.
- **Spring Can (Medium and Difficult):** a terracotta can standing on a visible steel coil. When the ball hits it, it winds up for 0.07 s and then springs up and forward with one physics impulse, launching whatever rests on it. Every chain can that falls pays +75, and two or more show "SPRING BONUS!". It has its own one-time tutorial and HUD icon. Values live in the `SPRING` object.
- **Planks:** real wooden boards (dynamic rigid bodies) used for bridges, shelves, seesaws and towers. They start asleep and fall when whatever holds them up is knocked away, so the puzzles are about finding the weak point.
- **Room:** a minimal indoor sports practice room with tall matte dark-teal panelled walls (light upper band on the right), an oak floor with planks running toward the target, a dark exposed ceiling with recessed spotlights, two warm light strips framing the play zone, and a bench with a basketball in the back corner. Lighting: soft overhead key, cyan ambience, a warm spot over the play zone.
- **UI type and colour:** Barlow Condensed 800 for LEVEL, SCORE, numbers and headings; Barlow 600 for buttons and gameplay labels; Inter 500 for objectives, hints and small text. Colours: HUD surface #F4EBDD, text #30251F / #76614F, warm action #F29A50, cyan accent #39C6D9 (focus and selection only), progress #86A982.
- **Feedback:** sparks and dust puffs, real dents in the can mesh around the impact point, camera shake on heavy hits, slow motion on a 6-can chain reaction, floating scores, the "NICE HIT!" / "CHAIN REACTION!" / "PERFECT CLEAR!" callouts, and confetti.
- **Three difficulty modes, 45 levels:**
  - **Easy** (mint green): the original 15 levels, unchanged.
  - **Medium** (electric blue): 15 structure puzzles: plank bridges, a seesaw, zigzag walls, islands at two heights, plank towers, a moving bridge, a hidden support behind glass, and Spring and Energy chain reactions. The finale combines all of them.
  - **Difficult** (deep purple with fiery orange): 15 advanced puzzles: a heavy-can fortress, stilts, layered depth, a needle slot, a counterweight beam, a spring cascade, twin cores, crossing doors, a skyline of tiny towers, a house of cards, a moving fortress, a three-wall gauntlet, a chain reactor, a vault and "The Machine".
  - **Design notes:** `LEVEL_DESIGN.md` covers every level's structure, challenge, strategy and why it sits in its mode, plus the test results.
  - **Theming:** each mode has its own colours on the mode cards, level select, HUD badge, buttons, progress bars, results screen, glass rails and wall light strips.
  - **Saving:** progress, unlocks and best scores are saved separately per mode.
  - **Old saves:** progress from before modes existed moves into Easy automatically.
- **Obstacles:** framed glass panels: fences, gaps (`gate`), posts, hanging panels with a slot (`hangGate`) and sliding doors (`mover`, kinematic bodies). The ball and cans bounce off them through normal physics.
- **Levels are solvable:** every new level was cleared by an automated playtest bot. The bot traces the ball's arc around the glass, waits for sliding doors, and stays inside the player's aiming limits. Ball counts were tuned so a slightly imperfect player still wins with spare balls.
- **Progression:** clear a level to unlock the next one. The Level Complete screen shows your score and best score, with Next Level, Replay and Main menu. Any unlocked level can be replayed from Level Select. Unused balls give +250 each, and clearing a level with one ball gives +1,000.
- **Settings:** sound, trajectory guide, camera shake, graphics quality (Low/Medium/High) and reset progress.
- **Audio:** synthesized with Web Audio (metal clanks, wood thuds, bounces, whoosh, chimes, win and lose). It starts only after your first tap or click.
- **Responsive:** works in portrait and landscape. Touch input never scrolls the page, and the game pauses when the tab is hidden.
- **Saving:** unlocked levels, best scores and settings are stored in `localStorage` and survive refreshes.

## Code layout

Everything is in `index.html`, split into numbered sections:

1. Utilities
2. Game data: can types, balls, skins, and the `LEVELS` array (data-driven)
3. Save data
4. Audio
5. Renderer, lighting and procedural textures
6. Physics world
7. Effects
8. Entities: platforms, cans, ball
9. Camera
10. Trajectory guide
11. Game state machine: `menu → intro → aim ⇄ resolving → victory | failure`, plus `paused`
12. Input
13. Simulation step: fixed 120 Hz
14. UI
15. Main loop
16. Boot

**Adding a level:** append an object to `EASY_LEVELS`, `MEDIUM_LEVELS` or `DIFFICULT_LEVELS`. The `MODES` table ties each array to its mode, and level numbers are assigned automatically. Obstacles go in an optional `obstacles: [...]` list, built with `glass(x, z, w, h, y)`, `gate(cx, gap, z, h, span)`, `hangGate(cx, gap, z, y, h, span)` or `mover(x, z, w, h, amp, period, phase)`.

```js
{ name: 'My Level', hint: 'Shown at the start.', balls: 4,
  platforms: [{ x: 0, z: 0, w: 1.4, stacks: [{ pyr: ['nnnn', 'nnn', 'nn', 'n'] }] }] }
```

Medium and Difficult levels use the structure builder instead of `stacks`:

```js
{ name: 'Bridge', hint: '…', balls: 4,
  platforms: [{ x: 0, z: 0, w: 1.4, build: (S) => {
    const t = S.col('nn', -0.4, 0); S.col('nn', 0.4, 0);   // two legs, 2 cans tall
    const p = S.plank(0, 0, 1.15, t);                      // a plank on top of them
    S.pyr(['nnn', 'nn', 'n'], 0, 0, p);                    // a pyramid on the plank
  } }] }
```

`S.can(code, x, z, y)`, `S.col(codes, x, z, y)`, `S.row(codes, x, z, y)`, `S.pyr(rows, x, z, y)`, `S.ring(codes, x, z, radius, y, startDeg)` and `S.plank(x, z, width, y, depth)` all use coordinates relative to the stand's centre and top, and each returns the height of what it built so pieces stack. Can codes: `n` standard, `s` small, `h` heavy, `g`/`G` gold, `e` Energy, `p` Spring. Set `specialCanType: 'spring'`, `'energy'` or `['spring', 'energy']` on levels that use them.

Each string is a row of cans, bottom to top; `n` is a standard can and `e` is an Energy Booster Can. Add `specialCanType: 'energy'` to a level that contains one (it turns on the HUD bolt icon and the first-time tutorial), and optionally `energy: { radius: 1.5, push: 4.0, lift: 2.3 }` to tune that level's pulse. Put several stacks on one stand with `x` offsets, or add a second stand to `platforms`. The goal is always to knock down every can.

## Limitations

- The game needs internet the first time it loads, to fetch the libraries and fonts. After that the browser usually caches them.
- Dents are vertex displacements on the can mesh, not a full deformable-body simulation. The physics collider stays a cylinder.
- Cans use 12-sided cylinder colliders, which are a good balance of accuracy and speed. Very slow rolling on the floor can look slightly faceted.
- Testing used an automated browser with mouse input and phone-size layouts. It has not been checked on a physical phone, and frame rate was not measured.
