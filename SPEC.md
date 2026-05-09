# DRIFT — Game Specification

## Concept & Vision

DRIFT is a hypnotic 3D endless runner set inside an infinite abstract void. You pilot a luminous geometric ship through twisting corridors of dark matter, dodging crystalline obstacles that materialize from the darkness. The aesthetic is stark and cosmic — deep blacks, electric neons, and a sense of serene danger. The vibe: flying through the universe's nervous system at mach speed.

## Design Language

- **Aesthetic:** Dark void + neon wireframe. Inspired by Tron's light-grid world meets brutalist geometry. Everything is sharp, glowing, and intentional.
- **Colors:**
  - Background: `#000008` (near-absolute black with a blue tint)
  - Ship: `#00ffcc` (electric cyan) with `#ff00aa` (hot magenta) accent glow
  - Obstacles: `#ff3366` (danger red) with pulsing glow
  - Score / UI: `#ffffff` with subtle `#00ffcc` accent
  - Particles: `#00ffcc`, `#ff00aa`, `#4444ff`
- **Typography:** Orbitron (Google Fonts) — geometric, futuristic. Score: 48px bold. UI labels: 14px.
- **Spatial system:** Full viewport canvas, HUD overlaid. No borders, no chrome — pure immersion.
- **Motion:** Ship has subtle banking animation on horizontal movement. Obstacles pulse in/out. Screen shake on collision. Death explosion: 50-particle burst.
- **Visual assets:** All procedural — Three.js geometry. No external images.

## Layout & Structure

- **Start Screen:** Title "DRIFT" centered, pulsing glow, "TAP TO BEGIN" instruction, high score display.
- **Game Screen:** Full-screen canvas, minimal HUD: score (top-left), current multiplier (top-right).
- **Death Screen:** Slow-mo explosion, score reveal, "TAP TO RETRY", high score update.
- **Responsive:** Portrait mobile-first. Landscape supported but optimized for portrait.

## Features & Interactions

### Core Mechanics
- **Movement:** Touch drag (or mouse) — ship follows finger/cursor X position with smooth lerp (0.12 factor). Ship locked to bottom 25% of screen vertically.
- **Obstacles:** Rectangular slabs materialize from fog ahead, drifting toward player at constant speed. Speed increases every 10 seconds (caps at 2.5x base speed).
- **Collision:** AABB hitbox, slightly forgiving (80% of visual size). On hit: explosion, game over.
- **Scoring:** +10 points per obstacle passed. Multiplier increases by 0.1x every 5 seconds (caps at 5x). Multiplier resets on death.
- **Lanes:** 3 implicit lanes. Obstacles spawn in 1-2 lanes at a time, leaving at least 1 escape route.

### Controls
- **Touch (mobile):** Drag anywhere on screen to steer.
- **Tilt (mobile):** DeviceOrientation — phone tilt steers ship (smoothed with gyro sensitivity option).
- **Mouse (desktop):** Mouse X position controls ship.

### States
- **IDLE → PLAYING:** Tap to start, 3-2-1 countdown, then obstacles begin.
- **PLAYING → DEAD:** Collision triggers explosion, slow-mo, then death screen.
- **DEAD → PLAYING:** Tap to retry, instant restart.

## Component Inventory

### Ship
- Geometry: Octahedron (two pyramids joined at base) — sharp, crystalline
- Material: MeshBasicMaterial with cyan color + emissive glow via point light
- States: Normal (steady glow), Banking (rotation.z ±0.4 based on velocity), Dead (explodes into particles)

### Obstacles
- Geometry: BoxGeometry slabs, varying heights (1x to 3x ship size)
- Material: MeshBasicMaterial red with pulsing opacity (0.7 → 1.0, 500ms cycle)
- Spawn: Randomized lane position, spawn rate increases over time

### Particle System
- Explosion: 50 small spheres, random velocities, fade out over 1 second
- Trail: Ship leaves faint cyan particle trail (10 particles, short lifespan)

### HUD
- Score: Top-left, Orbitron 48px, white with subtle text-shadow glow
- Multiplier: Top-right, Orbitron 24px, cyan

### Screens
- Start: Title + subtitle + tap instruction, fade-in on load
- Death: Overlay with final score, high score, tap to retry

## Technical Approach

- **Engine:** Three.js (r150+) via CDN — no build step, pure ES modules
- **Rendering:** WebGLRenderer, antialias on, alpha off (solid black background)
- **Audio:** Web Audio API — synthesized engine hum, collision sound, score chime (no external files)
- **Persistence:** localStorage for high score
- **Deployment:** Single `index.html` — Three.js loaded from CDN, all code inline
- **Mobile:** `<meta name="viewport">` with `user-scalable=no`, touch events, DeviceOrientation API
- **Performance target:** 60fps on mid-range mobile (iPhone 12 / Pixel 5 class)
