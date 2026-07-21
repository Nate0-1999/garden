# M1 Relay Board

Packets are claimed and completed according to `PLAN.md`. Row order is the
deterministic packet order when more than one dependency-ready packet exists.

| id | packet                         | deps                              | status      | claim                        | verdict |
|----|--------------------------------|-----------------------------------|-------------|------------------------------|---------|
| P0 | Agent Zero                     | —                                 | DONE        | codex / 2026-07-17 / 70ed    |         |
| S1 | DB layer & CAS rules           | P0                                | DONE        | codex / 2026-07-17 / 5f1c    |         |
| S2 | Memory CRUD & dedup bands      | S1                                | DONE        | codex / 2026-07-17 / c842    |         |
| S3 | Scorer v0 + inject/prepare     | S2                                | DONE        | codex / 2026-07-19 / b73a    |         |
| S4 | Commit, feedback, quarantine   | S3, S5                            | DONE        | codex / 2026-07-19 / a4c9    |         |
| S5 | origin_path metadata           | S3                                | DONE        | codex / 2026-07-19 / e5a7    |         |
| S6 | /v1/search                     | S2                                | DONE        | codex / 2026-07-19 / f6a2    |         |
| D1 | GCP deploy & remote verify     | S4, S7                            | DONE        | codex / 2026-07-20 / b82e    | report 017 |
| S7 | Broker-routed embeddings       | S2                                | DONE        | codex / 2026-07-20 / c105    |         |
| D2 | Billing circuit breaker        | P0                                | DONE        | codex / 2026-07-19 / b61b    |         |
| H1 | Envelope + daemon WS           | P0                                | DONE        | codex / 2026-07-20 / 25ff    |         |
| H2 | spine_client + contract tests  | S2                                | DONE        | codex / 2026-07-20 / db84    |         |
| H3 | Agent + memory tools           | H2, S6                            | DONE        | codex / 2026-07-20 / 7c3a    |         |
| H4 | Web shell + chat               | H1, H7                            | DONE        | codex / 2026-07-20 / c84d    |         |
| H5 | The gate                       | D1, H4                            | IN_PROGRESS | codex / 2026-07-21 / a5e1    |         |
| H6 | Memory panel                   | H5                                | TODO        |                              |         |
| H7 | Envelope v1.12 & loop controls | H1                                | DONE        | codex / 2026-07-20 / 9d2f    |         |
| D3 | Deploy & onboarding command    | J                                 | TODO        |                              |         |
| I1 | Integration & AC dry run       | S1-S6, H1-H6                      | TODO        |                              |         |
| J  | Judge                          | I1                                | TODO        |                              |         |

Statuses: `TODO` · `IN_PROGRESS` · `DONE` · `BLOCKED` · `FAILED_JUDGMENT`.
