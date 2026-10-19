# Space Jumpers — Game Design Document

Luis Ramirez - Game Engine: Phaser - Art Direction: Pixel, space-themed

## 1. Executive Summary

*Space Jumpers* is a one-button, 2D pixel-art platformer in which a tiny astronaut runs automatically back and forth across vertical maps using perfectly timed jumps and double jumps to climb upward through space stations, asteroid fields, glowing nebulae and the edge of a black hole. Every day a brand-new level launches.

### 1.1 Premise

The Skyline Tether, a colossal orbital elevator stretching from the planet Veyra to a deep-space beacon, has gone dark. Its power relays have failed one by one, and only the Jumpers, a corps of small, fearless, spring-booted cadets, are nimble enough to climb its shafts and reignite them. The player controls Pip, the newest Jumper, who must ascend sector by sector, reactivating Relay Checkpoints along the way and lighting the Beacon at the summit of each climb.

### 1.2 Key Features

| Feature | Description |
|---|---|
| One-button control | Pip runs automatically and turns around when he hits a wall. The player only decides when to jump and double jump. |
| Vertical climbs | Tall, screen-wide levels divided into Sectors by checkpoints, ending at a Beacon at the summit. |
| Gravity as a core mechanic | Low-gravity fields, zero-G bubbles, gravity inverters and gravity wells change how Pip moves from biome to biome. |
| A new level every day | The Daily Launch calendar adds a fresh climb every 24 hours, and every past day stays playable. |
| Handcrafted campaign | A 30-level Star Map campaign across five space biomes, plus a weekly hardcore Eclipse Run, ghost time trials and free practice. |
| Fun rewards to earn | Characters, suits, trails and gadgets unlock through milestones and points. |

### 1.3 Presentation

The game renders at a base resolution of 192 × 336 pixels (12 × 21 tiles at 16 × 16 pixels each) in portrait orientation, scaled up by whole-number multiples so pixels stay crisp. On desktop the play area is centered with themed side panels showing the run's stats. Phaser runs with `pixelArt: true` and rounded pixel positions to avoid sub-pixel shimmer. The palette is built around a deep indigo void, with each biome contributing two or three accent colors, and hazards always use a reserved warning color ramp (red/orange with a distinct shape language) so danger is readable at a glance.

### 1.4 Win Conditions

The player wins at three levels of scale.

**Level win.** A climb (called an *Ascent*) is won when Pip reaches and touches the Beacon at its summit. There is no timer and no lives counter in standard play, so any player who keeps trying will eventually win.

**Mastery wins.** Each Ascent awards optional medals that represent a higher standard of victory: the *Comet Medal* for collecting all three hidden Comet Shards, the *Clean Orbit Medal* for reaching the Beacon without a single death, the *Lightspeed Medal* for beating the par time, and the *Stardust Medal* for collecting at least 90% of the Stardust in the level. Earning all four on one Ascent marks it as *Supernova*.

**Meta win.** Clearing every Daily Launch in a calendar month lights up that month's constellation on the Star Chart and unlocks an exclusive cosmetic. Completing the final Ascent of the Star Map campaign (reigniting the Tether's master Beacon at the Event Horizon) is the narrative win and rolls the credits, although Daily Launches continue indefinitely afterward.

### 1.5 Lose Conditions

Failure in *Space Jumpers* is frequent, quick and forgiving.

- Pip is "knocked out" when he touches a hazard (spikes, solar flare beams, plasma, electrified rails) or an enemy from the side or below, falls below the bottom edge of the camera view, is pulled into the core of a gravity well, or is caught by the rising Void Front in pursuit sectors.
- Pip respawns at the most recently activated Relay Checkpoint in under half a second. Any Stardust and Comet Shards collected since that checkpoint are lost (they reappear in the level so they can be collected again), the Orbit Chain multiplier resets, and the Clean Orbit Medal becomes unavailable for that attempt. There is no game over: the run only ends when the player wins or chooses to quit.

---

## 2. Game Mechanics

- The entire game is playable with a single button. Complexity comes from level design and timing, not from the control scheme.
- Every biome bends gravity in a new way, and the space theme is expressed through how Pip *moves*, not just how the backgrounds look.
- Deaths are always readable, respawns are near-instant, and checkpoints are frequent, so the player is never punished with lost time.
- The Daily Launch calendar creates a gentle daily ritual without punishing players who miss days.

### 2.1 Player-Facing Actions

