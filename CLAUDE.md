# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Vanilla JS Tetris (HTML5 Canvas). No package.json, build, linter, or tests. The README and UI text are in Spanish; keep new user-facing strings in Spanish.

## Running

Open `index.html` directly, or serve statically (e.g. `python -m http.server 8000`) and visit `http://localhost:8000`.

## Architecture

All logic lives in `game.js` (one classic script, `'use strict'`, global state, no modules). `index.html` supplies the DOM ids it looks up at the top of the file (`board`, `next-canvas`, `score`, `lines`, `level`, `overlay`, `overlay-title`, `overlay-score`, `restart-btn`, `theme-toggle`); renaming any id requires updating `game.js`.

- Board is a `ROWS×COLS` matrix of `0` or a piece-type index 1–7; the same index selects the entry in `COLORS` and `PIECES` (index 0 is `null` in both).
- State is module-level `let` variables, reset in `init()`. `restartBtn` calls `init()`.
- Flow: `loop` (requestAnimationFrame) accumulates `dropAccum` against `dropInterval` and calls `lockPiece()` → `merge()` → `clearLines()` → `spawn()`. `spawn()` calls `endGame()` if the new piece collides immediately.
- Input is a single `keydown` handler; it ends with `updateHUD()`. Pause cancels the animation frame and re-enters `loop` on resume.
- Rotation (`tryRotate`) uses simple horizontal kicks `[0,-1,1,-2,2]`, not SRS.
- Changing `COLS`, `ROWS`, or `BLOCK` requires updating the `<canvas id="board">` width/height in `index.html` (`COLS*BLOCK` × `ROWS*BLOCK`). The next-piece canvas is 120×120 with its own 30px block size hardcoded in `drawNext`.
