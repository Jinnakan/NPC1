# F-02 Project authoring and submission

Contract v1.0 · 2026-11-06 · Requirements: FR-04, FR-05, FR-06, BR-01, BR-02, BR-03, BR-07, BR-09, NFR-04 · Depends on: F-01, F-05

## Behavior
A student creates a project for their own team, edits it while allowed, and submits it for review. After a revision request the student edits and resubmits.

Workflow: create (DRAFT) -> edit -> submit (SUBMITTED). From REVISION_REQUESTED: edit -> submit (SUBMITTED).
Business rules: BR-01 one active project per team; BR-02 only own-team students, only DRAFT or REVISION_REQUESTED; BR-03 allowed transitions only; BR-07 length limits; the team is derived from the session user's membership, never from the request.
Failure and boundary cases: title 2 or 121 characters, description 9 or 2001 characters, user without a team (403), second project while one is active, a REJECTED project does not block a new one.
Permissions: Student only. Supervisor gets 403. A project of another team is reported as 404.

## UI
`/projects/new`: title and description fields with per-field errors, Save draft button. `/projects/[id]`: for a student on own team, edit form visible only in DRAFT and REVISION_REQUESTED, Submit button, status badge, latest feedback shown above the form after a revision request.

## API

| Method and path | Request | Success | Errors |
|---|---|---|---|
| POST /api/projects | `{title, description}` | 201 project `{id, teamId, title, description, status:"DRAFT", ...}` | 401, 403, 409 `ACTIVE_PROJECT_EXISTS`, 422 |
| PATCH /api/projects/:id | `{title?, description?}` | 200 project | 401, 403, 404, 409 `INVALID_STATE`, 422 |
| POST /api/projects/:id/submit | none | 200 project with `status:"SUBMITTED"`, `submittedAt` set | 401, 403, 404, 409 `INVALID_STATE` |

## Data
`Project` and its partial unique index on active statuses; `TeamMember` unique (userId, courseId). Introduced by F-05 migration. No further change.

## Acceptance criteria

| AC | Criterion |
|---|---|
| AC-07 | A student on a team creates a project with valid fields: 201, status DRAFT, teamId equals the student's team, and the project is stored. |
| AC-08 | Title or description outside BR-07 limits returns 422 with field errors; nothing is stored. |
| AC-09 | If the team already has an active project, creation returns 409 and no second project is stored. A team whose only project is REJECTED may create a new one. |
| AC-10 | Editing a DRAFT or REVISION_REQUESTED project returns 200 and persists the change; editing a SUBMITTED, APPROVED or REJECTED project returns 409 and changes nothing. |
| AC-11 | Submitting a DRAFT or REVISION_REQUESTED project sets SUBMITTED and `submittedAt`; submitting a SUBMITTED, APPROVED or REJECTED project returns 409. |
| AC-12 | A student from another team editing or submitting this team's project gets 404 and nothing changes. |
| AC-13 | A supervisor calling create, edit or submit gets 403 and nothing changes. |
