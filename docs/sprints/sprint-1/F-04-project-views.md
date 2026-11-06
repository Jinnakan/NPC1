# F-04 Role-scoped project views

Contract v1.0 · 2026-11-06 · Requirements: FR-10, BR-06, NFR-01, NFR-07 · Depends on: F-01, F-02, F-03

## Behavior
Students see their own team's projects; supervisors see projects of teams they supervise. Detail view includes the review history. Out-of-scope projects behave as non-existent.

Business rules: BR-06 scope by membership or supervision; supervisor list orders SUBMITTED first, then by `updatedAt` descending; student list by `updatedAt` descending.
Failure and boundary cases: user with no team or no assigned teams sees an empty list (200, `[]`); malformed project id (404); no pagination in Sprint 1.
Permissions: any authenticated Student or Supervisor, within scope.

## UI
`/projects`: table of title, team, status, last updated; loading indicator, empty message ("No projects yet"), error banner with Retry. Student with no project sees a "Create project" button. Row opens `/projects/[id]`. Detail shows fields, status badge, and review history (decision, reviewer name, feedback, timestamp), newest first.

## API

| Method and path | Request | Success | Errors |
|---|---|---|---|
| GET /api/projects | none | 200 `{projects:[{id,title,teamId,teamName,status,updatedAt}]}` scoped by role | 401 |
| GET /api/projects/:id | none | 200 `{project:{..., reviews:[{id,decision,feedback,reviewerName,createdAt}]}}` | 401, 404 |

## Data
Read-only over `Project`, `Review`, `Team`, `TeamMember`. No change.

## Acceptance criteria

| AC | Criterion |
|---|---|
| AC-20 | A student's list contains only their own team's projects. |
| AC-21 | A supervisor's list contains only projects of assigned teams, SUBMITTED first. |
| AC-22 | A student of another team, or an unassigned supervisor, requesting a project detail gets 404 with no project data. |
| AC-23 | Detail returns the review history (decision, feedback, reviewer name, timestamp) to the owning team and the assigned supervisor, newest first. |
| AC-24 | The list screen shows a loading state, an empty state with no projects, and an error state with retry when the API fails. |
