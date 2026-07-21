# The Garden Plan — loop-enabled M1 execution

Companion to docs/SPEC.md v2.4. This is OPERATIONS, not constitution: it may
change freely; the spec may not. This document is written to be executed by a
relay of agents, generation 0 through n, each arriving with no memory of the
last. Everything an agent needs is in files; nothing lives in anyone's head.

**Workspace layout (created by Agent Zero, P0):**
```
garden/            # ops repo: THIS file (PLAN.md), SPEC.md (the MASTER
                   #   constitution), BOARD.md, FLAGS.md, AMENDMENTS.md,
                   #   reports/          remote: Nate0-1999/garden
spine/             # product repo (SPEC C.1)   remote: Nate0-1999/memory-palace
harness/           # product repo (SPEC C.1)   remote: Nate0-1999/harness
```
All three are sibling git repos in one workspace. Sessions launch at the
workspace root. `garden/SPEC.md` is the constitution's master; each product
repo carries a frozen copy at `docs/SPEC.md`, refreshed only at human gates.
Amending the master is a human act — agents propose via FLAGS, or enact
qualifying COMPLETIONS via AMENDMENTS.md (Section 2), and never edit
SPEC.md themselves.

**Cloud footprint (D1; live-audited 2026-07-21):** GCP project
`n8-memory-palace` is ACTIVE and billed, with a $100/month budget alerting at
50/90/100%. Cloud SQL `n8-memory-palace-db` is RUNNABLE in `us-central1`
(Postgres 16, db-f1-micro), contains the `spine` database and built-in user,
has backups/PITR and deletion protection enabled, and is migrated to Alembic
`0002 (head)`. Three region-pinned secrets and the dedicated least-privilege
`spine-runtime` identity feed one Gen2 Cloud Run service from the immutable
regional `spine` Artifact Registry repository. Revision
`n8-memory-palace-spine-00003-pjh` serves 100% of default traffic at
`https://n8-memory-palace-spine-713925718873.us-central1.run.app`. GCS was not
created: it is not an M1 dependency, and snapshot lifecycle remains M4 horizon.

D1 passed an application-bearer health check, real OpenRouter create → hard
duplicate → prepare → commit round trip, and typed Harness client smoke. Cloud
Run reserves some paths ending in `z`, so remote verification uses Spine's
authenticated, OpenAPI-hidden `/health` alias; the specified `/healthz` remains
the local `docker compose` C.8 acceptance path. The ignored `harness/.env` is
mode 0600 and points at the deployed service; deployed runtime credentials live
only in Secret Manager. OpenRouter is the default for chat and embeddings, so
no OpenAI credits are needed and the OPENAI line is a direct-provider-only
legacy slot. Every cloud command must still carry explicit project and region
flags; ambient gcloud configuration is never authority.

---

## 0. THE BOOT SEQUENCE — every agent, every session, in this order

You are one runner in a relay. The runners before you left everything you
need in files; the runners after you will have only what you leave in files.

**STEP 1 — Law.** Read `garden/PLAN.md` (this file) top to bottom. Read
`garden/AMENDMENTS.md` — enacted completions there are law, equal to the
spec. Read the `CLAUDE.md` / `AGENTS.md` ground rules in any repo you will
touch. Do NOT read the whole spec yet — your packet will name its sections.

**STEP 2 — Ground truth.** Read `garden/BOARD.md`. Read the LAST handoff
report in `garden/reports/` (at most the last three — respect your context;
older history is in git if truly needed). Then VERIFY inherited ground: run
the test suites of both product repos. If anything is red, broken, or
contradicts the last report's claims, your role is FIXER (Section 3) — the
board's next packet waits. **Never build on unverified ground.**

**STEP 3 — Dispatch.** Determine your role from the board using Section 3's
rules. The situation assigns your role; the human does not need to.

**STEP 4 — Claim.** Edit BOARD.md: set your packet IN_PROGRESS with a session
tag (tool + date + short id) and commit this claim to `garden` BEFORE any
other work. The claim-commit is your baton pickup.

**STEP 5 — Focused law.** Now read exactly the spec sections your packet
charge names — fully, at full attention, nothing more. If your packet
touches user-facing surface (UI, visualization, interaction), ALSO read
`garden/NATES_VISION.md` — the owner's extended vision (GUIDANCE per SPEC
1.4): deviate from it only with a journaled DECISIONS.md entry.

