# Towerfall
A single-player roguelike tower defense game

# Link: 
[▶ Play Towerfall V1](https://oviiy.github.io/Towerfall/main.html)

[▶ Play Towerfall V2](https://oviiy.github.io/Towerfall/v2.html)

[▶ Play Towerfall V3](https://oviiy.github.io/Towerfall/v3.html)

[▶ Play Cyber](https://oviiy.github.io/Towerfall/Cyber.html)

# Prompt
Build a single-player roguelike tower defense game as ONE self-contained file: `index.html`. It must run by double-clicking the file in any modern browser — no build step, no server, no external assets, no dependencies (CDN allowed only if absolutely necessary; prefer pure vanilla).

# Tech requirements
- Single `index.html` containing all HTML, CSS, JS inline.
- Vanilla JavaScript, HTML5 Canvas for rendering.
- Pixel-art aesthetic. Render at a low internal resolution (e.g. 320×180 or 480×270) and CSS-scale up with `image-rendering: pixelated`. Sprites can be drawn procedurally with canvas primitives in a pixel grid — no external image files.
- Fixed-timestep game loop at 60 FPS using `requestAnimationFrame` with accumulator.
- Persist all meta-progression in `localStorage` under a single key (`td_save_v1`).

# Game shape
Roguelike tower defense. Each run is finite: 25 waves on one map, ending in a boss at wave 25. Win = boss dies. Lose = base HP reaches 0.

## Per-run loop
1. Player picks a map (1 unlocked at start, 3 total).
2. Draft phase: pick 3 towers from a random pool of 8 drawn from the player's unlocked tower set.
3. Combat: enemies follow a fixed pre-defined path on a tile grid. Player places drafted towers on grass tiles adjacent to the path. Towers cost gold; gold earned from kills.
4. Between every wave: a shop modal appears offering 3 random choices of: (a) upgrade an existing tower (+damage / +range / +fire rate, tiered I→II→III), (b) place an additional tower from drafted set at discount, (c) gain a random relic. Player picks 1.
5. Wave 25 spawns a boss with high HP and a special ability.
6. Run end (win or lose) → Run Summary screen showing wave reached, kills, gold earned, meta-currency awarded (= waves cleared × 5, +50 if boss killed).

## Meta loop
- **Main menu** screens: Play, Unlock Lab, Upgrade Tree, Stats.
- **Unlock Lab**: spend currency to permanently add new tower types and relics to the random run pool. Start with 4 towers + 5 relics unlocked; unlock 4 more towers and 10 more relics over time.
- **Upgrade Tree**: a small linear list of ~10 passive buffs the player can buy once each. Examples: +20 starting gold, +1 base HP, +1 max relics, +5% sell value, +1 reroll per shop, etc.
- **Stats**: best wave reached, total runs, total kills, towers unlocked / total.

# Content (v1 scope, all must ship in the file)

## Towers (8 total)
Each tower has: name, cost, base damage, range, fire rate, projectile/effect, unique trait. Include:
1. **Archer** — cheap, fast, single-target.
2. **Cannon** — slow, AoE splash.
3. **Frost** — low damage, slows enemies in range.
4. **Lightning** — chains to 3 nearby enemies.
5. **Poison** — applies DoT.
6. **Sniper** — long range, very high single-shot damage, slow fire rate.
7. **Buffer** — no damage; boosts adjacent towers' fire rate +30%.
8. **Mint** — no damage; generates +2 gold/sec.

Each tower has 3 upgrade tiers with visible visual change (color shift, size bump, extra detail).

## Enemies (6 + 3 bosses)
- Grunt (basic), Runner (fast/low HP), Tank (slow/high HP), Swarm (spawns in clusters), Shielded (immune to first N damage), Healer (heals nearby).
- Bosses: one per map. Examples — "Behemoth" (massive HP), "Phantom" (periodically dodges shots), "Hivemind" (spawns minions on hit).

## Relics (15 total)
Passive run modifiers. Examples: +25% archer range, frost slow effect lasts 2× longer, 10% chance towers double-shot, +50 gold at start of every 5th wave, sniper crits do 3× instead of 2×, towers gain +1 damage per relic owned, etc. Cap inventory at 5 (raise via upgrade tree).

## Maps (3 total)
Each is a 20×11 tile grid with a hand-defined path (array of tile coords from spawn to base). Different aesthetics: green meadow, desert canyon, frozen tundra. Different path shapes (one curvy, one zigzag, one with a fork-and-rejoin).

# UI / UX
- **Main menu**: title, currency display top-right, four buttons.
- **Map select**: shows 3 maps as cards; locked ones grayed.
- **Draft screen**: shows 8 towers face-up; player clicks 3 to confirm.
- **In-game HUD** (top bar): wave # / 25, lives, gold, speed toggle (1× / 2× / 3×), pause button. Bottom bar: drafted towers as buttons with cost.
- **Place tower**: click a tower button → click a valid grass tile to place. Click a placed tower → popover with (returns 50% of total spend).
- **Shop modal**: 3 cards, click one, optional reroll button (free first reroll, then costs gold).
- **Run Summary**: win/lose banner, stats, "currency earned: +X", buttons: Play Again / Main Menu.

Controls: mouse only. Keyboard shortcuts: 1/2/3 select drafted towers, Space pause, +/- speed.

# Visuals
- Pixel-art style, 16×16 tile size at internal resolution.
- Towers: distinct silhouettes, color-coded by element (red/cannon, cyan/frost, yellow/lightning, green/poison, brown/archer, gray/sniper, purple/buffer, gold/mint).
- Enemies: simple 12×12 sprites with a single-color HP bar above.
- Path tiles: dirt/sand/snow per map; placeable tiles: grass/rock/ice; non-placeable scenery (trees, etc.) decorate but don't allow placement.
- Particle effects (canvas dots) for hits, kills, gold pickups.
- Smooth tower rotation toward target.

# Audio
Generate sound effects via WebAudio (oscillators + envelopes) — no audio files. Beep on shoot, thump on kill, jingle on wave clear, sting on lose. Mute toggle in HUD.

# Balance targets
- Wave 1 should be winnable with the cheapest tower placed once.
- Run length: 10–15 minutes at 1× speed.
- Win rate for a player on their 5th run with a couple of unlocks should feel like ~50%.
- Bosses: clearly the hardest part, but losable to a poorly-built run on wave 18–22, not just at the boss.

# Code organization (within the single file)
Use a clean module structure inside the script tag: `Game`, `Map`, `Tower`, `Enemy`, `Projectile`, `Wave`, `Shop`, `Relic`, `Save`, `UI`, `Audio`. Top of file: a single `CONFIG` object with all balance numbers (tower stats, enemy stats, wave definitions, relic effects). Make this object easy to edit so balance can be tuned without touching logic.

# Acceptance criteria
- Opening `index.html` immediately shows the title screen.
- A new player can complete a full run start-to-finish without any console errors.
- All 8 towers, 6 enemies, 3 bosses, 15 relics, and 3 maps are implemented and reachable through gameplay.
- Saving works: closing and reopening the tab preserves currency, unlocks, upgrades, and stats.
- Frame rate stays at 60 FPS with 100+ enemies on screen.
- The whole file is under ~3000 lines.

Build it complete in one shot. Do not stub anything. Do not output explanations — just the final `index.html`.
