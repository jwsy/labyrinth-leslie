# Labyrinth by Leslie — Game Specification

## Overview

A single-file HTML5 PWA maze game installable on iOS/iPadOS. The player is a pixel-art bunny navigating a dark labyrinth inspired by the Greek myth of Theseus and the Minotaur. Collect three golden powerups, defeat the Minotaur, and escape through the exit — all before the timer runs out.

**Files**: `index.html`, `manifest.json`, `sw.js`  
**Dependencies**: None (all sprites drawn via Canvas 2D API; audio via Web Audio API)  
**Optional**: `bgm.mp3` for background music (falls back to synthesized drone)

---

## PWA Requirements

- `manifest.json`: name `"Labyrinth by Leslie"`, short_name `"Labyrinth"`, display `"standalone"`, orientation `"portrait"`, theme/background `#0d0d1a`
- `sw.js`: cache-first strategy, caches `index.html`, `manifest.json`, `bgm.mp3`
- Meta tags: `apple-mobile-web-app-capable`, `apple-mobile-web-app-status-bar-style: black-translucent`, `viewport` with `user-scalable=no, maximum-scale=1.0`
- Lock orientation to portrait on game start via `screen.orientation.lock('portrait')` (try/catch for desktop)

---

## Visual Style

- All sprites drawn with Canvas 2D `fillRect` in pixel-art style — no image files
- **Palette**: walls `#1a1a2e`, floor `#0d0d1a`, gold accent `#f0c040`, muted purples/blues for UI
- Canvas scaled by `window.devicePixelRatio` for crisp Retina rendering
- **Grid**: 15 columns × 21 rows; cell size computed as `floor(screenWidth / 15)` × `floor((screenHeight - 50) / 21)`

---

## Maze

- **Algorithm**: Recursive Backtracker (DFS) on odd-dimension grid; every passable cell reachable
- `maze[row][col] === 1` = wall, `=== 0` = floor
- **Player start**: `(1, 1)`
- **Exit**: `(rows-2, cols-2)` — golden archway, always visible through fog
- **Items**: Sword, Shoes, Lantern placed randomly on open floor cells, not overlapping each other, start, or exit
- **Minotaur start**: center open cell
- **Pineapples**: count configurable (default 3), placed randomly

### JSON Export/Import

Export schema:
```json
{
  "maze": [[1,0,...], ...],
  "playerStart": [1, 1],
  "exit": [19, 13],
  "items": { "sword": [r,c], "shoes": [r,c], "lantern": [r,c] },
  "minotaurStart": [r, c],
  "pineappleStarts": [[r,c], ...],
  "seed": 12345
}
```

- **Export**: button in pause menu downloads JSON file
- **Import**: file input on start screen loads JSON instead of generating

---

## Ariadne's Thread

- Every cell the player visits is recorded in order
- Rendered as a thin line (2px, `#cc2244`, 50% alpha) connecting cell centers in visit order
- Drawn below all sprites
- Resets on respawn (new life starts fresh thread)

---

## Fog of War

- Default: Chebyshev radius 3 around player is visible; rest is `#0d0d1a`
- Exit tile always rendered regardless of fog
- Previously visited cells rendered at 50% brightness (explored-but-dark)
- **Lantern powerup**: permanently removes fog; full maze visible

---

## Sprites (pixel art via fillRect)

### Bunny (Player)
- White body, pink-lined tall ears, black dot eyes, pink nose
- 4-frame walk animation (ear droop + foot alternation), advances every 150ms of movement
- **With Sword**: yellow diagonal line extending right (sword)
- **With Shoes**: small brown rectangles at bottom (boots)
- **Stun flash**: flash white for 300ms after pushback

### Minotaur
- Dark brown body, lighter brown head, white horn triangles, red dot eyes
- 2-frame animation (1px body shift), advances every 400ms
- **Defeated**: replaced by pixel-art skull (X eyes, zigzag teeth)

### Pineapple (Enemy)
- Yellow oval body, green spike crown, black dot eyes
- 2-frame bob animation, advances every 500ms

### Items (on floor, gold glow)
- **Sword**: horizontal yellow rect + crossguard
- **Shoes**: two small brown boot shapes
- **Lantern**: yellow hexagon + handle

### Exit
- Golden arch: two vertical yellow rects + horizontal connecting rect, always visible

---

## Math Quiz Overlay

Triggered by interactions; pauses all game movement and timer while visible.

**UI**: centered `<div>` overlay, dark background `#1a1a2e`, gold border `#f0c040`, 3 multiple-choice buttons

**Math**: multiply two factors each drawn from configurable range (default 5–9). Question: `"6 × 8 = ?"`. Generate 1 correct + 2 wrong answers (wrong within ±10, never negative, never equal to correct).

### Trigger conditions