**STEP 6 — Work.** Within CONTRACTS (SPEC 1.4). Journal every non-dictated
decision in the repo's DECISIONS.md citing a Problem Tree node. Defects you
discover: Blight Protocol (SPEC 2.1) — fix at the deepest containing node or
flag. A contract that is silent or self-inconsistent on a detail you need:
COMPLETION — enact it in AMENDMENTS.md and keep moving (Section 2 says what
qualifies). Anything requiring a FORBIDDEN feature or a true contract
change: STOP, write a FLAG (Section 4), set your packet BLOCKED, end your
session.

**STEP 7 — Handoff ritual.** In order: packet exit criteria met → both test
suites green → DECISIONS.md entries committed → handoff report written
(template, Section 4) → BOARD.md updated (DONE, or honestly back to TODO /
BLOCKED with the report explaining why) → all commits pushed with your packet
id in messages. **A clean unfinished handoff beats a dirty finished one** —
returning a half-done packet with an honest report is success, not failure.

**STEP 8 — Stop.** One packet per session. Do not begin the next packet, no
matter how much context or energy remains. Fresh ground needs fresh eyes.

---

## 1. BOARD.md — the state ledger

Agent Zero instantiates it from Section 5's packet list. One row per packet:

```
| id | packet             | deps      | status       | claim              | verdict |
|----|--------------------|-----------|--------------|--------------------|---------|
| P0 | Agent Zero         | —         | TODO         |                    |         |
| S1 | DB layer & CAS     | P0        | TODO         |                    |         |
...
```

**Statuses:** TODO · IN_PROGRESS · DONE · BLOCKED (see FLAGS.md) ·
FAILED_JUDGMENT (see verdict path).

**Rules:**
- Claim only a TODO packet whose deps are all DONE. If several qualify,
  take the lowest id (determinism beats optimization).
- HUMAN packets or substeps explicitly marked in Section 5 are never claimed
  or executed by an agent. The current D1 packet is relay-owned. D2's
  destructive billing-breaker deployment remains human-only even though its
  build packet is DONE.
- **Stale claim recovery:** an IN_PROGRESS claim from a session that left no
  handoff report is stale after inspection confirms the session is dead.
  Inspect its branch: salvage what passes tests into your work if trivial,
  otherwise reset the packet to TODO, journal what you found, and note it in
  your report. Never silently adopt unverified work.
- Only the claiming session edits its row (plus the human, who may edit
  anything).

## 2. AMENDMENTS.md & FLAGS.md — solve it yourself, or stop the line

Contract trouble has two exits. Take the first whenever it qualifies; the
second is for what the first cannot carry.

**AMENDMENTS.md — decide-and-declare (the default).** Where law is SILENT or
SELF-INCONSISTENT on a detail your packet needs — a missing field, an
undefined response shape, a promise the DDL forgot — do not halt: choose the
minimal completion that honors the spec's stated intent, append it to
`garden/AMENDMENTS.md` as an EXACT spec diff (template, Section 4), cite the
amendment id in DECISIONS.md, and build against it. Enacted amendments are
law for every later agent and the judge. The human audits them at the normal
between-session review and may veto; a veto becomes a FIXER charge.
A completion QUALIFIES only if it (a) touches no Invariant (SPEC 1.3), no
FORBIDDEN row (B.4), no auth or data-loss semantics; (b) reverses no ADR;
(c) changes nothing an already-DONE packet built; (d) contradicts no
explicit spec sentence — it fills silence or repairs an internal
contradiction in the direction the spec itself points. Rule of thumb: if a
wrong guess would rework committed work or violate standing law, FLAG; if it
can be completed compatibly and declared where every future agent will read
it, complete it and move on.

**FLAGS.md — stop-the-line.** Append-only. A FLAG is how an agent halts
safely when completion does not qualify: FORBIDDEN-feature need, Invariant
conflict, ADR reversal, a Blight-Protocol escalation (fix would disturb an
ancestor node), or a genuine design fork (two readings, materially different
products). Format:
`[flag-id] [packet] [tree node] — what was found, why COMPLETION does not
qualify, the minimal proposed change, what it disturbs.`
A BLOCKED packet stays blocked until the human resolves the flag (usually by
amending the spec) and resets the row to TODO.

## 3. Role dispatch (derived from the board, in priority order)

