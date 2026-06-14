# Cool Map Generator

An infinite, isometric world generated on the fly using **Wave Function Collapse (WFC)**, layered with a noise-driven biome system. Pan around with the mouse and watch new terrain generate seamlessly as you explore.

## Features

- **Wave Function Collapse terrain generation** — tiles collapse based on socket-compatibility rules, producing coherent terrain (no floating islands or impossible transitions).
- **Infinite world** — terrain is generated chunk-by-chunk on demand and cached for the session. Adjacent chunks share border constraints so transitions stay seamless.
- **Biomes** — temperature and moisture fields (driven by fractal Brownian motion noise) classify each region into one of six biomes (ocean, tropical, temperate, arid, tundra, highland), each biasing which tiles are likely to appear.
- **Tile clustering** — tiles with a `clusterBoost` prefer to sit next to others of the same type, forming natural-looking regions like oceans, forests, and mountain ranges.
- **Isometric rendering** — chunks are drawn on an HTML canvas with correct depth ordering via a world-row sweep.
- **Pan controls** — click and drag to move the camera around the world.

## Tech Stack

- [TypeScript](https://www.typescriptlang.org/)
- [Vite](https://vitejs.dev/) for dev server and bundling
- HTML5 Canvas (no rendering framework/engine)

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) (with npm)

### Install dependencies

```bash
npm install
```

### Run the dev server

```bash
npm run dev
```

Open the printed local URL in your browser.

### Build for production

```bash
npm run build
```

### Preview the production build

```bash
npm run preview
```

## Controls

- **Click and drag** on the canvas to pan the camera across the world.

## Project Structure

```
src/
├── main.ts              # Entry point — wires input, logic, and rendering together
├── data/
│   ├── tiles.ts          # Tile definitions, sockets, weights, and compatibility rules
│   ├── biomes.ts          # Biome definitions and noise-based biome classification
│   └── mock-tiles.ts      # Earlier mock tile set (pre-biome system)
├── logic/
│   ├── types.ts           # Shared types (Cell, Grid, TileDefinition, Chunk, ...)
│   ├── tile-registry.ts   # Registry of tiles and their adjacency/socket rules
│   ├── wfc-engine.ts      # Core WFC algorithm: superposition, collapse, propagation
│   ├── chunk-manager.ts   # On-demand chunk generation and caching for the infinite world
│   └── noise.ts           # Hashing and fractal Brownian motion (fBm) noise functions
├── render/
│   ├── canvas.ts          # Canvas setup and resize handling
│   ├── camera.ts          # Camera state and viewport calculations
│   ├── isometric.ts       # Isometric projection math
│   └── grid-renderer.ts   # Renders visible chunks with correct depth ordering
└── input/
    └── mouse.ts           # Mouse drag tracking for camera panning
```

## How It Works

1. **Tiles** are defined with sockets on each side (north/south/east/west). Two tiles can be neighbors only if their facing sockets are compatible — terrain forms a gradient from `deep_water` → `water` → `sand` → `grass` → (`forest`/`hill`/`dirt`/`marsh`) → `mountain` → `snow`.
2. **WFC** starts every cell in "superposition" (all tiles possible), repeatedly collapses the lowest-entropy cell to a single tile, and propagates that constraint to neighbors via BFS until the whole chunk is resolved (or retried on contradiction).
3. **Biomes** sample two independent noise fields (temperature and moisture) per world cell to bias tile weights toward biome-appropriate terrain, without ever fully excluding a tile.
4. **Chunks** are generated lazily as the camera moves. When a new chunk borders an already-generated one, the neighbor's edge tiles seed the new chunk's WFC run so terrain transitions seamlessly across chunk boundaries.
5. **Rendering** projects the world grid isometrically and draws visible chunks in a depth-correct sweep.
