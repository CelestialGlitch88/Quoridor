# Quoridor Online

A self-contained, single-file implementation of **Quoridor** — the abstract strategy board game where you race to the far side of the board while dropping walls to slow your rival down. Play pass-and-play against a friend, or against a local AI bot with an adjustable ELO slider.

No build step, no dependencies to install, no server required — it's one `.html` file. Open it in a browser and play.

---

## Features

- **Two game modes**
  - **vs Bot** — play a local AI opponent
  - **vs Friend** — pass-and-play on one device
- **Adjustable AI strength** — a 400–2400 ELO slider (Beginner → Grandmaster) that continuously scales the bot's move randomness, whether it considers wall placements at all, and how far ahead it looks
- **Side swap** — choose to play as Deepslate (moves first) or Calcite
- **Full, correct Quoridor rules**
  - Orthogonal pawn movement
  - Straight and diagonal jumps over an adjacent rival
  - Wall placement with overlap/crossing validation
  - Path-blocking prevention — a wall can *never* fully seal off anyone's route to their goal (validated with pathfinding, not just a local check)
- **Game log** — a running, per-turn record of every move and wall placement
- **Undo** — steps back one full turn (both plies, if you undo mid-bot-turn)
- **Win celebration** — a burst of colored streaks and dots in the winner's color when someone reaches the far rail
- **Input support** — mouse, touchpad, and touchscreen all work identically; arrow keys move your pawn
- **Responsive, animated UI** — dark, space-themed interface with glowing accents, smooth pawn transitions, and wall-placement animations
- Respects `prefers-reduced-motion` (disables animations/celebration accordingly)

---

## How to play

**Goal:** be the first to move your piece to the opposite side of the board.

- **Move:** click/tap a highlighted cell, or use the arrow keys. You can only move to an orthogonally adjacent, unblocked cell.
- **Jump:** if your rival is directly adjacent to you with no wall between you, you can jump straight over them. If a wall or the edge of the board blocks that straight jump, you can instead jump diagonally to either side of them.
- **Wall:** click/tap any gap between cells to drop a wall there (10 per player). Walls block movement across the two cell-edges they cover. A wall placement is rejected if it overlaps or crosses an existing wall, or if it would seal off *any* player's last path to their goal — you can slow your rival down, but never fully trap them.

---

## Controls

| Action | Input |
|---|---|
| Move pawn | Click/tap a lit cell, or Arrow keys |
| Place wall | Click/tap a gap between cells |
| Preview a wall | Hover over a gap (mouse/touchpad) — green if legal, red if not |
| Undo | Undo button |
| Restart | New Game button |
| Switch mode | "vs Bot" / "vs Friend" toggle |
| Change bot strength | ELO slider (applies on release, starts a new game) |
| Choose your side | "Play Deepslate" / "Play Calcite" toggle (vs Bot only) |

---

## The AI opponent

The bot doesn't call out to any external model — it runs entirely client-side using breadth-first search and a small evaluation search, so moves are instant with no network dependency:

1. **Instant win check** — takes a winning move immediately if one's available.
2. **Candidate generation** — every legal pawn move, plus wall placements that block an edge along the opponent's *current* shortest path to their goal (found via BFS). This keeps the search focused instead of trying all ~128 possible wall slots every turn.
3. **Evaluation** — each candidate is scored as `(opponent's shortest-path length) − (bot's own shortest-path length)`, with a penalty for leaving the opponent one move from winning and a small bonus for holding more walls.
4. **Lookahead (higher ELO only)** — for its best few candidates, the bot simulates the opponent's best reply and discounts moves that hand back too strong a follow-up — a lightweight two-ply search.
5. **Randomness** — scales down smoothly as ELO increases; low-ELO play is noisy and often suboptimal, high-ELO play is close to deterministic.

---

## Running it

There's nothing to install. Either:

- Hop onto `https://celestialglitch88.github.io/Quoridor`, or
- Double-click the `.html` file to open it in your default browser, or
- Serve it with any static file server, e.g. `python3 -m http.server` from the folder it's in, then visit `http://localhost:8000/quoridor.html`

Fonts (Silkscreen / Space Grotesk / Inter) load from Google Fonts, so an internet connection is needed for the intended look — the game itself still works fully offline, just with fallback system fonts.

---

## Tech

- Vanilla HTML, CSS, and JavaScript — no frameworks, no build tools, no external JS libraries
- Board and pieces rendered as inline SVG
- All game logic (movement, jumps, wall validation, pathfinding, AI) lives in a single `<script>` block

---

## License

Free to use, modify, and share as you like, when not used for commercial purposes.
