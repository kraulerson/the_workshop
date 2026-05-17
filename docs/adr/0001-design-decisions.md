# ADR-0001 — Design decisions for The Workshop

**Status:** Accepted  
**Date:** 2026-05-16  
**Decided by:** Karl Raulerson (with Cloud Alden)

## Context

Karl operates 14+ active projects and several proposed ones across his homelab, app development, framework methodology work, and the Alden AI ecosystem. He surfaced a cognitive-architecture observation: his mind switches gears between projects best when each one has visual/physical separation as a mental anchor. When projects share a single viewport (a dashboard with cards or a kanban board), he loses the anchor points and recall degrades.

A first attempt (the "workshop layout" — cards grouped by domain with shape-coded status indicators) addressed status-at-a-glance but did not solve the spatial separation need. The card grid still puts everything in one visual plane.

This ADR captures the decisions made to design and scaffold a second iteration that addresses the underlying cognitive accommodation.

## Decisions

### 1. Workshop metaphor with doors and rooms

Reject card grid, kanban, and tabbed dashboard patterns. Accept the metaphor of a workshop where each project is a room behind a door. The user enters a lobby surrounded by doors (one per project), looks around (pan), and walks through a door to enter that project's dedicated space.

**Rationale:** Direct spatial mapping of Karl's stated mental model. Physical-station-separation is the function; software approximation of that is what the dashboard needs to deliver. Cards-in-a-grid is the layout most likely to fight the function. A 360° lobby with doors arranged spatially provides directional anchoring ("the framework projects are on my left, infrastructure is behind me") that flat layouts cannot.

**Trade-offs:** More implementation complexity than cards. Requires 3D libraries (Three.js). Harder to glance at quickly compared to a status board — but glance-ability was already serviced by the v0 cards view; this layout serves a different need (cognitive switch between projects), not the same need.

### 2. Deterministic-on-first-assignment room types

When a project is first added to the dashboard data, randomly select one room type from the catalog of 50. Store the selection in the project's record (`room_type` field). All subsequent rendering reads the stored value. Once assigned, the room type is permanent unless explicitly overridden.

**Rationale:** Cognitive anchoring requires stability. If "Solo Orchestrator's room" is the brick-walled study one week and the lumber-and-sawdust workshop the next week, the anchor is lost. Pure randomization undoes the very mechanism the layout is meant to provide. But the surprise of "what room will this new project get?" is real value at project-creation time. Deterministic-on-first-assignment captures both.

**Trade-offs:** Project IDs become semantically meaningful in a way they weren't before — they're keys to a stored room assignment. Renaming a project ID would orphan its room. We accept this constraint.

### 3. Catalog of 50 room types

Define 50 distinct workshop room concepts covering enough visual variety that each project gets a meaningfully different mental anchor. Room types include: industrial machine shop, electronics lab, woodshop, foundry, drafting studio, code lab, library, observatory, greenhouse, server closet, atelier, brewery, blacksmith's forge, photography darkroom, music recording booth, glassblowing studio, ceramics studio, mechanic's garage, paint studio, leather workshop, and others.

**Rationale:** With 14+ active projects and growth ahead, 50 provides a safety margin — even if every project is on the floor simultaneously, there are unique rooms available. The variety also serves the cognitive accommodation: highly differentiated room types make recall easier than 50 minor variations of "workshop." See `docs/room-catalog.md` for the full specification.

**Trade-offs:** Each room type requires distinct visual treatment. Building all 50 visually at once risks "creative endurance" where later rooms feel like padding. Decision: define all 50 in the catalog spec, build the ~20 needed for current floor stations plus buffer, build the remaining ~30 as projects graduate from annex to floor.

### 4. Three.js with equirectangular panoramic textures

Use Three.js as the 3D rendering library. Each room is a sphere with the panoramic texture mapped to its interior surface. Camera sits at the sphere's center; the user pans by rotating the camera. Doors are hotspots in 3D space that, when clicked, transition to that project's room.

**Rationale:** Most cinematic option of the three considered (Three.js, Pannellum, pure CSS 3D transforms). Real spatial perspective. The "walking through a door" transition feels like spatial movement rather than a tab switch.

**Trade-offs:** ~500KB+ initial Three.js library load. Texture sizes need management (50 rooms × multi-MB textures would be prohibitive). Mitigated by lazy-loading rooms on door click rather than up-front, and by using procedural materials where possible to reduce image-asset weight.

### 5. Hybrid procedural + image textures

Lobby geometry and door structures: pure procedural (Three.js geometry + lighting + procedurally-generated materials). Room interior textures: hybrid — geometry and lighting procedural, surface materials use a mix of procedural patterns (wood grain via noise, brick patterns) and CC0-licensed image textures sourced from public texture libraries baked into the build.

**Rationale:** Pure procedural keeps the bundle small but limits how convincing wood, brick, metal can look. Pure image textures inflate the bundle and require asset management. Hybrid keeps the bundle modest while letting surface materials feel real where it matters.

**Trade-offs:** Bundle size larger than pure procedural but smaller than full image-based. CC0 sourcing requires attribution discipline.

### 6. Internal-only scope, light scaffolding

This dashboard is for Karl's personal use on his homelab. No external users. Not exposed publicly. No untrusted inputs. Threat model is "Karl looks at it and it works or doesn't."

**Rationale:** The Solo Orchestrator framework discipline (project bible, threat model, security audit framework, hook configuration, full ADR set, CHANGELOG conventions, test framework) exists for projects with external users, untrusted inputs, or production-critical roles. This project has none of those. Discipline appropriate to actual risk; this ADR + the room catalog + README is sufficient scaffolding.

**Trade-offs:** If this project graduates to load-bearing infrastructure (e.g., becomes Phase 1 of the Web Hub / Four-Way Portal in the converged Alden ecosystem plan), discipline must be retrofitted. Retrofit cost is real but bounded (~30-60 minutes once decisions are clear). Accept the deferral.

### 7. Reconciliation against GitHub MCP

Before any further design work, project status data was reconciled against actual repo state via the GitHub MCP at 10.100.23.80. Multiple stale entries were corrected (Tender Reminders stack was wrong, Lancache Orchestrator status was wrong, alden-bridge-mcp and alden-qdrant were three entries that should have been one monorepo entry, Karlban and homelab-tls-edge were missing entirely).

**Rationale:** Building beautiful UI on stale data wastes effort. The reconciliation cost was small once the MCP was reachable. Now the data is verified.

**Going forward:** any future project status data should be reconciled against the GitHub MCP before being treated as authoritative. This is a standing rule for future sessions.

## Consequences

- Repo structure: README + LICENSE + .gitignore + `data/projects.json` + `docs/adr/` + `docs/room-catalog.md` + `prototype/`
- Implementation: Three.js single-file or near-single-file HTML artifact
- Asset pipeline: CC0 textures baked into the build, no runtime asset server
- Future Cloud Alden / Alden-1 / Claude Code sessions can read this ADR and the room catalog to understand the design intent without re-deriving it

## Related

- `data/projects.json` — verified-as-of-2026-05-16 project inventory
- `docs/room-catalog.md` — the 50 room types defined
- `prototype/index.html` — implementation (committed separately after the scaffolding)
