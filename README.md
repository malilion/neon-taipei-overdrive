# Neon Taipei Overdrive

**English** · [繁體中文](README.zh-TW.md)

**Live demo:** https://malilion.github.io/neon-taipei-overdrive/

![Title screen](docs/screenshots/01-menu.png)

## Introduction

*Neon Taipei Overdrive* is a browser-based 3D cyberpunk action combat game. The stage is a rainy Taipei night that blends Ximending with the Xinyi district: wet asphalt mirrors Traditional Chinese neon signs, a rainbow scramble crossing flows underfoot, and the distant Taipei 101 Energy Tower pours cyan and magenta light into the sky. You are the last cyber-agent still online — dual plasma blades, an EMP railgun, and a quantum shotgun against waves of runaway tourist mechs, neon drones, kamikaze scooters, and every fifth wave the giant boss Sky Lantern Mothership. Defeat enemies for EXP, pick upgrades on level-up, and at LV.3 awaken the 101 tower to fire the orbital laser **101 Thunderfall** and clear the field.

The whole game is a single `index.html`: signs, ground, characters, enemies, and particles are built at runtime in code; music and SFX are synthesised on the fly. There are no image, model, or audio asset files, and no build step.

## Quick start

**Open directly**

Open `index.html` in desktop Chrome, Edge, or Firefox and click **ENGAGE**. The game loads three.js and Google Fonts from CDNs, so the first launch needs a network connection; if fonts fail to load it falls back to system fonts.

**Static server**

If the browser restricts local files, or you want to share on a LAN:

```bash
python3 -m http.server 8080
# or
npx http-server -p 8080
```

Then open <http://localhost:8080/>.

**Reproducible city with `?seed=`**

City layout (building heights and placement, signs, ground texture, aerial traffic, etc.) is driven by a seed. Append `?seed=` so the same seed always builds the same city:

```text
index.html?seed=101
http://localhost:8080/?seed=taipei
```

Any text up to 32 characters works. With no seed, one is generated at random and shown on the title and results screens so you can share it. The seed only affects the city look; enemy spawns, crits, and drops stay random.

## Screenshots

> Captured with software rendering and no GPU (`?seed=101`). On a normal GPU the frame looks the same at a much higher framerate.

| | |
|---|---|
| ![Title screen](docs/screenshots/01-menu.png) | ![Street combat](docs/screenshots/02-combat.png) |
| **Title screen**: controls, city seed, settings | **Street combat**: piercing railgun; HUD shows wave, score, combo |
| ![Perk select](docs/screenshots/03-perks.png) | ![Boss](docs/screenshots/04-boss.png) |
| **Choose 1 of 3**: pick from 13 upgrade modules | **Boss fight**: Sky Lantern Mothership and top HP bar |
| ![101 Thunderfall](docs/screenshots/05-ultimate.png) | |
| **Ultimate hit**: full-screen flash; chain lightning deals 300 to every enemy | |

## Controls

| Key | Action |
|---|---|
| `W` `A` `S` `D` | Move |
| Mouse move | Look / aim (crosshair turns red when locked on an enemy) |
| Left mouse (hold OK) | Attack |
| `1` `2` `3` / mouse wheel | Switch weapon |
| `Shift` / right mouse | Dash (invincible while dashing) |
| `R` / `Space` | Ultimate **101 Thunderfall** (unlocked at LV.3, when charged) |
| `1` `2` `3` (on level-up) | Pick a perk (or click a card) |
| `Q` | Toggle real-time ground reflections (off for more FPS) |
| `M` | Mute |
| `Esc` | Pause (sensitivity, volume, screen shake) |
| Arrow keys | Fallback look when pointer lock is unavailable |

The first click locks the pointer; `Esc` unlocks and pauses.

## Gameplay

**Weapons**

| Weapon | Role | Damage | Notes |
|---|---|---|---|
| 1. High-freq plasma twin blades | Fast melee | 25 | Cyan arc slash, high attack rate, light knockback; can cut enemy bullets |
| 2. EMP rail cannon | Piercing sniper | 65 | Magenta laser pierces all enemies, ring pulse + screen shake, longer cooldown |
| 3. Quantum shotgun | Mid-close suppression | 18 × 9 | Fan of gold pellets, strong knockback and brief slow; point-blank can break 100 |

Every weapon can crit; crit damage numbers enlarge and show **CRIT**.

**Waves and boss**

Enemies spawn in waves. Clearing a wave restores 15% HP, awards a wave bonus, and fires a small firework. Higher waves mean more enemies, more HP, and more types. Every 5th wave spawns the boss **Sky Lantern Mothership**: bullet rings, aimed bursts, drone summons; below half HP it enters phase 2 and rains burning lanterns. Defeat drops New Year–style fireworks over 101 and several power-up capsules.

**Enemies**

| Enemy | Behaviour |
|---|---|
| Neon drone | Hovers and shoots; occasional dive |
| Runaway tourist mech | Ground chase; charged melee smash |
| Kamikaze scooter | Locks a direction, high-speed ram and self-destruct; explosion hits nearby enemies |
| Sniper drone | Long-range aim with a red telegraph line before firing — dodge the line |
| Heavy tourist-bus mech | Armoured; five-shot fan and heavy punch |
| Sky Lantern Mothership (boss) | Every 5 waves; two phases |

**EXP, levels, and perks**

