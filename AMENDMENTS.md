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

[A-006] [S2] [SPEC C.2, C.4, C.5] [P1.4]
gap: C.2 and C.5 cap labels by characters and bodies by tokens but do not define
     the M1 tokenizer or the API behavior when either limit is exceeded.
law: After POST/PATCH `/v1/memories`' behavior, add: "For M1, label length is
     measured in Unicode code points and body length is measured with the
     `cl100k_base` tokenizer. On create, and for each non-null replacement value
     supplied to PATCH, require `len(label) <= cfg.label_max` and
     `tokens(body) <= cfg.memory_max_tokens`; a violation returns RFC7807 422
     before embedding or any database write."
why: This makes the existing 64-character/128-token atomic-memory limits
     deterministic across providers without changing either configured bound.

[A-007] [S3] [SPEC C.3] [P1.2]
gap: C.3 leaves the keyword tokenizer, decay clock, budget units and rounding,
     tie breaks, pin ordering, rank meaning, and budget-skipped near misses
     undefined even though they change the exact prepare result.
law: After C.3's scorer rules, add: "For scorer v0, `tokens` are maximal runs
     of Unicode alphanumeric characters after lowercase conversion. `kw`
     removes the exact stopword set `{a, an, and, are, as, at, be, by, for,
     from, has, he, in, is, it, its, of, on, that, the, to, was, were, will,
     with}`; each stored keyword is tokenized by the same rule before union.
     Time and human-edit ages are measured from `thread.snapshot_ts`, in
     elapsed seconds divided by 86400, with negative ages clamped to zero; a
     human edit is a `memory_revision` whose editor is exactly `user`. Semantic
     cosine is clamped to `[0,1]`. A project match requires a non-null thread
     project equal to the memory project; a null memory project scores 0.5.
     Eligible `pin=true` units are fetched outside the non-pinned vector pool
     and top-k limit, scored only to populate the explainability fields, ordered
     by `memory_id` ASC, and injected first regardless of threshold or budget.
     Their `cl100k_base` body-token costs reduce the regular budget to no less
     than zero; pins alone may exceed it. The regular budget is
     `min(budget_tokens, floor(budget_pct * model_context_tokens))`, and each
     regular card costs its body's `cl100k_base` tokens. The non-pinned vector
     pool orders cosine DESC then `memory_id` ASC; scored regular candidates
     order score DESC then `memory_id` ASC. Greedy selection scans that order,
     accepts at most `top_k`, and after an over-budget card continues to the
     next. Near misses are the first `near_miss_k` unselected regular candidates
     in score order, including candidates excluded by threshold, budget, or the
     top-k cap. Rank is one-based in the combined complete order: pins first,
     then all regular candidates in score order; returned lists retain those
     ranks even when intervening candidates are not returned. `kind='pinned'`
     without `pin=true` does not bypass scoring."
why: These are deterministic mechanics for the scorer C.3 already specifies;
     they add no feature, signal, or learning behavior.

[A-008] [S3] [SPEC C.4 POST /v1/inject/prepare] [P1.2]
gap: C.4 promises a frozen revision-backed read although C.2 cannot reconstruct
     historical scoring state, while M1 permits only one prepare per thread;
     it also leaves event membership and injection-counter membership unstated.
law: After POST `/v1/inject/prepare`'s behavior, add: "M1 accepts exactly one
     successful prepare per thread, as required by C.6's one-injection flow. A
     thread row with non-null `snapshot_ts` returns RFC7807 409 on another
     prepare. An existing unstamped row may be stamped only when its principal,
     agent, machine, and project fields exactly match the request; mismatch
     returns RFC7807 409. Prompt embedding completes before the atomic database
     phase. That phase uses one repeatable-read transaction, stamps
     `snapshot_ts` from the database clock, reads and scores the heads visible
     at that boundary, writes events, and updates statistics; a conflict rolls
     back the entire phase. Each returned card writes exactly one
     `injection_event`: injected `pin=true` cards use `shown_as='pinned'`, other
     injected cards use `shown_as='injected'`, and near misses use
     `shown_as='near_miss'`; stored score, six features, and rank equal the
     response and outcome is null. To preserve the frozen card for replay and
     commit without changing C.2's DDL, the event's features JSON additionally
     contains `_memory: {label, body, pin, updated_at}` from the scored
     snapshot; the wire `features` object remains the exact six-field C.4
     shape. `stats.injections` increments once and `stats.last_injected_at` is
     set to `snapshot_ts` for each card in `injected`, including pins, and never
     for a near miss. Those server writes use C.2 CAS, append revisions with
     editor `system:inject`, origin machine from the request, and reason
     `inject/prepare`."
why: The one-shot transaction is the smallest implementation of M1's explicit
     one-injection-per-thread law; event snapshots keep the log replayable, and
     the counter rule follows rather than weakens the standing CAS invariant.
