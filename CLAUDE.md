# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**VOID PROTOCOL** — A browser-based boss-rush action game. The entire game is a single self-contained `index.html` file with inline CSS and JavaScript (no build tools, no dependencies).

## Running the Game

Open `index.html` directly in a browser. No server or build step required.

```bash
# Windows
start index.html

# macOS
open index.html

# Linux
xdg-open index.html
```

## Architecture

Single-file architecture (`index.html`) with these major sections, in order:

1. **CSS** — UI overlay styling (title, death, victory screens), neon glow animations
2. **HTML** — Canvas element + UI overlay divs for game screens
3. **JavaScript** — All game logic, structured as:

| System | Description |
|---|---|
| `AudioSystem` | Procedural sound via Web Audio API (no audio files). Generates all SFX and ambient music from oscillators/noise buffers. |
| `Particle` / particle helpers | Particle system for visual effects (explosions, trails, sparks, rings). Global `particles` array. |
| Screen shake / flash | `addShake()` and `addFlash()` for juice/feedback. |
| Input system | Keyboard (`keys` map) + mouse tracking. Global state. |
| `Player` | Movement, dash (with i-frames), shooting, health. |
| Bullet pools | `playerBullets` / `bossBullets` arrays with update/draw/collision. Supports homing bullets. |
| `Boss` (base class) | Shared HP, phases (3 per boss), phase transitions, damage flash. |
| `SentinelX` | Boss 1 — aimed bursts, ring shots, spirals, charge attacks, sweeping laser. |
| `PhantomWeaver` | Boss 2 — teleporting, clone spawning, web shots, bullet walls. |
| `OblivionCore` | Boss 3 — gravity pulses, ground slams, dual beams, shield orbs. |
| Game manager | State machine (`title`→`intro`→`playing`→`death`/`victory`), screen transitions, stats tracking. |
| `gameLoop` | `requestAnimationFrame` loop with delta time. Renders background grid, all entities, HUD. |

## Key Constants

All tuning constants are at the top of the `<script>` block (arena size, player speed, dash params, fire rate, HP, etc.). Modify these to adjust game feel.

## Adding a New Boss

1. Create a class extending `Boss` with `update(dt)` and `draw(ctx)` methods
2. Implement `executeAttack()` with phase-specific attack arrays
3. Add entry to `BOSS_DEFS` array with `{ name, tier, cls }`
4. Add collision handling in `checkCollisions()` if the boss has unique collidable elements (orbs, clones, etc.)

## Rendering

Uses Canvas 2D with `shadowBlur` for neon glow effects. Arena is fixed 1200x800, scaled to fit the viewport via CSS. All coordinates are in arena space.