Defeated enemies drop green EXP orbs (auto-magnet when close). Each level-up fires a gold pillar, raises max HP and base damage, and pauses combat so you pick one of three random cards. There are 13 upgrades:

- Stat boosts: damage, crit rate, cooldown, move speed, max HP, kill heal, pickup range / EXP bonus, ultimate charge rate
- Neon barrier: periodic shield that blocks one hit
- Holo satellites: orbiting orbs that damage on contact
- Plasma sword waves: piercing waves from blade swings
- Chain arcs: railgun hits discharge to nearby enemies
- Extra pellets: more shotgun pellets

**Ultimate: Taipei Overdrive — 101 Thunderfall**

Unlocked at LV.3. Charge builds from hits, kills, and time. When full, press `R`: slow-mo and chromatic flash, Taipei 101 fires a massive orbital laser, chain lightning hits every enemy for 300 damage each, and small drones are wiped.

**Power-up capsules**

| Capsule | Effect |
|---|---|
| Overclock core | Damage ×2 for 8s |
| Neural haste | Cooldown halved, move speed +30% for 8s |
| Neon shield | Block 3 hits |
| Bubble-tea heal | Restore 40 HP |

**Score**

Kill points scale with combo multiplier (up to ×3); waves add a clear bonus. Results screen grades S / A / B / C; best score is stored in `localStorage`.

## Rendering

- **Three.js r160** via `<script type="importmap">` from jsDelivr (`three` and `three/addons/`); no bundler.
- **Post chain**: `EffectComposer` → `RenderPass` → `UnrealBloomPass` (HDR bloom) → custom `ShaderPass` (RGB split, scanlines, vignette, glitch, hurt edge, ultimate neon grade) → `OutputPass` (ACES + sRGB). Emissive materials use HDR colours > 1 so bloom makes neon glow.
- **Wet ground reflections**: a `Reflector` mirror under a translucent asphalt layer; puddles are more transparent so sign reflections show through. Ground material uses `onBeforeCompile` for a hex energy grid that brightens near the player and ripples outward; the ultimate pushes a shock ring. Rain, floaters, and HP bars sit on a separate layer and skip mirror rendering.
- **Procedural textures**: all signs (vertical, horizontal, gate), windows, shopfronts, hex ground, and lantern paper are `CanvasTexture`s using Noto Sans TC and Orbitron.
- **Procedural models**: agent, mechs, scooters, drones, mothership, and the 101 tower are primitive assemblies. The tower uses a custom shader for energy flowing up edges and floors.
- **Lighting**: `PMREMGenerator` bakes a small neon-panel scene into an env map for metal reflections; sky is a procedural gradient + stars shader.
- **FX**: 5,000 additive GPU point particles (sparks, explosions, fireworks); `InstancedMesh` for shards and zebra bars; custom shaders for slashes, beams, pillars, and shock rings; chain lightning is a jittered polyline rebuilt each frame.
- **Perf**: static city geometry merged per material (`BufferGeometryUtils.mergeGeometries`); sign materials shared by texture. Idle scene ~365 draw calls; ~20+ enemies ~900.
- **Audio**: WebAudio synthesises all SFX and a 108 BPM synthwave track that intensifies in combat and on boss waves.
- **Seed**: `?seed=` is string-hashed into mulberry32 and temporarily replaces `Math.random` only while the world is built.

## Project layout

```text
neon-taipei-overdrive/
├── index.html                # Full game: HTML UI + CSS HUD + JS module
├── LICENSE                   # Apache-2.0
├── README.md                 # English (this file)
├── README.zh-TW.md           # Traditional Chinese
├── .github/workflows/pages.yml
└── docs/
    └── screenshots/          # README screenshots
```

`index.html` is organised in numbered sections:

| Section | Contents |
|---|---|
| 0. Utilities / seed | Math helpers, `?seed=` hash, mulberry32 |
| 1. Audio | WebAudio SFX and soundtrack |
| 2. Renderer / Post-FX | Renderer, scene, post chain |
| 3. Canvas textures | Neon sign / window / shop generators |
| 4–7. Env / ground / city / 101 | Sky, lights, wet reflector ground, streetscape, energy tower |
| 8–9. Particles & FX | GPU particles, shards, slashes, beams, shocks, lightning |
| 10–11. Characters & enemies | Agent + weapons, drones, mechs |
| 12–14. State, HUD, combat | Game state, floating damage, three weapons |
| 15–15d. AI, waves, perks | Spawns/AI, scooter/sniper/boss, waves, perks, capsules, score |
| 16. Ultimate | 101 Thunderfall |
| 17–19. Input, camera, HUD | Pointer lock, third-person camera, radar |
| 20–22. Flow, loop, boot | Start / results / retry, game loop, world build |

Debug: browser console exposes `window.NTO` (e.g. `NTO.startWave(5)` to jump to a boss wave).

## License

Copyright 2026 Malilion. Licensed under the [Apache License, Version 2.0](LICENSE).

### Third-party

- [three.js](https://threejs.org/) r160 — MIT License (loaded from jsDelivr)
- [Orbitron](https://fonts.google.com/specimen/Orbitron), [Rajdhani](https://fonts.google.com/specimen/Rajdhani), [Noto Sans TC](https://fonts.google.com/noto/specimen/Noto+Sans+TC) — SIL Open Font License (Google Fonts)
