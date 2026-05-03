<img width="264" height="180" alt="image" src="https://github.com/user-attachments/assets/d5dde648-3465-4fb6-bddc-c753f49fa7fb" />

# Labyrinth by Leslie

A pixel-art PWA maze game for iOS/iPadOS. Navigate a dark labyrinth as a cute bunny, collect three golden powerups, slay the Minotaur, and escape — before the timer runs out. 

Inspired by the Greek myth of Theseus and the Minotaur.

### [https://jwsy.github.io/labyrinth-leslie](https://jwsy.github.io/labyrinth-leslie)

<img width="581" height="322" alt="image" src="https://github.com/user-attachments/assets/28fcc712-3dec-413e-9386-18f9d5eec768" />

---

## Play It

Open `index.html` in any modern browser. No build step. No dependencies.

### Run locally

```bash
# Any static file server works. Examples:

# Python 3
python3 -m http.server 8080

# Node (npx)
npx serve .

# VS Code: use the Live Server extension
```

Then open `http://localhost:8080` in your browser.

### Install on iPhone / iPad

1. Open the URL in **Safari** (must be Safari for PWA install)
2. Tap the **Share** button
3. Tap **"Add to Home Screen"**
4. Tap **Add**

The game will appear as **"Labyrinth"** on your home screen and run fullscreen with the screen locked to portrait.

---

## How to Play

| Action | Touch | Keyboard |
|---|---|---|
| Move | Swipe in any direction | Arrow keys or WASD |
| Pause / Menu | Tap ⏸ Menu button | Escape |

### Objective

1. Collect the **Sword** (enables killing the Minotaur)
2. Collect the **Shoes** (speed boost) and **Lantern** (reveal full map) — optional but helpful
3. **Kill the Minotaur** by stepping on it while carrying the Sword
4. **Reach the exit** (golden arch, always visible)

### Math Challenges

Every time you:
- Pick up a powerup
- Touch a Pineapple enemy
- Step on the Minotaur (with Sword)

...a **multiplication quiz** appears. Answer correctly to proceed. Answer wrong and you get pushed back (touching enemies also costs a heart).

### Hearts

- You start with **3 hearts**
- Losing all 3 respawns you at the entrance — **powerups are kept**
- The timer keeps running

### Enemies

| Enemy | Behavior | Danger |
|---|---|---|
| Minotaur | Random walker | Touch without Sword = lose heart; touch with Sword = math quiz to kill |
| Pineapples | Random walkers | Touch = math quiz; wrong answer = lose heart |

### Ariadne's Thread

A red line traces everywhere you've walked — your path back to the entrance, just like in the legend.

---

## Preferences

Access via the **⏸ Menu** button during play (or on the Start Screen):

| Setting | Options | Default |
|---|---|---|
| Math factor range | Min: 2–12, Max: 2–12 | 5 × 5 to 9 × 9 |
| Pineapple count | 1–5 | 3 |
| Pineapple speed | Slow / Medium / Fast | Medium |
| Timer duration | 30 / 45 / 55 / 90 / 120 seconds | 55s |

---

## Maze Export / Import

- **Export**: In the pause menu, tap **Export Maze** to download the current maze as a `.json` file
- **Import**: On the start screen, tap **Upload Maze** and select a previously exported `.json` file to replay a specific maze

---

## Optional: Background Music

Place a file named `bgm.mp3` in the same directory as `index.html`. If present it will loop as background music. If absent, a synthesized chiptune drone plays instead.

---

## Files

```
labyrinth-leslie/
├── index.html      # The entire game (self-contained)
├── manifest.json   # PWA manifest (home screen install)
├── sw.js           # Service worker (offline support)
├── README.md       # This file
└── SPEC.md         # Full game design specification
```

---

## Browser Support

- **iOS Safari 16.4+** — full PWA support including home screen install and orientation lock
- **Chrome / Edge / Firefox (desktop)** — playable; orientation lock may be ignored
- **Android Chrome** — playable and installable

---

## Credits

* Game design: Leslie
* Mythology: Ancient Greece
* Development: Claude Code. Code written completely by Claude Code using Sonnet 4.6 Medium, 2x 5 hr dev sessions on $20/month plan. Leslie's dad enabled GH Pages, clicked the button under Source labeled "GitHub Actions" and clicked Save.
