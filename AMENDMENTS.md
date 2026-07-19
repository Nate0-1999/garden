# AMENDMENTS.md — enacted contract completions (append-only)

Decide-and-declare log per PLAN.md §2 and SPEC 1.4 (COMPLETION class).
Entries here are LAW, equal to docs/SPEC.md, binding on every later agent
and the judge. The human audits new entries at the between-session review;
an amendment stands unless a veto is appended beneath it (a FIXER packet
then reverts it). Only qualifying completions belong here — PLAN §2 defines
the fence; everything past it is a FLAG. Entry format: PLAN §4 template.

Historical note: F001–F005 predate this mechanism and were resolved by the
human directly in SPEC v1.5 (FLAGS.md resolution, 2026-07-17). Numbering
starts at A-001.

---

[A-001] [S1] [SPEC C.2] [P1.3]
gap: C.2 requires every successful CAS update to write `memory_revision`, but
     does not identify which state that row records or how `updated_at` moves.
law: In C.2's final Rules paragraph, after "all in one transaction", add:
     "A successful cloud-head CAS from revision n to n+1 sets
     `memory_unit.updated_at = now()` and appends exactly one
     `memory_revision` with `revision = n+1`, `parent_uid` equal to the prior
     cloud-head revision's `rev_uid`, and `body` / `label` equal to the
     resulting `memory_unit` values. A failed CAS changes neither table."
why: This makes C.2's stated append-only history and atomic CAS rule executable
     without adding a new field, behavior family, or persistence constraint.

[A-002] [S2] [SPEC C.4] [P1.4]
gap: C.4 requires RFC7807 for errors while also prescribing incompatible exact
     JSON bodies for the memory endpoints' domain-specific 409 responses.
law: Replace C.4's opening error sentence with: "Unless a route specifies an
     exact error body below, errors use RFC7807 JSON. The `label_conflict`,
     `duplicate_of`, and `conflict` 409 bodies specified by POST/PATCH
     `/v1/memories` are exact `application/json` responses and are the only
     C.4 exceptions."
why: This preserves both explicit requirements, including the exact 409 schemas
     already frozen by P0, by narrowing the blanket rule only where it conflicts.

[A-003] [S2] [SPEC C.4 POST /v1/memories] [P1.4]
gap: C.4 defines dedup bands but not deterministic selection, similar-list
     membership, ordering, or the exact threshold boundaries.
law: After POST `/v1/memories`' dedup behavior, add: "Dedup comparisons include
     only ACTIVE units with the same principal. `duplicate_of` is the unit with
     greatest cosine similarity, breaking equal-score ties by `memory_id` ASC.
     `similar` contains every such unit whose score satisfies
     `dedup_sim <= score < dedup_dup`, ordered by score DESC then `memory_id`
     ASC; M1 applies no additional result cap. The configured thresholds are
     inclusive at `dedup_sim` and `dedup_dup` as those inequalities state."
why: This completes the existing two-band behavior with stable, testable output
     and introduces no new filter, score, or caller option.

[A-004] [S2] [SPEC C.4 PATCH /v1/memories/{id}] [P1.1]
gap: C.4 does not say how PATCH maintains the stored embedding or handles null,
     no-op, missing-ID, stale-versus-label, and reactivation cases.
law: After PATCH `/v1/memories/{id}`' behavior, add: "A mutable property whose
     JSON value is null is treated as omitted. With zero remaining mutable
     properties, return RFC7807 422; for an absent memory ID, return RFC7807
     404. A supplied non-null `body` is embedded before the CAS, and a successful
     CAS writes its `body`, `embedding`, and `embedding_model` atomically. The CAS
     condition is evaluated before active-label uniqueness: a stale revision
     returns `{conflict: MemoryUnit}`. Any successful-revision write whose
     resulting status and label would collide with another ACTIVE unit of that
     principal returns `{label_conflict}`, including reactivation without a
     label change."
why: These rules carry C.2's atomic head/embedding and active-label invariants
     through the already-promised PATCH surface without adding mutation fields.

[A-005] [S2] [SPEC C.4 GET /v1/memories] [P1.1]
gap: C.4 names list filters and paging fields but does not define their matching,
     composition, count boundary, or lower bounds.
law: After GET `/v1/memories`' behavior, add: "Supplied filters are ANDed;
     omitted `project_key` and `status` apply no filter. Trim `q`; blank applies
     no filter, otherwise it is a case-insensitive literal substring match over
     `label` or `body`. `total` is the filtered count before paging. Require
     `1 <= limit <= 200` and `offset >= 0`."
why: This gives the existing panel-list parameters their minimal conventional
     meaning while preserving its stated stable order and response shape.
