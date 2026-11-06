# Feature inventory

Version 1.0 · 2026-11-06 · Deliverable 1, Part B

| ID | Feature or responsibility | Description | Requirements | Depends on | Sprint |
|---|---|---|---|---|---|
| F-01 | Authentication and session | Login, logout, current user, session expiry, protected-route gate | FR-01, FR-02, FR-03, NFR-01, NFR-02, NFR-03 | F-05 | 1 |
| F-02 | Project authoring and submission | Create, edit, submit and resubmit a team project | FR-04, FR-05, FR-06, BR-01, BR-02, BR-03, BR-07, BR-09, NFR-04 | F-01, F-05 | 1 |
| F-03 | Supervisor proposal review | Approve, request revision or reject with feedback; immutable review record | FR-07, FR-08, FR-09, BR-03, BR-04, BR-05, BR-08 | F-01, F-02 | 1 |
| F-04 | Role-scoped project views | Project list and detail with review history, scoped by role and team | FR-10, BR-06, NFR-01, NFR-07 | F-01, F-02, F-03 | 1 |
| F-05 | Database, seed and setup (responsibility) | Prisma schema, migrations, reproducible seed, env config, README, test harness | NFR-05, NFR-06, NFR-08 | none | 1 |
| F-06 | Team management | Add and remove team members within size limit | FR-11, BR-09 | F-01 | 2 |
| F-07 | Milestones | Supervisor defines milestones with due dates | FR-12 | F-03 | 2 |
| F-08 | Milestone submission | Student submits a deliverable for a milestone | FR-13 | F-07 | 2 |
| F-09 | Milestone review | Supervisor gives feedback on a submission | FR-14 | F-08 | 2 |
| F-10 | Dashboard | Progress overview per role | FR-15 | F-07, F-08, F-09 | 2 |
| F-11 | Admin management | Users, courses and assignments | FR-16 | F-01 | 3 |
| F-12 | Notifications | In-app notifications on status change | FR-17 | F-03, F-09 | 3 |
| F-13 | Deadlines and late flag | Overdue detection on milestones | FR-18 | F-07 | 3 |
| F-14 | Attachments | File upload on submissions | FR-19 | F-08 | 3 |
| F-15 | Audit log | Record of every state transition | FR-20 | F-03 | 3 |
| F-16 | Product hardening (responsibility) | Performance check, accessibility pass, deployment readiness, final NFR verification | NFR-06, NFR-07, NFR-09, NFR-10 | all | 3 |
