# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page Tetris implementation in vanilla JavaScript (HTML5 Canvas + CSS). No frameworks, no build step, no dependencies, no `package.json`, no test suite.

## Running the game

Just open `index.html`, or serve the directory statically:

```bash
python3 -m http.server 8000
# or
npx serve .
```

Then visit `http://localhost:8000`. There is no build/lint/test command — changes to `game.js`, `index.html`, or `style.css` take effect on page reload.

## Architecture

Three files, all logic lives in `game.js` (~300 lines, no modules):

- **`index.html`** — DOM shell: `<canvas id="board">` (300×600, the 10×20 grid at `BLOCK`=30px/cell), a side panel (`score`/`lines`/`level` spans, `<canvas id="next-canvas">` for the next-piece preview), and a `#overlay` div reused for both PAUSE and GAME OVER states.
- **`style.css`** — dark/retro theme, no build step (plain CSS, no preprocessor).
- **`game.js`** — all game logic, driven by module-level mutable state (`board`, `current`, `next`, `score`, `lines`, `level`, `paused`, `gameOver`, `dropInterval`, etc.) rather than a class or store.

### Core flow

```
init() → createBoard() + randomPiece() → spawn() → requestAnimationFrame(loop)

loop(ts)
  ├─ accumulate dt against dropInterval; drop piece one row or lockPiece()
  ├─ draw() — grid, locked board, ghost piece, current piece
  └─ requestAnimationFrame(loop)

keydown → move / tryRotate() / softDrop() / hardDrop() / togglePause()
```

- **Board model**: `ROWS × COLS` matrix; each cell is `0` (empty) or a piece color index `1–7`.
- **Pieces**: square matrices in `PIECES`; rotation via `rotateCW` (transpose + reverse rows), with wall-kick offsets `[0, -1, 1, -2, 2]` tried in `tryRotate`.
- **Collision** (`collide`): bounds + already-locked-cell check.
- **Locking** (`lockPiece`): `merge()` current piece into `board` → `clearLines()` → `spawn()` next piece (spawn colliding immediately triggers `endGame()`).
- **Line clearing** (`clearLines`): scans bottom-up, splices completed rows, unshifts empty rows at top, recalculates `level` (every 10 lines) and `dropInterval` (`max(100, 1000 - (level-1)*90)` ms).
- **Scoring**: `LINE_SCORES = [0, 100, 300, 500, 800]` × `level`; hard drop adds 2 pts/row traveled, soft drop 1 pt/row.
- **Ghost piece**: `ghostY()` projects the current piece straight down to its landing row; drawn at `globalAlpha = 0.2`.

### Tunable constants (top of `game.js`)

`COLS`, `ROWS`, `BLOCK`, `COLORS`, `LINE_SCORES`, `dropInterval` (initial). If `COLS`/`ROWS`/`BLOCK` change, update the `<canvas id="board">` `width`/`height` in `index.html` to match (`COLS×BLOCK` × `ROWS×BLOCK`).
