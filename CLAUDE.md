# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

A self-contained, single-file Fortune Wheel web app (`index.html`). No build step, no dependencies, no package manager — open the file directly in a browser to run it.

## Running the app

Open `index.html` in any modern browser. There is no server required.

For a quick local server (e.g. to avoid browser file:// restrictions):

```
npx serve .
```

## Architecture

Everything lives in `index.html` in three inline sections:

- **`<style>`** — all CSS, including the dark theme, wheel container, result banner, and editor grid
- **`<body>`** — static HTML scaffolding (canvas, buttons, editor container)
- **`<script>`** — all game logic

### Key data structures

- `masterData[]` — array of 8 objects `{ label, desc, color }`, one per original segment; always length 8 and drives the editor inputs
- `segments[]` — the active wheel segments (subset of masterData); entries are spliced out as they are won

### Rendering

The wheel is drawn on a `<canvas>` element via `draw(rot)`. Each call clears and redraws all active segments using `segments[]` at the given rotation angle. Text wrapping for segment labels is done manually by measuring word widths against a max-width threshold.

### Spin lifecycle

1. `spin()` — calculates a random target angle (extra full rotations + random landing), then drives an `easeOut` animation via `requestAnimationFrame`
2. `finalize()` — once animation ends, maps the pointer position (top, −π/2) back to a segment index; calls `showWinner()`, then splices the winner out of `segments[]` after a 2.2 s display delay

### Segment removal

When a segment is removed, the remaining segments automatically fill the full circle because `draw()` always divides `2π` equally among the current `segments[]` length.