| Event | Has condition | Correct | Wrong |
|---|---|---|---|
| Step on powerup | — | Collect powerup, pickup SFX | Push back, no heart loss |
| Step on pineapple | — | Pineapple disappears, defeat SFX | Push back, lose 1 heart, stun |
| Step on minotaur | Has Sword | Minotaur defeated, roar SFX | Push back, lose 1 heart, stun |
| Step on minotaur | No Sword | No quiz — auto push back, lose 1 heart, stun, damage SFX |

---

## Hearts / Lives

- Start with 3 hearts; drawn as pixel-art hearts in UI bar
- Losing a heart: brief red screen flash (50ms), damage SFX
- 0 hearts: respawn at `(1,1)`, 3 hearts restored, powerups kept, thread resets, timer continues
- Minotaur respawns at start if not yet defeated

---

## Powerup Effects (all permanent)

| Item | Effect |
|---|---|
| Sword | Enables minotaur kill; sword added to bunny sprite |
| Shoes | Move cooldown 200ms → 100ms |
| Lantern | Fog of war permanently removed |

Collected powerups shown as icons in UI bar (right side).

---

## Win / Lose

- **Win**: Minotaur defeated AND player steps on exit → win screen overlay
- **Lose**: Timer hits 0 → game-over screen overlay
- Both screens: pixel-art result text, **Play Again** button (new generated maze), **Return to Start** button

---

## Timer

- Default 55 seconds, displayed as `MM:SS` in UI bar center
- Turns red at ≤10 seconds
- Pauses during quiz and pause menu
- Configurable on start screen and in pause menu: options `[30, 45, 55, 90, 120]` seconds (new game only)

---

## Controls

### Touch (swipe)
- `touchstart`: record `(x, y)`
- `touchend`: `|dx| > |dy|` → horizontal move; else vertical; minimum 10px threshold
- `preventDefault()` on both events

### Keyboard
- Arrow keys + WASD → move
- `Escape` → toggle pause menu
- Movement blocked when quiz visible or game paused

### Move cooldown
- 200ms between moves (100ms with Shoes)

---

## Audio (Web Audio API — fully synthesized)

| Sound | Synthesis |
|---|---|
| Footstep | 0.05s noise burst, ~80Hz |
| Pickup chime | C5→E5→G5 arpeggio, 0.1s/note, sine |
| Damage hit | 200Hz→100Hz descending buzz, 0.2s, sawtooth |
| Minotaur roar | 80Hz→40Hz sweep, 0.4s, square |
| Pineapple defeat | 300Hz→600Hz chirp, 0.15s, sine |
| Win fanfare | 5-note ascending, 0.15s/note, sine |
| BGM | Load `bgm.mp3` if present; else two square oscillators (110Hz + 220Hz) with subtle LFO |

Mute toggle button in UI bar.

---

## UI Bar (top 50px)

```
[ ⏸ Menu ]  ♥♥♥  [  0:55  ]  🗡👟🏮  [🔊]
```

1. Pause/Menu button (left)
2. Heart icons (3 pixel-art hearts)
3. Timer (center)
4. Powerup slots — icons appear when collected (right of center)
5. Mute button (right)

### Pause Menu Overlay (z-index 30, full-screen dark)

- Resume button
- **Math factor range**: two `<select>` (min: 2–12, max: 2–12), default 5–9
- **Pineapple count**: `<select>` options 1–5, default 3
- **Pineapple speed**: Slow / Medium / Fast
- **Timer duration**: `<select>` options 30/45/55/90/120s (next game)
- **Export Maze** button
- **New Maze** button (generates new maze, resets game)

---

## Start Screen

- Full-screen dark overlay, title "Labyrinth by Leslie" in gold
- Animated bunny walking in place (4-frame cycle)
- Timer `<select>` setting
- **Upload Maze** file input (`.json`)
- **Start Game** button → locks orientation, starts BGM, launches game loop

---

## Code Architecture

```
index.html
├── <style>   — UI layout, overlay styles, button styles
└── <script>
    ├── 1. Constants & Config
    ├── 2. Maze Generation   — generateMaze(), exportMaze(), importMaze()
    ├── 3. Game State        — single `state` object
    ├── 4. Sprite Drawing    — drawBunny(), drawMinotaur(), drawPineapple(),
    │                          drawItem(), drawExit(), drawHeart()
    ├── 5. Fog of War        — computeVisibility()
    ├── 6. Math Quiz         — showQuiz(onCorrect, onWrong), generateQuestion()
    ├── 7. Ariadne's Thread  — recorded in state.thread[], drawn as polyline
    ├── 8. Movement          — movePlayer(), moveMinotaur(), movePineapples()
    ├── 9. Collision         — checkCollisions()
    ├── 10. Audio            — initAudio(), playSFX(), startBGM(), toggleMute()
    ├── 11. UI Drawing       — drawUI(), drawHUD()
    └── 12. Game Loop        — requestAnimationFrame with delta timing
```

---

## Responsive Layout

- On `window.resize`: recompute `cellWidth`, `cellHeight`, resize canvas, redraw immediately
- Logical grid stays 15×21; only pixel size of cells changes
- `manifest.json` orientation lock + `screen.orientation.lock('portrait')` on game start
