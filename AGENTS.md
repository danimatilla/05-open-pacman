# AGENTS.md

Pac-Man clone in vanilla JS/HTML/CSS. It is a learning project for spec-driven development (see `README.md`).

## Run

No build, no dependencies, no tests, no linter. Open `src/index.html` directly in a browser, or serve `src/` with any static server.

## Architecture

Plain `<script>` tags, **no ES modules and no bundler**. Files communicate through globals assigned to `window`. `src/index.html` fixes the load order and it matters:

`js/maze.js` → `js/game.js` → `js/render.js` → `js/main.js`

- `maze.js`: maze data + constants (`MAZE`, `TUNNEL_ROW`, `PACMAN_START`, `GHOST_STARTS`).
- `game.js`: state and rules (`createGame`, `update`, `DIRS`). No DOM access.
- `render.js`: all canvas drawing (`draw`). Reads `game.grid`, never `MAZE`.
- `main.js`: game loop, keyboard, overlay screens. Uses the globals above.

When adding a file, add its `<script>` tag in the right order and export via `window.X`; there is no import system.

## Conventions an agent must not guess

- Tile encoding: `0` empty/walkable, `1` wall, `2` dot, `3` ghost-pen door (walls Pac-Man but not ghosts).
- The maze is 28x31 cells, authored as 31 strings of 28 chars in `maze.js` and parsed to numbers.
- `MAZE` is the pristine matrix. `createGame()` copies it into `game.grid`; dots are eaten only in `game.grid`. Never mutate `MAZE`.
- Movement is cell-aligned: actors snap to integer cells when aligned, then advance by fractional speed per frame (`PACMAN_SPEED`, `GHOST_SPEED`). Coordinates are cell units, origin top-left.
- Row `TUNNEL_ROW` (14) wraps horizontally at the edges.
- Canvas is `28*20` by `31*20` (`TILE = 20`); keep it in sync with the maze dimensions.
- Identifiers in English, comments and UI text in Spanish (matches the global `AGENTS.md`).

## Spec-driven workflow

Large features go through the repo-local skills in `.agents/skills/` (installed via `skills-lock.json`, source `Klerith/fernando-skills`):

- `/spec` — designs a spec, writes it to `specs/NN-slug.md` (folder does not exist yet; first spec is `01-`), state starts `Draft`.
- `/spec-impl` — implements an `Approved` spec; expects git branches (`spec-NN-slug`) and an optional `specs/.spec-config.yml`.

`spec-impl` assumes a git repository, but this directory is not one yet.