1. **FIXER** — Step 2 found broken ground, a packet row says
   FAILED_JUDGMENT, or the human vetoed an amendment (the veto note in
   AMENDMENTS.md is your charge). Claim that repair: your charge is the last
   report, the verdict file, or the veto; fix per the Blight Protocol; do
   not add features.
2. **JUDGE** — all build packets (S*, H*, I1) are DONE and J is TODO.
   **Independence check: if your context contains ANY build work from this
   milestone, REFUSE the role** — end the session instructing the human to
   launch a fresh one (different model preferred). A judge with build
   context is a rubber stamp.
3. **BUILDER** — otherwise: claim the next eligible packet (Section 1 rules)
   and follow its charge (Section 5).
4. **NOBODY** — board shows all DONE and J passed: M1 is complete. Write a
   closing report; instruct the human that M2 planning opens (SPEC B.1:
   M3 re-plans then too). Build nothing.

## 4. Templates

**Handoff report** (`garden/reports/NNN-<packet>.md`):
```
packet:            S3 — Scorer v0 + inject/prepare
session:           claude-code / 2026-07-.. / a1b2
status:            DONE | RETURNED_TODO | BLOCKED
what exists now:   (2-6 lines, concrete: files, endpoints, behaviors)
deviations:        (DECISIONS.md entry ids + tree nodes, or "none")
evidence:          (test names, verification/ paths)
notes to the next agent:  <- the generational memory; write what you wish
                   you had been told: surprises, fragile spots, half-truths
                   in your own work, the fastest path into the code.
```

**FLAG:** see Section 2 format.

**Amendment** (`garden/AMENDMENTS.md`, append-only):
```
[A-NNN] [packet] [spec section] [tree node]
gap: what the spec fails to say, or says twice differently (one sentence)
law: the exact contract text as it now stands — a precise diff or the full
     amended body/DDL/shape, complete enough that both repos and the judge
     implement identically from this entry alone
why: how this is the spec's own intent completed, not new design (one line)
```

## 5. Packet charges

Every charge implicitly begins with the Boot Sequence. "Sections" = the only
spec reading required.

**P0 — Agent Zero (groundskeeper).** Charge: SPEC C.10 verbatim, PLUS:
create the `garden` repo with PLAN.md (this file), BOARD.md instantiated
from this section, empty FLAGS.md, reports/ with your own report as 000;
create CLAUDE.md / AGENTS.md in both product repos from the template in
Section 6. Human reviews your journal entries and report for cultural fit
before the relay continues.

**Spine track**
- **S1 — DB layer & CAS rules.** Sections: C.2, ADR-004. Deliver: models
  matching the DDL exactly; transactional CAS-update + revision-write
  helper; tombstone helper; tests incl. forced 409 and parent_uid lineage.
  Nodes: P1.3.
- **S2 — Memory CRUD + dedup bands.** Sections: C.4 (/v1/memories, PATCH,
  GET), C.5. Deliver: create with 0.92/0.80 bands (409 duplicate /
  similar[] / insert + force), PATCH with CAS, paged list; embedding
  provider behind the C.1 interface with a deterministic fake for tests.
  Nodes: P1.4, P1.1.
- **S3 — Scorer v0 + inject/prepare.** Sections: C.3, C.4 (prepare),
  ADR-005. Deliver: six features exactly per formulas; pgvector candidate
  pool; budget-greedy selection; near-misses; pinned bypass; snapshot_ts
  semantics; injection_event writes with full features. Golden tests:
  hand-computed scores on fixed fixtures — computed from the SPEC, not from
  the code. Nodes: P1.2.