| Action | Input | Description |
|---|---|---|
| **Auto-run** | None (automatic) | Pip constantly runs left or right at a fixed speed. The player never controls horizontal direction directly. |
| **Turn around** | None (automatic) | When Pip runs into a wall, he reverses direction. Level design uses walls to steer Pip. |
| **Jump** | Tap / click / Space / Up / W | Pip leaps upward. Holding the input slightly longer produces a higher jump (variable jump height within a small range). |
| **Double jump** | Second tap while airborne | A second, slightly weaker jump powered by Pip's boot thrusters. A small thruster puff and sound confirm it. The double jump recharges on landing, on stomping an enemy, or on touching a Recharge Orb. |
| **Wall kick** | Tap while touching a wall in midair | Pip kicks off the wall in the opposite direction with an upward boost. This does not consume the double jump, enabling chimney climbs. |
| **Stomp** | Land on top of an enemy | Defeats most enemies, bounces Pip upward and refunds the double jump. |
| **Collect** | Touch a pickup | Stardust, Comet Shards, Data Logs, power-ups and Recharge Orbs are collected on contact. |
| **Activate checkpoint** | Touch a Relay | Lights the Relay, saves progress and banks collected Stardust. |
| **Pause / menu** | Esc / P / on-screen button | Opens the pause menu with retry, settings and quit. |

There is intentionally no attack button, no dash and no manual direction control. Those verbs are expressed through level objects and power-ups instead.

### 2.2 Core Gameplay Loop

The game runs on three nested loops.Pip runs, the player reads the next obstacle, times a jump or double jump, collects Stardust or avoids a hazard, and either succeeds and continues or is knocked out and respawns instantly. The satisfaction comes from rhythm: good players find a flow in which every tap lands exactly on the beat of the level's layout.

An Ascent is divided into six to ten Sectors separated by Relay Checkpoints. Each Sector introduces, develops or combines one or two mechanics. Reaching a Relay banks progress and Stardust, and the final Sector ends at the Beacon. The level summary screen shows medals earned, time, deaths and collectibles, and invites a replay for any missing medals.

Every level is a tall vertical shaft. The camera follows Pip upward smoothly and does not scroll back down past a soft floor, which means that falling far below the current view counts as a knockout. Levels are built from Sectors roughly 2 to 4 screens tall, and a full Ascent is 15 to 35 screens tall depending on mode and difficulty. Relay Checkpoints span the full width of the shaft so the player cannot miss them.


### 2.3 Biomes

The Star Map campaign is organized into five biomes, each introducing a signature gravity mechanic. Daily Launches draw from all unlocked biomes.

| # | Biome | Visual Identity | Signature Mechanic |
|---|---|---|---|
| 1 | **Launch Bay** | Clean station interior, hazard stripes, blinking consoles, a planet visible through windows | Standard gravity; thruster pads and conveyor belts teach the basics |
| 2 | **Asteroid Belt** | Rocky drifting platforms, distant sun, dust particles | Crumbling asteroids and orbiting debris that rotate around anchor rocks |
| 3 | **Nebula Gardens** | Bioluminescent alien flora in pinks and teals inside a gas cloud | Low-gravity fields and zero-G bubbles that change jump arcs |
| 4 | **Ion Storm** | Electric blues and violets, crackling conduits, lightning flashes | Phase platforms that blink in and out on a rhythm, electrified rails |
| 5 | **Event Horizon** | Warped starfield, gravitational lensing, accretion disk glow | Gravity inverters that flip Pip onto the ceiling and gravity wells that pull him inward |

### 2.4 Level Objects and Hazards

| Object | Behavior | Biome Introduced |
|---|---|---|
| **Thruster Pad** | Launches Pip high into the air on contact (spring equivalent) | Launch Bay |
| **Conveyor Track** | Speeds up or slows down Pip's run while he stands on it | Launch Bay |
| **Airlock Vent** | Blasts a column of air that pushes Pip sideways or upward while it is open | Launch Bay |
| **Spike Plating** | Static hazard; instant knockout on contact | Launch Bay |
| **Crumbling Asteroid** | Shakes for 0.5 s after Pip lands, then breaks apart and respawns after 3 s | Asteroid Belt |
| **Orbiting Debris** | Hazard chunks that circle an anchor point at a fixed speed | Asteroid Belt |
| **Warp Gate** | Paired portals that teleport Pip and preserve his direction and momentum | Asteroid Belt |
| **Low-G Field** | Shimmering zone where gravity drops to 40%, producing long floaty jumps | Nebula Gardens |
| **Zero-G Bubble** | Pip floats freely inside; each tap gives a small push in his facing direction | Nebula Gardens |
| **Spore Bouncer** | Organic platform that bounces Pip higher on each consecutive landing | Nebula Gardens |
| **Electrified Rail** | A rail that is safe to stand on when dark and lethal when lit, on a timer | Ion Storm |
| **Solar Flare Beam** | A horizontal laser that charges with a visible warning, then fires | Ion Storm |
| **Gravity Inverter** | Flips gravity so Pip runs along the ceiling until he crosses another inverter | Event Horizon |
| **Gravity Well** | Pulls Pip toward its center; touching the core is a knockout | Event Horizon |
| **Recharge Orb** | Refunds the double jump in midair; respawns after a few seconds | All |

