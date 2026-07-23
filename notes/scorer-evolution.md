# Scorer evolution — owner's gate-day design inputs (2026-07-21)

Status: NOTES for M2 planning (the Chrysopoeia). Not law yet. These are
scorer-v1 FEATURE CANDIDATES + philosophy, to be adjudicated by offline
replay against the injection_event log per ADR-005's evaluation doctrine.

## Candidate: f_gen — the generality axis

Owner: a memory like "Motivation as root context" applies across all
projects — a GENERALITY score, deliberately in opposition to the
location/directory axis. Analysis: partially, not fully, covered today —
a global memory (project_key NULL) scores f_proj=0.5 everywhere (middling
by absence of stamps), but a truly universal principle can never reach
full strength outside a project match. Two variants to evaluate:
- DECLARED generality: agent/human estimates at save time (a unit field
  or kind); cheap, subjective, gameable.
- EMPIRICAL generality (elegant): derived from the log — a memory that
  is kept/cited across many DISTINCT project_keys/threads IS general.
  Measurable today from injection_event outcomes; no schema change; the
  curator can maintain it as a stat. Generality becomes something a
  memory EARNS.
Recommendation: prototype both in replay; empirical is philosophically
aligned (log-first, learned-not-asserted).

## Candidate: f_phase — conversation-phase relevance

Owner: memories differ in WHEN they matter within a conversation —
motivation-type memories matter throughout; "narrow commits,
problem→solution titles" matters only during implement/review. Proxy
ideas: human interactions since thread start / since last compaction; or
categorical work phases (spec / implement / review).
Analysis: moot in M1 (one injection per thread) — becomes meaningful
exactly when re-scoring exists: OQ-15 (per-prompt re-scoring, M2
decision) and the ADR-010 movement-refresh (M3). KEY INSIGHT: in conduct
mode the ADR-012 work protocol KNOWS its phase (spec alignment → loop →
judge) as explicit state — no classifier needed; Duet mode would need a
cheap heuristic/classifier. injection_event already logs prompt_text +
full context, so phase features are retro-evaluable by replay before any
live rollout. Not overfit — schedule with OQ-15.

## Philosophy confirmations (owner Q3)

- TUNING ALGORITHM: M1 deliberately tunes NOTHING (base functionality +
  signal collection first — confirmed as the reason). The committed path
  (decision 010): M2 = SGD on the GLOBAL linear weights from gate signals
  (removals/add-backs per the ADR-005 table), b_m absorbing per-memory
  correction; M3 = hierarchical per-project offsets, partial pooling.
  Rationale: sparse single-user signal (dozens–hundreds of events, not
  millions) starves big models; interpretability is law (the gate shows
  per-feature contributions — a linear model's explanation IS its
  weights); scorer_version + replay make every step cheap to evaluate and
  roll back. JEPA-class learned joint-embedding matching is the right
  instinct at the wrong LAYER/stage: it would augment RETRIEVAL (learned
  memory↔context affinity) rather than the scoring weights, and needs
  data volumes that arrive only with shared palaces / multi-user. Revisit
  at that scale, gated by replay.
- PLUGGABLE PARAMETERS: already law — the f_loc null rule established the
  pattern (omit a feature and renormalize remaining weights), and
  scorer_config rows version weights+params, so features can be added or
  removed per scorer version without migrations.
- PERMANENT BACKTEST TABLE: already exists BY DESIGN — injection_event is
  the append-only training log (every outcome: kept, removed:reason,
  added_back, cited, mid_thread_removed — with full six-feature vector,
  prompt_text, scorer_version, context tuple); memory_revision captures
  every human add/edit (/remember, panel) permanently. C.8 criterion 6
  makes replay-reconstruction an acceptance test. The owner's first
  add-back (2026-07-21) is training row one.

## HORIZON candidate: agentic axes — the Chrysopoeia grows its own instruments

TERMINOLOGY (aligned with owner 2026-07-21): the CHRYSOPOEIA is the
learning loop — it changes the ALGORITHM (weights, features, injection).
The CURATORS are the M3 maintenance agents — they tend the CONTENTS
(dedup merges, splits, promotion, staleness, quarantine). Axis discovery
is Chrysopoeia work; at the horizon, curator-class agents may serve as
the Chrysopoeia's hands (running the feature-search Symphony). Mnemonic:
curators ask "is this memory in good condition?"; the Chrysopoeia asks
"did we show the right memories?"

Owner (2026-07-21): could the scorer's AXES themselves be added and
relegated over time, optimizing injection performance?
Analysis — YES, and the machinery is closer than it looks:
- RELEGATION is already law: the null-rule pattern (omit a feature,
  renormalize remaining weights) + scorer_config versioning retire an
  axis without migration.
