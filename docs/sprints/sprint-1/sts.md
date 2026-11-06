# Sprint 1 Software Test Specification (STS)

Version 1.0 · 2026-11-06 · Levels: U unit (service/validators), I API plus real PostgreSQL integration, E UI end-to-end (Playwright). Seeded fixtures: teams A and B, student a1 in A, student b1 in B, supervisor s1 assigned to A, supervisor s2 assigned to B.

| Test | AC | Level | Precondition or input | Action | Expected result |
|---|---|---|---|---|---|
| TEST-01 | AC-01 | I | Seeded user a1 | POST login with correct credentials | 200, cookie httpOnly and SameSite=Lax, body has id/name/email/role and no password or hash |
| TEST-02 | AC-02 | I | Wrong password; unknown email | POST login for each | 401 with identical message, no cookie, no Session row |
| TEST-03 | AC-03 | I | Logged-in a1; no cookie | GET /api/auth/me both ways | 200 with a1; 401 |
| TEST-04 | AC-04 | I | Logged-in a1 | POST logout, then GET /me with old cookie | 204, Session row gone, cookie cleared; 401 |
| TEST-05 | AC-05 | I | No cookie | Call every protected route (list below in test file) | Each returns 401, body has no project or user data |
| TEST-06 | AC-06 | I | Session with expiresAt in the past | GET /me | 401 |
| TEST-07 | AC-07 | I | a1, team A has no project | POST /api/projects valid | 201, status DRAFT, teamId = A, row stored |
| TEST-08 | AC-08 | U, I | Titles of 2 and 121 chars, description 9 and 2001 chars | POST /api/projects each | 422 with field errors; no row stored |
| TEST-09 | AC-09 | U, I | Team A has an active project; second case: only REJECTED | POST /api/projects | 409 and still one active project; REJECTED case returns 201 |
| TEST-10 | AC-10 | U, I | Projects in each of 5 statuses | PATCH each as a1 | DRAFT and REVISION_REQUESTED: 200 persisted; others: 409 unchanged |
| TEST-11 | AC-11 | U, I | Projects in each of 5 statuses | POST submit each | DRAFT and REVISION_REQUESTED: SUBMITTED with submittedAt; others: 409 |
| TEST-12 | AC-12 | I | Team A project; b1 logged in | PATCH and submit as b1 | 404, project unchanged |
| TEST-13 | AC-13 | I | s1 logged in | POST create, PATCH, submit | 403 each, nothing changed |
| TEST-14 | AC-14 | I | SUBMITTED project of A; s1 | POST review APPROVED | 201, project APPROVED, Review row with s1 and decision |
| TEST-15 | AC-15 | I | SUBMITTED project of A (two runs); s1 | POST review REVISION_REQUESTED and REJECTED with feedback | Status REVISION_REQUESTED and REJECTED, Review stored each time |
| TEST-16 | AC-16 | U, I | SUBMITTED project; feedback missing, "", "   " | POST review REJECTED and REVISION_REQUESTED | 422, status still SUBMITTED, zero Review rows |
| TEST-17 | AC-17 | U, I | Projects in DRAFT, APPROVED, REJECTED | POST review as s1 | 409 each, no Review rows |
| TEST-18 | AC-18 | I | SUBMITTED project of A; s2 logged in | POST review; GET detail | 404 both; no Review row; no project data |
| TEST-19 | AC-19 | I | SUBMITTED project of A; a1 logged in | POST review | 403, no Review row |
| TEST-20 | AC-20 | I | Projects in A and B; a1 | GET /api/projects | Only A's projects |
| TEST-21 | AC-21 | I | A has SUBMITTED and DRAFT projects; s1 | GET /api/projects | Only A's projects, SUBMITTED first; B's absent |
| TEST-22 | AC-22 | I | Project of A; b1 and s2 | GET /api/projects/:id | 404 for each, no data |
| TEST-23 | AC-23 | I | Project of A with two reviews; a1 and s1 | GET /api/projects/:id | reviews array with decision, feedback, reviewerName, createdAt, newest first |
| TEST-24 | AC-24 | E | API stubbed to delay, return empty, then 500 | Open /projects | Loading indicator, empty message, error banner with working Retry |
| TEST-25 | AC-25 | I | Empty test database | Run migrate, seed, seed again; insert a1 into team B | Demo rows exist once; duplicates absent; membership insert violates constraint |
| TEST-26 | AC-07, AC-11, AC-14, AC-23 | E | Seeded a1 and s1 | a1 creates and submits; s1 approves; a1 reopens project | a1 sees APPROVED and the review in history |
| TEST-27 | AC-16, AC-15 | E | SUBMITTED project of A; s1 | s1 chooses Reject with empty feedback, then with feedback | First attempt shows field error and status unchanged; second sets REJECTED |
| TEST-28 | AC-01, AC-04, AC-19 | E | Seeded users | Login as a1, open review route directly; logout, open /projects | Review form absent and API 403; after logout redirected to /login |
| TEST-29 | AC-01 | U | Plain password | Hash and verify | Stored value differs from password, bcrypt cost 10 or higher, verify true for correct and false for wrong password |

## TDD evidence plan
For TEST-09, TEST-16, TEST-17 and TEST-18 (rule work), commit the failing test first (message `test: ... (red)`), record the failing run output, then commit the implementation (`feat: ... (green)`) and record the passing run.

## Coverage commands
`npm run test` (unit and API), `npm run test:coverage` (service layer, target 80 percent), `npm run test:e2e` (Playwright).
