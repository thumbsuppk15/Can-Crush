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
- **Room:** a minimal indoor sports practice room with tall matte dark-teal panelled walls (light upper band on the right), an oak floor with planks running toward the target, a dark exposed ceiling with recessed spotlights, two warm light strips framing the play zone, and a bench with a basketball in the back corner. Lighting: soft overhead key, cyan ambience, a warm spot over the play zone.
- **UI type and colour:** Barlow Condensed 800 for LEVEL, SCORE, numbers and headings; Barlow 600 for buttons and gameplay labels; Inter 500 for objectives, hints and small text. Colours: HUD surface #F4EBDD, text #30251F / #76614F, warm action #F29A50, cyan accent #39C6D9 (focus and selection only), progress #86A982.
- **Feedback:** sparks and dust puffs, real dents in the can mesh around the impact point, camera shake on heavy hits, slow motion on a 6-can chain reaction, floating scores, the "NICE HIT!" / "CHAIN REACTION!" / "PERFECT CLEAR!" callouts, and confetti.
- **15 simple levels:** the goal is always to knock down every can. They start with 3 cans and grow gradually through small pyramids, wider rows, taller stacks and two stacks, up to a 21-can pyramid. Ball counts were tuned with an automated playtest so that every level is clearable with spare balls.
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

**Adding a level:** append an object to `LEVELS`.

```js
{ name: 'My Level', hint: 'Shown at the start.', balls: 4,
  platforms: [{ x: 0, z: 0, w: 1.4, stacks: [{ pyr: ['nnnn', 'nnn', 'nn', 'n'] }] }] }
```

Each string is a row of cans, bottom to top; `n` is a standard can. Put several stacks on one stand with `x` offsets, or add a second stand to `platforms`. The goal is always to knock down every can.

## Limitations

- The game needs internet the first time it loads, to fetch the libraries and fonts. After that the browser usually caches them.
- Dents are vertex displacements on the can mesh, not a full deformable-body simulation. The physics collider stays a cylinder.
- Cans use 12-sided cylinder colliders, which are a good balance of accuracy and speed. Very slow rolling on the floor can look slightly faceted.
- Testing used an automated browser with mouse input and phone-size layouts. It has not been checked on a physical phone, and frame rate was not measured.
