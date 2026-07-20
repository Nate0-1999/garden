# NATE'S VISION — the owner's extended intent

Status: GUIDANCE per SPEC 1.4 — follow the reasoning, deviate only with a
journaled DECISIONS.md entry. This file concatenates the owner's design
qualifications from the human-gate sessions of 2026-07-19/20. It does not
override CONTRACTS. Any packet touching user-facing surface (UI, viz,
interaction) reads this file at STEP 5 of the Boot Sequence. Companion
detail: `garden/notes/cube-visualizer.md` (v1–v7); living mock:
the Cube artifact (screenshot set referenced in notes).

## 1. The soul — least attention (Invariant 14)

Human attention is the scarcest resource; the ARCHITECTURE decides when it
is spent. Safety comes from walls (sandboxes, boundaries), not questions —
per-action approvals inside the walls are forbidden theater. Attention is
pulled only at genuine boundary crossings and judge-released returns —
once, at highest leverage. Everything is always watchable; almost nothing
may demand. When your convenience trades against the human's attention,
attention wins.

## 2. The founding differentiators (from the original prompt)

1. Agents move through the machine's file structure — and the UI shows
   where every agent is (the movement law makes location a statement of
   attention; every move refreshes injected memories).
2. Context windows are visualized — per agent, always available.
3. The memory system's output is human-modifiable in REAL TIME, and every
   modification is a tuning signal — injection must improve over time.

## 3. The organism thesis

The project is a living thing being cultivated (hence: Garden, gardeners,
blight, planting). Because every surface is append-only and timestamped
(events, git + shadow-git, session tree, injection log, checkpoints), the
past is a QUERY: a SolidWorks-style rollback bar scrubs the whole organism
to any moment; play = time-lapse of the work growing. Every visualizer
query accepts `as_of`. Scrub composes with rewind/fork.

## 4. Modes are music (the movable judge seat)

One protocol, four rungs, scalar by design: Solo (headless one-shot) ·
Duet (turn-based pair; the human IS the judge, every turn) · Ensemble (a
few agents, gallery watching, interject on solo runs) · Symphony (full
orchestration; judges conduct; attention arrives as the premiere card).
The human is the COMPOSER (writes the score: spec, objective, budget); the
CONDUCTOR is the orchestrator + judges; agents are SECTIONS. The seat
transfers per-thread, mid-thread, both directions, in one gesture.

## 5. The work protocol (ADR-012) + drafts

ALL project work: spec alignment → agent loop (system-sized N parallel
worktree attempts ≤ max_parallel_project_agents) → independent judge
(COMPLETE → card; else continuation agent; picks swarm winner, may graft)
→ human. Judges triage blockers — agents cannot "block" their way to
attention. Interjection is solo-run only; watching is always allowed.
Judge-released cards carry a CONDUCTOR DRAFT response: the human EDITS and
fires rather than composing — always machine-labeled, never auto-fired;
the ledger records edited-vs-unedited fires (an unedited streak means the
human has stopped judging).

## 6. Symphony search (the David)

Symphony mode is value-guided tree search (MCTS-shaped) over materialized
workspaces: nodes = (conversation, workspace checkpoint, spec); agents are
the expansion operator; judges/benchmarks are the value function. Four
operators: EXPAND (deepen), FORK (siblings seeded with named STRATAGEMS),
GRAFT (git-native crossover — merge the best of two branches), PRUNE
(defund; branches persist forever, auditable). The token budget is a
PORTFOLIO reallocated by expected value + uncertainty; beam width =
max_parallel_project_agents. Objectives are declared MEASURED (benchmark
judges — full autonomy) or JUDGED (model judges — taste). Evaluation
competes with expansion for budget (value-of-information). Every branch —
especially dead ones — files atomic lessons to the Palace, stamped with
project + origin_path: search that COMPOUNDS across projects. The
remembering orchestra is the moat.

## 7. The Cube (framework, not furniture)

One underlying object — the work — projected on faces of a rotatable cube
with true spatial geometry: FARM is the front face (the colony: the whole
directory tree as chambered burrows, files as cells, zoomable, floating
CAD-like on a black field with faint grid); ROOTS is the depth axis
(organic MEANDERING roots growing horizontally out of each colony; dead
roots desiccated and preserved; thickness = tokens spent; expected value
encoding pending OQ-C3 — opacity floor 0.25 minimum); TIPS is the
opposite face (roots end-on: per-project grids of starts for the next
round). ORBIT rotates AROUND (rotateY-style), plus face rotation; scroll
zooms. Other faces: DECK (the ONLY demanding surface — left rail,
time-ordered, auto-advance by wait), LEDGER (capital alluvial), PALACE
(memory), SCORE (context bars + timeline).

- ONE selection shared by every face; selection = navigation.
- Memory trace from ANY selection: injected memories with one-tap pop-off
  (mid_thread_removed) and near-miss SUGGESTIONS with one-tap add — pure
  consumers of the injection_event log; the viz can never perturb the
  scorer. Memory-injection optimization is sacred; visualization reads,
  never writes.
