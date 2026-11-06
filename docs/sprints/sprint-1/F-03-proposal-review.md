# F-03 Supervisor proposal review

Contract v1.0 · 2026-11-06 · Requirements: FR-07, FR-08, FR-09, BR-03, BR-04, BR-05, BR-08 · Depends on: F-01, F-02

## Behavior
A supervisor opens a SUBMITTED project of an assigned team and records one decision: approve, request revision, or reject. A Review row is stored and the project status changes in the same transaction.

Business rules: BR-03 only SUBMITTED may be reviewed; BR-04 only the team's assigned supervisor; BR-05 feedback required for REVISION_REQUESTED and REJECTED (optional for APPROVED); BR-08 reviews are never edited or deleted.
Failure and boundary cases: feedback of only whitespace (422), feedback of 2001 characters (422), unknown decision value (422), two concurrent reviews (the second sees a non-SUBMITTED project and gets 409; status update is conditional on current status).
Permissions: Supervisor assigned to the team only. Student gets 403. Unassigned supervisor gets 404.

## UI
On `/projects/[id]` a supervisor of an assigned team sees, when status is SUBMITTED, a review form: decision radio (Approve, Request revision, Reject), feedback textarea, Submit decision button. Feedback error shows next to the textarea. After success the page shows the new status and the review in history.

## API

| Method and path | Request | Success | Errors |
|---|---|---|---|
| POST /api/projects/:id/review | `{decision: "APPROVED"\|"REVISION_REQUESTED"\|"REJECTED", feedback?: string}` | 201 `{review:{id,decision,feedback,createdAt}, project:{id,status}}` | 401, 403, 404, 409 `INVALID_STATE`, 422 |

## Data
`Review` table (insert only). Status update on `Project` guarded with `WHERE status = 'SUBMITTED'`.

## Acceptance criteria

| AC | Criterion |
|---|---|
| AC-14 | The assigned supervisor approves a SUBMITTED project: project becomes APPROVED and a Review with that supervisor and decision is stored. |
| AC-15 | The assigned supervisor requests revision or rejects with feedback: project becomes REVISION_REQUESTED or REJECTED respectively and the Review is stored. |
| AC-16 | A revision or reject decision with missing or blank feedback returns 422; status is unchanged and no Review is stored. |
| AC-17 | Reviewing a project that is not SUBMITTED returns 409; no Review is stored. |
| AC-18 | A supervisor not assigned to the project's team gets 404; no Review is stored and no project data is returned. |
| AC-19 | A student calling the review endpoint gets 403; no Review is stored. |
