# Sassy Gangster! — Project Context for Claude

## What This Project Is

A single-file HTML5 Canvas browser game. Side-scrolling 2D platformer with a neon-pink gangster aesthetic. The player character — a sassy woman in a pinstripe suit and heels — moves through playground levels, shanking NPCs to steal their lunch money while managing a "heat" meter (wanted level).

Purely client-side, no build tools, no dependencies beyond a Google Fonts import. Open `index.html` in a browser and play.

---

## Tech Stack

- **Vanilla HTML/CSS/JS** — single file (`index.html`, ~800 lines)
- **HTML5 Canvas** — 1280x720, all rendering via `CanvasRenderingContext2D`
- **Web Audio API** — procedural sound effects (oscillator-based, no audio files)
- **Google Fonts** — `Press Start 2P` for retro pixel aesthetic
- **No build step, no bundler, no framework**

---

## File Structure

```
Sassy Gangster/
├── CLAUDE.md       ← this file
└── index.html      ← the entire game
```

---

## Game Architecture

### Game States
`start` → `playing` → `gameover` | `levelcomplete`

Controlled by `gameState` variable. Screens are HTML overlay divs toggled with `display: flex/none`.

### Core Game Loop (`gameLoop()`)
Runs via `requestAnimationFrame`. Each frame:
1. `updatePlayer()` — input, physics, collision, animation state
2. `updateVictims()` — idle sway, cleanup fainted
3. `updateParticles()` — physics + lifetime
4. Heat decay (2% chance per frame, -0.6)
5. Combo timer decay
6. Draw: background → platforms → victims → player → particles → UI
7. Check win/lose conditions

### Player (`player` object)
- **Movement:** A/D or arrow keys, speed 6px/frame
- **Jump:** Space, jumpPower -18, gravity 0.9/frame
- **Attack:** Click/tap or J key → `attemptShank()`
- **Cooldown:** 25 frames between shanks
- **States:** `idle`, `walk`, `jump`, `shank`
- **Rendering:** Procedural Canvas drawing (no sprites) — bouffant hair, pinstripe suit, shoulder pads, hoop earrings, 6-inch heels, rhinestone switchblade

### Victims (`Victim` class)
Four types with different stats:

| Type       | Wallet | Max Trauma | Notes              |
|------------|--------|------------|--------------------|
| `kid`      | $25    | 80         | Green shirt, small |
| `teen`     | $45    | 120        | Blue jacket        |
| `nerd`     | $60    | 150        | Yellow, glasses    |
| `lunchlady`| $100   | 150        | Red dress, large   |

- Idle sway animation (sinusoidal)
- Trauma accumulates per hit: `28 + combo * 8`
- Faint at max trauma (skull emoji overlay)
- Money extracted: 30% of remaining wallet per hit, multiplied by combo

### Combat / Scoring System
- **Shank range:** 140px horizontal, 100px vertical
- **Hit:** +money, +combo (max 8), particle effects, trauma to victim
- **Miss:** +4 heat, -2 combo
- **Victim faint:** +18 heat
- **Combo timer:** 120 frames, decays combo by 1 when expired
- **Win:** Score >= $850 with heat < 95
- **Lose:** Heat >= 100

### Particle System (`Particle` class)
Types: `sparkle` (magenta squares), `coin` (money bag emoji), `heart` (pink heart emoji), `text` (dollar amount)
- Physics: velocity + gravity (0.3/frame)
- Lifetime: 60 frames, alpha fades with life

### Camera
- Smooth follow: `cameraX = cameraX * 0.92 + targetCam * 0.08`
- Clamped to `[0, LEVEL_WIDTH - canvasWidth]`
- Target: `player.x - 400`

### Level Data
- **Level 1: "Playground Panic"**
- Ground at y=520, level width 3800px
- 5 elevated platforms (jungle gym)
- Background: purple-to-pink gradient, parallax pink clouds, cyan jungle gym bars, magenta slide, gold swings
- 7 victims: 3 kids, 2 teens, 1 nerd, 1 lunch lady (mini-boss)

### UI (HTML overlay, not Canvas)
- **Lunch Money:** Green dollar counter, top-left
- **Heat bar:** Yellow-to-red gradient fill bar
- **Sass Combo:** Multiplier display, turns yellow at >4x
- "SLAYING" text floats above player when combo >3

### Audio
`playSassSound()` creates a new `AudioContext` per call (note: this could hit browser limits on rapid attacks). Two oscillator notes:
1. 680-980 Hz, 120ms
2. 920 Hz, 180ms (60ms delayed)

---

## Visual Style

- **Color palette:** Magenta (#ff00ff), cyan (#00ffff), gold (#ffd700), neon green (#00ff00)
- **Font:** Press Start 2P (pixel/retro)
- **Canvas border:** 8px magenta + 8px black + 16px cyan triple border
- **Buttons:** Magenta fill, cyan border, hover rotates 5deg + scale 1.1
- **Title animation:** Pulsing scale 1.0-1.05

---

## Controls

| Input              | Action      |
|--------------------|-------------|
| A / Arrow Left     | Move left   |
| D / Arrow Right    | Move right  |
| Space              | Jump        |
| J / Mouse click    | Shank       |
| Touch (mobile)     | Shank       |

---

## Known Issues / Limitations

1. **Platform collision bug:** Only detects landing when `player.vx > 0` — platforms don't work when moving left or falling straight down
2. **AudioContext per shank:** Creates a new `AudioContext` every attack — browsers limit concurrent contexts. Should reuse a single context.
3. **No mobile movement controls:** Touch only triggers shank, no on-screen d-pad for movement
4. **Single level only:** `nextLevel()` just shows an alert and restarts level 1
5. **Start screen not shown on load:** `#start-screen` has `display: none` but no JS sets it to `flex` on page load — the start screen never appears unless CSS is changed
6. **Combo decays every frame:** `comboTimer--` runs every frame, so combo drops almost immediately — timer of 120 frames (~2 seconds) is short

---

## Planned Features (not yet built)

- Level 2: School Cafeteria (mentioned in `nextLevel()` alert)
- Additional levels beyond that
- Mobile movement controls (on-screen d-pad)
- High score persistence (localStorage)
- More enemy types with AI (patrol, flee, call for help)
- Boss fights
- Sound toggle / volume control

---

## Development Conventions

- Everything in one file — keep it that way unless splitting becomes necessary
- All rendering is procedural Canvas (no sprite sheets, no images)
- Retro pixel aesthetic — use `image-rendering: pixelated`, blocky shapes
- Over-the-top particle effects on every interaction
- Neon color palette: magenta, cyan, gold, neon green
- Game state managed via string variable, not a state machine class

---

## Git / GitHub

- **Repo:** `danielmiljeteig/sassy-gangster` (public)
- **Branch:** `main`
- Single commit so far (initial)