- **S4 — Commit, feedback, quarantine.** Sections: C.4 (commit, feedback),
  C.3 (never rule), C.6 (final_block format). Deliver: outcome writes;
  never → bias → quarantine at 3; "wrong" returns unit; exact final_block
  renderer; stats updates. Nodes: P1.2.1a–d. (Deps: S3, S5 — S5 first:
  S4's wrong_removed serializes MemoryUnit, which now carries origin_path.)
- **S5 — origin_path metadata.** Sections: C.2, C.4 (as amended),
  ADR-005 location-relevance note. Deliver: migration 0002 adding
  origin_path; create/PATCH/MemoryUnit surfaces carrying it; scoring
  UNTOUCHED (inert metadata — scorer v0 neither reads it nor may any later
  scorer penalize its absence, per the null rule); tests. Nodes: P1.3.
  (Deps: S3.)
- **S7 — Broker-routed embeddings.** Sections: C.5 (v2.3), C.1 embeddings
  interface. Deliver: `embed_base_url` setting wired through to the
  existing adapter's base_url parameter; embed_model default updated to
  the namespaced broker id; compose/env passthrough
  (SPINE_EMBED_BASE_URL); dimension validation unchanged (1536 stays
  law); tests: config default = OpenRouter, override = direct provider,
  both against the deterministic fake; OpenAPI freshness. Small packet —
  the adapter already accepts base_url; this exposes it. Nodes: P1.1.
  (Deps: S2.)
- **S6 — /v1/search.** Sections: C.4 (search, shared shapes), C.3
  (candidate rule). Deliver: POST /v1/search per the exact body — embed
  query; top-k by cosine over ACTIVE units of the principal, applying the
  C.3 project filter when project_key is supplied; results as MemoryCards
  with score = cosine similarity, features/rank null; tests on
  deterministic embeddings. The last 501 dies here. Nodes: P1.1.
  (Deps: S2.)

**Cloud gate**
- **D1 — GCP deploy & remote verification.** Sections: ADR-003, C.5, C.8.
  Starting truth is the live-audited cloud footprint at the top of this plan.
  In project `n8-memory-palace`, region `us-central1`, the claiming relay agent
  may perform only this packet's named mutations:
  1. tighten `harness/.env` to mode 0600 without printing values; create the
     built-in Cloud SQL user/database `spine` with a generated credential;
     enable automated backups/PITR and deletion protection; run production
     Alembic through Cloud SQL Auth Proxy and prove head `0002` (migrations
     never run at container boot);
  2. enable Secret Manager; create secrets for the complete database URL,
     static Spine token, and OpenRouter key; bind the latter to runtime env
     `SPINE_OPENAI_API_KEY`, the adapter's generic compatible-bearer slot;
  3. create dedicated runtime identity `spine-runtime`, granting only Cloud
     SQL Client at the project and Secret Accessor on those exact secrets;
     never deploy on the broad default compute identity;
  4. create exactly one Docker-format Artifact Registry repository, `spine`,
     in `us-central1`; use the active deployer locally (not Cloud Build or a
     new build identity) to build the current Spine Dockerfile for Linux/amd64.
     This Apple-Silicon workspace uses Homebrew `docker-buildx` for the required
     cross-build. If it is absent, D1 may install only that package and add
     `/opt/homebrew/lib/docker/cli-plugins` to Docker's `cliPluginsExtraDirs`,
     preserving all other Docker config. Prove Buildx,
     tag the image
     `us-central1-docker.pkg.dev/n8-memory-palace/spine/spine:<SPINE_SHA>`, and
     push it without any build-IAM mutation. Deploy exactly one service,
     `n8-memory-palace-spine`, from that immutable tag with the SQL attachment,
     gen2 execution, port 8000, max scale 1, the three secret env bindings, and
     explicit C.5 broker URL/model.
     Cloud Run transport must allow unauthenticated invocation because the
     frozen application boundary is the Spine static bearer; this does not
     make the API itself unauthenticated;
  5. verify authenticated `/health` on Cloud Run, then a real OpenRouter-backed
     create → hard-duplicate → prepare → commit round trip; update only the
     ignored local `harness/.env` `SPINE_URL`, preserve mode 0600, and prove a
     typed Harness client call against the deployed URL. Retain `/healthz` for
     local C.8 acceptance: Cloud Run reserves some paths ending in `z`, so that
     exact path can be intercepted before the container.

  Every command uses explicit `--project=n8-memory-palace` and the applicable
  `--region`/`--location=us-central1`. Never echo, commit, or place secret
  values in normal Cloud Run environment settings or reports. No deletes, GCS,
  billing/budget/D2 changes, broad IAM, default-runtime use, unrelated resource
  edits, or destructive replacement are authorized. Unexpected existing state,
  a non-forward migration, interactive reauthentication, or any need for wider
  authority returns the packet honestly to TODO/BLOCKED for the human.

  Exit: both product suites green; redacted evidence under
  `spine/verification/d1/` records project/region, DB and Alembic state, backup
  protection, service URL, revision, image digest, Spine SHA, IAM topology,
  and the remote round trip; the D1 handoff records the URL and BOARD is DONE.
  H5 and later Harness work use the deployed Spine. Local contract tests may
  still use their deterministic container. (Deps: S4, S7.) Rationale: B.3
  commits M1 to "spine on Cloud Run"; the gate prevents the relay from
  finishing M1 having only ever talked to localhost.
- **D2 — Billing circuit breaker (build: agent; deploy: HUMAN).** Sections:
  none (pure infra; pattern: Google's documented "disable billing with
  notifications"). Deliver in spine `infra/billing-breaker/`: a Cloud Run
  function (Python 3.12) subscribed to a `billing-breaker` Pub/Sub topic
  that parses budget notifications and, when costAmount >= budgetAmount,
  DETACHES billing from project `n8-memory-palace` via the Cloud Billing
  API (updateBillingInfo, empty billingAccountName) — deliberately
  terminating all project services; idempotent and decision-logged; unit
  tests on fixture notifications with NO live cloud calls; a deploy script
  that creates the topic, points the existing $100 budget at it, and
  deploys the function under a dedicated service account holding Project
  Billing Manager on this project ONLY; a README runbook with a
  synthetic-message drill and billing re-attach steps. Agents build and
  test but NEVER execute the deploy script or any gcloud mutation — the
  human runs the runbook at a gate. Revisit when Google's Spend Caps
  (private preview 2026; covers Cloud Run but NOT Cloud SQL) reaches GA.
  Nodes: P4. (Deps: P0.)

**Harness track**
- **D3 — The deploy & onboarding command (packaging wave).** Sections:
  ADR-019, ADR-013. Deliver: `nocturne` CLI (init/up/deploy/open) per the
  two-secrets rule; both wheels with bundled web assets and packaged
  migrations; `nocturne deploy` = the executed D1 runbook as idempotent
  code with --dry-run, honoring D.2 045's boundaries (no deletes, no
  broad IAM; the D2 breaker step prompts for HUMAN confirmation);
  quickstart README. Verification per ADR-019's clause: fresh machine +
  Docker + one OpenRouter key → working browser chat. Nodes: P4.
  (Deps: J — packaging opens after the judge; C.8 stays repo-based.)

- **H1 — Envelope + daemon WS.** Sections: C.7, C.1. Deliver: envelope
  models, WS server, type routing, malformed-envelope rejection tests.
  Nodes: P3. (Eligible after P0.)
- **H2 — spine_client + contract tests.** Sections: C.4. Deliver: typed
  client mirroring every body; contract-test job against a live spine
  container covering S1–S2. Nodes: P1.1. (Deps: S2.)
- **H3 — Agent + memory tools.** Sections: C.6 (tools, instructions,
  /remember), C.5, ADR-013. Deliver: pydantic-ai agent — chat + memory
  tools ONLY; docstrings per spec; similar/409 surfaced to the model;
  /remember with generated label. MemoryCapability is the ADR-013 seam's
  FIRST feature: define the minimal internal capability protocol and the
  single adapter module; ship it as a pydantic-ai v2 Capability subclass;
  nothing outside the adapter imports pydantic-ai capability machinery.
  Nodes: P1.2, P1.4. (Deps: H2.)
- **H4 — Web shell + chat.** Sections: C.1 web/, C.7 (as amended), ADR-009
  mobile law, B.6 rule 7. Deliver: responsive SPA — thread list, chat pane,
  run.delta streaming; sane at 390px. Verification per B.6 rule 7: drive
  the real UI with browser automation as a user would; screenshots of every
  acceptance state committed as evidence. Nodes: P2, P3. (Deps: H1, H7.)
- **H5 — The gate.** Sections: C.6 steps 1–4, ADR-005 gate UX, B.6 rule 7.
  Deliver: prepare-on-send; modal with full bodies + per-feature scores;
  one-tap ✕, modifier reasons, near-miss add-back; hard pause; commit →
  run. Verification per B.6 rule 7 (browser-driven, screenshot evidence).
  Nodes: P1.2.1a–c. (Deps: D1, H4.) The human personally uses the result
  before the relay continues (Section 7).
- **H6 — Memory panel.** Sections: C.6 (live panel), C.4 (feedback, PATCH),
  B.6 rule 7. Deliver: live list; ad-hoc remove → mid_thread_removed →
  re-render next call; edit flow with CAS conflict surfacing; manual pin
  toggle. Verification per B.6 rule 7 (browser-driven, screenshot
  evidence). Nodes: P1.2.1d, P1.3. (Deps: H5.)
- **H7 — Envelope v1.12 & loop controls.** Sections: C.7 (as amended), ADR-014
  (M1 subset). Deliver: daemon behavior + envelope models for run.started,
  run.cancel (confirmed abort, work preserved), prompt.queued
  (queue-to-turn-boundary), thread.snapshot (hydration on (re)connect),
  run.usage, gate.dismiss; stop_reason on run.done; typed run.delta union;
  gate.open kind field; reserved-type passthrough (forward/ignore unknown
  types). Tests: cancel mid-stream is confirmed by run.done(cancelled);
  reconnect hydrates from snapshot only; a queued prompt fires exactly once
  after turn end. Nodes: P3. (Deps: H1. Runs BEFORE H4 — the UI consumes
  every one of these.)

**Closing**
- **I1 — Integration & AC dry run.** Sections: C.8. Deliver: compose
  end-to-end; walk all seven criteria as a BUILDER (not judge); fix gaps;
  seed demo memories; verification/README pointing a judge at everything.
  (Deps: all S*, H*.)
- **J — Judge.** Sections: B.6, C.8, C.9, plus garden/AMENDMENTS.md
  (enacted amendments are law) — nothing else. Fresh session, different
  model than the builders (Codex if built by Claude Code). Execute J0–J8
  with browser automation for screenshots; produce
  `verification/m1/VERDICT.md`. Any FAIL → set FAILED_JUDGMENT on the
  packets the verdict implicates (or I1 if diffuse); a FIXER inherits the
  verdict.

## 6. CLAUDE.md / AGENTS.md template (both product repos)

```
# Ground rules (read every session)
1. You are one runner in a relay governed by ../garden/PLAN.md — run its
   Boot Sequence before anything else.
2. The constitution is docs/SPEC.md (v2.4): sections 1 -> 2 -> B -> C; read
   fully the sections your packet names.
3. You are in Milestone M1 unless your charge says otherwise. Feature
   ledger (SPEC B.4) applies: FORBIDDEN means do not build, stub, or
   "prepare for" — flag instead (garden/FLAGS.md).
4. Every non-dictated decision -> DECISIONS.md, citing a Problem Tree node
   (SPEC §2). Features that cannot name their problem do not get built.
5. Defects -> Blight Protocol (SPEC 2.1): deepest containing node;
   escalate ancestors/contracts via FLAG.
6. Contracts are literal: DDL C.2, API C.4, envelope C.7, invariants 1.3.
   A contract gap or self-contradiction is a COMPLETION (PLAN §2): enact
   it in garden/AMENDMENTS.md and proceed — never guess silently, never
   stall on what qualifies.
7. Done means judged (SPEC B.6): leave experiential + traced evidence.
8. One packet per session. Handoff ritual, then stop.
9. Invariant 14 is the product's soul: LEAST ATTENTION. Walls, not
   questions. Nothing you build may demand the human's attention except
   judge-released returns and true boundary crossings; everything you
   build must be watchable without demanding. When a design choice trades
   your convenience against the human's attention, attention wins.
```

## 7. Tool roles & human calibration gates

- **Claude Code:** primary builder; pre-commit hook greps FORBIDDEN
  patterns (weight updates, extraction, relay client, maintenance jobs,
  auth beyond bearer) and blocks with a pointer to SPEC B.4.
- **Codex:** the judge (fresh sessions, Playwright), and occasional
  second-opinion reviewer of PRs.
- **Code Puppy:** daily driver; after S4, pointing a Code Puppy experiment
  at the spine API is the first live test of ADR-002's bidirectional
  coupling.
- **Human gates (you, between sessions):**
  - after P0 — journal/report culture check; redo P0 if it reads like a
    contractor wrote it (cheapest possible recalibration).
  - after S3 — hand-verify one injection's scores against C.3 by
    calculator (catches golden tests written from code instead of spec).
  - after D1 — review the relay handoff and its redacted cloud evidence.
    Agents have the narrow D1 grant above; billing, budget, D2 deployment,
    destructive recovery, and any authority expansion remain yours.
  - after H5 — use the gate yourself for a day of real prompts; the gate
    is the product's soul and no judge substitutes for your hands.
  - after J — read VERDICT.md beside its screenshots; only then is M1
    done, and M2 planning opens (SPEC B.1; M3 re-plans too).
- Otherwise your between-session review is: the diff, the DECISIONS.md
  entries, the handoff report, and any new AMENDMENTS.md entries — an
  amendment stands unless you veto it there (append the veto; a FIXER
  inherits it). Supervise the relay closely through S2; loosen as the
  reports earn it.
