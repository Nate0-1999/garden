[F001] [P0] [P1.4] — C.2 promises principal-scoped active-label uniqueness in the `label` comment, but the authoritative DDL defines no such partial unique index. P0 cannot choose whether prose or executable DDL wins; minimally add the exact index DDL or remove the promise, disturbing the C.2 persistence contract.
[F002] [P0] [P1.4] — C.4 says a caller may retry memory creation with `force=true`, but the exact request body and route define no location for `force`. P0 cannot invent a cross-repository field; minimally add it to the exact body or route query and define its default, disturbing the C.4 API contract.
[F003] [P0] [P1.3] — C.2 requires `origin_machine_id` on every memory revision, while the C.4 create and PATCH bodies provide no origin and no normative derivation rule. P0 cannot choose an attribution source; minimally add the field or state an exact server-side source rule, disturbing C.2 and C.4.
[F004] [P0] [P1.2.1b] — C.4 says committing a `wrong` removal additionally returns the memory unit, but the exact commit response contains only `final_block`. P0 cannot manufacture a response shape; minimally add the unit field and schema or remove the return promise, disturbing C.4 and the C.6 edit flow.
[F005] [P0] [P1.4] — C.4 calls `GET /v1/memories` paged but defines neither page inputs nor a response envelope; its PATCH success/conflict unit shapes are also undefined. P0 cannot make matching Spine and Harness contracts from that text; minimally specify pagination and the exact unit/409 bodies, disturbing C.4.

---
[RESOLUTION 2026-07-17 — human gate] F001–F005 resolved by SPEC v1.5 (D.2
entry 028), amended in the workspace master `garden_v1/harness-memory-spec.md`.
F001 → C.2 gains `memory_unit_active_label` partial unique index
(principal_id, label) WHERE status='active'; create/PATCH label collision →
409 {label_conflict}; tombstone/quarantine frees the label. F002 →
`force?: bool = false` on POST /v1/memories; overrides ONLY the 0.80–0.92
similar band, never the ≥0.92 duplicate 409. F003 → `machine_id` added to
create and PATCH bodies; spine writes it verbatim to
memory_revision.origin_machine_id. F004 → commit response gains
`wrong_removed: [MemoryUnit]`. F005 → shared MemoryUnit shape defined (C.2
row minus embedding); GET list takes limit/offset and returns
{items,total,limit,offset} ordered updated_at DESC, memory_id ASC; PATCH →
200 MemoryUnit | 409 {conflict: MemoryUnit}. Also: create returns
{created: MemoryUnit}; MemoryCard.score = cosine similarity in dedup and
/v1/search responses. P0 reset to TODO — the next P0 session must refresh
the frozen docs/SPEC.md copies in both product repos, update the v1.4
references in CLAUDE.md/AGENTS.md to v1.5, align the C.4 501-stub routes and
harness client stubs with the amended bodies, and re-verify before DONE
(see reports/000-P0.md notes).
