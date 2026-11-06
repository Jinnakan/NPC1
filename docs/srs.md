# SRS: Course Project Management System (ระบบจัดการโปรเจกต์รายวิชา)

Version 1.0 · 2026-11-06 · CPE 334 Deliverable 1, Parts A

## 1. Stakeholder problem and context

Course projects are coordinated over LINE, email and shared drives. Supervisors cannot see which teams have an approved topic, which proposals wait for review, or who has fallen behind. Students cannot tell what state their proposal is in or why it was sent back.

The product is a web application where student teams propose and deliver a course project and supervisors review, give feedback and track progress. Context: one university course, teams of 1 to 2 students, one or more supervisors per course.

## 2. Actors

| Actor | Responsibilities |
|---|---|
| Student | Belongs to one team per course. Creates, edits and submits the team's project proposal. Reads feedback. Later: submits milestone deliverables. |
| Supervisor | Assigned to teams. Reviews submitted proposals (approve, request revision, reject) with feedback. Later: defines milestones and reviews submissions. |
| Admin | Sprint 3 only. Manages courses, users and team/supervisor assignments. Before Sprint 3 this is done through seed data. |

## 3. Scope

In scope: authentication, project proposal lifecycle, supervisor review, role-scoped views (Sprint 1); teams, milestones, submissions, feedback, dashboard (Sprint 2); admin tools, notifications, deadlines, attachments, audit log, hardening (Sprint 3).

Explicit exclusions: self-registration, password recovery, advanced account administration, real-time chat, grading and plagiarism detection, email delivery to external addresses, mobile apps.

## 4. Project lifecycle

`DRAFT -> SUBMITTED -> APPROVED | REVISION_REQUESTED | REJECTED`, and `REVISION_REQUESTED -> SUBMITTED`.
Active statuses: DRAFT, SUBMITTED, REVISION_REQUESTED, APPROVED. REJECTED is terminal and not active.

## 5. Functional requirements

| ID | Requirement | Actor | Sprint |
|---|---|---|---|
| FR-01 | A user logs in with email and password and receives an authenticated session. | All | 1 |
| FR-02 | A user logs out and the session can no longer be used. | All | 1 |
| FR-03 | The system identifies the current user (id, name, role) from the session only. | All | 1 |
| FR-04 | A student creates a project proposal (title, description) in DRAFT for their own team. | Student | 1 |
| FR-05 | A student edits the team's project while it is DRAFT or REVISION_REQUESTED. | Student | 1 |
| FR-06 | A student submits the project (DRAFT or REVISION_REQUESTED to SUBMITTED). | Student | 1 |
| FR-07 | A supervisor approves a SUBMITTED project of an assigned team. | Supervisor | 1 |
| FR-08 | A supervisor requests revision of a SUBMITTED project with written feedback. | Supervisor | 1 |
| FR-09 | A supervisor rejects a SUBMITTED project with written feedback. | Supervisor | 1 |
| FR-10 | Users list and view projects limited to their role scope, including review history. | Student, Supervisor | 1 |
| FR-11 | A student team is managed (add/remove members within size limit). | Student | 2 |
| FR-12 | A supervisor defines milestones with due dates for an approved project. | Supervisor | 2 |
| FR-13 | A student submits a deliverable for a milestone. | Student | 2 |
| FR-14 | A supervisor reviews a milestone submission with feedback. | Supervisor | 2 |
| FR-15 | A dashboard shows project and milestone progress per role. | Student, Supervisor | 2 |
| FR-16 | An admin manages users, courses and team/supervisor assignments. | Admin | 3 |
| FR-17 | Users receive in-app notifications on status changes. | All | 3 |
| FR-18 | Overdue milestones are flagged as late. | All | 3 |
| FR-19 | Students attach files to submissions. | Student | 3 |
| FR-20 | Every state transition is recorded in an audit log. | Admin | 3 |

## 6. Business rules

| ID | Rule |
|---|---|
| BR-01 | A team cannot have a second active project in the same course. |
| BR-02 | Only members of the owning team may edit or submit a project, and only while it is DRAFT or REVISION_REQUESTED. |
| BR-03 | Only the transitions in section 4 are allowed; any other transition is rejected and the status is unchanged. |
| BR-04 | A supervisor may review only SUBMITTED projects of teams assigned to them. |
| BR-05 | A REVISION_REQUESTED or REJECTED decision requires non-blank feedback (max 2000 characters). |
| BR-06 | A student can read only their own team's projects; a supervisor only assigned teams' projects. Out-of-scope records are indistinguishable from non-existent ones. |
| BR-07 | Title is 3 to 120 characters; description is 10 to 2000 characters. |
| BR-08 | A review is immutable once stored. The project status equals the outcome of its latest review or submission. |
| BR-09 | A student belongs to at most one team per course; a team has 1 to 2 students. |

## 7. Non-functional requirements

| ID | Requirement and target | Verification | Sprint |
|---|---|---|---|
| NFR-01 | An unauthenticated request to any protected endpoint is rejected with 401 and no private data. | API test over every protected route | 1 |
| NFR-02 | Passwords are stored only as bcrypt hashes (cost 10 or higher) and never appear in any API response. | Unit test on hashing, API test on response bodies | 1 |
| NFR-03 | Session cookie is httpOnly, SameSite=Lax, expires in 8 hours; logout invalidates it server-side. | API test on cookie flags, logout and expiry | 1 |
| NFR-04 | Invalid input returns 422 with field errors and no stack trace or internal detail. | API tests on invalid payloads | 1 |
| NFR-05 | A fresh clone is runnable with README steps: install, set env, migrate, seed, run, test. | Manual run on a clean checkout, recorded in Part F | 1 |
| NFR-06 | The app is configured only by environment variables and keeps state only in PostgreSQL, so it can be containerized. | Design review in SDS; Docker build in Lab 5 | 1 design, 3 verify |
| NFR-07 | Every screen shows loading, empty and error states. | E2E and component tests | 1 for Sprint 1 screens, 2 and 3 for the rest |
| NFR-08 | Line coverage of service layer is 80 percent or higher. | Vitest coverage report | 1 |
| NFR-09 | List and detail endpoints respond in under 500 ms (p95) with a 1,000 project dataset. | Scripted load check | 3 |
| NFR-10 | Forms are labelled and operable by keyboard. | Manual checklist plus Playwright axe check | 3 |

## 8. Assumptions, decisions and questions

| ID | Item | Resolution |
|---|---|---|
| D-01 | Stack | Next.js, Prisma, PostgreSQL, Vitest, Playwright. Decided 2026-11-06. |
| D-02 | Business rules location | Service layer behind REST route handlers, not UI or Server Actions. Decided 2026-11-06. |
| D-03 | Auth | Custom bcrypt login with a `Session` table and cookie, not Auth.js. Decided 2026-11-06. |
| D-04 | HTTP status convention | 401 unauthenticated, 403 wrong role, 404 out-of-scope record, 409 rule/state conflict, 422 validation. |
| A-01 | Teams have 1 to 2 students; team and supervisor assignment come from seed data until Sprint 3. | Assumed, confirmed by the team 2026-11-06. |
| A-02 | Theme is a proposal-approval workflow. `group_work_specs.docx` was not available when this was written. | Open: re-check against the master spec and record any change in section 8. |
