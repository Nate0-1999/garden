# H5 closing checklist — human tests before clearing the hold (2026-07-22)

Status: NOTES / gate-day SOP for the owner. Each test: DO → EXPECT → LAW.
Tests marked [DEFER] exercise machinery that ships in a later packet — do
not chase them now. After the owner runs the live tests, the human gate
audits injection_event / memory_revision through the proxy and confirms
every event landed with correct outcomes.

## Tier 1 — untested law (highest value; only fixtures have touched these)

1. THE OTHER THREE REMOVAL REASONS. So far the owner has personally done
   keeps and one add-back. In a real thread's gate: remove one card as
   "not relevant here" (one-tap default), one as "wrong/stale" (should
   open the edit/expire flow), one as "never show this".
   EXPECT: distinct logged outcomes; "never" applies a visible bias hit
   next thread. LAW: ADR-005 signals table.
2. QUARANTINE AT 3 KILLS. Create a junk memory (/remember something
   silly), then "never" it in three separate threads.
   EXPECT: after the third, it stops appearing anywhere (status
   'quarantined' in the DB). LAW: C.3 quarantine.
3. DEDUP BANDS WITH REAL EMBEDDINGS. Tell the agent to remember the same
   preference twice in different words across two threads.
   EXPECT: second save surfaces "similar memory exists" to the agent,
   which should EDIT rather than duplicate (C.6 instruction). Then try an
   exact repeat: hard 409, reinforcement confirm (v2.14 — if the build
   predates v2.14, a raw 409 is a KNOWN GAP to note, not a defect).
   LAW: C.4 bands, ADR-021 cl.2.
4. EDIT FLOW + LINEAGE. Ask the agent to update a stored memory's content.
   EXPECT: revision bumps, old text in lineage, next thread injects the
   new text. LAW: ADR-004 CAS/revisions.
5. SNAPSHOT PINNING. While a thread is OPEN, edit (via a second thread) a
   memory the first thread already has injected.
   EXPECT: the open thread's block does NOT change; a brand-new thread
   gets the revised text. LAW: ADR-004 thread snapshot.

## Tier 2 — scale & feel (the gate exists to judge experience)

6. POPULATION STRESS. Seed the palace to ~15-20 REAL memories (/remember
   preferences, facts about current projects). Start threads on different
   topics. EXPECT: top-k caps at 8, exactly 3 near-misses, the selection
   FEELS right (note any memory that obviously should/shouldn't appear —
   these observations are M2 scorer-tuning gold). LAW: C.3/C.5 constants.
7. TOKEN-CAP ENFORCEMENT. /remember a paragraph well over 128 tokens.
   EXPECT: clean enforcement (reject with clear message or guided split),
   not a silent truncation or a 500. LAW: unit atomicity, decision 015.
8. PROJECT SCOPING. Save one project_scoped memory; start a thread in a
   different project context. EXPECT: it ranks lower/absent there (f_proj).
   LAW: ADR-005 f_proj. (M1 project context is thin — observations here
   shape the M3 movement law's UX.)
9. COLD OPEN. Start a thread on a topic with no relevant memories.
   EXPECT: graceful gate (small or empty set + near-misses), no awkward
   forced injection. LAW: τ threshold behavior.
10. LATENCY FEEL. Note perceived injection delay at thread start — prose
    observation for the record (B.6 r8 spirit).

## Tier 3 — resilience (fail-soft law)

11. SPINE UNREACHABLE. Kill the network briefly mid-session; try to chat
    and to /remember. EXPECT: clear degraded behavior (no crash, no data
    loss, comprehensible error), recovery on reconnect. LAW: ADR-003.
12. RESTART PERSISTENCE. Restart daemon + browser. EXPECT: thread list
    and history intact (spine is source of truth).
13. UNICODE/EMOJI in /remember and chat. EXPECT: round-trips cleanly
    through embedding + storage + render.

## [DEFER] — do not test now; later packets deliver these

- Memory side panel: live list, ad-hoc mid-thread removal, edit UI, pin
  toggle → H6.
- Markdown rendering, active-model visibility, /remember keyword
  generation → H8.
- Citations, extraction, seed ingestion, hybrid retrieval, learning,
  Vitals → M2. Curators, staging, movement refresh → M3.

## Closing protocol

Owner finishes Tier 1-3 → the human gate audits the DB (all events
present, outcomes correct, quarantine flipped, lineage intact) → owner
tells a relay session "H5 human-use hold cleared" → H6 unblocks (H8
follows; then I1, J).
