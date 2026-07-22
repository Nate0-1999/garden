# Gate-day feedback + tool-parity directive (owner, 2026-07-21)

Status: NOTES — gate-day observations per B.6 rule 8's design-friction
channel, plus an owner directive for M2/M3 planning. Not yet law.

## Gate-day observations (H5 human-use hold, in progress)

1. LOOP PROVEN: /remember → unit created → surfaced as near-miss in the
   gate → human add-back → injected next thread → agent behavior visibly
   shaped by the memory (asked for motivation before building). The M1
   thesis works end to end with real embeddings against the cloud spine.
2. UI FRICTION — active model not visible: the owner wants to see which
   model a thread is running (CHAT_MODEL currently only in .env). The
   command-center mock's top bar already shows mode/model readouts; the
   minimal H4 shell does not. Route to H6/I1 polish or M2 UI planning:
   show active model (and per-thread selector per C.5) in the thread
   header/top bar.

## Owner directive: tool & skill parity shopping list

"We need to make sure that we can use skills and have the best tools
available in the harness." Compile and adopt the best/most-common agent
tools, via the ADR-013 seam (adopt, wrap, pin — never rebuild commodities).
Initial list to research and sequence at M2/M3 planning (extends ADR-007's
parity index):

- BROWSER USE — owner names Codex's Chrome-extension browser control as
  the reference ("probably just port theirs, I think it's open source").
  Evaluate porting vs pydantic-ai/harness browser batteries; must compose
  with ADR-015 walls (browser = boundary-crossing surface) and B.6 rule 8
  (agents already need browser use for SOPs — dogfood candidate).
- SKILLS — the open Agent Skills standard (SKILL.md folders, progressive
  disclosure, project > user precedence) per ADR-007.
- MCP — client support through the seam (deferred tool definitions for
  token economy).
- CodeMode — already an ADR-013 adoption target (fan-out tool calls in
  one sandboxed program).
- Background tasks / long-running shells; scheduled runs (cron/recipes).
- Compaction batteries (ProcessHistory chassis — already law, D.4).
- Sub-agent archetypes (read-only Explorer, Reviewer) as defaults.
- Cost/usage introspection surfaces (pairs with run.usage + the console).
- Candidate deep-dive at planning: re-run a field survey (like the
  2026-07-20 harness survey) scoped to TOOLS/SKILLS specifically, and
  fold winners into M3 packet charges.

## Q&A recorded for the owner

- "Did adding a below-the-line memory tune the algorithm?" NO — by design
  in M1 (ADR-005 phasing): the add-back was LOGGED as `added_back` with
  the full feature vector + scorer_version in injection_event; scorer v0
  weights are hand-set and only `never` moves per-memory bias. The
  Chrysopoeia (M2 learning loop) trains on exactly these logged signals.
  The owner's add-back is the first real human tuning example in the log.