- ADDITION and the owner's backfill worry ("can't create the new axis for
  old memories without fake data"): the null rule is the answer — an old
  memory that cannot express a new feature is OMITTED-and-renormalized,
  never fake-zeroed. No fake data, ever. And features DERIVED from logged
  history (empirical generality from cross-project outcomes; phase from
  logged contexts; location from origin_path) can be backfilled HONESTLY
  because every surface is append-only — we kept everything.
- The search itself is ADR-017 ALREADY: curator feature-discovery is a
  Symphony search where STRATAGEMS = candidate axes and the objective is
  MEASURED — replay performance on the permanent injection log. The
  Chrysopoeia growing its own instruments is stratagem search over
  feature space.
- Guardrails: a curator-proposed axis enters a scorer version ONLY after
  beating the incumbent in offline replay; scorer_version keeps every
  step auditable and rollbackable; human gate approves axis adoption in
  early eras. Milestone: post-M3 (needs curator agents + replay tooling).

## Sessions table — schema sketch (owner directive + riff, 2026-07-21)

Owner: all sessions in a table; session id = alphanumeric + timestamp;
riff requested on sub-agents. Sketch, grounded in existing law:
- The id instinct is ALREADY REALIZED by our id discipline: a ULID is
  a 48-bit timestamp + randomness, lexicographically time-sortable
  alphanumeric — the id IS the timestamp. run_id/prompt_id/message_id
  are ULIDs today (A-016/A-017); rev_uid/event_uid likewise.
- `thread` already exists in spine (C.2). Add at durable-sessions time
  (M2/M3, per the perpetuity directive below):
  session_message(message_id ULID PK, thread_id FK, parent_id ULID —
  the ADR-016 tree, run_id, role, content, thinking, events JSONB,
  agent_id, machine_id, ts). Append-only; tombstones never deletes.
- SUB-AGENTS: child conversations are the SAME table, own thread_id,
  plus lineage: thread gains parent_thread_id + spawn_run_id (which
  run of which parent thread spawned it). Sub-agent sessions form a
  FOREST hanging off top-level threads — the FOURTH instance of the
  one parent-pointer shape (memory_revision lineage, message tree,
  Symphony attempt branches, now thread spawning). Deck law unchanged
  (sub-agents never card); the Cube/Ant Farm walk the lineage; audits
  can descend premiere → sub-agent work. Retention: perpetuity
  directive applies.
- PRIOR ART to examine at the tools deep-dive: Code Puppy (owner recall:
  stores memories/state with sub-agents locally) — its DBOS plugin
  checkpoints every agent step (inputs, LLM responses, tool calls) to
  SQLite/Postgres; also Claude Code's per-subagent persistent `memory`
  dirs. Both are candidate patterns for sub-agent session persistence.

## Gate-day tactical findings (2026-07-21, cont.)

- MARKDOWN RENDERING missing in chat pane: model output shows raw
  pipes/asterisks (screenshot evidence). Table-stakes across all models.
  → packet H8 (with model-visibility from earlier feedback).

## Owner directive: complete logs in perpetuity

Keep COMPLETE logs of all conversations in perpetuity, until cost forces
otherwise — full auditability + future training. Current honest state:
memories, revisions, and the injection log (incl. full prompt_text) are
already permanent and append-only; but M1 CONVERSATION TRANSCRIPTS are
process-local in the daemon (A-016/A-017 — they die with the daemon
process; the browser catalog is navigation metadata only). Directive for
M2/M3 session persistence (ADR-016 tree + durable sessions): transcripts
become durable, append-only, retained indefinitely with a cost-triggered
archive path (e.g., GCS cold storage at M4) rather than deletion.

## Capture status (owner asked)

Design riffs land in the GARDEN (notes → law at gates) — the project's
memory. The PALACE holds the product's memories via the owner's own
/remember and gate actions. Deliberately two systems; meta-content is not
backfilled into the Palace (would pollute injection). Symmetry worth
savoring: ADR-019 clause 4 seed ingestion (M2) will let the owner upload
these very notes as markdown and have curators split them into atomic
memories — the Garden's knowledge can migrate into the Palace when the
owner chooses, through the standard consent/approval pipeline.

## Queued deep-dive

Tool/skill parity (browser use via Codex extension port, Agent Skills,
MCP, CodeMode, etc. — see gate-day-feedback.md): owner wants a dedicated
detailed session; plan a focused field survey + adoption charges when it
opens.

## Live Palace audit + v0.2 proposal review (2026-07-22)

