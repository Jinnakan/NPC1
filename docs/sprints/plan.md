# Three-sprint plan

Version 1.0 · 2026-11-06 · Deliverable 1, Part D

| Sprint | Goal | Features | Dependencies | Completion outcome | Checkpoint |
|---|---|---|---|---|---|
| 1 | Working proposal-review slice with authentication, permissions and persistence | F-01, F-02, F-03, F-04, F-05 | none | A student submits a proposal and a supervisor decides on it through UI, API and database. Login, logout, role restrictions and business-rule rejection are tested. Released and tagged on main. | Deliverable 1, 8 Nov |
| 2 | Delivery tracking | F-06, F-07, F-08, F-09, F-10 | Sprint 1 (approved project, roles, team data) | Approved projects get milestones; students submit, supervisors give feedback; dashboards show progress. Each feature has a contract, AC and tests. | Deliverable 2, 29 Nov |
| 3 | Remaining features and hardening | F-11 to F-16 | Sprints 1 and 2 | Admin tools, notifications, late flags, attachments and audit log done; NFR-09 and NFR-10 verified; Docker, CI/CD and GCP deployment in Lab 5. | Progress 29 Nov; complete 13 Dec |

Together the three sprints cover every FR, BR and NFR in `docs/traceability.md`.

## Scope of Sprint 1

In: F-01 to F-05 only, with two seeded roles (Student, Supervisor), two seeded teams and seeded supervisor assignment.
Out: team editing, milestones, attachments, notifications, admin UI, Docker and deployment.
Feasibility: Sprint 1 has roughly two working days left, so features are kept to the smallest thin slice each: no pagination, plain forms, no styling beyond usability states.

## Sprint 1 workflow

- One GitHub Issue per feature, linked to its contract in `docs/sprints/sprint-1/`.
- Branches `feature/F-0x-name` from `sprint1-staging`; teammate reviews each PR into staging; the author does not approve their own PR.
- Staging release PR into `main` after integration tests; tag `v0.1.0-sprint1`.
- Contract and test specification are committed before implementation. TDD: the failing test commit precedes the fix commit for rule work.