### 2.5 Enemies

| Enemy | Behavior | Stompable? |
|---|---|---|
| **Drift Mite** | Small alien bug that walks back and forth on a platform | Yes |
| **Hover Drone** | Floats in a fixed vertical or horizontal pattern | Yes |
| **Space Jelly** | Pulses slowly up and down; stomping it gives an extra-high bounce | Yes (bounce) |
| **Spiked Urchin** | Rolls along platforms; covered in spikes | No |
| **Gravity Eel** | Swims in circles around gravity wells | No |

### 2.6 Collectibles and Scoring

- **Stardust** is the common collectible, placed in trails that hint at good routes. It contributes to 
- **Comet Shards** are three hidden collectibles per Ascent, placed off the main path, behind risky jumps or inside secret alcoves. Collecting all three earns the Comet Medal.

### 2.7 Power-Ups (Found In-Level Only)

Power-ups are placed by the level designer and can never be bought.

| Power-Up | Effect | Duration |
|---|---|---|
| **Shield Bubble** | Absorbs one hit | Until hit or checkpoint |
| **Jet Pack** | Grants a third midair jump | 15 seconds |
| **Stardust Magnet** | Pulls nearby Stardust toward Pip | 12 seconds |
| **Chronometer** | Slows all hazards and enemies to 60% speed | 8 seconds |
| **Star Boots** | Pip becomes invulnerable to enemies (but not to falling or spikes) and defeats any enemy he touches | 10 seconds |

---

## 3. AI Architecture

### 3.1 Overview

*Space Jumpers* is built by a team of specialized AI agents coordinated by an Orchestrator (Producer) agent. The agents work in a shared version-controlled repository and communicate through structured artifacts (JSON files, code modules and markdown briefs) that follow defined schemas, rather than through free-form conversation. Human developers sit at review gates to approve direction, judge subjective quality (especially art and audio) and sign off on licensing.


| Agent | What It Does | What the Player Notices |
|---|---|---|
| **Orchestrator / Producer** | Breaks the GDD into tasks, assigns them to agents, tracks dependencies and keeps every agent's output aligned with this document. It escalates conflicts and subjective decisions to the human director. | A cohesive game where art, audio, levels and code all feel like they belong to the same vision. |
| **Game Systems Designer** | Turns the design pillars into precise rules and tuning values: movement physics, hazard timings, power-up durations and medal thresholds. | Movement that feels tight and consistent, with jumps that behave the same way every time and hazards with fair, learnable timings. |
| **Technical Architect** | Defines the Phaser project structure, scene flow, data schemas (level JSON, save data), coding conventions and module boundaries. It pins the exact Phaser version and writes the interface contracts other agents code against. | Fast loading, stable performance and the absence of weird bugs that come from systems fighting each other. |
| **Gameplay Engineer** | Implements the player controller, physics, level objects, enemies, power-ups, camera, checkpoints and game modes in Phaser. It writes code against the Architect's contracts and the Designer's config. | Responsive controls (coyote time, jump buffering), smooth camera follow and level objects that work exactly as described. |
| **UI/UX Engineer** | Builds menus, the Daily Launch calendar, the results screen, the unlock gallery, settings and the HUD, with touch and keyboard support. | Clear, fast menus, a satisfying calendar and results flow, and a HUD that informs without cluttering the small portrait screen. |
| **Level Designer** | Designs Ascents as structured level data using the teach–test–twist pattern, a library of hand-approved Sector chunks, the biome's mechanics and a target difficulty. It places collectibles, secrets and power-ups and writes a short design intent note for each Sector. | A new, interesting climb every day, levels that teach mechanics naturally, and clever hidden Comet Shards that reward exploration. |
| **Playtester** | Runs a deterministic reachability solver and simulated input playthroughs using the game's real physics constants, then interprets the reports to flag impossible jumps, unfair blind drops, unreachable collectibles or difficulty spikes. Failed levels go back to the Level Designer with specific feedback. | Every level is guaranteed to be beatable, and deaths feel fair rather than caused by a broken layout. |
| **Pixel Art Agent** | Produces sprites, tilesets, backgrounds and UI elements as indexed-palette pixel data (or prompts and curates an external image tool, see Section 4), enforcing the master palette and tile grid. | A crisp, consistent pixel-art style in which each biome has its own identity while hazards stay instantly recognizable. |
| **Animation Agent** | Builds frame-by-frame animations (run, jump, double jump, land, knockout, enemy cycles) from the base sprites and defines the Phaser animation configs, including frame timings and anchor points. | Pip and the enemies move with charm and personality, with squash, stretch and clear motion that makes timing easy to read. |
| **Music Curator** | Searches royalty-free and CC0 music libraries for tracks that match each biome's mood and tempo requirements, using metadata such as BPM, genre, tags and loopability, and produces a ranked shortlist with source links for human listening. | Catchy, fitting background music for each biome, with tempos that line up with rhythm-based platforms. |
| **SFX Curator / Designer** | Finds sound effects from CC0 libraries and generates retro effects procedurally by writing parameter sets for sfxr/jsfxr-style synthesizers. It names, tags and organizes them into the audio manifest. | Punchy, distinct jump, double-jump, stomp, pickup and checkpoint sounds that make every action feel satisfying. |