- Right inspector: context-window visualizer always present for the
  selected agent. Bottom console: car-style dials for RATES (tokens/min,
  spend/hr, frontier load), odometer digits for COUNTS.
- Sub-agents never card, never address the human — visualizer-only.
- Scrubber: one line per HUMAN input; click to jump; lines anchor
  checkpoints (rewind scope: conversation | files | both).
- Ableton doctrine: the layout is a RACK. Users add/remove/author panels
  and faces; layouts are savable per-mode SETS. Plugins get exactly three
  surfaces (event stream, query, selection bus); no notify API exists —
  Invariant 14 is structural. Panel plugins = sandboxed iframes; face
  plugins = data-driven scene schemas (never raw renderer access).

## 8. Aesthetics — THEMES, swappable; NEO-NOIR is the default

The aesthetic is a THEME: a loadable style configuration (tokens, motif
set, material language) in the Ableton rack — easily swapped, easily
authored, like everything else on the rack. Two named themes exist at
birth; users add their own.

**THEME: NEO-NOIR (DEFAULT).** The owner's voice, verbatim from his own
generator config — these phrases are the design language, not literal
config: *"neo-noir, Syd Mead inspired · Blade Runner aesthetic · neon
colors against dark urban backgrounds · neon blues and reds · street
level views, claustrophobic spaces · urban dystopia."* Visual elements:
*"neon lights, corporate architecture, street tech, digital interfaces,
holographic displays, rain-slicked surfaces, urban decay, volumetric
lighting."* Emphasize gritty detail and technological elements. Feel
reference: *"cluttered cyberpunk workspace, multiple floating holographic
screens, neon lighting, rain-streaked window, corporate towers visible
outside, tech noir."* Applied to the Cube: panels read as street tech and
holographic displays; the stage swims in volumetric neon against dark;
reflective/rain-slicked material treatments on the procedural parts;
tone street-wise, pacing quick and sharp; if sound ever ships, synthetic
and industrial.

**THEME: COBALT-SERAPH (alternate).** The 90s cel-anime chrome world
distilled earlier: near-black grounds + starfield, white/steel cel-crisp
linework, orange accents (#d97a3a family), chrome gradient for peak
moments, coral-red #e6404d danger, 4-point glints, whisper scanlines,
inverted light Palace minimap. The current mock wears this theme.

**THEME-INDEPENDENT INVARIANTS (no theme may break these):** themes STYLE
meaning, never re-encode it — data encodings (thickness=spend, fleet
identity colors, needles-for-rates/digits-for-counts) survive every
theme; each theme's fleet palette is machine-validated (dataviz six
checks) on that theme's grounds before shipping; exactly one danger
color per theme; Invariant 14 is not a style. Palace scene direction
(pale monumental architecture, ghost-drone curators — original designs,
never copied assets) persists across themes as the Palace's own identity.
Fonts: display = Michroma / Eurostile-Bold-Extended-alike; body = Inter;
data = JetBrains Mono (self-hosted; themes may override with the same
self-host rule).

## 9. Stack & runtime (per stack discussion)

Hybrid at the physics boundary: React DOM for rails/text; ONE WebGL/WebGPU
stage scene (three.js + react-three-fiber, TSL shaders dual-targeting
WGSL/GLSL) for the Cube; instancing for populations, compute shaders for
living systems; text never enters the canvas. One zustand store = event
reducer + selection bus + query cache; event-sourced client with keyframe
snapshots (IndexedDB) makes time-scrub cheap. Chromium-class browsers ONLY
through M4 (Chrome app mode; PWA at M3). Discipline: refs + useFrame,
never per-frame setState; parallel DOM/table rendering for accessibility.

## 10. Quality doctrine

B.6 rule 7 is the law this vision is built under: UI packets verify by
EMULATED HUMAN USE from the first packet — browser automation, screenshots
at every acceptance state as first-class evidence, assertions on rendered
outcomes. The screenshot is the test (it caught cascade collisions,
charset drift, and dim linework that no unit test would see). Run the
palette validator; never eyeball color accessibility.

## 11. Anti-vision (do NOT)

- No approval fatigue, no attention theater, no demanding visualizations.
- No literal-3D gimmickry that trades readability for spectacle: faces
  render flat when focused; the cube is navigation, not decoration.
- No heavy yellow; no blue-saturated grounds (direction: black/white +
  orange); no copied game assets.
- No visualization that writes to, delays, or biases the memory scorer.
- The mock is a ROUGH APPROXIMATION — match its intent and vocabulary,
  refine its craft. When in doubt: crisper, calmer, more alive.

## 12. Procedural law (added 2026-07-20)

The visuals are MODELS, not pictures: real 3D parts with materials and
lighting, and every one of them GROWN procedurally from the work's own
metadata — roots from search data, colonies from the live directory tree,
the Palace from its memory populations. Deterministic (same data, same
geometry) so the rollback bar can regrow any moment. Nothing hand-modeled
ever enters the stage: an asset that isn't generated from truth is a
picture of the organism, not the organism.
