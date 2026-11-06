# F-05 Database, seed and setup (responsibility)

Contract v1.0 · 2026-11-06 · Requirements: NFR-05, NFR-06, NFR-08 · Depends on: none

## Scope
Foundation shared by F-01 to F-04: Next.js project, Prisma schema and first migration, idempotent seed, environment configuration, test harness, README. No user-facing behavior.

## Design
- Prisma schema implements the entities and constraints in `docs/system-sds.md` section 2, including the partial unique index (raw SQL in the migration) and unique (userId, courseId) on TeamMember.
- Seed (`prisma/seed.ts`): 1 course, 2 supervisors, 4 students, 2 teams of 2 with one supervisor each, one project per team in different states (for example SUBMITTED and DRAFT). Uses upsert so reruns do not duplicate. Password from `SEED_PASSWORD`.
- Config: `.env.example` lists DATABASE_URL, SESSION_TTL_HOURS, SEED_PASSWORD. Tests use a separate database via `DATABASE_URL_TEST`, reset between test files.
- README: prerequisites, install, env setup, migrate, seed, run, test, coverage commands.
- UI and API: no change.

## Acceptance criteria

| AC | Criterion |
|---|---|
| AC-25 | On an empty database, migrate then seed succeeds and creates the demo users and teams; running seed a second time creates no duplicates; a student cannot be inserted into two teams of the same course. |
