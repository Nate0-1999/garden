# The Cube — unified work visualizer (design notes, pending approval)

Status: CONCEPT — mock built for human approval 2026-07-20; not yet law.
Owner: human + assistant. Relay agents: read-only context, build nothing
from this file until it lands in SPEC (future ADR-009/016 amendment).

## Concept

One underlying object — the work (session tree + agents + memory + budget +
time) — projected onto SIX faces of a rotatable cube. Rotation = changing
projection, never changing data. Selection is SHARED: picking a thread,
branch, or agent on any face highlights it on every face and can open it
(navigation), honoring Invariant 14 — the Cube is a map you go to; it never
demands. Cards arrive only in the Deck.

## The six faces

1. DECK   — the flashcard deck (attention surface; judge-released cards).
2. FARM   — the Ant Farm: agents in SPACE — colored ants at their current
            directories in the workspace tree (movement law makes it live).
3. ROOTS  — the search tree (MCTS view): branches from the root problem;
            EXPAND/FORK/GRAFT/PRUNE visible; worktrees = branches.
4. LEDGER — the capital alluvial: budget flowing through stratagems (time).
5. PALACE — memory: injections everywhere; selecting anything traces its
            injection_events (memories, features, scores, outcomes).
6. SCORE  — time/context: per-agent context bars, run/gate/judge timeline.

## Encodings (ROOTS face)

- Edge THICKNESS = total tokens/cost expended pursuing the branch.
- Branch OPACITY = current expected value — WITH A FLOOR (~0.25): pruned
  and low-EV branches must stay visible for audit/lessons. Alternative to
  evaluate in mock review: EV as node-fill on a sequential ramp instead of
  opacity (opacity fights dark/light themes and overlap legibility).
- Pruned = dashed + stub node; Graft = two edges merging (brass).
- Fleet color = stratagem/agent identity, consistent across all faces.

## Memory-injection traceability (hard requirement)

The PALACE face and all trace views are pure CONSUMERS of the
injection_event log — the same append-only table the learning loop reads.
Zero contact with the scoring path; zero schema change needed (thread_id,
memory_id, features, score, outcome already logged per event). Rendering
can never perturb injection optimization.

## Devil's advocate (raised at concept stage)

- Literal 3D cube = demo-candy risk. Compromise: cube as navigation
  metaphor (face transitions, corner cube widget) but each face renders
  FLAT when focused. 2.5D, not WebGL.
- Opacity-as-EV hides exactly the branches audits need; floor + ramp
  alternative noted above.
- Six equal faces would violate least-attention; resolved: only DECK
  demands, the rest are watchable.
- Scope: nothing here touches M1; H4 ships the minimal thread list + chat
  as charged. Cube is M2/M3 viz law after approval.

## v2 direction (human walkthrough, 2026-07-20)

