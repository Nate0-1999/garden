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

## HORIZON candidate: agentic axes — the curator grows its own instruments

Owner (2026-07-21): could the scorer's AXES themselves be added and
relegated over time by the curators, optimizing injection performance?
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
