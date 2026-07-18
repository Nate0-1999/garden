# M1 Relay Board

Packets are claimed and completed according to `PLAN.md`. Row order is the
deterministic packet order when more than one dependency-ready packet exists.

| id | packet                         | deps                              | status      | claim                        | verdict |
|----|--------------------------------|-----------------------------------|-------------|------------------------------|---------|
| P0 | Agent Zero                     | —                                 | DONE        | codex / 2026-07-17 / 70ed    |         |
| S1 | DB layer & CAS rules           | P0                                | DONE        | codex / 2026-07-17 / 5f1c    |         |
| S2 | Memory CRUD & dedup bands      | S1                                | IN_PROGRESS | codex / 2026-07-17 / c842    |         |
| S3 | Scorer v0 + inject/prepare     | S2                                | TODO        |                              |         |
| S4 | Commit, feedback, quarantine   | S3                                | TODO        |                              |         |
| H1 | Envelope + daemon WS           | P0                                | TODO        |                              |         |
| H2 | spine_client + contract tests  | S2                                | TODO        |                              |         |
| H3 | Agent + memory tools           | H2                                | TODO        |                              |         |
| H4 | Web shell + chat               | H1                                | TODO        |                              |         |
| H5 | The gate                       | S4, H4                            | TODO        |                              |         |
| H6 | Memory panel                   | H5                                | TODO        |                              |         |
| I1 | Integration & AC dry run       | S1-S4, H1-H6                      | TODO        |                              |         |
| J  | Judge                          | I1                                | TODO        |                              |         |

Statuses: `TODO` · `IN_PROGRESS` · `DONE` · `BLOCKED` · `FAILED_JUDGMENT`.