DATA FINDINGS (direct DB inspection via proxy):
- Empty axes diagnosis: /remember memories carry NO keywords/project/path
  (M1 shell has no project/location concept — by design; movement law
  (M3, CONTRACT) populates path/proj automatically). The agent-saved
  memory ('Project motivation: SQL/ETL…', editor=agent:harness-agent)
  ALSO carried kw=[] — the save_memory keywords param is optional and the
  agent skipped it. ACTIONABLE: C.6 agent instructions should mandate 2–5
  keywords per save; /remember could generate keywords in the same
  completion that generates the label. Keyword-less memories are
  handicapped on f_kw (label tokens only).
- THE f_gen SMOKING GUN: the owner's 'Motivation as root context' reached
  the gate as near-miss rank 1 with sem≈0.05, kw=0 — semantically almost
  INVISIBLE to the ETL prompt — yet the owner added it back. This is a
  perfect real-data justification for the generality axis: universal
  memories are exactly the ones embeddings can't see. Training row one
  argues for f_gen all by itself.
- TRAINING-DATA HYGIENE (new rule needed): H5 verification fixtures wrote
  ~25 events under machine 'h5-verification-machine' / editors
  'verification:h5'. The Chrysopoeia MUST exclude verification/test
  machine ids and editor prefixes from learning data, or fixture clicks
  will tune the scorer. Add a hygiene filter spec to M2 learning law.
- Minor: D1 verification left one active project-scoped memory
  ('D1 broker 4f6500c7') in the production Palace — mostly inert (scoped
  to a synthetic project) but candidate for tombstone hygiene.

V0.2 PROPOSAL REVIEW (agent-polished; owner asked for incremental ideas):
Already ours (no action): budget %/cap+pins, path priors (our continuous
2^(−d/2) beats its discrete ladder), retrieval_log (injection_event is
richer), user/project prior (M3 offsets), reuse-success (f_freq+outcomes),
LLM merge consolidation (curators M3), demote/expire (quarantine,
staleness), compaction→durable candidates (M2 extraction), "mark this
remembered" (/remember+pin). REJECTED: post-session questionnaire tuning
(Invariant 14 — our gate signals collect the same information passively);
SQLite local-first (cloud law); mandatory-question compaction (parked
plan-mode already covers the idea gently).
ADOPT-LIST (incremental, real):
1. HYBRID RETRIEVAL (strong; M2 packet candidate): candidate pool is
   vector-only top-50 today — an exact-keyword memory with weak embedding
   can never surface. Union Postgres FTS (tsvector) with the vector pool;
   scorer ranks the union. v0.2's "single search mode misses too much" is
   correct, and the owner's sem≈0.05 near-miss is adjacent evidence.
2. CONTRADICT/SUPERSEDE taxonomy (moderate-strong; M3 curators): extend
   write/consolidation decisions beyond duplicate/similar to
   merge | new | contradict | supersede, with supersession recorded (see
   3). Contradiction detection protects truth over time.
3. TYPED EDGE OVERLAY (moderate; M3): typed edges (supersedes,
   contradicts, relates-to) as rows in the same DB — 1-hop expansion and
   consolidation aid, never first-pass retrieval. Refines the original
   'searchable graph' idea to overlay-not-architecture.
4. PROMOTION ≠ RAW FREQUENCY (good; M3 law refinement): pin-promotion
   should blend stability + importance + reuse + manual signal ("frequency
   alone promotes nonsense").
5. CANDIDATE STATUS (small; M2): a formal 'candidate' memory status for
   approval-queue items (extraction + seed ingestion) instead of
   overloading active.
6. ENTITY + IMPORTANCE/STABILITY AXES (agentic-axes candidates): entity
   tags (files/modules/concepts) and declared importance — enter through
   the replay-gated feature pipeline alongside f_gen/f_phase.
7. "OPEN LOOPS" compaction output (nice; M3): compaction emits working
   summary + open loops + durable candidates — open loops is a genuinely
   useful third artifact.

## Lifecycle red-team residue (2026-07-22, post-ADR-021/v2.11)

Enacted as v2.11 closures C1-C6. NOT enacted, parked here for M2 planning:
- ATTENDED-THREAD SAMPLING BIAS: unattended conduct-mode threads generate
  mostly passive 'kept' signal; chat threads generate rich labels. C5's
  actor classes carry the fix's raw material — consider thread-attendance
  as a context feature in replay before weighting classes.
- STAGED CITATION STATS: citations of staged units accrue in staging and
  fold in on promotion (die with archived branches) — implied by C1's
  pattern; make explicit in the M3 staging migration spec.
- JUDGE ATTENTION BOUNDS: judge memory-gate review is bounded by rate cap
  × winning-branch depth; if big searches strain it, rank-and-truncate
  with digest overflow rather than raising judge spend.
