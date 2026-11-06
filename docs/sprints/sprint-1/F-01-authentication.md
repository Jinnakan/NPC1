# F-01 Authentication and session

Contract v1.0 · 2026-11-06 · Requirements: FR-01, FR-02, FR-03, NFR-01, NFR-02, NFR-03 · Depends on: F-05

## Behavior
A user submits email and password. On success the server creates a session and sets an httpOnly cookie. Protected endpoints and pages require a valid session. Logout deletes the session.

Business rules: generic failure message for unknown email and wrong password; sessions expire after SESSION_TTL_HOURS; passwords are never returned.
Failure and boundary cases: missing fields (422), malformed JSON (400), expired session (401), reused cookie after logout (401), email case differences (login is case-insensitive on email).
Permissions: login is open; all other endpoints require a session.

## UI
`/login`: email and password fields, submit button, error banner for 401 or 422, disabled button while loading. Success redirects to `/projects`. Header on every protected page shows name, role and Logout. Any protected page without a session redirects to `/login`.

## API

| Method and path | Request | Success | Errors |
|---|---|---|---|
| POST /api/auth/login | `{email, password}` | 200 `{user:{id,name,email,role}}` and `Set-Cookie: sid=...` | 401 `INVALID_CREDENTIALS`, 422 `VALIDATION_ERROR`, 400 |
| POST /api/auth/logout | none | 204, cookie cleared, session row deleted | 401 |
| GET /api/auth/me | none | 200 `{user:{id,name,email,role}}` | 401 `UNAUTHENTICATED` |

## Data
Uses `User` and `Session` (see system-sds.md). No schema change beyond F-05.

## Acceptance criteria

| AC | Criterion |
|---|---|
| AC-01 | Valid credentials return 200, set an httpOnly SameSite=Lax cookie, and return the user without any password or hash field. |
| AC-02 | Wrong password or unknown email returns 401 with the same message, sets no cookie, and creates no session. |
| AC-03 | `GET /api/auth/me` with a valid session returns the current user; without a session it returns 401. |
| AC-04 | Logout deletes the session row and clears the cookie; the old cookie then returns 401 on `/api/auth/me`. |
| AC-05 | Every protected endpoint (all `/api/projects*` and `/api/auth/me`, `/api/auth/logout`) returns 401 with no private data when unauthenticated. |
| AC-06 | A session past its expiry returns 401. |
