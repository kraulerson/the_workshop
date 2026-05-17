# The Workshop

A visual/spatial project dashboard for Karl Raulerson's homelab ecosystem. Built as a cognitive accommodation, not a generic project manager.

## What this is

Karl's mind works best with **visual and physical separation** between projects. Workshop stations as a mental model: Station 1 is Alden, Station 2 is Solo Orchestrator, etc. Each project gets dedicated mental real estate so context-switching has anchor points.

This dashboard is a software approximation of that workshop. A 360° lobby with doors around you (Zillow-style virtual tour). Click a door to walk into that project's room. Each room has its own visual character — wood, brick, metal, machining, code lab, library — assigned once when the project is created and never changing.

## Why this exists

> "I have too many projects to keep track of mentally and what their status is plus, I have a ton of proposed projects that I want to accomplish as well. The issue is I'm getting lost because everything is within a single computer's monitor and my brain works best by both visually and physically separating the projects from one another so that my mind can switch gears to the active project. Because they are visually in the same physical space, I have a harder time recalling where I was at because I don't have a mental anchor point." — Karl

The cognitive accommodation reframe is the core design constraint. Decorative immersion fights the function; spatial anchoring serves it. The room metaphor is load-bearing, not aesthetic.

## Status

**Phase: prototype.** Verified project data reconciled against GitHub MCP on 2026-05-16. Panorama layout decided in principle (Three.js + equirectangular, hybrid procedural/image textures). Repo scaffolding chosen as light/internal-only — full Solo Orchestrator discipline deferred unless this graduates to load-bearing infrastructure.

## Structure

```
the_workshop/
├── README.md                — this file
├── LICENSE                  — MIT (private repo, formal anyway)
├── .gitignore               — Node/web standard
├── data/
│   └── projects.json        — source-of-truth project data
├── prototype/
│   └── index.html           — panorama prototype (see docs/adr/0001)
└── docs/
    ├── adr/
    │   └── 0001-design-decisions.md
    └── room-catalog.md      — 50 room type specifications
```

## Major decisions

See `docs/adr/0001-design-decisions.md` for the full chain. Headlines:

- **Workshop metaphor with doors and rooms**, not card grid or kanban
- **Deterministic-on-first-assignment room types** — once a project gets a room, it keeps it forever
- **50 room types defined, 20 built initially**, remaining built as projects graduate from annex to floor
- **Three.js + equirectangular panoramas** for the cinematic 3D feel
- **Hybrid texture approach** — procedural geometry + CC0 image textures for material richness
- **Internal-only, no external access** — security audit framework deferred

## Lineage

This project itself is Station 11 (`command-center` in projects.json). It tracks its own status. If the dashboard doesn't work for Karl, the dashboard will say so on its own action items.
