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