---

## 4. Technical Strategy


| Agent | Model | Avg. Input / Task | Avg. Output / Task | Est. Tasks | Total Input | Total Output | Total Tokens |
|---|---|---|---|---|---|---|---|
| Orchestrator / Producer | Opus 5.5 | 15,000 | 2,000 | 400 | 6.0M | 0.80M | **6.80M** |
| Game Systems Designer | Opus 5.5 | 25,000 | 8,000 | 40 | 1.0M | 0.32M | **1.32M** |
| Technical Architect | Opus 5.5 | 30,000 | 10,000 | 15 | 0.45M | 0.15M | **0.60M** |
| Gameplay Engineer | Opus 5.5 | 40,000 | 8,000 | 250 | 10.0M | 2.00M | **12.00M** |
| UI/UX Engineer | Sonnet 5.5 | 30,000 | 6,000 | 120 | 3.6M | 0.72M | **4.32M** |
| Level Designer | Sonnet 5.5 | 12,000 | 6,000 | 450 | 5.4M | 2.70M | **8.10M** |
| Playtester | Haiku 5.5 (escalates to Sonnet) | 6,000 | 1,000 | 700 | 4.2M | 0.70M | **4.90M** |
| Pixel Art Agent | Sonnet 5.5 | 8,000 | 6,000 | 300 | 2.4M | 1.80M | **4.20M** |
| Animation Agent | Sonnet 5.5 | 10,000 | 8,000 | 120 | 1.2M | 0.96M | **2.16M** |
| Music Curator | Sonnet 5.5 (with web search) | 20,000 | 2,000 | 60 | 1.2M | 0.12M | **1.32M** |
| SFX Curator / Designer | Haiku 5.5 | 8,000 | 1,500 | 160 | 1.28M | 0.24M | **1.52M** |
| Code Review | Sonnet 5.5 | 25,000 | 2,000 | 250 | 6.25M | 0.50M | **6.75M** |

### 4.1 Constraints and Limitations

- Each response has a maximum output length, so large files (a full Ascent's tile data, a long scene file) must be generated in pieces, such as one Sector at a time, and assembled by deterministic scripts rather than in a single generation.
- Language models are unreliable at precise two-dimensional geometry when working through text representations of grids. They make off-by-one placement errors, misjudge whether a gap is actually jumpable given the real jump arc, and sometimes produce layouts that look reasonable but are impossible or trivially easy. This is why the Level Designer never has the final word: a deterministic solver using the exact physics constants from the config file checks reachability, and the language model's job is to propose designs and interpret failures, not to prove solvability. Building levels from a library of human-approved Sector chunks further reduces the risk.
- Claude models take text and images as input and produce text as output. They do not generate raster images or audio files natively, and they cannot listen to audio. This shapes two parts of the pipeline. The Pixel Art and Animation agents must either write sprites as text-based pixel data (palette-index grids converted to PNG by a script) or operate an external image-generation tool. The Music and SFX agents must choose tracks based on metadata, descriptions, tags, BPM and waveform statistics computed by scripts, and cannot judge how something actually sounds. A human must listen to every shortlisted track and sound before it is approved.