Videogame-grade rendering; the viz IS the app (ComfyUI pattern): a large
CENTER STAGE (the cube's current face) with satellite panels, navigation by
clicking into the visualization. Aesthetic: sci-fi HUD — X-wing pilot
display / Nostromo terminal: near-black phosphor ground, wireframe
structures, mono readouts, angular clipped panels, restrained glow.

Layout law (from the three scenarios):
- LEFT RAIL = the deck, always present, ordered by completion/wait time;
  selecting an agent on stage tops its card; cards carry a DRAFT response
  (conductor-prepared, clearly machine-labeled) the human tweaks and
  fires; after firing, the rail auto-advances to the longest-waiting.
- CENTER STAGE = the cube face, large: FARM (projects + agents as ants in
  wireframe directory trees) ⇄ rotate ⇄ ROOTS (selected agent's subagents
  fanned in full color; other subtrees low-opacity ghosts).
- RIGHT RAIL (on selection) = inspector: context-window visualizer,
  injected memories with one-tap pop-off (mid_thread_removed), and
  SUGGESTIONS to add — near-misses surfaced live (both already in the
  event log; zero new data needed).
- BOTTOM-RIGHT overlay = Palace minimap: curator agents drifting through
  the memory field (M3), LIVE·CLOUD connection status.

Answers to OQ-C1/C2 implied: stage+rails replaces "default face" (deck is
a rail, not a face); cube is GLOBAL (fleet-wide; scenario 3 hops projects).
OQ-C3 (EV: opacity-floor vs node ramp) still open.
New concept to codify with ADRs: DRAFT RESPONSES on judge-released cards —
attention spent editing, not composing. Guard: drafts are labeled machine
text; a card is never auto-fired.

## v3 principle: framework, not furniture (Ableton doctrine, 2026-07-20)

The Cube is a RACK, not a fixed layout. The human's described arrangement
(stage + deck rail + inspector + minimap) is the DEFAULT SET, not the law.
Users add, remove, rearrange, and author visualizers and panels.

Plugin architecture (the "Max for Live" layer), enabled by existing law:
- Every visualizer — built-in or user-authored — is a consumer of exactly
  three surfaces: (1) the C.7 envelope EVENT STREAM (live), (2) the QUERY
  surface (injection_event log, session tree, board/ledger state — all
  append-only reads), (3) the shared SELECTION BUS (one selection, every
  panel follows). ADR-014's "UI renders only from the event stream" is
  what makes third-party panels possible with zero core hooks.
- Panels declare slots (stage face / rail / overlay), receive fleet-color
  and theme tokens, and MUST honor Invariant 14: no plugin may demand —
  cards remain the Deck's monopoly. A plugin that notifies is rejected at
  the API, not by review.
- Layouts are savable/sharable PRESETS (per project, per mode — a Duet
  set vs a Symphony set), like Ableton sets.
- Distribution rides the ADR-013 seam philosophy: plugins are wrapped,
  versioned, sandboxed to their three surfaces; a broken plugin can only
  break its own panel.

## v4 face aesthetics (human direction, 2026-07-20)

- FARM: a literal ant farm — the WHOLE directory tree as chambered burrows:
  directories = chambers, files = small empty cells, tunnels connect.
  Scroll to zoom in/out. The colony floats in space like a CAD/SolidWorks
  object on a black field with a faint white line grid.
- ROOTS: organically root-like — meandering, not smooth beziers (seeded
  jitter, deterministic per branch). COMPLETE: every agent across every
  project grows a root/sankey flow in parallel; dead roots (pruned/stalled
  loops) visibly desiccated. Clustered by project.
- TIPS (new face — geometrically OPPOSITE the Farm): the roots seen
  end-on — a cross-section of root tips in per-project grids; each tip is
  a start for the next round. Gives the cube true spatial logic:
  Farm (surface) → Roots (growth) → Tips (frontier).
- CONSOLE (bottom strip): car-dashboard dials — tokens/min, main agents,
  total agents, spend past hour. Rate data suits needles; totals as
  odometer-style readouts.
- PALACE (full scene, future workstream): architecturally modern but
  abstract — pale monumental geometry floating in void-mist, gold accents
  (Destiny Trials-of-the-Nine ENERGY, original design, no copied assets).
  Curator agents render as small faceted drone-shells with a glowing core
  (Ghost-INSPIRED, original silhouette), visibly combining/separating
  memory units, driven by live feeds (curator ops = events like any other).

## v5: the time axis — the project as organism (2026-07-20)

The SolidWorks ROLLBACK BAR is the missing control: a time scrubber under
the stage. Drag it and the whole organism rebuilds to that moment — the
colony's chambers appear and vanish (files added/removed), roots extend
and die, tips bloom, the Palace restructures. Release at NOW and it's live
again. Time-lapse mode: replay a day of work in seconds — the project
visibly grows like a living thing (which completes the Garden metaphor:
this is a terrarium, and the work is alive).

Architectural cost ≈ zero BY CONSTRUCTION: every surface is append-only
and timestamped (envelope events, git/shadow-git history, session tree,
injection_event log, checkpoints per human turn). Rendering the past is a
query, not a feature.

ONE law-level requirement to carry into the Cube ADR: every visualizer
query surface accepts `as_of` (timestamp/checkpoint ref), so any face can
render any moment. The scrubber, time-lapse, and live view are then one
mechanism at three speeds — and the scrubber composes with rewind/fork:
scrub to a moment, fork from it (ADR-016 tree).

## v6: rendering stack + extensibility architecture (2026-07-20)

STACK (amends ADR-008's PROPOSED details; H4/M1 chat shell unaffected):
- Split at the physics boundary: ONE WebGL/WebGPU stage scene for the Cube
  (three.js + react-three-fiber inside the existing React+Vite app; TSL
  shaders dual-target WGSL/GLSL so WebGPU runs where available, WebGL
  falls back automatically); React DOM for all rails/panels/text. Text
  never enters the canvas — HUD labels are DOM overlays.
- Scale: instanced meshes (ants, cells, root segments), compute shaders
  for living systems (curator swarms, root growth). Discipline: refs +
  useFrame, never per-frame setState.
- One shared store (zustand): the event-stream reducer + selection bus +
  query cache. Stage, rails, and every plugin are subscribers — the store
  IS the plugin three-surface contract materialized.
- Client state is event-sourced with keyframe snapshots (IndexedDB):
  time-scrub = nearest keyframe + re-reduce; client twin of `as_of`.
- Accessibility: every stage view keeps a parallel DOM/table rendering;
  keyboard paths exercised by B.6 rule 7 tests.

RUNTIME TARGET:
- Chromium-class browsers ONLY through M4 (Chrome app mode `--app=` for
  desktop feel, PWA install at M3 — both already law in ADR-008/009).
  Single-user product: no Safari/Firefox compat tax for relay agents;
  revisit at multi-principal. Chromium gives WebGPU-first, FS Access API,
  OffscreenCanvas, import maps.

PLUGIN ARCHITECTURE (Ableton doctrine made concrete):
- Plugins are ES MODULES with a manifest (name, version, slots, entry);
  loaded via dynamic import()/import maps — no bundler at runtime. The
  harness daemon serves user plugins from a local plugins/ dir alongside
  web/ (later: a directory/marketplace).
- Two trust tiers:
  (1) PANEL plugins (rails/console/card renderers): sandboxed IFRAMES,
      postMessage bridge carrying ONLY the three surfaces (events, query,
      selection). Strong isolation; a broken/hostile plugin owns nothing
      but its rectangle. No notify API exists — Invariant 14 structurally.
  (2) FACE plugins (stage): cannot iframe into the scene, so faces are
      DATA-DRIVEN — a declarative scene schema (nodes/edges/instances/
      curves/encodings) interpreted by our renderer; plugins emit data,
      never touch three.js. Keeps the contract and the render loop ours.
- Plugin API is VERSIONED (surfaces v1); upstream/ecosystem churn is
  contained exactly like ADR-013 wraps pydantic-ai.
